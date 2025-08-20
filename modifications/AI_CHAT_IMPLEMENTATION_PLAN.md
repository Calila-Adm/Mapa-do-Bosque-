# Planejamento - Implementação do Chat LLM Avançado

Este documento detalha o plano completo para implementar um **Chat LLM Contextual** no canto inferior esquerdo de cada notebook, similar ao disponível na versão premium do Briefer.

---

## 🎯 **Visão Geral do Recurso**

### **Funcionalidades Desejadas:**
- 💬 **Chat flutuante** no canto inferior esquerdo dos notebooks
- 🧠 **LLM avançada** com melhor compreensão de contexto
- 📊 **Contexto de Data Sources** automático
- 🔍 **Execução de queries** direto do chat
- 💡 **Sugestões inteligentes** baseadas no notebook atual
- 📝 **Histórico de conversas** persistente

### **Análise do Sistema Atual:**
O projeto **já possui** infraestrutura básica de AI:
- ✅ **API Python** com FastAPI em `/ai/`
- ✅ Hook `useAI` para comunicação com AI
- ✅ API endpoints para SQL/Python edit
- ✅ Streaming com FastAPI StreamingResponse
- ✅ Integração com LangChain
- ❌ **Não possui** chat conversacional
- ❌ **Não possui** integração com Claude/Anthropic
- ❌ **Não possui** contexto de data sources

---

## 🏗️ **Arquitetura Proposta**

### **1. Frontend - Componente de Chat**

```typescript
// Estrutura de arquivos:
/apps/web/src/components/AIChat/
├── index.tsx              // Componente principal
├── ChatWindow.tsx         // Janela do chat
├── ChatMessage.tsx        // Mensagem individual
├── ChatInput.tsx          // Input do usuário
├── ChatContext.tsx        // Context API para estado
├── FloatingButton.tsx     // Botão flutuante
└── hooks/
    ├── useChat.ts         // Hook principal do chat
    ├── useDataContext.ts  // Hook para contexto de dados
    └── useChatHistory.ts  // Hook para histórico
```

### **2. Backend - API do Chat (Python)**

```python
# Estrutura de arquivos - Expandindo a pasta /ai existente:
/ai/
├── api/
│   ├── app.py             # FastAPI app existente
│   ├── llms.py            # LLMs config existente
│   └── chains/
│       ├── stream/        # Chains existentes
│       └── chat/          # NOVO - Chat chains
│           ├── __init__.py
│           ├── conversation.py  # Chain de conversação
│           ├── context.py       # Contexto de dados
│           └── prompts.py       # System prompts
├── services/              # NOVO - Serviços
│   ├── __init__.py
│   ├── data_source.py    # Contexto de data sources
│   ├── schema_cache.py   # Cache LRU para schemas
│   └── history.py        # Gerenciamento de histórico
└── requirements.txt       # Adicionar anthropic SDK
```

---

## 📋 **Plano de Implementação Detalhado**

### **FASE 1: Chat Básico (3-4 dias)**

#### **1.1 Criar Componente de Chat Flutuante**

**Arquivo:** `/apps/web/src/components/AIChat/FloatingButton.tsx`
```tsx
import { ChatBubbleLeftRightIcon } from '@heroicons/react/24/outline'
import { useState } from 'react'
import ChatWindow from './ChatWindow'

export default function FloatingButton() {
  const [isOpen, setIsOpen] = useState(false)
  
  return (
    <>
      {/* Botão Flutuante */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="fixed bottom-6 left-6 z-50 
                   bg-primary-500 hover:bg-primary-600 
                   text-white rounded-full p-4 shadow-lg
                   transition-all transform hover:scale-110"
      >
        <ChatBubbleLeftRightIcon className="h-6 w-6" />
      </button>
      
      {/* Janela do Chat */}
      {isOpen && (
        <ChatWindow onClose={() => setIsOpen(false)} />
      )}
    </>
  )
}
```

#### **1.2 Implementar Janela do Chat com Error Boundary**

**Arquivo:** `/apps/web/src/components/AIChat/ChatWindow.tsx`
```tsx
import { ErrorBoundary } from 'react-error-boundary'

function ChatWindowContent({ onClose }) {
  const [messages, setMessages] = useState([])
  const [input, setInput] = useState('')
  const { sendMessage, loading } = useChat()
  
  return (
    <div className="fixed bottom-20 left-6 z-50 
                    w-96 h-[600px] bg-white rounded-lg shadow-2xl
                    border border-gray-200 flex flex-col">
      {/* Header */}
      <div className="p-4 border-b flex justify-between items-center">
        <h3 className="font-semibold">AI Assistant</h3>
        <button onClick={onClose}>
          <XMarkIcon className="h-5 w-5" />
        </button>
      </div>
      
      {/* Messages */}
      <div className="flex-1 overflow-y-auto p-4">
        {messages.map((msg, i) => (
          <ChatMessage key={i} message={msg} />
        ))}
      </div>
      
      {/* Input com Debounce */}
      <ChatInput 
        value={input}
        onChange={setInput}
        onSend={() => sendMessage(input)}
        loading={loading}
        debounceMs={300} // Evita múltiplas requisições
      />
    </div>
  )
}

// Componente com Error Boundary
export default function ChatWindow({ onClose }) {
  return (
    <ErrorBoundary
      fallback={
        <div className="fixed bottom-20 left-6 z-50 w-96 p-4 bg-red-50 rounded-lg">
          <p className="text-red-600">Erro no chat. Por favor, recarregue a página.</p>
        </div>
      }
      onReset={() => window.location.reload()}
    >
      <ChatWindowContent onClose={onClose} />
    </ErrorBoundary>
  )
}
```

#### **1.3 Adicionar ao Layout do Notebook**

**Modificar:** `/apps/web/src/components/PrivateDocumentPage.tsx`
```tsx
import FloatingAIChat from '@/components/AIChat/FloatingButton'

// Adicionar dentro do componente:
{showAIChat && (
  <FloatingAIChat 
    workspaceId={workspaceId}
    documentId={documentId}
  />
)}
```

---

### **FASE 2: Contexto de Data Sources (2-3 dias)**

#### **2.1 Criar Serviço de Contexto com Cache**

**Arquivo:** `/ai/services/schema_cache.py`
```python
from cachetools import TTLCache
import asyncio
from typing import Dict, Any, Optional
import httpx
import os

class DataSourceContext:
    def __init__(self):
        # Cache com TTL de 10 minutos e máximo de 100 schemas
        self.schema_cache = TTLCache(maxsize=100, ttl=600)
        self.api_base_url = os.getenv("API_BASE_URL", "http://localhost:3000")
    
    async def get_schema_context(self, data_source_id: str, auth_token: str) -> Dict[str, Any]:
        # Verificar cache primeiro
        if data_source_id in self.schema_cache:
            print(f"Schema cache hit for {data_source_id}")
            return self.schema_cache[data_source_id]
        
        # Buscar schemas via API interna
        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"{self.api_base_url}/api/v1/datasources/{data_source_id}/schema",
                headers={"Authorization": f"Bearer {auth_token}"}
            )
            schema = response.json()
        
        formatted = {
            "tables": [
                {
                    "name": table["name"],
                    "columns": [
                        {
                            "name": col["name"],
                            "type": col["type"],
                            "nullable": col.get("nullable", True)
                        }
                        for col in table["columns"]
                    ]
                }
                for table in schema.get("tables", [])
            ]
        }
        
        # Salvar no cache
        self.schema_cache[data_source_id] = formatted
        
        return formatted
    
    async def get_sample_data(self, data_source_id: str, table_name: str, auth_token: str):
        # Buscar dados de exemplo
        query = f"SELECT * FROM {table_name} LIMIT 5"
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.api_base_url}/api/v1/datasources/{data_source_id}/query",
                headers={"Authorization": f"Bearer {auth_token}"},
                json={"query": query}
            )
            return response.json()
    
    def invalidate_cache(self, data_source_id: str):
        """Limpar cache quando schema mudar"""
        if data_source_id in self.schema_cache:
            del self.schema_cache[data_source_id]
```

#### **2.2 Hook para Contexto**

**Arquivo:** `/apps/web/src/components/AIChat/hooks/useDataContext.ts`
```typescript
export function useDataContext(workspaceId: string) {
  const [dataSources] = useDataSources(workspaceId)
  const [activeDataSource, setActiveDataSource] = useState(null)
  const [schema, setSchema] = useState(null)
  
  useEffect(() => {
    if (activeDataSource) {
      fetchSchema(activeDataSource.id).then(setSchema)
    }
  }, [activeDataSource])
  
  return {
    dataSources,
    activeDataSource,
    schema,
    setActiveDataSource
  }
}
```

---

### **FASE 3: Integração com LLM (3-4 dias)**

#### **3.1 API Endpoint do Chat com Claude (Python)**

**Arquivo:** `/ai/api/app.py` (adicionar ao existente)
```python
from anthropic import AsyncAnthropic
from fastapi.responses import StreamingResponse
from pydantic import BaseModel
from typing import List, Optional, Dict, Any
import json

# Adicionar ao arquivo existente

class ChatMessage(BaseModel):
    role: str  # 'user' ou 'assistant'
    content: str

class ChatInputData(BaseModel):
    message: str
    history: List[ChatMessage]
    context: Dict[str, Any]  # Contexto do notebook/data source
    workspaceId: str
    documentId: str
    dataSourceId: Optional[str] = None

@app.post("/v1/stream/chat")
async def v1_stream_chat(data: ChatInputData, _ = Depends(get_current_username)):
    # Inicializar Claude
    anthropic = AsyncAnthropic(
        api_key=config("ANTHROPIC_API_KEY")
    )
    
    # Importar serviços
    from services.schema_cache import DataSourceContext
    from api.chains.chat.prompts import build_system_prompt
    
    # Obter contexto se houver data source
    context_service = DataSourceContext()
    schema_context = None
    
    if data.dataSourceId:
        schema_context = await context_service.get_schema_context(
            data.dataSourceId, 
            # Token seria passado no header
            request.headers.get("Authorization", "").replace("Bearer ", "")
        )
    
    # Construir system prompt com contexto
    system_prompt = build_system_prompt(data.context, schema_context)
    
    # Formatar histórico para Claude
    messages = [
        {
            "role": msg.role if msg.role in ["user", "assistant"] else "assistant",
            "content": msg.content
        }
        for msg in data.history
    ]
    messages.append({"role": "user", "content": data.message})
    
    async def generate():
        # Stream com Claude
        async with anthropic.messages.stream(
            model="claude-3-5-sonnet-20241022",
            system=system_prompt,
            messages=messages,
            max_tokens=4096,
            temperature=0.7
        ) as stream:
            async for chunk in stream:
                if chunk.type == "content_block_delta":
                    yield json.dumps({"text": chunk.delta.text}) + "\n"
    
    return StreamingResponse(generate(), media_type="text/plain")
```

#### **3.2 System Prompt Inteligente**

**Arquivo:** `/ai/api/chains/chat/prompts.py`
```python
from typing import Dict, Any, Optional

def build_system_prompt(context: Dict[str, Any], schema_context: Optional[Dict[str, Any]] = None) -> str:
    """Construir system prompt com contexto do notebook e data source"""
    
    prompt = """You are an advanced data analysis assistant integrated into Mapa do Bosque.

CONTEXT:
- Current notebook: {notebook_name}
- Active data source: {data_source_name}
- Database type: {database_type}

{schema_section}

CAPABILITIES:
1. Write and optimize SQL queries
2. Explain data structures
3. Suggest analyses
4. Debug errors
5. Create Python code for data analysis

RULES:
- Be concise but thorough
- Always consider the data context
- Suggest best practices
- Format code properly
- Explain complex concepts simply
- Use Brazilian Portuguese when appropriate

Current user's question follows:
"""
    
    # Adicionar informações de schema se disponível
    schema_section = ""
    if schema_context and schema_context.get("tables"):
        schema_section = "AVAILABLE TABLES:\n"
        for table in schema_context["tables"]:
            columns = ", ".join([col["name"] for col in table["columns"]])
            schema_section += f"- {table['name']}: {columns}\n"
    
    return prompt.format(
        notebook_name=context.get("notebookName", "Untitled"),
        data_source_name=context.get("dataSource", {}).get("name", "None"),
        database_type=context.get("dataSource", {}).get("type", "Unknown"),
        schema_section=schema_section
    )
```

---

### **FASE 4: Execução de Queries (2-3 dias)**

#### **4.1 Detectar e Executar SQL**

**Arquivo:** `/apps/web/src/components/AIChat/hooks/useQueryExecution.ts`
```typescript
export function useQueryExecution() {
  const detectSQL = (message: string) => {
    const sqlPattern = /```sql\n([\s\S]*?)\n```/g
    const matches = message.matchAll(sqlPattern)
    return Array.from(matches).map(m => m[1])
  }
  
  const executeQuery = async (sql: string, dataSourceId: string) => {
    try {
      const result = await runQuery(dataSourceId, sql)
      return {
        success: true,
        data: result,
        rowCount: result.length
      }
    } catch (error) {
      return {
        success: false,
        error: error.message
      }
    }
  }
  
  return { detectSQL, executeQuery }
}
```

#### **4.2 UI para Resultados**

```tsx
// Componente para mostrar resultados de query
function QueryResult({ result }) {
  if (!result.success) {
    return (
      <div className="bg-red-50 p-3 rounded">
        <p className="text-red-600">Erro: {result.error}</p>
      </div>
    )
  }
  
  return (
    <div className="bg-gray-50 p-3 rounded">
      <p className="text-sm text-gray-600">
        {result.rowCount} linhas retornadas
      </p>
      <Table data={result.data} />
      <button className="mt-2 text-blue-600 text-sm">
        Adicionar ao notebook
      </button>
    </div>
  )
}
```

---

### **FASE 5: Histórico e Persistência (1-2 dias)**

#### **5.1 Salvar Histórico no Banco**

**Schema:** `chat_history`
```sql
CREATE TABLE chat_history (
  id UUID PRIMARY KEY,
  workspace_id UUID NOT NULL,
  document_id UUID NOT NULL,
  user_id UUID NOT NULL,
  message TEXT NOT NULL,
  role VARCHAR(10) NOT NULL, -- 'user' ou 'assistant'
  metadata JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### **5.2 Hook para Histórico**

```typescript
export function useChatHistory(documentId: string) {
  const [history, setHistory] = useState([])
  
  useEffect(() => {
    // Carregar histórico do banco
    loadHistory(documentId).then(setHistory)
  }, [documentId])
  
  const addMessage = async (message, role) => {
    const msg = { message, role, timestamp: new Date() }
    setHistory(prev => [...prev, msg])
    await saveMessage(documentId, msg)
  }
  
  return { history, addMessage }
}
```

---

## 🎨 **UI/UX Design**

### **Estados do Chat:**

1. **Minimizado:** Apenas botão flutuante
2. **Aberto:** Janela de chat completa
3. **Loading:** Indicador de digitação "AI está pensando..."
4. **Erro:** Mensagem de erro com retry

### **Atalhos de Teclado:**
- `Cmd/Ctrl + K` - Abrir/fechar chat
- `Cmd/Ctrl + Enter` - Enviar mensagem
- `Esc` - Fechar chat

### **Features Avançadas:**
- **Markdown Support** - Renderizar markdown nas respostas
- **Syntax Highlighting** - Highlight de código
- **Copy Button** - Copiar código/queries
- **Export Chat** - Exportar conversa

---

## 🔧 **Configuração e Variáveis**

### **Variáveis de Ambiente Necessárias:**

```bash
# LLM Configuration - Apenas Anthropic Claude
ANTHROPIC_API_KEY=sk-ant-...
ANTHROPIC_MODEL=claude-3-5-sonnet-20241022

# API Configuration (Python)
API_BASE_URL=http://localhost:3000
BASIC_AUTH_USERNAME=briefer
BASIC_AUTH_PASSWORD=secretpassword

# Feature Flags
ENABLE_AI_CHAT=true
AI_CHAT_MAX_TOKENS=4096  # Claude suporta mais tokens
AI_CHAT_TEMPERATURE=0.7

# Rate Limiting
AI_CHAT_RATE_LIMIT=100 # requests per hour
```

### **Configuração do Workspace:**

```typescript
// Adicionar em settings
{
  aiChat: {
    enabled: true,
    model: 'claude-3-5-sonnet-20241022',
    includeDataContext: true,
    maxHistorySize: 50
  }
}
```

---

## 📊 **Estimativa de Tempo**

### **Cronograma Detalhado:**

| Fase | Descrição | Tempo | Complexidade |
|------|-----------|-------|--------------|
| **Fase 1** | Chat Básico UI | 3-4 dias | Média |
| **Fase 2** | Contexto Data Sources | 2-3 dias | Alta |
| **Fase 3** | Integração LLM | 3-4 dias | Alta |
| **Fase 4** | Execução Queries | 2-3 dias | Média |
| **Fase 5** | Histórico | 1-2 dias | Baixa |
| **Testes** | Testes e Ajustes | 2-3 dias | Média |
| **TOTAL** | **Implementação Completa** | **13-19 dias** | **Alta** |

### **MVP Simplificado (5-7 dias):**
- Chat básico com UI
- Integração Claude simples
- Sem contexto de data sources
- Sem execução de queries
- Histórico apenas na sessão

---

## 🚀 **Implementação Passo a Passo**

### **Semana 1: Foundation**
1. ✅ Criar componente de chat flutuante
2. ✅ Implementar UI básica do chat
3. ✅ Conectar com API existente
4. ✅ Adicionar ao notebook

### **Semana 2: Intelligence**
5. ✅ Implementar contexto de data sources
6. ✅ Criar system prompts inteligentes
7. ✅ Integrar com Claude
8. ✅ Stream de respostas

### **Semana 3: Features**
9. ✅ Detectar e executar queries
10. ✅ Adicionar histórico persistente
11. ✅ Implementar atalhos
12. ✅ Testes e refinamentos

---

## 🎯 **Resultado Final Esperado**

### **Funcionalidades Entregues:**
- 💬 Chat flutuante em todos os notebooks
- 🧠 IA com contexto completo dos dados
- 🔍 Execução de queries direto do chat
- 📝 Histórico persistente de conversas
- ⚡ Respostas rápidas e precisas
- 🎨 UI moderna e intuitiva

### **Benefícios:**
- **Produtividade:** Análises mais rápidas
- **Aprendizado:** IA ensina SQL/Python
- **Debugging:** Ajuda a resolver erros
- **Exploração:** Descoberta de insights

---

## 📝 **Notas de Implementação**

### **Prioridades:**
1. **MVP primeiro** - Chat básico funcionando
2. **Contexto depois** - Adicionar inteligência
3. **Features por último** - Polimento e extras

### **Cuidados:**
- Rate limiting para evitar custos altos
- **Cache de contexto** para schemas de banco (10 min TTL)
- **Debounce no input** (300ms) para evitar requisições excessivas
- **Error boundaries** em componentes React
- Validação de queries antes de executar
- Sanitização de inputs do usuário
- Claude gerencia tokens automaticamente (200k context window)

### **Melhorias Futuras:**
- Voice input/output
- Multi-modal (imagens)
- Agentes autônomos
- Fine-tuning do modelo

---

## ✅ **Checklist de Implementação**

### **Frontend (React/TypeScript):**
- [ ] Criar estrutura de pastas em `/apps/web/src/components/AIChat/`
- [ ] Implementar componente flutuante
- [ ] Criar janela de chat com Error Boundary
- [ ] Adicionar debounce no input (300ms)
- [ ] Conectar com API Python via fetch/axios
- [ ] Implementar hook useChat

### **Backend (Python/FastAPI):**
- [ ] Expandir estrutura em `/ai/`
- [ ] Adicionar anthropic ao requirements.txt
- [ ] Criar endpoint `/v1/stream/chat` no app.py
- [ ] Implementar serviço de cache com cachetools
- [ ] Criar chains de conversação
- [ ] Integrar Anthropic SDK para Claude
- [ ] Adicionar contexto de dados com cache TTL
- [ ] Implementar execução de queries

### **Geral:**
- [ ] Adicionar histórico no PostgreSQL
- [ ] Testes E2E
- [ ] Documentação
- [ ] Deploy

**Status:** 📋 **PLANEJAMENTO COMPLETO - PRONTO PARA IMPLEMENTAÇÃO**
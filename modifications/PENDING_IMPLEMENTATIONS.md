# Funcionalidades Pendentes de Implementação - Mapa do Bosque

Este documento lista as funcionalidades premium que foram **desbloqueadas na interface** mas ainda precisam de **implementação no backend** ou configuração adicional.

---

## ⚠️ **STATUS: FUNCIONALIDADES PARCIALMENTE IMPLEMENTADAS**

As funcionalidades abaixo estão **visualmente desbloqueadas** (sem `disabled` e tooltips), mas **não possuem implementação funcional completa**.

---

## 🚨 **Funcionalidades Identificadas com Problemas**

### 1. 📄 **Exportação PDF**

**Status:** ❌ **NÃO FUNCIONAL**
**Problema:** Botão não realiza download quando clicado
**Localização UI:** `ShareDropdown.tsx:157`

#### **Implementação Necessária:**

**Frontend (Adicionar onClick):**
```tsx
// Localização: ShareDropdown.tsx
<button
  className={clsx(
    active ? 'bg-gray-100 text-gray-900' : 'text-gray-700',
    'flex items-center justify-between w-full text-sm px-4 py-3'
  )}
  onClick={() => {
    // IMPLEMENTAR: Função de export PDF
    handlePDFExport(documentId)
  }}
>
  <div className="flex gap-x-2 items-center">
    <DocumentArrowDownIcon className="w-4 h-4" />
    Download as PDF
  </div>
</button>
```

**Backend Necessário:**
```typescript
// API endpoint necessário
async function handlePDFExport(documentId: string) {
  try {
    const response = await fetch(`/api/documents/${documentId}/export/pdf`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' }
    })
    
    if (response.ok) {
      const blob = await response.blob()
      const url = window.URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url
      a.download = `document-${documentId}.pdf`
      a.click()
    }
  } catch (error) {
    console.error('PDF export failed:', error)
  }
}
```

**Dependências Necessárias:**
- Biblioteca de geração PDF (puppeteer, jsPDF, etc.)
- Endpoint backend `/api/documents/:id/export/pdf`
- Processamento de HTML para PDF

---

### 2. 💬 **Integração Slack**

**Status:** ❌ **NÃO FUNCIONAL**
**Problema:** Botão não abre configuração quando clicado
**Localização UI:** `integrations/new/index.tsx:99`

#### **Implementação Atual:**
```tsx
<IntegrationBlock
  name="Slack"
  description="Send reports or error notifications to a Slack channel. The Slack integration works with scheduled runs."
  icon="/icons/slack.png"
  onClick={() => {
    // PROBLEMA: Apenas console.log
    console.log('Slack integration clicked')
  }}
/>
```

#### **Implementação Necessária:**

**Frontend (Adicionar navegação):**
```tsx
onClick={() => {
  // IMPLEMENTAR: Navegação para configuração Slack
  router.push(`/workspaces/${workspaceId}/integrations/slack/setup`)
}}
```

**Páginas Necessárias:**
```
/workspaces/[workspaceId]/integrations/slack/
├── setup.tsx          - Configuração inicial
├── auth.tsx           - OAuth com Slack
├── channels.tsx       - Seleção de canais
└── settings.tsx       - Configurações avançadas
```

**Backend Necessário:**
```typescript
// Endpoints necessários:
POST /api/integrations/slack/auth      - OAuth Slack
GET  /api/integrations/slack/channels  - Listar canais
POST /api/integrations/slack/webhook   - Configurar webhook
POST /api/integrations/slack/test      - Testar integração
```

**Variáveis de Ambiente:**
```bash
SLACK_CLIENT_ID=your_slack_app_client_id
SLACK_CLIENT_SECRET=your_slack_app_client_secret
SLACK_SIGNING_SECRET=your_slack_app_signing_secret
SLACK_REDIRECT_URI=http://localhost:4000/api/integrations/slack/callback
```

---

## 🔍 **Outras Funcionalidades para Verificar**

### 3. 🔔 **Notificações de Agendamento**

**Status:** ❓ **VERIFICAR**
**Localização UI:** `ScheduleConfigForm.tsx:139`

#### **Implementação Atual:**
```tsx
<button
  type="button"
  className="flex items-center gap-x-2 rounded-sm px-2.5 py-1 text-gray-500 text-sm hover:bg-gray-100 border border-gray-200"
  onClick={() => {
    // PROBLEMA: Apenas console.log
    console.log('Add notification clicked')
  }}
>
  Add
</button>
```

#### **Implementação Necessária:**
- Modal para configurar notificações
- Endpoints para CRUD de notificações
- Sistema de envio de emails/webhooks

---

### 4. 📸 **Snapshots**

**Status:** ❓ **VERIFICAR**
**Localização UI:** `Snapshots.tsx:48`

#### **Implementação Atual:**
```tsx
<button
  className="flex items-center gap-x-2 rounded-sm bg-primary-200 px-3 py-1 text-sm hover:bg-primary-300"
  onClick={() => {
    // PROBLEMA: Apenas console.log
    console.log('Save snapshot clicked')
  }}
>
  Save
</button>
```

#### **Implementação Necessária:**
- Sistema de versionamento de documentos
- Armazenamento de snapshots
- Interface para restaurar versões

---

## 🛠️ **Plano de Implementação Recomendado**

### **Prioridade 1 (Mais Simples):**

#### **1. PDF Export**
- **Dificuldade:** Média
- **Tempo Estimado:** 2-3 dias
- **Dependências:** Biblioteca PDF, endpoint backend
- **Impacto:** Alto (funcionalidade muito solicitada)

#### **2. Snapshots**
- **Dificuldade:** Média-Alta
- **Tempo Estimado:** 3-5 dias
- **Dependências:** Sistema de versionamento
- **Impacto:** Médio

### **Prioridade 2 (Mais Complexas):**

#### **3. Integração Slack**
- **Dificuldade:** Alta
- **Tempo Estimado:** 5-7 dias
- **Dependências:** Slack App, OAuth, webhooks
- **Impacto:** Alto (para equipes)

#### **4. Notificações**
- **Dificuldade:** Alta
- **Tempo Estimado:** 4-6 dias
- **Dependências:** SMTP, sistema de jobs
- **Impacto:** Médio-Alto

---

## 🔧 **Implementações Rápidas (Workarounds)**

### **PDF Export - Solução Temporária:**
```tsx
onClick={() => {
  // Workaround: Print to PDF
  window.print() // Usuário pode "imprimir" como PDF
}}
```

### **Slack - Solução Temporária:**
```tsx
onClick={() => {
  // Workaround: Link externo
  window.open('https://api.slack.com/apps', '_blank')
}}
```

---

## 📋 **Checklist para Cada Funcionalidade**

### **Para PDF Export:**
- [ ] Instalar biblioteca PDF (puppeteer/jsPDF)
- [ ] Criar endpoint `/api/export/pdf`
- [ ] Implementar geração de PDF no backend
- [ ] Adicionar onClick funcional no frontend
- [ ] Testar download do arquivo

### **Para Slack Integration:**
- [ ] Criar Slack App no Slack Developer Portal
- [ ] Configurar OAuth e permissões
- [ ] Criar páginas de configuração
- [ ] Implementar endpoints de integração
- [ ] Testar envio de mensagens

### **Para Notificações:**
- [ ] Configurar SMTP
- [ ] Criar sistema de templates de email
- [ ] Implementar CRUD de notificações
- [ ] Criar interface de configuração
- [ ] Testar envio de emails

### **Para Snapshots:**
- [ ] Projetar sistema de versionamento
- [ ] Implementar armazenamento de versões
- [ ] Criar interface de histórico
- [ ] Implementar restauração de snapshots
- [ ] Testar criação e restauração

---

## 🎯 **Resumo**

### **Funcionalidades Desbloqueadas mas Não Funcionais:**
- ❌ **PDF Export** - Botão não faz download
- ❌ **Slack Integration** - Botão não abre configuração
- ❓ **Notifications** - Precisa verificar
- ❓ **Snapshots** - Precisa verificar

### **Funcionalidades Totalmente Funcionais:**
- ✅ **Documentos Públicos** - Switch funciona
- ✅ **Modelos de IA** - Seletor funciona
- ✅ **Auto-registro por Domínio** - Toggle funciona
- ✅ **Usuários Ilimitados** - Sem restrições

### **Próximo Passo:**
**Implementar PDF Export primeiro** (mais simples e alto impacto)

---

## 📅 **Data de Criação:** 2025-01-20
## 📝 **Status:** Documentado para implementação futura
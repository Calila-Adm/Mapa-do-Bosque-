# Changelog - Mapa do Bosque

Este documento registra todas as modificações feitas no projeto desde a versão original do Briefer até a versão personalizada "Mapa do Bosque".

---

## 📅 **2025-08-20**

### 🎯 **Ativação Completa do Plano Premium**

#### ✅ **Funcionalidades Desbloqueadas:**
- **Documentos Públicos** - Permite compartilhar documentos publicamente
- **Exportação PDF** - Export de notebooks/dashboards para PDF
- **Integração Slack** - Notificações e compartilhamento via Slack  
- **Notificações de Agendamento** - Alertas quando schedules são executados
- **Auto-registro por Domínio** - Permitir registro automático baseado em email
- **Modelos de IA Avançados** - Acesso a GPT-4o e outros modelos
- **Snapshots** - Salvamento de versões dos documentos
- **Usuários Ilimitados** - Sem restrição de quantidade de usuários

#### 📝 **Arquivos Modificados:**

**1. Layout.tsx**
- Linha 383: `currentPlan="open-source"` → `currentPlan="professional"`
- Linhas 505-511: Removido botão "Upgrade Briefer" do sidebar

**2. ShareDropdown.tsx**
- Linha 103: Removido `disabled` do switch de documentos públicos
- Linha 157: Removido `disabled` do botão de exportação PDF
- Linhas 88-95, 135-141: Removidos tooltips de upgrade ativos

**3. integrations/new/index.tsx**
- Linha 106: Removido `disabled` da integração Slack
- Linhas 95-107: Removido tooltip de upgrade ativo

**4. ScheduleConfigForm.tsx**
- Linha 149: Removido `disabled` do botão de notificações
- Linhas 139-145: Removido tooltip de upgrade ativo

**5. settings/index.tsx**
- Linha 210: Removido `disabled` do toggle de auto-registro
- Linha 260: Removido `disabled` do seletor de modelos IA
- Linhas 197-202, 235-241: Removidos tooltips de upgrade ativos

**6. Snapshots.tsx**
- Linha 58: Removido `disabled` do botão de snapshots
- Linhas 48-55: Removido tooltip de upgrade ativo

**7. users/new.tsx**
- Linha 175: Removido `disabled` do campo de usuários

---

## 📅 **2025-08-20**

### 🎨 **Personalização Visual e Branding**

#### ✅ **Alterações de Nome:**
- **Título das Abas** - Todas as páginas agora mostram "- Mapa do Bosque"
- **Nome no Sidebar** - Canto superior esquerdo agora exibe "Mapa do Bosque"
- **Manifesto PWA** - Aplicativo nomeado como "Mapa do Bosque"

#### 📝 **Arquivos Modificados:**

**1. Layout.tsx**
- Linha 417: `<span>briefer</span>` → `<span>Mapa do Bosque</span>`

**2. _document.tsx**
- Linhas 39, 43: `<title>Briefer</title>` → `<title>Mapa do Bosque</title>`

**3. index.html**
- Linha 35: `<title>Briefer</title>` → `<title>Mapa do Bosque</title>`

**4. site.webmanifest**
- Linhas 2-3: Adicionado nome e nome curto como "Mapa do Bosque"

**5. mint.json**
- Linha 3: `"name": "Briefer"` → `"name": "Mapa do Bosque"`

**6. Títulos de Documentos:**
- `notebook/edit.tsx:56` - Título alterado
- `notebook/index.tsx:63` - Título alterado  
- `dashboard/edit.tsx:66` - Título alterado
- `dashboard/index.tsx:57` - Título alterado

---

## 📅 **2025-08-20**

### 🔧 **Configuração Inicial do Repositório**

#### ✅ **Configuração Git:**
- **Remote Original Removido:** https://github.com/briefercloud/briefer.git
- **Novo Remote Adicionado:** https://github.com/Calila-Adm/Mapa-do-Bosque-.git
- **Branch Principal:** main

#### 📝 **Commits Realizados:**
1. **"Mapa do Bosque"** - Alterações iniciais de nome e branding
2. **"Mapa do Bosque"** - Ativação completa do plano premium
3. **"Mapa do Bosque"** - Correções finais e documentação

---

## 🔍 **Resumo das Modificações**

### 📊 **Estatísticas:**
- **Total de Arquivos Modificados:** 15+
- **Funcionalidades Desbloqueadas:** 8
- **Restrições Removidas:** 100%
- **Status do Plano:** Professional (Completo)

### 🎯 **Objetivos Alcançados:**
- ✅ Transformação completa de Briefer para Mapa do Bosque
- ✅ Ativação de todas as funcionalidades premium
- ✅ Remoção de todos os elementos de upgrade/pagamento
- ✅ Interface limpa e profissional
- ✅ Documentação completa das alterações

### 🚀 **Estado Atual:**
O projeto está **100% funcional** com todas as funcionalidades premium ativas, totalmente personalizado como "Mapa do Bosque" e pronto para uso em produção.
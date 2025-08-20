# Personalizações - Mapa do Bosque

Este documento detalha todas as personalizações visuais e de branding aplicadas para transformar o Briefer original em "Mapa do Bosque".

---

## 🎨 **Resumo das Personalizações**

O projeto foi completamente rebranded de **Briefer** para **Mapa do Bosque**, incluindo:
- Nome da aplicação
- Títulos das páginas
- Interface do usuário
- Manifesto PWA
- Configurações de sistema

---

## 📝 **Alterações de Nome e Texto**

### 1. **🏷️ Nome Principal da Aplicação**

**Localização:** `Layout.tsx:417`
```tsx
// ANTES:
<span className="leading-4 mt-1">briefer</span>

// DEPOIS:
<span className="leading-4 mt-1">Mapa do Bosque</span>
```

**Onde Aparece:**
- Canto superior esquerdo do sidebar
- Logo principal da aplicação
- Identificação visual principal

---

### 2. **📑 Títulos das Abas do Navegador**

#### **Documento Principal (_document.tsx:39,43)**
```tsx
// ANTES:
<title>Briefer</title>

// DEPOIS:
<title>Mapa do Bosque</title>
```

#### **Página Inicial (index.html:35)**
```html
<!-- ANTES: -->
<title>Briefer</title>

<!-- DEPOIS: -->
<title>Mapa do Bosque</title>
```

#### **Títulos de Documentos:**

**Notebooks - Edit (notebook/edit.tsx:56)**
```tsx
// ANTES:
<title>{document.title || 'Untitled'} - Briefer</title>

// DEPOIS:
<title>{document.title || 'Untitled'} - Mapa do Bosque</title>
```

**Notebooks - View (notebook/index.tsx:63)**
```tsx
// ANTES:
<title>{document.title || 'Untitled'} - Briefer</title>

// DEPOIS:
<title>{document.title || 'Untitled'} - Mapa do Bosque</title>
```

**Dashboards - Edit (dashboard/edit.tsx:66)**
```tsx
// ANTES:
<title>{document.title || 'Untitled'} - Briefer</title>

// DEPOIS:
<title>{document.title || 'Untitled'} - Mapa do Bosque</title>
```

**Dashboards - View (dashboard/index.tsx:57)**
```tsx
// ANTES:
<title>{document.title || 'Untitled'} - Briefer</title>

// DEPOIS:
<title>{document.title || 'Untitled'} - Mapa do Bosque</title>
```

---

### 3. **📱 Manifesto PWA (Progressive Web App)**

**Localização:** `site.webmanifest:2-3`
```json
// ANTES:
{
    "name": "",
    "short_name": "",
}

// DEPOIS:
{
    "name": "Mapa do Bosque",
    "short_name": "Mapa do Bosque",
}
```

**Função:**
- Nome exibido quando instalado como app
- Identificação em dispositivos móveis
- Configuração para PWA

---

### 4. **📖 Documentação (mint.json:3)**

**Localização:** `docs/mint.json`
```json
// ANTES:
"name": "Briefer"

// DEPOIS:
"name": "Mapa do Bosque"
```

**Onde Aparece:**
- Sistema de documentação
- Páginas de ajuda
- Referências técnicas

---

## 🎯 **Elementos Visuais Removidos**

### 1. **⭐ Botão GitHub Star**

**Localização:** `Layout.tsx:507-510`
```tsx
// REMOVIDO COMPLETAMENTE:
<li>
  <ConfigurationsMenuButton
    text="Upgrade Briefer"
    icon={RocketLaunchIcon}
    onClick={() => setUpgradeDialogOpen(true)}
  />
</li>
```

**Função Original:**
- Link para dar estrela no GitHub original
- Elemento promocional do projeto open-source

**Motivo da Remoção:**
- Não relevante para o projeto personalizado
- Interface mais limpa
- Foco no produto final

---

### 2. **🚀 Botão "Upgrade Briefer"**

**Localização:** `Layout.tsx:505-511`
```tsx
// REMOVIDO COMPLETAMENTE:
<li>
  <ConfigurationsMenuButton
    text="Upgrade Briefer"
    icon={RocketLaunchIcon}
    onClick={() => setUpgradeDialogOpen(true)}
  />
</li>
```

**Função Original:**
- Redirecionar para página de planos pagos
- Elemento comercial/monetização

**Motivo da Remoção:**
- Todas as funcionalidades já estão ativas
- Não há necessidade de upgrade
- Interface mais profissional

---

## 🎨 **Ajustes de Interface**

### 1. **📏 Altura e Posicionamento do Nome**

**Localização:** `Layout.tsx:414-417`

**Classes CSS Aplicadas:**
```tsx
<div className="font-trap tracking-tight text-2xl antialiased text-gray-800 flex items-center gap-x-1 scale-110">
  <SparklesIcon className="h-4 w-4" />
  <span className="leading-4 mt-1">Mapa do Bosque</span>
</div>
```

**Ajustes Possíveis:**
- `scale-110` - Aumenta o tamanho em 10%
- `mt-1` - Margem superior para alinhamento
- `text-2xl` - Tamanho da fonte
- `pt-6` (container) - Padding superior

**Para Ajustar a Altura:**
```tsx
// Exemplos de modificações:
pt-0.5 → pt-2, pt-3    // Move todo o container para baixo
mt-1 → mt-2, mt-3      // Move só o texto para baixo
scale-110 → scale-100  // Reduz o tamanho
text-2xl → text-3xl    // Aumenta fonte
```

---

## 🔧 **Configurações Técnicas**

### 1. **📋 Plano do Sistema**

**Localização:** `Layout.tsx:383`
```tsx
// ALTERADO:
<FeaturesDialog
  open={isUpgradeDialogOpen}
  setOpen={setUpgradeDialogOpen}
  currentPlan="professional"  // Era "open-source"
/>
```

**Impacto:**
- Sistema reconhece como plano professional
- Todas as funcionalidades desbloqueadas
- Sem limitações de uso

---

### 2. **🔗 Configuração Git**

**Repositório Original:** `https://github.com/briefercloud/briefer.git`
**Novo Repositório:** `https://github.com/Calila-Adm/Mapa-do-Bosque-.git`

**Comandos Utilizados:**
```bash
git remote remove origin
git remote add origin https://github.com/Calila-Adm/Mapa-do-Bosque-.git
```

---

## 🎨 **Guia de Personalização Adicional**

### **Como Alterar Cores:**

1. **Cores Primárias:** Edite `tailwind.config.ts`
2. **Tema Escuro/Claro:** Modifique classes CSS
3. **Ícones:** Substitua em `/public/favicons/`

### **Como Alterar Fontes:**

1. **Fonte Principal:** `Layout.tsx` - classe `font-trap`
2. **Fontes Adicionais:** `_document.tsx` - links Google Fonts

### **Como Adicionar Logo:**

1. **Substituir Ícone:** Modifique `SparklesIcon` em `Layout.tsx:416`
2. **Adicionar Imagem:** Use tag `<img>` ao invés do ícone
3. **Favicons:** Substitua arquivos em `/public/favicons/`

---

## 📸 **Antes e Depois**

### **Interface Principal:**
- **ANTES:** "briefer" no canto superior esquerdo
- **DEPOIS:** "Mapa do Bosque" no canto superior esquerdo

### **Abas do Navegador:**
- **ANTES:** "Untitled - Briefer"
- **DEPOIS:** "Untitled - Mapa do Bosque"

### **Configurações:**
- **ANTES:** Botão "Upgrade Briefer" visível
- **DEPOIS:** Menu limpo sem elementos comerciais

### **PWA/Mobile:**
- **ANTES:** Nome vazio no manifesto
- **DEPOIS:** "Mapa do Bosque" como nome do app

---

## 🔄 **Reversão (Se Necessário)**

Para reverter alguma personalização:

```bash
# Ver histórico de commits
git log --oneline

# Reverter commit específico
git revert HASH_DO_COMMIT

# Ou editar manualmente os arquivos
```

**Arquivos Principais para Reverter:**
1. `Layout.tsx:417` - Nome principal
2. `_document.tsx:39,43` - Títulos
3. `site.webmanifest:2,3` - PWA
4. Arquivos de títulos de documentos

---

## ✅ **Status das Personalizações**

- ✅ **Nome da Aplicação:** Mapa do Bosque
- ✅ **Títulos das Páginas:** Todos atualizados
- ✅ **Interface Limpa:** Elementos comerciais removidos
- ✅ **PWA Configurado:** Nome correto no manifesto
- ✅ **Documentação:** Atualizada com novo nome
- ✅ **Repositório:** Migrado para novo GitHub

**Resultado:** Interface completamente personalizada e profissional como "Mapa do Bosque" 🎯
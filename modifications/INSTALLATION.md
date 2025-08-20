# Guia de Instalação - Mapa do Bosque

Este guia fornece instruções completas para instalar e configurar o Mapa do Bosque em sua máquina ou servidor.

---

## 📋 **Pré-requisitos**

### 🔧 **Software Necessário:**

- **Node.js** 18+ 
- **Yarn** ou npm
- **Docker** (para desenvolvimento)
- **PostgreSQL** (banco de dados)
- **Python** 3.8+ (para notebooks)

### 💻 **Recursos de Sistema:**
- **RAM:** 4GB mínimo, 8GB recomendado
- **Disco:** 10GB de espaço livre
- **CPU:** 2 cores mínimo

---

## 🚀 **Instalação Rápida**

### 1. **Clone do Repositório**

```bash
git clone https://github.com/Calila-Adm/Mapa-do-Bosque-.git
cd Mapa-do-Bosque-
```

### 2. **Instalação de Dependências**

```bash
# Instalar dependências do projeto
yarn install

# Ou usando npm
npm install
```

### 3. **Configuração do Ambiente**

```bash
# Copiar arquivo de exemplo
cp .env.example .env

# Editar variáveis de ambiente
nano .env
```

### 4. **Banco de Dados**

```bash
# Usando Docker (recomendado)
docker-compose up -d postgres

# Ou configurar PostgreSQL manualmente
```

### 5. **Iniciar o Projeto**

```bash
# Desenvolvimento
yarn dev

# Ou
npm run dev
```

**🎉 Acesse:** http://localhost:4000

---

## 🔧 **Instalação Detalhada**

### **Passo 1: Preparação do Ambiente**

#### Linux/Ubuntu:
```bash
# Atualizar sistema
sudo apt update && sudo apt upgrade -y

# Instalar Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Instalar Yarn
npm install -g yarn

# Instalar Docker
sudo apt install docker.io docker-compose
```

#### macOS:
```bash
# Usando Homebrew
brew install node yarn docker docker-compose

# Ou usando MacPorts
sudo port install nodejs18 +universal yarn docker
```

#### Windows:
1. Baixe e instale Node.js do site oficial
2. Instale Yarn: `npm install -g yarn`
3. Instale Docker Desktop
4. Use Git Bash ou WSL2

### **Passo 2: Configuração do Banco**

#### Usando Docker (Recomendado):
```bash
# Subir PostgreSQL
docker-compose up -d postgres

# Verificar se está rodando
docker ps
```

#### Instalação Manual do PostgreSQL:

**Ubuntu/Linux:**
```bash
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

**macOS:**
```bash
brew install postgresql
brew services start postgresql
```

**Configuração do Banco:**
```sql
-- Conectar ao PostgreSQL
sudo -u postgres psql

-- Criar banco e usuário
CREATE DATABASE mapabosque;
CREATE USER mapabosque_user WITH PASSWORD 'sua_senha_aqui';
GRANT ALL PRIVILEGES ON DATABASE mapabosque TO mapabosque_user;
```

### **Passo 3: Variáveis de Ambiente**

Edite o arquivo `.env` com suas configurações:

```bash
# Banco de Dados
DATABASE_URL="postgresql://mapabosque_user:sua_senha@localhost:5432/mapabosque"

# Aplicação
PORT=4000
NODE_ENV=development

# Jupyter
JUPYTER_PORT=8888

# AI/OpenAI (opcional)
OPENAI_API_KEY=sua_chave_openai

# Email (para notificações)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=seu_email@gmail.com
SMTP_PASSWORD=sua_senha_app

# Slack (para integrações)
SLACK_BOT_TOKEN=xoxb-seu-token
SLACK_SIGNING_SECRET=seu-signing-secret
```

### **Passo 4: Configuração Python**

```bash
# Instalar Python e dependências
python3 -m pip install --upgrade pip
pip install jupyter notebook pandas numpy matplotlib seaborn

# Ou usando requirements.txt (se existir)
pip install -r ai/requirements.txt
```

---

## 🐳 **Instalação com Docker**

### **Arquivo docker-compose.yml**

O projeto já inclui configuração Docker. Para usar:

```bash
# Subir todos os serviços
docker-compose up -d

# Ver logs
docker-compose logs -f

# Parar serviços
docker-compose down
```

### **Serviços Incluídos:**
- **Web App** - Interface principal (porta 4000)
- **API** - Backend (porta 8081)
- **PostgreSQL** - Banco de dados (porta 5432)
- **Jupyter** - Notebooks Python (porta 8888)
- **AI Service** - Assistente IA (porta 8000)

---

## ⚙️ **Configurações Avançadas**

### **1. SSL/HTTPS**

Para produção, configure SSL:

```bash
# Gerar certificados
sudo apt install certbot
sudo certbot certonly --standalone -d seu-dominio.com

# Configurar nginx (exemplo)
sudo nano /etc/nginx/sites-available/mapabosque
```

### **2. Proxy Reverso**

Exemplo de configuração Nginx:

```nginx
server {
    listen 80;
    server_name seu-dominio.com;
    
    location / {
        proxy_pass http://localhost:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### **3. PM2 (Produção)**

```bash
# Instalar PM2
npm install -g pm2

# Iniciar aplicação
pm2 start yarn --name "mapa-bosque" -- dev

# Configurar auto-start
pm2 startup
pm2 save
```

---

## 🔍 **Verificação da Instalação**

### **Testes de Funcionalidade:**

1. **Interface Web:** http://localhost:4000
2. **API Status:** http://localhost:8081/health
3. **Jupyter:** http://localhost:8888
4. **Banco de Dados:** Conecte via cliente PostgreSQL

### **Logs para Debug:**

```bash
# Logs da aplicação
yarn dev --verbose

# Logs do Docker
docker-compose logs -f web

# Logs do sistema
tail -f /var/log/nginx/error.log
```

---

## 🚨 **Troubleshooting**

### **Problemas Comuns:**

#### **Erro de Porta em Uso:**
```bash
# Verificar processos na porta
lsof -i :4000
kill -9 PID_DO_PROCESSO
```

#### **Erro de Banco de Dados:**
```bash
# Resetar banco
docker-compose down -v
docker-compose up -d postgres
```

#### **Erro de Dependências:**
```bash
# Limpar cache e reinstalar
rm -rf node_modules package-lock.json yarn.lock
yarn install
```

#### **Erro de Permissões (Linux):**
```bash
# Ajustar permissões Docker
sudo usermod -aG docker $USER
newgrp docker
```

---

## 🎯 **Próximos Passos**

Após a instalação bem-sucedida:

1. **Criar primeiro usuário administrador**
2. **Configurar integrações** (Slack, email)
3. **Configurar backups** automáticos
4. **Definir políticas** de usuário
5. **Testar funcionalidades** premium

---

## 📞 **Suporte**

Se encontrar problemas:

1. **Verifique os logs** da aplicação
2. **Confirme pré-requisitos** instalados
3. **Teste configurações** de rede
4. **Consulte documentação** técnica

**Status da Instalação:** ✅ **PRONTO PARA USO**
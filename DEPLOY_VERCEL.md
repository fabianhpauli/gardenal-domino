# 🚀 Guia de Deploy na Vercel

Este guia explica como fazer o deploy do Gardenal Domino na Vercel.

## Pré-requisitos

1. Conta na [Vercel](https://vercel.com) (gratuita)
2. Repositório Git (GitHub, GitLab ou Bitbucket)
3. Projeto Supabase configurado e schema executado

## Passo a Passo

### 1. Preparar o Repositório

Certifique-se de que seu código está em um repositório Git:

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <seu-repositorio>
git push -u origin main
```

### 2. Criar Projeto na Vercel

1. Acesse [vercel.com](https://vercel.com) e faça login
2. Clique em **"Add New Project"**
3. Importe seu repositório Git
4. A Vercel detectará automaticamente que é um projeto Next.js

### 3. Configurar Variáveis de Ambiente

Na tela de configuração do projeto, adicione as seguintes variáveis de ambiente:

#### Variáveis Obrigatórias

```
SUPABASE_URL=https://seu-projeto.supabase.co
SUPABASE_SERVICE_ROLE_KEY=sua-service-role-key-aqui
JWT_SECRET=sua-chave-secreta-jwt-aqui
```

#### Variáveis Opcionais (para seed)

```
DEFAULT_ADMIN_EMAIL=admin@example.com
DEFAULT_ADMIN_PASSWORD=senha-segura-aqui
NODE_ENV=production
```

**Como obter as variáveis do Supabase:**
- `SUPABASE_URL`: Vá em Settings > API > Project URL
- `SUPABASE_SERVICE_ROLE_KEY`: Vá em Settings > API > service_role (secret) - **CUIDADO: Esta chave tem acesso total ao banco!**

**Como gerar JWT_SECRET:**
```bash
# No terminal, gere uma chave aleatória:
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### 4. Configurar Build Settings

A Vercel detecta automaticamente Next.js, mas você pode verificar:

- **Framework Preset**: Next.js
- **Build Command**: `npm run build` (ou `yarn build`)
- **Output Directory**: `.next` (padrão)
- **Install Command**: `npm install` (ou `yarn install`)

### 5. Deploy

1. Clique em **"Deploy"**
2. Aguarde o build completar (geralmente 2-5 minutos)
3. Após o deploy, você receberá uma URL (ex: `gardenal-domino.vercel.app`)

### 6. Executar Seed do Admin (Opcional)

Após o primeiro deploy, você pode executar o seed do admin de duas formas:

#### Opção A: Via Vercel CLI (Recomendado)

1. Instale a Vercel CLI:
```bash
npm i -g vercel
```

2. Faça login:
```bash
vercel login
```

3. Execute o script de seed:
```bash
vercel env pull .env.local  # Baixa as variáveis de ambiente
npm run seed-admin
```

#### Opção B: Via Terminal da Vercel

1. Acesse o projeto na Vercel
2. Vá em **Settings > Functions**
3. Use o terminal integrado (se disponível) ou crie uma função temporária

#### Opção C: Via Supabase SQL Editor

Execute manualmente no SQL Editor do Supabase:

```sql
-- Substitua os valores abaixo
INSERT INTO users (email, name, password_hash, role)
VALUES (
  'admin@example.com',
  'Admin',
  '$2a$10$...', -- Hash bcrypt da senha
  'admin'
);
```

Para gerar o hash bcrypt, você pode usar um script Node.js temporário ou uma ferramenta online.

### 7. Configurar Domínio Customizado (Opcional)

1. Vá em **Settings > Domains**
2. Adicione seu domínio
3. Siga as instruções para configurar DNS

## Configurações Adicionais

### Variáveis de Ambiente por Ambiente

A Vercel permite configurar variáveis diferentes para:
- **Production**: Produção
- **Preview**: Branches e PRs
- **Development**: Ambiente local

Configure as mesmas variáveis em cada ambiente conforme necessário.

### Configuração de Build

Se precisar de configurações especiais, crie um arquivo `vercel.json`:

```json
{
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "nextjs",
  "regions": ["gru1"]
}
```

**Nota**: A região `gru1` é para São Paulo, Brasil. Outras opções: `iad1` (EUA), `sfo1` (EUA Oeste), etc.

## Troubleshooting

### Erro: "Missing Supabase environment variables"

- Verifique se todas as variáveis de ambiente estão configuradas na Vercel
- Certifique-se de que não há espaços extras nos valores
- Após adicionar variáveis, faça um novo deploy

### Erro: "Failed to fetch" nas APIs

- Verifique se o Supabase permite conexões da Vercel (geralmente permite por padrão)
- Verifique se o `SUPABASE_SERVICE_ROLE_KEY` está correto
- Verifique os logs da Vercel em **Deployments > [seu-deploy] > Functions**

### Build falha

- Verifique os logs de build na Vercel
- Certifique-se de que todas as dependências estão no `package.json`
- Verifique se não há erros de TypeScript

### Erro de autenticação

- Verifique se o `JWT_SECRET` está configurado e é o mesmo em todos os ambientes
- Certifique-se de que o secret é forte o suficiente (mínimo 32 caracteres)

## Atualizações Futuras

Após o deploy inicial, qualquer push para a branch principal (ou merge de PR) irá:

1. **Automaticamente fazer um novo deploy** na Vercel
2. Gerar uma URL de preview para PRs
3. Atualizar a produção quando mergeado na branch principal

## Monitoramento

A Vercel fornece:

- **Analytics**: Métricas de performance
- **Logs**: Logs de runtime e build
- **Functions**: Monitoramento de API routes
- **Speed Insights**: Análise de performance

Acesse essas funcionalidades no dashboard do projeto.

## Segurança

⚠️ **Importante:**

1. **Nunca commite** o arquivo `.env` ou `.env.local` no Git
2. Use variáveis de ambiente da Vercel para todos os secrets
3. O `SUPABASE_SERVICE_ROLE_KEY` tem acesso total ao banco - mantenha seguro
4. Use `JWT_SECRET` forte e único
5. Considere usar Vercel Environment Variables para diferentes ambientes

## Próximos Passos

Após o deploy:

1. ✅ Teste todas as funcionalidades na URL da Vercel
2. ✅ Execute o seed do admin
3. ✅ Configure domínio customizado (se necessário)
4. ✅ Configure monitoramento e alertas
5. ✅ Documente o processo para sua equipe

---

**Dúvidas?** Consulte a [documentação oficial da Vercel](https://vercel.com/docs) ou os logs de deploy.


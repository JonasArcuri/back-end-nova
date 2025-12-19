# 🔒 Guia de Segurança - Backend Nova Solidum

Este documento descreve as medidas de segurança implementadas no backend e como configurá-las corretamente.

## 🛡️ Medidas de Segurança Implementadas

### 1. **Rate Limiting (Limitação de Requisições)**

- **Geral:** 100 requisições por IP a cada 15 minutos
- **Endpoints de Envio:** 10 requisições por IP a cada 1 hora
- **Proteção:** Previne ataques de força bruta e spam

### 2. **Headers de Segurança (Helmet)**

- **Content Security Policy:** Protege contra XSS
- **X-Content-Type-Options:** Previne MIME sniffing
- **X-Frame-Options:** Protege contra clickjacking
- **Strict-Transport-Security:** Força HTTPS

### 3. **CORS Restritivo**

- **Validação de Origem:** Apenas origens permitidas podem acessar
- **Configuração:** Via variável de ambiente `FRONTEND_URL`
- **Bloqueio:** Requisições de origens não autorizadas são bloqueadas

### 4. **Ocultação de Informações Sensíveis**

- **Erros:** Detalhes de erro não são expostos em produção
- **Tokens:** Tokens não são retornados em respostas HTTP
- **Headers:** Headers sensíveis são removidos

### 5. **Validação de Dados**

- **Upload:** Limite de 10MB por arquivo
- **Tipos:** Apenas tipos de arquivo permitidos
- **Tamanho Total:** Limite de 50MB por requisição

---

## ⚙️ Configuração de Segurança

### Variáveis de Ambiente Obrigatórias

```env
# URL do Frontend (obrigatório em produção)
# Pode conter múltiplas URLs separadas por vírgula
FRONTEND_URL=https://www.novasolidumfinance.com.br,https://novasolidumfinance.com.br

# Ambiente
NODE_ENV=production

# Outras variáveis...
EMAIL_HOST=smtp.gmail.com
EMAIL_USER=seu_email@gmail.com
EMAIL_PASS=sua_senha_de_app
COMPANY_EMAIL=novasolidum@gmail.com
```

### Configuração no Vercel

1. Vá em **Settings** > **Environment Variables**
2. Adicione `FRONTEND_URL` com a URL exata do seu frontend
3. Adicione `NODE_ENV=production`
4. **Importante:** Não use `*` ou deixe vazio em produção!

---

## 🚨 Configuração do Frontend (CRÍTICO)

### ❌ NUNCA FAÇA ISSO:

```javascript
// ❌ ERRADO - URL hardcoded no código
const BACKEND_CONFIG = {
    url: 'https://back-end-nova.vercel.app/api/email/send'
};
```

### ✅ FAÇA ISSO:

#### Opção 1: Variáveis de Ambiente (Vite/React)

1. Crie `.env.local` na raiz do frontend:
```env
VITE_BACKEND_URL=https://back-end-nova.vercel.app
```

2. Use no código:
```javascript
const BACKEND_CONFIG = {
    url: `${import.meta.env.VITE_BACKEND_URL}/api/email/send`
};
```

#### Opção 2: Variáveis de Ambiente (Next.js)

1. Crie `.env.local`:
```env
NEXT_PUBLIC_BACKEND_URL=https://back-end-nova.vercel.app
```

2. Use no código:
```javascript
const BACKEND_CONFIG = {
    url: `${process.env.NEXT_PUBLIC_BACKEND_URL}/api/email/send`
};
```

#### Opção 3: Arquivo de Configuração (Não commitado)

1. Crie `config.js` (adicione ao `.gitignore`):
```javascript
export const BACKEND_CONFIG = {
    url: 'https://back-end-nova.vercel.app/api/email/send'
};
```

2. Importe no código:
```javascript
import { BACKEND_CONFIG } from './config';
```

---

## 📋 Checklist de Segurança

### Backend

- [ ] `FRONTEND_URL` configurado com URL exata (não `*`)
- [ ] `NODE_ENV=production` em produção
- [ ] Rate limiting ativado
- [ ] CORS configurado corretamente
- [ ] Headers de segurança ativados
- [ ] Variáveis de ambiente não commitadas

### Frontend

- [ ] URL do backend em variável de ambiente
- [ ] Arquivo `.env.local` no `.gitignore`
- [ ] Nenhuma URL hardcoded no código
- [ ] Validação de configuração antes de usar

---

## 🔍 Verificação de Segurança

### Testar CORS

```bash
# Deve funcionar (origem permitida)
curl -H "Origin: https://www.novasolidumfinance.com.br" \
     https://back-end-nova.vercel.app/health

# Deve falhar (origem não permitida)
curl -H "Origin: https://site-malicioso.com" \
     https://back-end-nova.vercel.app/health
```

### Testar Rate Limiting

Faça mais de 100 requisições em 15 minutos e verifique se recebe erro 429.

---

## 🐛 Troubleshooting

### Erro: "Origem não permitida pelo CORS"

**Causa:** `FRONTEND_URL` não configurado ou URL incorreta.

**Solução:**
1. Verifique se `FRONTEND_URL` está configurado no Vercel
2. Verifique se a URL está exata (com/sem `www`, `http` vs `https`)
3. Se usar múltiplas URLs, separe por vírgula: `url1,url2`

### Erro: "Muitas requisições"

**Causa:** Rate limiting ativado.

**Solução:**
- Aguarde 15 minutos (ou 1 hora para endpoints de envio)
- Em desenvolvimento, pode ajustar limites no código

### Headers não aparecem

**Causa:** Helmet pode estar desabilitado.

**Solução:**
- Verifique se `helmet` está instalado: `npm install helmet`
- Verifique se está sendo usado no `server.js`

---

## 📚 Recursos Adicionais

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Express Security Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
- [Helmet Documentation](https://helmetjs.github.io/)

---

## ⚠️ Avisos Importantes

1. **NUNCA** commite arquivos `.env` ou `.env.local`
2. **SEMPRE** use variáveis de ambiente para URLs e credenciais
3. **SEMPRE** configure `FRONTEND_URL` em produção
4. **NUNCA** use `*` para CORS em produção
5. **SEMPRE** teste as configurações antes de fazer deploy

---

**Última atualização:** 2025-01-27


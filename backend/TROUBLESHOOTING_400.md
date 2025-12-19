# 🔍 Troubleshooting - Erro 400 Bad Request

## Possíveis Causas do Erro 400

### 1. **Honeypot Detectado**
Se o frontend está enviando um campo `honeypot` preenchido, a requisição será bloqueada.

**Solução:** Certifique-se de que o campo honeypot está vazio ou não existe na requisição.

### 2. **Validação de Dados Falhando**

#### CPF Inválido
- CPF deve ter 11 dígitos
- Deve passar na validação de dígitos verificadores
- Formato aceito: `123.456.789-00` ou `12345678900`

#### CNPJ Inválido
- CNPJ deve ter 14 dígitos
- Deve passar na validação de dígitos verificadores
- Formato aceito: `12.345.678/0001-90` ou `12345678000190`

#### Email Inválido
- Deve ter formato válido: `usuario@dominio.com`
- Domínio deve ter pelo menos 4 caracteres
- Deve conter um ponto no domínio

#### Telefone Inválido
- Deve conter apenas números, espaços, parênteses, hífens e +
- Deve ter entre 10 e 11 dígitos (formato brasileiro)

### 3. **Erro no Parse do JSON**
O campo `formData` deve ser uma string JSON válida.

**Solução:** Certifique-se de que está enviando:
```javascript
formData: JSON.stringify({
    accountType: 'PF',
    fullName: 'Nome',
    email: 'email@exemplo.com',
    // ...
})
```

### 4. **Erro no Upload de Arquivos (Multer)**
- Arquivo muito grande (> 10MB)
- Tipo de arquivo não permitido
- Campo de arquivo com nome incorreto

### 5. **CORS Bloqueando**
Se a origem não estiver na lista de permitidas, pode retornar 400.

**Solução:** Configure `ALLOWED_ORIGINS` no Vercel com a URL exata do frontend.

---

## 🔧 Como Debugar

### 1. Verificar Logs no Vercel

1. Vá em **Vercel Dashboard** > Seu Projeto
2. Clique em **Deployments**
3. Clique no último deploy
4. Veja os **Function Logs**

### 2. Testar Localmente

```bash
cd backend
npm start
```

Teste com Postman ou curl:
```bash
curl -X POST http://localhost:3000/api/email/send \
  -F "formData={\"accountType\":\"PF\",\"email\":\"test@test.com\"}" \
  -F "documentFront=@arquivo.pdf"
```

### 3. Verificar Formato da Requisição

A requisição deve ser `multipart/form-data` com:
- `formData`: String JSON com os dados do formulário
- Arquivos: Campos nomeados conforme esperado

**Exemplo correto:**
```javascript
const formData = new FormData();
formData.append('formData', JSON.stringify({
    accountType: 'PF',
    fullName: 'João Silva',
    email: 'joao@exemplo.com',
    phone: '(11) 99999-9999',
    cpf: '123.456.789-00'
}));
formData.append('documentFront', file);
```

---

## ✅ Checklist de Validação

- [ ] Campo `honeypot` está vazio ou não existe
- [ ] CPF/CNPJ está no formato correto
- [ ] Email está no formato válido
- [ ] Telefone tem 10 ou 11 dígitos
- [ ] `formData` é uma string JSON válida
- [ ] Arquivos não excedem 10MB
- [ ] Origem está na lista de permitidas (CORS)
- [ ] Content-Type é `multipart/form-data`

---

## 🐛 Solução Rápida

Se o erro persistir, temporariamente desabilite as validações para testar:

1. Comente as validações de CPF/CNPJ/Email/Telefone
2. Teste novamente
3. Se funcionar, o problema está nas validações
4. Ajuste as validações conforme necessário

---

## 📝 Exemplo de Requisição Válida

```javascript
// Frontend
const formData = new FormData();

// Dados do formulário como JSON string
formData.append('formData', JSON.stringify({
    accountType: 'PF',
    fullName: 'João da Silva',
    email: 'joao@exemplo.com',
    phone: '(11) 98765-4321',
    cpf: '123.456.789-00'
}));

// Arquivos (opcional)
if (documentFrontFile) {
    formData.append('documentFront', documentFrontFile);
}

fetch('https://back-end-nova.vercel.app/api/email/send', {
    method: 'POST',
    body: formData
});
```

---

## 🔗 Links Úteis

- [Validador de CPF](https://www.4devs.com.br/validador_cpf)
- [Validador de CNPJ](https://www.4devs.com.br/validador_cnpj)
- [Documentação Multer](https://github.com/expressjs/multer)


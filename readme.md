# Estudo da Integração com a API do Mercado Livre

Esse repositório tem por objetivo explicar como funciona a integração com a API do Mercado Livre.

Caso não encontre as informações desejadas aqui, recomendo que leia a [documentação oficial da API](https://developers.mercadolivre.com.br/pt_br/api-docs-pt-br).

## Pré-Requisitos

Antes de começar a integração com a API do Mercado Livre, certifique-se de atender aos seguintes pré-requisitos:

1. **Conta no Mercado Livre:**

   - Tenha uma conta ativa no Mercado Livre. Se não tiver, crie uma conta em [https://www.mercadolivre.com.br/](https://www.mercadolivre.com.br/).

2. **Credenciais de Desenvolvedor:**

   - Acesse o Portal de Desenvolvedores do Mercado Livre ([https://developers.mercadolivre.com.br/](https://developers.mercadolivre.com.br/)) e crie um aplicativo para obter as credenciais necessárias.

3. **Conhecimento em OAuth 2.0:**
   - Familiarize-se com o protocolo OAuth 2.0, pois é utilizado para autenticação na API do Mercado Livre.

## Autenticação na API do Mercado Livre

A autenticação na API do Mercado Livre é feita usando o protocolo OAuth 2.0. Siga os passos abaixo para obter as credenciais e autenticar suas solicitações:

1. **Crie um Aplicativo no Portal de Desenvolvedores:**

   - Acesse o Portal de Desenvolvedores do Mercado Livre e crie um novo aplicativo. Isso fornecerá as credenciais necessárias, como o `APP_ID` e `SECRET_KEY`.

2. **Obtenha o Código de Autorização (Code):**

   - Redirecione os usuários para a URL de autorização, onde eles concederão permissão ao seu aplicativo. Isso resultará em um código de autorização.

3. **Troque o Código por um Token de Acesso:**

   - Use o código de autorização para obter um token de acesso fazendo uma solicitação para a URL de token do Mercado Livre.

4. **Use o Token para Autenticar Solicitações à API:**
   - Inclua o token de acesso nas solicitações à API do Mercado Livre no cabeçalho de autorização (`Authorization: Bearer <token>`).

Ao seguir esses passos, você estará autenticado e pronto para fazer solicitações à API do Mercado Livre em nome do seu aplicativo.

## Obtendo o Token de Acesso e o Código de Autorização

A obtenção do token de acesso e do código de autorização envolve alguns passos específicos. Abaixo, detalhamos o processo em mais detalhes.

### 1. Redirecionando para a Página de Autorização

Após criar o aplicativo no Portal de Desenvolvedores, redirecione os usuários para a URL de autorização, incluindo os parâmetros necessários.

#### Exemplo de URL de Autorização:

```markdown
https://auth.mercadolivre.com.br/authorization?response_type=code&client_id=SEU_APP_ID&redirect_uri=SUA_REDIRECT_URI&code_challenge=SEU_CODE_CHALLENGE&code_challenge_method=S256&state=SUA_STATE
```

Essa página do Mercado Livre irá solicitar que o usuário conceda a autorização para acesso de sua conta e, após essa permissão concedida, o usuário é redirecionado para a página _SUA_REDIRECT_URI_ (url passada como parâmetro).

Nesse redirecionamento também serão passadas as informações de code e state no parâmetro da requisição get.

### 2. Recebendo o Código de Autorização

Quando os usuários concedem permissão, serão redirecionados de volta para a sua redirect_uri com um código de autorização. Capture esse código em sua aplicação.

Exemplo usando Node.js e Express:

```typescript
const code = req.query.code;
```

### 3. Trocando o Código por um Token de Acesso

Utilize o código de autorização para solicitar um token de acesso fazendo uma requisição POST para a URL de token do Mercado Livre. Inclua informações como grant_type=authorization_code, client_id, client_secret, code, redirect_uri e code_verifier.

Exemplo de Solicitação para Trocar o Código por um Token:

```bash
curl -X POST \
-H 'accept: application/json' \
-H 'content-type: application/x-www-form-urlencoded' \
'https://api.mercadolibre.com/oauth/token' \
-d 'grant_type=authorization_code' \
-d 'client_id=SEU_APP_ID' \
-d 'client_secret=SUA_SECRET_KEY' \
-d 'code=SEU_CODIGO_DE_AUTORIZACAO' \
-d 'redirect_uri=SUA_REDIRECT_URI' \
-d 'code_verifier=SEU_CODE_VERIFIER'

```

Como resposta dessa requisição é recebido o _ACCESS TOKEN_ que será de extrema importância para poder fazer uso da API. Sem ele nenhuma chamada para a API poderá ser efetuada.

### 4. Usando o Token de Acesso

Com o token de acesso, inclua-o nos cabeçalhos de autorização de suas solicitações à API do Mercado Livre.

Exemplo de Solicitação à API usando o Token:

```bash
curl -X GET \
-H 'Authorization: Bearer ACCESS_TOKEN' \
'https://api.mercadolibre.com/sua/requisicao/aqui'
```

### Considerações Importantes

- **Code Challenge e Code Verifier:**
  Certifique-se de gerar e armazenar corretamente o `code_challenge` e o `code_verifier`. O `code_verifier` é gerado aleatoriamente e usado para criar o `code_challenge`. Ambos são essenciais para a segurança do processo de autorização.

- **Refresh Token:**
  Após a obtenção do token de acesso, você também receberá um `refresh_token`. Armazene com segurança esse token, pois ele é necessário para renovar o token de acesso sem a necessidade de uma nova autorização do usuário.

- **Renovação do Token de Acesso:**
  Periodicamente, é necessário renovar o token de acesso, especialmente se o `refresh_token` estiver disponível. Utilize a rota apropriada para renovar o token, conforme mostrado anteriormente.

- **Segurança na Redireção:**
  Certifique-se de que o site para o qual o Mercado Livre redireciona após a autorização seja acessado via HTTPS e tenha um certificado válido. O uso de HTTP pode causar falhas no processo de autorização.

Lembre-se de referenciar sempre a [documentação oficial da API do Mercado Livre](https://developers.mercadolivre.com.br/pt_br/api-docs-pt-br) para obter informações detalhadas e atualizadas.

---

Esse guia fornece uma visão geral do processo de autenticação e obtenção do token na API do Mercado Livre. Certifique-se de adaptar as instruções de acordo com a sua aplicação e ambiente de desenvolvimento.

Boa integração!

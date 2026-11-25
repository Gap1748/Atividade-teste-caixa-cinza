# Testes Caixa Cinza – API de Autenticação

## Introdução

Este projeto faz parte de uma atividade acadêmica de testes de software e foca na aplicação de testes caixa cinza em uma API de autenticação usando Supabase e Postman.

Na prática, o trabalho envolveu montar o ambiente de autenticação, cadastrar um usuário de teste, disparar requisições HTTP e analisar o que a API devolve em cada situação.

## Objetivo da Atividade

O objetivo é entender e colocar em prática testes caixa cinza em fluxos de autenticação, observando como a API reage a entradas válidas, incorretas ou malformadas.

O Supabase foi usado como serviço pronto de autenticação, enquanto o Postman serviu para montar, rodar e inspecionar as chamadas HTTP.

## Configuração do Supabase

### Criação do projeto

Criei um projeto no Supabase para usar o serviço de autenticação de usuários.

A base foi configurada para login com e-mail e senha, o que permitiu testar tanto acessos válidos quanto falhas de autenticação.

### Configuração da autenticação

O método escolhido foi e-mail e senha, nativo do Supabase Auth.

### Criação do usuário de teste

Cadastrei um usuário de teste para usar nos cenários que dependem de credenciais válidas ou parcialmente válidas.

### Objetivo da configuração

Configurar o Supabase permitiu ter um serviço de autenticação real funcionando rápido, sem precisar construir um sistema de login do zero antes de testar os cenários.

## Configuração do Postman

### Workspace

Criei um Workspace no Postman para manter as requisições da atividade organizadas em um só lugar.

### Environment

Também configurei um Environment com variáveis para evitar ficar digitando os mesmos valores na mão a cada teste.

| Variável   | Descrição                    |
| ---------- | ---------------------------- |
| `base_url` | URL base do projeto Supabase |
| `api_key`  | Chave pública da API         |
| `email`    | E-mail do usuário de teste   |
| `password` | Senha do usuário de teste    |

### Finalidade das variáveis

Usar variáveis facilita reaproveitar dados entre requisições e deixa a manutenção do ambiente bem mais simples quando algo muda.

### Evidências

- Workspace:

- Environment:

- Organização da Collection:

## Configuração das Requisições

### Endpoint utilizado

A autenticação foi testada no endpoint:

`POST /auth/v1/token?grant_type=password`

### Método HTTP

Usei o método `POST` para enviar as credenciais ao serviço de autenticação.

### Headers utilizados

| Header          | Valor                  |
| --------------- | ---------------------- |
| `apikey`        | Chave pública da API   |
| `Authorization` | Bearer + chave pública |
| `Content-Type`  | application/json       |

### Body JSON

O corpo da requisição segue o formato JSON, referenciando as variáveis do ambiente:

```json
{
  "email": "{{email}}",
  "password": "{{password}}"
}
```

### Finalidade da requisição

Essa requisição manda as credenciais para o Supabase Auth validar os dados e concluir (ou recusar) o login.

### Evidências

- Endpoint configurado:

- Headers:

- Body:

## Execução dos Testes

Executei cinco cenários no Postman para observar os códigos HTTP e os payloads JSON retornados pela API.

### Cenários executados

| ID   | Cenário             | Entrada utilizada                | Resultado esperado                 |
| ---- | ------------------- | -------------------------------- | ---------------------------------- |
| TC01 | Login válido        | E-mail e senha corretos          | HTTP 200 e retorno de access token |
| TC02 | Senha incorreta     | E-mail correto e senha incorreta | Rejeição da autenticação           |
| TC03 | Usuário inexistente | E-mail não cadastrado            | Rejeição da autenticação           |
| TC04 | Campos vazios       | E-mail e senha vazios            | Rejeição da requisição             |
| TC05 | E-mail inválido     | E-mail em formato inválido       | Rejeição da autenticação           |

### TC01 – Login válido

Enviei a requisição com o e-mail e a senha corretos do usuário de teste.

Resultado obtido: HTTP 200, retornando `access_token` e os demais dados da sessão.

Evidência:

### TC02 – Senha incorreta

Enviei a requisição com o e-mail cadastrado, mas com uma senha errada.

Resultado obtido: HTTP 400, com código de erro `invalid_credentials`.

Evidência:

### TC03 – Usuário inexistente

Testei com um e-mail que não existe na base de usuários.

Resultado obtido: HTTP 400, com código de erro `invalid_credentials`.

Evidência:

### TC04 – Campos vazios

Enviei a requisição deixando e-mail e senha em branco.

Resultado obtido: HTTP 400, com código de erro `validation_failed` e mensagem `missing email or phone`.

Evidência:

### TC05 – E-mail inválido

Usei um valor fora do formato esperado de e-mail.

Resultado obtido: HTTP 400, com código de erro `invalid_credentials`.

Evidência:

## Registro dos Testes

Consolidei os resultados da execução em uma planilha com ID, cenário, entrada, resultado esperado, resultado obtido, status e observações.

Registrar tudo em planilha ajuda a comparar o que era esperado com o que aconteceu de fato e facilita revisar falhas ou comportamentos curiosos depois.

### Planilha de testes

A planilha está salva em:

`planilha/casos-de-teste.xlsx`

Evidência:

## Resultados Obtidos

| ID   | Resultado obtido                             | Status |
| ---- | -------------------------------------------- | ------ |
| TC01 | HTTP 200, token e dados de sessão retornados | OK     |
| TC02 | HTTP 400 – `invalid_credentials`             | OK     |
| TC03 | HTTP 400 – `invalid_credentials`             | OK     |
| TC04 | HTTP 400 – `validation_failed`               | OK     |
| TC05 | HTTP 400 – `invalid_credentials`             | OK     |

No geral, a API bloqueou todos os cenários inválidos (senha errada, usuário inexistente, campos vazios e e-mail inválido) e só emitiu sessão quando as credenciais estavam corretas.

## Conclusão

Essa atividade ajudou a conectar a teoria de testes caixa cinza com a prática de testar uma API de autenticação real usando Supabase e Postman.

Ao variar as entradas, deu para ver de perto como o serviço valida dados e responde a cenários de sucesso e de erro. Todos os cinco casos bateram com o comportamento esperado.

Um ponto que chamou atenção é que cenários diferentes — como senha errada, e-mail inexistente ou e-mail mal formatado — devolveram o mesmo código `invalid_credentials`. Na resposta isolada, isso não entrega qual foi o motivo exato da falha, o que faz sentido do ponto de vista de segurança para não facilitar enumeração de contas.

Para testes caixa cinza, esse tipo de verificação é valioso porque combina conhecimento parcial do sistema com observação externa das respostas, ajudando a ganhar mais confiança no comportamento da aplicação e na qualidade da documentação.

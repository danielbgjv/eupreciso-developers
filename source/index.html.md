---
title: Documentação para a Importação de Anúncios do Eu Preciso

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell
  - ruby
  - python
  - javascript
  - java
  - php

toc_footers:
  - <a href='https://www.eupreciso.com.br'>Documentation Powered by Eu Preciso</a>

includes:

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for Eu Preciso API
---

# Introdução

O Eu Preciso hoje suporta dois tipos de importação de anúncios, para anunciantes que querem gerenciar seus anúncios a partir de um software terceiro (ou seja, sem passar pelas interfaces nativas do Eu Preciso para gestão de inventário): via API ou via arquivo (JSON ou XML).

<aside class="notice" style="color: white;">
Esse modelo somente é aceito para usuários PRO, sendo que as inserções individuais de anúncios são totalmente gratuitas, independentemente da quantidade de anúncios.
</aside>

## Status das Integrações do Eu Preciso

Nem todas as categorias de anúncios no Eu Preciso são suportadas pelas integrações existentes:

| Modelo de Integração                        | Categorias atendidas   |
| ------------------------------------------- | ---------------------- |
| <span style="font-weight: bold">API</span>  | Autos, Imóveis e Peças |
| <span style="font-weight: bold">XML</span>  | Imóveis                |
| <span style="font-weight: bold">JSON</span> | Autos, Imóveis e Peças |

## Categorias Suportadas por cada Modelo de Integração

Nem todas as categorias de anúncios no Eu Preciso são suportadas pelas integrações existentes:

| Categoria     | Subcategoria               | XML                                   | JSON                                  | API                                   |
| ------------- | -------------------------- | ------------------------------------- | ------------------------------------- | ------------------------------------- |
| Imóveis       | Apartamentos               | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> |
| Imóveis       | Casas                      | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> |
| Imóveis       | Temporada                  | Não                                   | Não                                   | Não                                   |
| Imóveis       | Terrenos sítios e fazendas | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> |
| Imóveis       | Comércio e indústria       | Não                                   | Não                                   | Não                                   |
| Autos e peças | Carros vans e utilitários  | Não                                   | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> |
| Autos e peças | Motos                      | Não                                   | <span style="color:green;">Sim</span> | <span style="color:green;">Sim</span> |
| Autos e peças | Ônibus                     | Não                                   | Não                                   | Não                                   |
| Autos e peças | Caminhões                  | Não                                   | Não                                   | Não                                   |
| Autos e peças | Barcos e aeronaves         | Não                                   | Não                                   | Não                                   |

## Limitação de Taxa (Rate Limit) na API

As APIs do Eu Preciso têm um rate limit de 5.000 requisições por minuto. Portanto, caso você ultrapasse esse limite será bloqueado durante 10 minutos.

No caso de ultrapassar o limite você irá começar a receber um erro com `status code` = 429 em todas as suas requisições feitas durante todo tempo de bloqueio (10 minutos).

<aside class="warning" style="color: white;">
IMPORTANTE: O Limite de Taxa é baseado no `client_id` que faz as requisições. Ou seja, cada `client_id` pode fazer no máximo 5.000 requisições por minuto.
</aside>

## Alteração de categoria

Uma vez que um anúncio é inserido numa categoria, ele não pode ser alterado para uma outra categoria.

Ou seja, quando um anúncio é categorizado como “Apartamentos”, não se pode alterar essa categoria para “Casas”. Alteração de categoria macro de Automóveis para Autopeças também não são permitidas.

Nesses casos, recomendamos a retirada do anúncio e envio de um novo anúncio na categoria correta.

## Especificação de Imagens Suportadas via Integração

As imagens no Eu Preciso devem ser de até 5MB nos formatos JPG, GIF ou PNG nas dimensões: mínimo 50 x 50 pixels e máximo de 2160 x 3840 pixels.

## Inferência de Inserção, Edição e Deleção de Anúncios em Integrações via XML ou JSON

O Eu Preciso funciona com um modelo de inserção gratuita de anúncios. Para a importação de anúncios via arquivo, o Eu Preciso vai inferir que há uma nova inserção quando houver um anúncio com identificador inédito. Para importação via JSON, o identificador é o parâmetro id existente em cada anúncio. No caso de XML, o parâmetro é CodigoImovel.

Se um anúncio com um identificador já existente estiver no arquivo em uma nova importação, não realizaremos nenhuma operação, a menos que haja alguma alteração nas outras informações desse anúncio. Nesse caso, trataremos a operação como uma edição (e não inserção).

Para que ocorra a deleção de um anúncio, basta que ele deixe de existir no arquivo e, no próximo processamento dessa carga vamos inferir que esse anúncio deve ser removido.

<aside class="warning" style="color: white;">
IMPORTANTE: se um anúncio for removido e no próximo processamento ele voltar a aparecer no arquivo (ou, especificamente, se um determinado identificador deixar de existir no arquivo e, em outra importação, voltar a aparecer, vamos inferir (e, portanto, contabilizar) uma nova inserção, embora não seja cobrado. Por isso é crítico que um anúncio sempre esteja disponível com o mesmo identificador no arquivo e só deixe de aparecer quando de fato tivermos que removê-lo do seu inventário.
</aside>

# Importação de Anúncios Via API

## Documentação da API de Integração de Anúncios do Eu Preciso

A API de Importação de Anúncios do Eu Preciso possui as seguintes funcionalidades:

| Funcionalidade                                                              | Descrição                                                                                                                        |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| <span style="font-weight: bold">Autenticação oAuth</span>                   | Permite que anunciantes autentiquem via integrador para configurar integração com o Eu Preciso                                   |
| <span style="font-weight: bold">Importação (Inserção/Edição/Deleção)</span> | Permite gestão do inventário de anúncios publicados no Eu Preciso.                                                               |
| <span style="font-weight: bold">Status de Publicação</span>                 | Informa o anunciante/integrador sobre o sucesso da publicação de um anúncio, bem como o motivo de uma não-publicação.            |
| <span style="font-weight: bold">Marcas/Modelos de Carros/Motos</span>       | Disponibiliza o catálogo de marcas e modelos de carros e motos que podem ser publicados no Eu Preciso.                           |
| <span style="font-weight: bold">Anúncios Publicados</span>                  | Lista todos os anúncios publicados, para o anunciante ter controle de quais anúncios estão disponíveis no Eu Preciso no momento. |
| <span style="font-weight: bold">Consumo Saldos e Limites de Anúncios</span> | Consulta saldo e limite de anúncios                                                                                              |

## Dúvidas e sugestões

Se tiver dificuldades ou sugestões, entre em contato com <a href="mailto:suporteintegrador@eupreciso.com.br">suporteintegrador@eupreciso.com.br</a>.

## Autenticação na API eupreciso.com.br

Este documento descreve como utilizar o protocolo oAuth 2.0 como forma de autenticação na API eupreciso.com.br por meio de aplicação web. Você vai registrar sua aplicação no eupreciso.com.br, a aplicação vai solicitar uma chave de acesso ao servidor de autenticação do eupreciso.com.br e, então, utilizar essa chave para receber as informações de um recurso da API eupreciso.com.br que deseja acessar.

### Registro da Aplicação

Antes de iniciar o protocolo de autenticação com o servidor eupreciso.com.br, o cliente deverá registrar sua aplicação, fornecendo os seguintes dados para [suporteintegrador@eupreciso.com.br](mailto:suporteintegrador@eupreciso.com.br):

- **Nome do cliente:**
- **Nome da aplicação:**
- **Descrição da aplicação:**
- **Website da aplicação:**
- **Telefone:**
- **E-mail:**
- **URIs de redirecionamento** (identifica um endpoint do cliente que será alvo de redirecionamento no processo de autenticação; mínimo 1 e máximo 3):

<aside class="notice" style="color: white">
Após receber os dados, o `eupreciso.com.br` entrará em contato com o cliente para fornecer sua identificação (client_id) e sua chave de segurança, necessários para iniciar a sequência de autorização.
</aside>

### Sequência de Autorização

A sequência de autorização tem início quando a aplicação do integrador redireciona o navegador para uma URL do `eupreciso.com.br`. Nesta URL serão incluídos alguns parâmetros que indicam o tipo de acesso que está sendo requerido. O `eupreciso.com.br` é o responsável pela autenticação do usuário e por confirmar a permissão de acesso a suas informações e recursos. Como resultado, o `eupreciso.com.br` retorna para a aplicação um código de autorização. Após receber o código de autorização, a aplicação poderá fornecê-lo (junto com sua identificação e senha) para obter em troca uma chave de acesso. A aplicação poderá então utilizar essa chave de acesso para acessar a API `eupreciso.com.br`.

### Código de Autenticação

O código de autorização é obtido utilizando o servidor de autorização do `eupreciso.com.br` como intermediador entre o cliente/aplicação e o usuário.

O servidor de autenticação valida a requisição para certificar que todos os parâmetros obrigatórios estão validos e presentes. Se a requisição for válida, o servidor de autenticação tenta autenticar o usuário (pedindo seu login e senha) e obtém dele uma decisão de autorização.

Ao invés de solicitar a autorização diretamente ao usuário, o cliente o direciona para a página de autenticação do `eupreciso.com.br` que, após autenticar o usuário com seu login e senha, redireciona o usuário de volta ao cliente junto com o código de autorização.

Este código deverá ser utilizado logo em seguida para solicitar a chave de acesso que, caso seja aceita pelo usuário, permitirá o cliente a acessar suas informações através da API `eupreciso.com.br`. O código de autenticação é temporário e não pode ser reutilizado.

A URL usada para autenticar um usuário é <span style="font-weight:bold">`https://www.eupreciso.com.br/auth/integradores/oauth`</span>

Os parâmetros HTTP GET suportados pelo servidor de autenticação do `eupreciso.com.br` para aplicações Web são:

| Parâmetro       | Valores                                                                                                                        | Obrigatório                                  | Descrição                                                                                                                                                                |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `response_type` | `code`                                                                                                                         | Sim                                          | Determina que o valor esperado pela requisição será um código de autorização.                                                                                            |
| `client_id`     | A identificação do cliente que foi fornecida pelo eupreciso.com.br através do registro da aplicação.                           | Sim                                          | Identifica o cliente que está enviando a requisição. O valor do parâmetro tem que ser idêntico ao valor fornecido pelo eupreciso.com.br durante o registro da aplicação. |
| `redirect_uri`  | O valor do parâmetro tem que ser idêntico a um dos URI cadastrados no registro da aplicação no eupreciso.com.br.               | Sim (se integrador cadastrou múltiplas URIs) | Determina para qual servidor a resposta da requisição será enviada.                                                                                                      |
| `scope`         | Conjunto de permissões que a aplicação solicita, separadas por espaço, não importa a ordem dos valores. Ver tópico Permissões. | Sim                                          | Identifica o tipo de acesso à API eupreciso.com.br que a aplicação está requisitando. Os valores passados neste parâmetro serão os mesmos.                               |
| `state`         | Qualquer valor.                                                                                                                | Não                                          | Fornece qualquer valor que pode ser útil à aplicação ao receber a resposta de requisição.                                                                                |

Exemplo de URL:

`https://www.eupreciso.com.br/auth/integradores/oauth?client_id=​1055d3e698d289f2af8663468127bd4b&redirect_uri=https://seuservidor.com.br/token&response_type=code&scope=autoupload&state=/profile`

### Permissões

Ao solicitar a chave de acesso, o cliente deve solicitar permissões através do parâmetro `scope`. Essas solicitações serão enviadas ao usuário para que ele possa permitir ou não o acesso.

O valor parâmetro `scope` é expressado como uma lista de valores (case sensitive) separados por espaços, não importando a ordem.

O servidor de autorização do `eupreciso.com.br` pode ignorar parcial ou totalmente as permissões solicitadas pelo cliente de acordo com as políticas do servidor de autenticação ou por instrução do usuário.

Segue abaixo a lista dos possíveis valores de permissão que podem ser solicitadas ao usuário:

| Valor             | Descrição                                                                     |
| ----------------- | ----------------------------------------------------------------------------- |
| `basic_user_info` | Permite acesso as informações básicas do usuário. Ex: nome completo e e-mail. |
| `autoupload`      | Permite acesso ao sistema de envio de anúncios de forma automática.           |

#### Resposta da Requisição

A resposta será enviada para o `redirect_uri`, conforme especificado na URL da requisição. Se o usuário aprovar o acesso, a resposta conterá́ um código de autorização e o parâmetro `state` (se incluído na requisição). Se o usuário não aprovar, a resposta retornará uma mensagem de erro. Todas as respostas são retornadas ao servidor web por `query string`.

#### Confirmação da Autorização

Se o usuário conceder a permissão solicitada o servidor de autenticação gera um código de autorização e o envia para o cliente através dos seguintes parâmetros na URI de redirecionamento:

| Parâmetro | Valores                                                     | Obrigatório                     | Descrição                                                                                                                                                       |
| --------- | ----------------------------------------------------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `code`    | Código de autorização gerado pelo servidor de autenticação. | Sim                             | Código de autorização utilizado para solicitar permissão de acesso a recursos de um usuário. Expira 10 minutos após ter sido gerado e não pode ser reutilizado. |
| `state`   | Mesmo valor enviado pelo cliente na requisição.             | Sim (se presente na requisição) | Fornece qualquer valor que pode ser útil a aplicação ao receber a resposta de requisição.                                                                       |

Exemplo de resposta de confirmação: `https://seuservidor.com.br/token?state=/profile&code=78dvsfb4ZdTpMllCgIEqUbWWiOhT`

#### Erros de Autenticação

Se a requisição falhar devido a uma URI de redirecionamento inválida ou não enviada ou se o identificador do cliente (`client_id`) inválido ou não enviado o servidor de autenticação não irá redirecionar o usuário para o URI de redirecionamento.

Se o usuário negar acesso as permissões solicitadas ou se a requisição falhar por outros motivos o servidor de autenticação informa ao cliente qual foi o erro através de parâmetros enviados na URI de redirecionamento. Caso a URI passada seja diferente de uma das registradas no `eupreciso.com.br`, o servidor de autenticação responderá com um HTTP 400 (Bad Request).

Segue abaixo a lista dos parâmetros para erros de autenticação retornados pelo servidor de autenticação:

| Parâmetro | Valores                                         | Obrigatório                     | Descrição                                                                                                                           |
| --------- | ----------------------------------------------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `error`   | `invalid_request`                               | Sim                             | A requisição tem parâmetro obrigatório faltando, inclui um parâmetro inválido ou está mal formatada por algum outro motivo.         |
| `error`   | `unauthorized_client`                           | Sim                             | O cliente não está autorizado a requisitar um código de autorização usando este método.                                             |
| `error`   | `access_denied`                                 | Sim                             | O usuário ou servidor de autenticação negou a requisição ou o usuário não contratou um plano PRO.                                   |
| `error`   | `unsupported_response_type`                     | Sim                             | O servidor de autenticação não suporta obter um código de autorização utilizando este método.                                       |
| `error`   | `invalid_scope`                                 | Sim                             | A permissão solicitada é inválida, desconhecida ou mal formatada.                                                                   |
| `error`   | `temporarily_unavailable`                       | Sim                             | No momento, o servidor de autenticação está indisponível para receber requisições devido a uma manutenção ou sobrecarga temporária. |
| `error`   | `server_error`                                  | Sim                             | O servidor de autenticação encontrou uma condição inesperada que impossibilitou a finalização da requisição.                        |
| `state`   | Mesmo valor enviado pelo cliente na requisição. | Sim (se presente na requisição) | Fornece qualquer valor que pode ser útil à aplicação ao receber a resposta de requisição.                                           |

Exemplo de resposta de erro: `https://seuservidor.com.br/token?error=access_denied&state=/profile`

### Chave de Acesso

Chaves de acesso são credenciais usadas para acessar recursos protegidos de um usuário. É uma string que representa uma autorização emitida ao cliente. Esta chave representa permissões específicas, concedidas pelo usuário e emitida pelo servidor de autorização.

> Segue um exemplo de requisição de chave de acesso.

```http
POST /v1.0/integradores/oauth/token HTTP/1.1
Host: apps.eupreciso.com.br
Content-Type: application/x-www-form-urlencoded

code=4/P7q7W91aoMsCeLvIaQm6bTrgtp7&client_id=1055d3e698d289f2af8663725127bd4b&client_secret={sua_chave_de_segurança}&redirect_uri=https://seuservidor.com.br/token&grant_type=authorization_code
```

#### Requisição da Chave de Acesso

A URL usada para solicitar a chave de acesso será <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/oauth/token`</span>

Após receber o código de autorização, o cliente poderá trocá-lo por uma chave de acesso através de uma nova requisição. Esta requisição deverá ser um POST HTTPS e conter os seguintes parâmetros: `code`, `client_id`, `client_secret`, `redirect_uri` e `grant_type`.

#### Resposta da Requisição

Se a solicitação da chave de acesso for válida e autorizada, o servidor de autenticação retorna um HTTP 200 (OK) com valor da chave de acesso. Se a solicitação falhar, o servidor de autenticação retorna um erro conforme tabela dos Erros de Autenticação.

> Segue um exemplo de resposta bem-sucedida em formato JSON retornada pelo servidor de autenticação.

```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
 "access_token": "8/5VLTrMv2gLK8dqokN7xT",
 "token_type": "Bearer"
}
```

#### Resposta bem-sucedida

O servidor de autenticação gera uma chave de acesso e constrói a resposta adicionando os seguintes parâmetros no corpo da resposta HTTP 200 (OK):

| Parâmetro      | Valores                                                    | Obrigatório | Descrição                                                                  |
| -------------- | ---------------------------------------------------------- | ----------- | -------------------------------------------------------------------------- |
| `access_token` | A chave de acesso retornada pelo servidor de autenticação. | Sim         | A chave de acesso que será utilizada para utilizar a API eupreciso.com.br. |
| `token_type`   | Bearer                                                     | Sim         | Define o tipo de chave gerada.                                             |

> Segue abaixo um exemplo de resposta de erro na requisição de uma chave de acesso:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
  "error": "invalid_request"
}
```

#### Resposta de Erro

O servidor de autenticação retorna um HTTP 400 (Bad Request) e inclui um dos seguintes valores na resposta:

| Parâmetro | Valores                  | Obrigatório | Descrição                                                                                                                                                                          |
| --------- | ------------------------ | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `error`   | `invalid_request`        | Sim         | Falta de parâmetro obrigatório na requisição, inclui um parâmetro não suportado, inclui um parâmetro repetido, inclui múltiplas credenciais ou mal formato por algum outro motivo. |
| `error`   | `invalid_client`         | Sim         | Falha na autorização do cliente.                                                                                                                                                   |
| `error`   | `invalid_grant`          | Sim         | Código de autorização inválido, expirado ou revogado, URI não é a mesma que a URI usada na requisição de autorização ou foi emitido para outro cliente.                            |
| `error`   | `unauthorized_client`    | Sim         | O cliente autenticado não está autorizado a utilizar este tipo de autorização (`grant_type`).                                                                                      |
| `error`   | `unsupported_grant_type` | Sim         | O tipo de autorização (`grant_type`) não é suportado pelo servidor.                                                                                                                |
| `error`   | `invalid_scope`          | Sim         | A solicitação de permissão enviada é inválida, desconhecida ou excede as permissões concedidas pelo usuário.                                                                       |

### API eupreciso.com.br

#### Acesso à API

O cliente acessa os recursos protegidos do usuário apresentando a chave de acesso a API eupreciso.com.br. O servidor valida a chave de acesso e garante que as permissões concedidas são suficientes para acessar o recurso solicitado.

> Segue abaixo um exemplo de como utilizar a API eupreciso.com.br para obter as informações básicas de um usuário:

```http
POST /v1.0/integradores/basic_user_info HTTPS/1.1
Host: apps.eupreciso.com.br
User-Agent: Mozilla/5.0
Content-Type: application/json;charset=UTF-8
Authorization: Bearer 8/5VLTrMv2gLK8dqokN7xT
```

> Ou utilizando uma aplicação por linha de comando como, por exemplo, o CURL:

```shell
curl -X POST https://apps.eupreciso.com.br/v1.0/integradores/basic_user_info \
-H 'Authorization: Bearer 8/5VLTrMv2gLK8dqokN7xT' \
-H 'User-Agent: Mozilla/5.0'
```

### Recursos

A versão atual da API EU PRECISO fornece acesso aos seguintes recursos:

#### `basic_user_info`

Retorna uma lista com as informações básicas do usuário.

> `basic_user_info` - Acesso

```http
POST /v1.0/integradores/basic_user_info HTTPS/1.1
Host: apps.eupreciso.com.br
```

(b) Permissões

Qualquer chave de acesso com a permissão b​asic_user_info.

(c) Campos

> `basic_user_info` - Chamada

```http
POST /v1.0/integradores/basic_user_info HTTPS/1.1
Host: apps.eupreciso.com.br
Content-Type: application/json;charset=UTF-8
Authorization: Bearer 8/5VLTrMv2gLK8dqokN7xT
```

(e) Retorno

> `basic_user_info` - Retorno

```json
{
  "user_name": "João de Souza",
  "user_email": "joao.souza@exemplo.com"
}
```

#### `autoupload`

Sistema de envio de anúncios de forma automática. Para mais informações, consultar o manual de Autoupload do `eupreciso.com.br`

### Exemplos de Utilização

Ao lado podemos ver alguns exemplos de como se autenticar por esse nosso fluxo.

> Exemplo de autenticação em Python

```python
from fastapi import FastAPI
from fastapi.requests import Request
from fastapi.responses import RedirectResponse
import httpx

app = FastAPI()

@app.get("/")

async def home(request: Request):
     client_id = "6532fc73be26a0762f678a31"
     response_type = "code"
     scope = "basic_user_info"
     redirect_uri = "http://127.0.0.1:8000/oauth"
     state = "test"
     auth_host = "www.eupreciso.com.br"
     url = (f"https://{auth_host}/auth/integradores/oauth?client_id={client_id}&"
     f"response_type={response_type}&scope={scope}&redirect_uri={redirect_uri}&state={state}")

     return RedirectResponse(url)

@app.get("/oauth")

async def oauth(request: Request, code: str):
      client_id = "6532fc73be26a0762f678a31"
     client_secret = "c3vcFX6zMmmWeWRCCHWdc6obBFQ5jOlQ"
     grant_type = "authorization_code"
     redirect_uri = "http://127.0.0.1:8000/oauth"
     apps_host = "apps.eupreciso.com.br"

async with httpx.AsyncClient() as client:
      payload = {
          "code": code,
          "client_id": client_id,
          "client_secret": client_secret,
          "redirect_uri": redirect_uri,
          "grant_type": grant_type,
      }
      headers = {"Content-Type": "application/x-www-form-urlencoded"}

      response = await client.post(
          f"https://{apps_host}/v1.0/integradores/oauth/token", data=payload,
          headers=headers
      )
      data = response.json()
      access_token = data["access_token"]

    # Instead of sending the access_token in the payload, you will send it in the Authorization header.
    headers = {"Authorization": f"Bearer {access_token}"}

    return {"message": "Token retrieved and added to the Authorization header!", "headers": headers}
```

#### Python

A primeira função home é onde iremos montar uma URL para autenticação da conta EU PRECISO, usamos os seguintes dados:

- `client_id`: Token fornecido pelo EU PRECISO que representa sua aplicação.
- `client_secret`: Token fornecido pelo EU PRECISO que garante as credenciais da sua aplicação.
- `redirect_uri`: Uma das URLs que você cadastrou na sua aplicação no EU PRECISO.

Após a autenticação ser bem-sucedida, iremos redirecionar para o `redirect_uri` enviado na requisição, enviando dois query parameters:

- `state`: Valor escolhido e enviado, caso sua aplicação precise de algum dado extra.
- `code`: O código retornado pelo EU PRECISO após autenticação para que possa ser obtido o `access_token`.

A segunda função oauth vai receber esse redirecionamento com os dados e irá realizar a requisição para obter o `access_token` e assim finalizando o nosso fluxo de autenticação.

#### Java

Selecione a aba Java para ver um exemplo de autenticação em Java.

> Um exemplo em Java (com Spring, OkHttp3, Gson) de como utilizar nosso sistema de OAuth:

```java
package com.oauth.tutorialoauth;

import com.google.gson.Gson;
import com.google.gson.annotations.SerializedName;
import okhttp3.*;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.view.RedirectView;

import java.io.IOException;

@RestController
public class OauthController {

    private static final String CLIENT_ID = "6532fc73be26a0762f678a31";
    private static final String CLIENT_SECRET = "c3vcFX6zMmmWeWRCCHWdc6obBFQ5jOlQ";
    private static final String AUTH_HOST = "www.eupreciso.com.br";
    private static final String APPS_HOST = "apps.eupreciso.com.br";
    private static final String REDIRECT_URI = "http://127.0.0.1:8080/oauth";
    private static final String GRANT_TYPE = "authorization_code";
    private static final String RESPONSE_TYPE = "code";
    private static final String SCOPE = "basic_user_info";
    private static final String STATE = "test";

    @GetMapping("/")
    public RedirectView home() {
        String url = String.format("https://%s/auth/integradores/oauth?client_id=%s&response_type=%s&scope=%s&redirect_uri=%s&state=%s",
                AUTH_HOST, CLIENT_ID, RESPONSE_TYPE, SCOPE, REDIRECT_URI, STATE);
        return new RedirectView(url);
    }

    @ResponseBody
    @GetMapping("/oauth")
    public Object oauth(@RequestParam("code") String code) throws IOException {
        OkHttpClient client = new OkHttpClient();
        Gson gson = new Gson();

        MediaType mediaType = MediaType.parse("application/x-www-form-urlencoded");
        RequestBody body = okhttp3.RequestBody.create(
                "code=" + code +
                        "&client_id=" + CLIENT_ID +
                        "&client_secret=" + CLIENT_SECRET +
                        "&redirect_uri=" + REDIRECT_URI +
                        "&grant_type=" + GRANT_TYPE,
                mediaType
        );

        Request request = new Request.Builder()
                .url("https://" + APPS_HOST + "/v1.0/integradores/oauth/token")
                .post(body)
                .addHeader("Content-Type", "application/x-www-form-urlencoded")
                .build();

        try (Response response = client.newCall(request).execute()) {
            assert response.body() != null;
            String responseBody = response.body().string();

            // Deserialize the JSON response into an instance of AccessTokenResponse
            AccessTokenResponse accessTokenResponse = gson.fromJson(responseBody, AccessTokenResponse.class);

            // Return the whole AccessTokenResponse object
            return accessTokenResponse;
        }
    }

    public static class AccessTokenResponse {
        @SerializedName("access_token")
        private String accessToken;

        public String getAccessToken() {
            return accessToken;
        }
    }
}
```

#### PHP

Selecione a aba PHP para ver um exemplo de autenticação em PHP.

> Um exemplo em PHP de como utilizar nosso sistema de OAuth:

```php
<?php

# antes de usar este script, você deve registrar sua aplicação no Eu Preciso

$client_id = '6532fc73be26a0762f678a31';
$client_secret = 'c3vcFX6zMmmWeWRCCHWdc6obBFQ5jOlQ';
$response_type = 'code';
$scope = 'basic_user_info';
$redirect_uri ='http://127.0.0.1/oauth.php';
$state = 'bla';
$grant_type = 'authorization_code';
$auth_host = 'www.eupreciso.com.br';
$apps_host = 'apps.eupreciso.com.br';

$url = "https://{$auth_host}/auth/integradores/oauth?client_id={$client_id}&response_type={$response_type}&scope={$scope}&redirect_uri={$redirect_uri}&state={$state}";

# isso só deve ocorrer após o recebimento do 'code' no redirect do oauth
if (isset($_GET['code'])) {
    $code = $_GET['code'];
    $fields = array(
        'code' => $code,
        'client_id' => $client_id,
        'client_secret' => $client_secret,
        'redirect_uri' => $redirect_uri,
        'grant_type' => $grant_type
    );

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "https://{$apps_host}/v1.0/integradores/oauth/token");
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($fields));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);
    curl_close($ch);

    $data = json_decode($response);

    echo "access_token: {$data->access_token}<br />";

    // Exemplo de método para salvar o token
    //$generic_database->saveAccessToken($data->access_token);

    $request_data = array('access_token' => $data->access_token);

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "https://{$apps_host}/v1.0/integradores/basic_user_info");
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($request_data));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array(
        'Authorization: Bearer ' . $data->access_token,
        'Content-Type: application/json'
    ));
    $response = curl_exec($ch);
    curl_close($ch);

    echo "basic_user_info: {$response}";
}

?>

<a href="<?php echo $url; ?>">Fazer login no eupreciso.com.br</a><br><br>
```

## Importação de Anúncios (Inserção/Edição/Deleção)

### Autenticação oAuth no Eu Preciso

Para utilizar a integração de anúncios via API, é necessário autenticar-se em nome de um usuário do Eu Preciso através do protocolo oAuth. A documentação da autenticação oAuth encontra-se aqui.

Na autenticação, o sistema solicitante receberá o `client_id` e o `client_secret` que deverão ser usados na URL de conexão. Durante o fluxo oAuth será requisitado que o usuário dê permissão ao integrador para gerenciar seus anúncios no Eu Preciso. No handshake do oAuth, é requisitado também o scope que a aplicação-cliente necessitará. Para utilizar o sistema de integração de anúncios via API, é preciso o scope autoupload.

### Importação de Anúncios

O processo de integração de anúncios via API consiste no envio de um arquivo no formato JSON descrevendo um ou mais anúncios para inserção, edição ou deleção.

A URL usada para envio da requisição é: <span style="font-weight: bold"> `https://apps.eupreciso.com.br/v1.0/integradores/autoupload/import`</span>

O nosso servidor deve receber a chamada com método do tipo `POST` e o header enviado deverá ser: `Content-type: application/json`, Authorization: seu `access_token`. O formato do encode do JSON deverá ser UTF-8 e o tamanho do payload não pode ultrapassar 1 MB.

### Inserção ou Edição de Anúncios

Para uma inserção ou edição de anúncios, é necessário montar o JSON com parâmetros básicos para quaisquer categorias, além de específicos de cada categoria e/ou subcategoria.

> A seguir um JSON de exemplo com uma inserção:

```json
{
  "ad_list": [
    {
      "id": "12345678910",
      "action": "insert",
      "category": "Autos e peças",
      "secondcategory": "Carros, vans e utilitários",
      "title": "BMW X1 2022 em Excelente Estado",
      "body": "Vendo carro...",
      "operation": "venda",
      "price": 240000,
      "zipcode": 89221000,
      "showPhone": "1",
      "acceptNoValidatedChat": false,
      "images": ["..."],
      "params": {
        //inserir parâmetros específicos da subcategoria
      }
    }
  ]
}
```

#### Parâmetros Básicos

| Parâmetro               | Valores                                     | Tipo            | Obrigatório                              | Descrição                                                                                                                                                                                                            |
| ----------------------- | ------------------------------------------- | --------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `access_token`          |                                             | string          | Sim                                      | Token de acesso, segue no headers da requisição.                                                                                                                                                                     |
| `id`                    | Regular expression: `[A-Za-z0-9_{}-]{1,19}` | string          | Sim                                      | O identificador do anúncio. Deve ser único no JSON (caso haja mais de um anúncio no JSON).                                                                                                                           |
| `action`                | `insert` ou `delete`                        | string          | Sim                                      | Valores para identificar qual será a ação a ser feita: `insert` para inserir ou editar anúncios, `delete` para despublicar anúncios.                                                                                 |
| `category`              |                                             | string          | Sim                                      | Categoria do anúncio.                                                                                                                                                                                                |
| `secondcategory`        |                                             | string          | Sim                                      | Subcategoria do anúncio.                                                                                                                                                                                             |
| `title`                 |                                             | string          | Sim                                      | Título do anúncio. Mínimo de 2 e máximo de 90 caracteres.                                                                                                                                                            |
| `body`                  |                                             | string          | Sim                                      | Descrição do anúncio. Mínimo de 2 e máximo de 6 mil caracteres.                                                                                                                                                      |
| `operation`             | `venda` ou `aluguel`                        | string          | Sim                                      | Tipo de oferta do anúncio.                                                                                                                                                                                           |
| `price`                 |                                             | integer         | Não                                      | Preço do anúncio (não aceita centavos).                                                                                                                                                                              |
| `zipcode`               |                                             | string numérica | Sim                                      | O CEP do anúncio.                                                                                                                                                                                                    |
| `showPhone`             | `"1"` ou `"2"`                              | string          | Não (interpreta como "1" se não enviado) | Exibir telefone? `"1"` para sim e `"2"` para ocultar o telefone no anúncio. Caso não envie o comando, ele será interpretado como `"1"` e o telefone será exibido.                                                    |
| `params`                |                                             | object          | Não                                      | Lista de parâmetros com as características do anúncio. Os valores dessa lista variam de acordo com a categoria do anúncio.                                                                                           |
| `images`                | URL da imagem                               | array de string | Não                                      | URL de imagens que serão inseridas no anúncio do eupreciso.com.br. Não pode haver URLs repetidas neste array. Máximo de 20 imagens. Importante: a primeira imagem da lista será a imagem principal do anúncio!       |
| `youtube`               | URL do vídeo                                | string          | Não                                      | URL de vídeo que será inserida no anúncio do eupreciso.com.br para o caso de usuários PRO. Deve ser apenas do https://www.youtube.com/. Aceito 1 vídeo por anúncio!                                                  |
| `instagram`             | URL da postagem ou reels no Instagram       | string          | Não                                      | URL de postagem/reels do Instagram que será inserida no anúncio do eupreciso.com.br para o caso de usuários PRO. Aceito 1 postagem/reels por anúncio!                                                                |
| `priceExchange`         |                                             | integer         | Somente se `params.exchange` for "1"     | Preço do anúncio em caso de troca (não aceita centavos). Esse campo é obrigatório quando o `params.exchange` é igual a "1", sendo utilizado na futura implementação do Eu Troco, aplicativo de trocas do Eu Preciso. |
| `acceptNoValidatedChat` | `true` ou `false`                           | Boolean         | Não                                      | Aceitar o envio de chat por pessoas não verificadas. Padrão: `false`.                                                                                                                                                |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>2 Formato recomendado de URL: https://www.youtube.com/watch?v=6PpdlhZecVs.</p>
<p>3 Formatos aceitos de URL: https://www.instagram.com/p/CTQY8Y8J8ZU/ ou https://www.instagram.com/reel/CTQY8Y8J8ZU/</p>
<p>4 O recurso de visibilidade em comunidades não é suportado via API. A visibilidade em comunidades deve ser configurada através da plataforma.</p>
<br/>
</aside>

<aside class="warning" style="color:white">
<p>IMPORTANTE: Na categoria de imóveis, as fotos do condomínio devem ser enviadas em campo próprio condoImages nos parâmetros específicos, caso exista o condomínio na listagem da API Eu Preciso, sob pena de envio do anúncio para correção. Maiores destalhes na seção de Imóveis.</p>
</aside>

#### Parâmetros Específicos

| Categoria     | Subcategoria                |
| ------------- | --------------------------- |
| Imóveis       | Apartamentos                |
| Imóveis       | Casas                       |
| Imóveis       | Terrenos, sítios e fazendas |
| Autos e peças | Carros, vans e utilitários  |
| Autos e peças | Motos                       |

- As peças e os veículos são cadastrados sob a mesma categoria no Eu Preciso, sendo que a identificação será dada em params, conforme será demonstrado nessa documentação.
- Outras categorias ainda não são suportadas pela API EU PRECISO.
- As subcategorias não listadas ainda estão sendo implementadas e não são suportadas pela API EU PRECISO.

#### Pagamento

Os anúncios no Eu Preciso são grátis, independentemente da quantidade, mas o usuário deve ter um plano PRO ativo para usar a integração de terceiros.

> A seguir um JSON de exemplo com uma deleção:

```json
{
  "ad_list": [
    {
      "id": "12345678910",
      "action": "delete"
    }
  ]
}
```

### Deleção de Anúncios

Para uma deleção, basta enviar o `id` e a `action` `delete`.

O `access_token` deve ser sempre enviado no headers da requisição Authorization.

O anúncio permanecerá publicado a menos que uma operação de deleção seja enviada para o Eu Preciso com o id utilizado em sua inserção. Portanto recomendamos atenção redobrada para o integrador enviar a deleção para o Eu Preciso, para evitar que anúncios de produtos já vendidos continuem publicados - prejudicando a experiência do anunciante e do comprador.

<aside class="warning" style="color:white">
ATENÇÃO: Os vendedores com Recursos PRO possuem renovação automática. Por isso é importante manter a base de anúncios atualizada. Uma semana antes do vencimento é possível desativar a renovação automática, conforme descrito na seção renovação.
</aside>

> Exemplos de JSONs de retorno da API:

```json
{
  "token": "78dvsfb4ZdTpMllCgIEqUbWWiOhT",
  "statusCode": 0,
  "statusMessage": "The ads were imported and will be processed",
  "errors": []
}
```

```json
{
  "token": null,
  "statusCode": -4,
  "statusMessage": "An ad had problems on import",
  "errors": [
    {
      "id": "78192371",
      "status": "error",
      "messages": [
        {
          "category": "NO_REGION"
        }
      ]
    }
  ]
}
```

### Retorno Esperado

O formato do retorno de nosso servidor será do tipo JSON, que contém a seguinte estrutura:

| Parâmetro       | Valor | Descrição                                                                                       |
| --------------- | ----- | ----------------------------------------------------------------------------------------------- |
| `token`         |       | Retorna uma string com um token a ser usado para posteriormente acessar o status da importação. |
| `statusMessage` |       | Explica o retorno síncrono da importação, com detalhamento de erros da validação síncrona.      |
| `statusCode`    |       | Identifica o retorno síncrono da importação.                                                    |
| `errors`        | array | Retorna uma lista de erros.                                                                     |

Os `statusCode` e `statusMessage` possíveis são os seguintes:

| Código | Mensagem                                                                                                                            | Descrição                                                                                                                                               |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0      | The ads were imported and will be processed                                                                                         | No caso, os anúncios foram validados sincronamente. Não é garantia de publicação, dado que há a validação assíncrona posterior (moderação, etc).        |
| -1     | Unexpected error                                                                                                                    | Erro inesperado.                                                                                                                                        |
| -2     | The request was blocked                                                                                                             | Usuário não pode importar pois está bloqueado temporariamente por excesso de requisições.                                                               |
| -3     | There is no ad to import                                                                                                            | Não há anúncios para importar.                                                                                                                          |
| -4     | An ad had problems on import                                                                                                        | Se um anúncio falhar em sua validação, a importação é cancelada.                                                                                        |
| -5     | Import is down                                                                                                                      | O serviço de importação está desativado.                                                                                                                |
| -6     | Without permission                                                                                                                  | Usuário sem permissão.                                                                                                                                  |
| -7     | Trying to import "n" ad(s), but user just have slot for "f" more.                                                                   | O usuário está tentando importar "n" anúncios, mas só pode importar mais "f".                                                                           |
| -8     | Trying to import "n" ad(s), only "f" were imported and will be processed. The following ads were ignored due to limit exceeded: "t" | Tentando importar "n" anúncios, só "f" foram importados e serão processados. Os seguintes anúncios foram ignorados devido ao limite tempo "t" excedido. |

No caso do erro do tipo -4, alguma validação síncrona aconteceu e, por isso, algum dos anúncios deixou de ser importado com sucesso. Os possíveis motivos são os seguintes:

| Código                        | Descrição                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| UNDEFINED_AD_ID               | Anúncio enviado sem ID                                                                |
| NO_IMAGE                      | Anúncio enviado sem imagens                                                           |
| NO_REGION                     | CEP enviado corresponde a região inválida                                             |
| NO_REGION                     | CEP enviado em formato inválido                                                       |
| ERROR_FUEL_4_DEPRECATED       | Tentando enviar o valor 4 - Gás Natural ou outro valor inválido no campo fuel         |
| ERROR_FINANCIAL_INVALID       | Tentando enviar o valor 1 - Financiado ou outro valor inválido no parâmetro financial |
| ERROR_CAR_FEATURE_2_INVALID   | Tentando enviar o valor 2 - Direção hidráulica no parâmetro car_features              |
| ERROR_CAR_TYPE_1_OR_4_INVALID | Tentando enviar o valor 1 ou 4 no parâmetro cartype                                   |
| ERROR_VEHICLE_TAG_INVALID     | Placa não enviada ou em formato inválido                                              |
| ERROR_VEHICLE_BRAND_INVALID   | Marca não enviada ou em formato inválido                                              |
| ERROR_VEHICLE_MODEL_INVALID   | Modelo não enviado ou em formato inválido                                             |
| ERROR_VEHICLE_VERSION_INVALID | Versão não enviada ou em formato inválido                                             |

## Categoria de Autos e Peças

Para importação da categoria de Autos e peças, é necessário informar a `category` e `secondcategory` que será utilizada para que o anúncio esteja disponível na categoria correta. As subcategorias existentes são as seguintes:

| category      | Secondcategory / subcategorias |
| ------------- | ------------------------------ |
| Autos e peças | Carros, vans e utilitários     |
| Autos e peças | Motos                          |

No momento a API do Eu Preciso apenas aceita as subcategorias (`secondcategory`) “Carros, vans e utilitários” e “Motos”.

### Parâmetros específicos por subcategoria

Cada subcategoria de Autos tem seu conjunto de parâmetros e valores específicos. Para isso, você deverá considerar os parâmetros específicos para cada subcategoria, bem como os parâmetros gerais para quaisquer anúncios no Eu Preciso.

> Aqui está um exemplo de JSON para inserção ou edição de anúncios na subcategoria Carros, vans e utilitários:

```json
{
  "ad_list": [
    {
      "id": "5555555555",
      "action": "insert",
      "category": “Autos e peças”,
      “secondcategory”: “Carros, vans e utilitários”
      "title": "Carro Novo",
      "body": "Corpo do anúncio",
      "operation": "venda",
      "price": 10500,
      "zipcode": "24230090",
      "params": {
        "complete_plate": "ABC1234",
        "brand": “BMW",
        "model": " X1 SDRIVE 20i 2.0/2.0 TB Acti.Flex Aut.",
        "regdate": "2021",
        "gearbox": "1",
        "financial": [
          "2",
          "3"
        ],
        "fuel": "1",
        "cartype": “9",
        "mileage": 10000,
        "doors": "2",
        "engine": "2.0",
        "steering": "1",
        "carcolor": "1",
        "car_features": [
          "1",
          "3"
        ]
      },
      "images": [
        "http://www.a.com/image1.png",
        "http://www.a.com/image2.png"
      ]
    }
  ]
}
```

### Carros, vans e utilitários

Para esta subcategoria, é necessário preencher o parâmetro `category` com a string “Autos e peças” e o parâmetro `secondcategory` com a string “Carros, vans e utilitários”.

Além disso, há parâmetros específicos para esta subcategoria, que devem constar dentro do parâmetro `params` e preenchidos conforme a tabela a seguir:

| Parâmetro           | Valor                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Tipo             | Obg.             | Descrição                                                                                                                       |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `regdate`           | <p>Ano do veículo para os fabricados a partir de 1980 ou</p><p>1975 para Entre 1975 e 1980</p><p>1970 para Entre 1970 a 1975</p><p>1965 para Entre 1965 e 1970</p><p>1960 para Entre 1960 e 1965</p><p>1955 para Entre 1955 e 1960</p><p>1950 para 1950 ou anterior</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | string           | Sim              | Ano do automóvel.                                                                                                               |
| `mileage`           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | integer          | Sim              | Quilometragem do automóvel.                                                                                                     |
| `gearbox`           | <p>1 para Manual</p><p>2 para Automático</p><p>3 para Semi-Automático</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | string           | Não <sup>1</sup> | Tipo de câmbio.                                                                                                                 |
| `fuel`              | <p>1 para Gasolina</p><p>2 para Álcool</p><p>3 para Flex</p><p>4 para Gás Natural</p><p>5 para Diesel</p><p>6 para Híbrido</p><p>7 para Elétrico</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | string           | Não <sup>1</sup> | Tipo de combustível.                                                                                                            |
| `gnv`               | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Caso o automóvel possua Kit GNV.                                                                                                |
| `brand`             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | string           | Sim              | Marca do automóvel. Para verificar as disponíveis, use o serviço do Eu Preciso, conforme descrito nesta documentação.           |
| `model`             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | string           | Sim              | Modelo da marca do automóvel. Para verificar os disponíveis, use o serviço do Eu Preciso, conforme descrito nesta documentação. |
| `complete_plate`    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | string           | Sim              | Placa do Carro (sem traços ou sinais especiais).                                                                                |
| `condition`         | <p>“1” para novo</p><p>“2” para usado</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | string           | Sim              | Condição do veículo.                                                                                                            |
| `parts`             | <p>1 para peças</p><p>2 para veículos</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | string           | Não <sup>2</sup> | Identifica se se trata de autopeça ou veículo.                                                                                  |
| `car_features`      | <p>1 - ACC - Piloto Automático Adaptativo</p><p>2 - Airbag</p><p>3 - Alarme</p><p>4 - Alerta de ponto cego</p><p>5 - Ar condicionado</p><p>6 - Ar quente</p><p>7 - Banco com regulagem de altura</p><p>8 - Bancos dianteiros com aquecimento</p><p>9 - Bancos dianteiros com massagem</p><p>10 - Bancos em couro</p><p>11 - Blindado</p><p>12 - Capota marítima</p><p>13 - CD e mp3 player</p><p>14 - CD player</p><p>15 - Câmera de ré</p><p>16 - Câmera 360º</p><p>17 - Computador de bordo</p><p>18 - Controle automático de velocidade</p><p>19 - Controle de tração</p><p>20 - Desembaçador traseiro</p><p>21 - Detector de fadiga</p><p>22 - Direção hidráulica</p><p>23 - Disqueteira</p><p>24 - DVD player</p><p>25 - Encosto de cabeça traseiro</p><p>26 - Farol a laser</p><p>27 - Farol de xenônio</p><p>28 - Freio abs</p><p>29 - GPS</p><p>30 - Leitor de placas</p><p>31 - Limpador traseiro</p><p>32 - Piloto automático comum</p><p>33 - Protetor de caçamba</p><p>34 - Rádio</p><p>35 - Rádio e toca fitas</p><p>36 - Retrovisor fotocrômico</p><p>37 - Retrovisores elétricos</p><p>38 - Rodas de liga leve</p><p>39 - Sensor de chuva</p><p>40 - Sensor de estacionamento</p><p>41 - Teto solar</p><p>42 - Tração 4x4</p><p>43 - Travas elétricas</p><p>44 - Vidros elétricos</p><p>45 - Volante com regulagem de altura</p> | array de strings | Não <sup>1</sup> | Opcionais.                                                                                                                      |
| `doors`             | <p>1 para 2 portas</p><p>2 para 4 portas</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | string           | Não <sup>1</sup> | Número de portas.                                                                                                               |
| `steering`          | <p>1 para Hidráulica</p><p>2 para Elétrica</p><p>3 para Mecânica</p><p>4 para Assistida</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | string           | Não              | Direção.                                                                                                                        |
| `engine`            | <p>1.0 para 1.0</p><p>1.2 para 1.2</p><p>1.3 para 1.3</p><p>1.4 para 1.4</p><p>1.5 para 1.5</p><p>1.6 para 1.6</p><p>1.7 para 1.7</p><p>1.8 para 1.8</p><p>1.9 para 1.9</p><p>2.0 - 2.9 para 2.0 - 2.9</p><p>3.0 - 3.9 para 3.0 - 3.9</p><p>4.0 - para 4.0 ou mais</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | string           | Não <sup>1</sup> | Potência do motor.                                                                                                              |
| `cartype`           | <p>1 para antigo</p><p>2 para buggy</p><p>3 para Caminhão leve</p><p>4 para Conversível</p><p>5 para Hatch</p><p>6 para Passeio</p><p>7 para Pick-up</p><p>8 para Sedã</p><p>9 para SUV</p><p>10 para Van/utilitário</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | string           | Não <sup>1</sup> | Tipo de automóvel.                                                                                                              |
| `carcolor`          | <p>1 para Preto</p><p>2 para Branco</p><p>3 para Prata</p><p>4 para Vermelho</p><p>5 para Cinza</p><p>6 para Azul</p><p>7 para Amarelo</p><p>8 para Verde</p><p>9 para Laranja</p><p>10 para Outra</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | string           | Não <sup>1</sup> | Cor do automóvel.                                                                                                               |
| `exchange`          | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Depende          | Aceita trocas pelo produto. Obrigatório quando a operação for “venda”.                                                          |
| `financial`         | <p>1 para Financiado</p><p>2 para Quitado</p><p>3 para IPVA Pago</p><p>4 para Com multas</p><p>5 para De leilão</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | array de strings | Não <sup>1</sup> | Estado financeiro.                                                                                                              |
| `owner`             | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Único dono.                                                                                                                     |
| `manual`            | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Com manual do Automóvel.                                                                                                        |
| `extra_key`         | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Com chave reserva.                                                                                                              |
| `dealership_tuneup` | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Com revisões feitas em concessionária.                                                                                          |
| `warranty`          | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não <sup>1</sup> | Com garantia.                                                                                                                   |
| `parts_name_cars`   | <p>1 para pneus</p><p>2 para rodas</p><p>3 para calotas</p><p>4 para peças automotivas</p><p>5 para GPS</p><p>6 para som e multimídia</p><p>7 para tuning e performance</p><p>8 para acessórios para interior</p><p>9 para acessórios para exterior</p><p>10 outras peças</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | string           | Depende          | Tipo de peça. Obrigatório, se o campo “parts” for preenchido com o valor “1”.                                                   |
| `rent_type`         | <p>1 – por dia</p><p>2 – por semana</p><p>3 – por mês</p><p>4 - pacote</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | string           | Depende          | Tipo de pagamento (aluguel).                                                                                                    |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>- Os campos regdate, mileage, complete_plate, brand e model deixam de ser obrigatórios se params.parts tiver valor igual a “1”. Nesse caso, o parâmetro parts_name_cars passa a ser obrigatório.</p>
<p>- O campo rent_type é obrigatório quando o campo operation for preenchido como “aluguel”.</p>
<p>2 Se você não enviar o parâmetro parts, o sistema automaticamente atribuirá o valor “2”, isto é, o sistema Eu Preciso vai identificar como sendo um anúncio de veículo.</p>
</aside>

> Exemplo de resposta:

```json
{
  "status": "ok",
  "data": {
    "Integra GS 1.8": 1,
    "Legend 3.2/3.5": 2,
    "NSX 3.0": 3
  }
}
```

#### Listagem de Marcas e Modelos de Automóveis no Eu Preciso

Os endpoints disponíveis para consultar detalhes de marcas e modelos para Automóveis no Eu Preciso são os seguintes:

| Subcategoria | Descrição                                  | Endpoint                                                                                                 |
| ------------ | ------------------------------------------ | -------------------------------------------------------------------------------------------------------- |
| Carros       | Marcas de carros disponíveis               | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/car_info                                 |
| Carros       | Modelos de carros de uma determinada marca | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/car_info/{id_marca}                      |
| Carros       | Anos de carros de um determinado modelo    | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/car_info/{id_marca}/years/{id_do_modelo} |

Nosso servidor deve receber a requisição com método do tipo POST, sendo que o formato do arquivo a ser enviado para nosso servidor deverá ser do tipo JSON.

- O `access_token` deve ser fornecido no headers da requisição.
- O modelo a ser preenchido no campo `params.model` é o nome do modelo e não o código, de forma idêntica à constante da resposta. Por exemplo: “Pajero HPE 3.5 4x4 Flex 5p Aut.”.

> Aqui está um exemplo de JSON para inserção ou edição de anúncios na subcategoria Motos:

```json
{
  "ad_list": [
    {
      "id": "5555555555",
      "action": "insert",
      "category": “Autos e peças”,
      “secondcategory”: “Motos”
      "title": "Moto Nova",
      "body": "Corpo do anúncio",
      "operation": "venda",
      "price": 10500,
      "zipcode": "24230090",
      "params": {
        "complete_plate": "ABC1234",
        "brand": “HONDA",
        "model": "SUPER HAWK 1000",
        "regdate": "1998",
        "financial": [
          "2",
          "3"
        ],
        "mototype": “9",
        "mileage": 10000,
        "engine": "22",
        "carcolor": "1",
        "moto_features": [
          "1",
          "3"
        ]
      },
      "images": [
        "http://www.a.com/image1.png",
        "http://www.a.com/image2.png"
      ]
    }
  ]
}
```

### Motos

Para esta subcategoria, é necessário preencher o parâmetro `category` com a string “Autos e peças” e o parâmetro `secondcategory` com a string “Motos”.

Além disso, há parâmetros específicos para esta subcategoria, que devem constar dentro do parâmetro params e preenchidos conforme a tabela a seguir:

| Parâmetro           | Valor                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Tipo             | Obg.             | Descrição                                                                                                                  |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `regdate`           | <p>Ano do veículo para os fabricados a partir de 1980 (p.ex.: “2023”) ou</p><p>1975 para Entre 1975 e 1980</p><p>1970 para Entre 1970 a 1975</p><p>1965 para Entre 1965 e 1970</p><p>1960 para Entre 1960 e 1965</p><p>1955 para Entre 1955 e 1960</p><p>1950 para 1950 ou anterior</p>                                                                                                                                                                                                                                                                                                                                                        | string           | Sim              | Ano da moto                                                                                                                |
| `mileage`           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | integer          | Sim              | Quilometragem                                                                                                              |
| `brand`             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | string           | Sim              | Marca da moto. Para verificar as disponíveis, use o serviço do Eu Preciso, conforme descrito nesta documentação.           |
| `model`             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | string           | Sim              | Modelo da marca da moto. Para verificar os disponíveis, use o serviço do Eu Preciso, conforme descrito nesta documentação. |
| `complete_plate`    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | string           | Sim              | Placa do Carro (sem traços ou sinais especiais)                                                                            |
| `condition`         | <p>“1” para novo</p><p>“2” para usado</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | string           | Sim              | Condição da moto.                                                                                                          |
| `parts`             | <p>1 para peças</p><p>2 para motos</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | string           | Não <sup>2</sup> | Identifica se se trata de autopeça ou moto                                                                                 |
| `moto_features`     | <p>1 para ABS</p><p>2 para Computador de bordo</p><p>3 para Escapamento esportivo</p><p>4 para Bolsa / Baú / Bauleto</p><p>5 para Contra peso no guidon</p><p>6 para Alarme</p><p>7 para Amortecedor de direção</p><p>8 para Faróis de Neblina</p><p>9 para GPS</p><p>10 para Som</p>                                                                                                                                                                                                                                                                                                                                                          | array de strings | Não <sup>1</sup> | Opcionais                                                                                                                  |
| `engine`            | <p>1 - 50 cilindradas</p><p>2 - 100 cilindradas</p><p>3 - 125 cilindradas</p><p>4 - 150 cilindradas</p><p>5 - 160 cilindradas</p><p>6 - 200 cilindradas</p><p>7 - 250 cilindradas</p><p>8 - 300 cilindradas</p><p>9 - 350 cilindradas</p><p>10 - 400 cilindradas</p><p>11 - 450 cilindradas</p><p>12 - 500 cilindradas</p><p>13 - 550 cilindradas</p><p>14 - 600 cilindradas</p><p>15 - 650 cilindradas</p><p>16 - 700 cilindradas</p><p>17 - 750 cilindradas</p><p>18 - 800 cilindradas</p><p>19 - 850 cilindradas</p><p>20 - 900 cilindradas</p><p>21 - 950 cilindradas</p><p>22 - 1000 cilindradas</p><p>23 - Acima de 1000 cilindradas</p> | string           | Sim              | Cilindradas                                                                                                                |
| `mototype`          | <p>1 para Street</p><p>2 para Esportiva</p><p>3 para Custom</p><p>4 para Trail</p><p>5 para Naked</p><p>6 para Scooter</p><p>7 para Offroad</p><p>8 para Touring</p><p>9 para Utilitária</p><p>10 para Supermotard</p><p>11 para Triciclo</p><p>12 para Quadriciclo</p><p>13 para Trial</p><p>14 para Minicross</p>                                                                                                                                                                                                                                                                                                                            | string           | Não <sup>1</sup> | Tipo de automóvel                                                                                                          |
| `carcolor`          | <p>1 para Preto</p><p>2 para Branco</p><p>3 para Prata</p><p>4 para Vermelho</p><p>5 para Cinza</p><p>6 para Azul</p><p>7 para Amarelo</p><p>8 para Verde</p><p>9 para Laranja</p><p>10 para Outra</p>                                                                                                                                                                                                                                                                                                                                                                                                                                         | string           | Não <sup>1</sup> | Cor da moto                                                                                                                |
| `exchange`          | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Depende          | Aceita trocas pelo produto. Obrigatório quando a operação for “venda”                                                      |
| `financial`         | <p>1 para Financiado</p><p>2 para Quitado</p><p>3 para IPVA Pago</p><p>4 para Com multas</p><p>5 para De leilão</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | array de strings | Não <sup>1</sup> | Estado financeiro                                                                                                          |
| `owner`             | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Não <sup>1</sup> | Único dono                                                                                                                 |
| `manual`            | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Não <sup>1</sup> | Com manual do Automóvel                                                                                                    |
| `extra_key`         | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Não <sup>1</sup> | Com chave reserva                                                                                                          |
| `dealership_tuneup` | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Não <sup>1</sup> | Com revisões feitas em concessionária                                                                                      |
| `warranty`          | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | string           | Não <sup>1</sup> | Com garantia                                                                                                               |
| `parts_name_motos`  | <p>1 para pneus</p><p>2 para rodas</p><p>3 para calotas</p><p>4 para capacetes</p><p>5 para Acabamento</p><p>6 para roupas de moto</p><p>7 para Bagageiros, baús e mochilas</p><p>8 para suportes</p><p>9 para alarmes</p><p>10 para peças de motos</p><p>11 outras peças</p>                                                                                                                                                                                                                                                                                                                                                                  | string           | Depende          | Obrigatório, se o campo “parts” for preenchido com o valor “1”                                                             |
| `rent_type`         | <p>1 – por dia</p><p>2 – por semana</p><p>3 – por mês</p><p>4 - pacote</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | string           | Depende          | Tipo de pagamento (aluguel)                                                                                                |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>- Os campos regdate, mileage, complete_plate, brand e model deixam de ser obrigatórios se params.parts tiver valor igual a “1”. Nesse caso, o parâmetro parts_name_cars passa a ser obrigatório.</p>
<p>- O campo rent_type é obrigatório quando o campo operation for preenchido como “aluguel”.</p>
<p>2 Se você não enviar o parâmetro parts, o sistema automaticamente atribuirá o valor “2”, isto é, o sistema Eu Preciso vai identificar como sendo um anúncio de uma moto.</p>
</aside>

> Exemplo de resposta:

```json
{
  "status": "ok",
  "data": {
    "AVAJET 100cc/ CLASSIC 100cc": 2874,
    "CONCOURS14 1352cc": 4632,
    "D-TRACKER X 250cc": 5153,
    "ER-5 500cc": 2875,
    "ER-6N 650cc": 5154,
    "KLX 110": 4876
  }
}
```

#### Listagem de Marcas e Modelos de Motos no Eu Preciso

Os endpoints disponíveis para consultar detalhes de marcas e modelos para Motos no Eu Preciso são os seguintes:

| Subcategoria | Descrição                                 | Endpoint                                                                                                  |
| ------------ | ----------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Motos        | Marcas de motos disponíveis               | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/moto_info                                 |
| Motos        | Modelos de motos de uma determinada marca | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/moto_info/{id_marca}                      |
| Motos        | Anos de motos de um determinado modelo    | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/moto_info/{id_marca}/years/{id_do_modelo} |

Nosso servidor deve receber a requisição com método do tipo POST, sendo que o formato do arquivo a ser enviado para nosso servidor deverá ser do tipo JSON.

- O `access_token` deve ser fornecido no headers da requisição.
- O modelo a ser preenchido no campo params.model é o nome do modelo e não o código, de forma idêntica à constante da resposta. Por exemplo: “KLX 110”.

## Categoria de Imóveis

Para importação da categoria de Imóveis, é necessário informar a `category` e `secondcategory` que será utilizada para que o anúncio esteja disponível na categoria correta. As subcategorias existentes são as seguintes:

| Category | Secondcategory / Subcategoria no Eu Preciso |
| -------- | ------------------------------------------- |
| Imóveis  | Apartamentos                                |
| Imóveis  | Casas                                       |
| Imóveis  | Terrenos, sítios e fazendas                 |

No momento a API do Eu Preciso apenas aceita as subcategorias “Casas”, “Apartamentos” e “Terrenos, sítios e fazendas”.

### Parâmetros específicos por subcategoria

Cada subcategoria de Imóveis tem seu conjunto de parâmetros e valores específicos. Para isso, você deverá considerar os parâmetros específicos para cada subcategoria, bem como os parâmetros gerais para quaisquer anúncios no Eu Preciso.

> Aqui está um exemplo de JSON para inserção ou edição de anúncios na subcategoria Apartamentos:

```json
{
  "ad_list": [
    {
      "id": "111111111111",
      "action": "insert",
      "category": “Imóveis”,
      “secondcategory”: “Apartamentos”,
      "title": "Apartamento Novo",
      "body": "Descrição do anúncio\nNova linha da descrição\nAinda outra linha da descrição",
      "phoneNumber": 4799999999,
      "operation": "venda",
      "price": 950000,
      "zipcode": "89999999",
      "params": {
        "rooms": "3",
        "bathrooms": "2",
        "garage_spaces": "2",
        "size": 150,
        "iptu": 1000,
        "condominio": 500,
        "apartment_type": "3",
        "apartment_features": [
          "1",
          "2"
        ],
        "apartment_complex_features": [
          "1",
          "2"
        ],
        “launch”: true,
        “launch_modality”: “1”
      },
      "images": [
        "http://www.a.com/image1.png",
        "http://www.a.com/image2.png"
      ]
    },
    {
      "id": "222222222222",
      "action": "insert",
      "category": “Imóveis”,
      “secondcategory”: “Apartamentos”,
      "title": "Apartamento Novo",
      "body": "Descrição do anúncio\nNova linha da descrição\nAinda outra linha da descrição",
      "phoneNumber": 4799999999,
      "operation": "venda",
      "price": 950000,
      "zipcode": "89999999",
      "params": {
        "rooms": "2",
        "bathrooms": "1",
        "garage_spaces": "0",
        "size": 90,
        "iptu": 200,
        "condominio": 300,
        "apartment_type": "2",
        "apartment_features": [
          "1",
          "2"
        ],
        "apartment_complex_features": [
          "1",
          "2"
        ],
        “launch”: false,
        “condominioName”: “Nome do Condomínio aprovavado pela API Eu Preciso”,
        “condoImages”: [
          “https://www.condoimages.com/image1.png”,
          “https://www.condoimages.com/image2.png”,
        ]
      },
      "images": [
        "http://www.a.com/image1.png",
        "http://www.a.com/image2.png"
      ]
    }
  ]
}
```

### Apartamentos

| Parâmetro                    | Valor                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Tipo             | Obrigatório     | Descrição                                                                                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `rooms`                      | <p>0 para 0 quartos</p><p>1 para 1 quarto</p><p>2 para 2 quartos</p><p>3 para 3 quartos</p><p>4 para 4 quartos</p><p>5 para 5 ou mais quartos</p>                                                                                                                                                                                                                                                                                                                                                                                                                         | string           | Sim             | Quantidade de quartos                                                                                                      |
| `bathrooms`                  | <p>0 para 0 banheiro</p><p>1 para 1 banheiro</p><p>2 para 2 banheiros</p><p>3 para 3 banheiros</p><p>4 para 4 banheiros</p><p>5 para 5 ou mais banheiros</p>                                                                                                                                                                                                                                                                                                                                                                                                              | string           | Não<sup>1</sup> | Quantidade de banheiros                                                                                                    |
| `garage_spaces`              | <p>0 para 0 vagas</p><p>1 para 1 vaga</p><p>2 para 2 vagas</p><p>3 para 3 vagas</p><p>4 para 4 vagas</p><p>5 para 5 ou mais vagas</p>                                                                                                                                                                                                                                                                                                                                                                                                                                     | string           | Não<sup>1</sup> | Quantidade de vagas de garagem                                                                                             |
| `size`                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Área do apartamento (m²). Não aceita frações                                                                               |
| `apartment_type`             | <p>1 para Padrão</p><p>2 para Cobertura</p><p>3 para Duplex/triplex</p><p>4 para Kitnet</p><p>5 para Loft</p><p>6 para Multipropriedade</p>                                                                                                                                                                                                                                                                                                                                                                                                                               | string           | Sim             | Tipo de apartamento                                                                                                        |
| `apartment_features`         | <p>1 para Ar condicionado</p><p>2 para Área de Serviço</p><p>3 para Armários na Cozinha</p><p>4 para Armários no Quarto</p><p>5 para Banheira/spa/jacuzzi</p><p>6 para Mobiliado</p><p>7 para Piscina privativa</p><p>8 para Quarto de serviço</p><p>9 para Varanda</p>                                                                                                                                                                                                                                                                                                   | array de strings | Não<sup>1</sup> | Detalhes do imóvel                                                                                                         |
| `apartment_complex_features` | <p>1 para Academia</p><p>2 para Banheira/Spa/Jacuzzi</p><p>3 para Campo de golf</p><p>4 para Condomínio fechado</p><p>5 para Coworking</p><p>6 para Elevador</p><p>7 para Energia fotovoltaica</p><p>8 para Permitido animais</p><p>9 para Piscina</p><p>10 para Portão eletrônico</p><p>11 para Portaria</p><p>12 para Quadra de tênis</p><p>13 para Quadra poliesportiva</p><p>14 para Reaproveitamento de água da chuva</p><p>15 para Salão de beleza</p><p>16 para Salão de festas</p><p>17 para Sala de Massagem</p><p>18 para Sauna</p><p>19 para Segurança 24h</p> | array de strings | Não<sup>1</sup> | Detalhes do condomínio                                                                                                     |
| `iptu`                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do IPTU                                                                                                       |
| `condominio`                 |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do condomínio                                                                                                 |
| `condominioName`             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | String           | Não             | Nome do condomínio. Para verificar os nomes disponíveis use o serviço do Eu Preciso, conforme descrito nesta documentação. |
| `condoImages`                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Array de strings | Não             | Fotos do condomínio<sup>2</sup>.                                                                                           |
| `launch`                     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Boolean          | Não             | Para imóveis em fase de lançamento.                                                                                        |
| `launch_modality`            | <p>1 para Pronto para morar ou construir</p><p>2 para na Planta</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | String           | Depende         | A modalidade de lançamento será obrigatória quando o parâmetro launch for true.                                            |
| `exchange`                   | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | string           | Depende         | Aceita trocas pelo produto. Obrigatório quando a operação for “venda”.                                                     |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>2 Na categoria de imóveis, as fotos do condomínio devem ser enviadas este campo próprio, caso exista o condomínio na listagem da API Eu Preciso, sob pena de envio do anúncio para correção.</p>
</aside>

### F.3) Casas

| Parâmetro               | Valor                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Tipo             | Obrigatório     | Descrição                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `rooms`                 | <p>0 para 0 quartos</p><p>1 para 1 quarto</p><p>2 para 2 quartos</p><p>3 para 3 quartos</p><p>4 para 4 quartos</p><p>5 para 5 ou mais quartos</p>                                                                                                                                                                                                                                                                                                                                                                                                                         | string           | Sim             | Quantidade de quartos                                                                                                      |
| `bathrooms`             | <p>1 para 1 banheiro</p><p>2 para 2 banheiros</p><p>3 para 3 banheiros</p><p>4 para 4 banheiros</p><p>5 para 5 ou mais banheiros</p>                                                                                                                                                                                                                                                                                                                                                                                                                                      | string           | Não<sup>1</sup> | Quantidade de banheiros                                                                                                    |
| `garage_spaces`         | <p>0 para 0 vagas</p><p>1 para 1 vaga</p><p>2 para 2 vagas</p><p>3 para 3 vagas</p><p>4 para 4 vagas</p><p>5 para 5 ou mais vagas</p>                                                                                                                                                                                                                                                                                                                                                                                                                                     | string           | Não<sup>1</sup> | Quantidade de vagas de garagem                                                                                             |
| `size`                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Área da casa (m²). Não aceita frações                                                                                      |
| `home_type`             | <p>1 para Padrão</p><p>2 para Casa de vila</p><p>3 para Casa de condomínio</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | string           | Sim             | Tipo de casa                                                                                                               |
| `home_features`         | <p>1 para Ar condicionado</p><p>2 para Área de Serviço</p><p>3 para Armários na Cozinha</p><p>4 para Armários no Quarto</p><p>5 para Banheira/spa/jacuzzi</p><p>6 para Mobiliado</p><p>7 para Piscina privativa</p><p>8 para Quarto de serviço</p><p>9 para Varanda</p>                                                                                                                                                                                                                                                                                                   | array de strings | Não<sup>1</sup> | Detalhes do imóvel                                                                                                         |
| `home_complex_features` | <p>1 para Academia</p><p>2 para Banheira/Spa/Jacuzzi</p><p>3 para Campo de golf</p><p>4 para Condomínio fechado</p><p>5 para Coworking</p><p>6 para Elevador</p><p>7 para Energia fotovoltaica</p><p>8 para Permitido animais</p><p>9 para Piscina</p><p>10 para Portão eletrônico</p><p>11 para Portaria</p><p>12 para Quadra de tênis</p><p>13 para Quadra poliesportiva</p><p>14 para Reaproveitamento de água da chuva</p><p>15 para Salão de beleza</p><p>16 para Salão de festas</p><p>17 para Sala de Massagem</p><p>18 para Sauna</p><p>19 para Segurança 24h</p> | array de strings | Não<sup>1</sup> | Detalhes do condomínio                                                                                                     |
| `iptu`                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do IPTU                                                                                                       |
| `condominio`            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do condomínio                                                                                                 |
| `condominioName`        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | String           | Não             | Nome do condomínio. Para verificar os nomes disponíveis use o serviço do Eu Preciso, conforme descrito nesta documentação. |
| `condoImages`           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Array de strings | Não             | Fotos do condomínio<sup>2</sup>.                                                                                           |
| `launch`                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Boolean          | Não             | Para imóveis em fase de lançamento.                                                                                        |
| `launch_modality`       | <p>1 para Pronto para morar ou construir</p><p>2 para na Planta</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | String           | Depende         | A modalidade de lançamento será obrigatória quando o parâmetro launch for true.                                            |
| `exchange`              | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | string           | Depende         | Aceita trocas pelo produto. Obrigatório quando a operação for “venda”.                                                     |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>2 Na categoria de imóveis, as fotos do condomínio devem ser enviadas este campo próprio, caso exista o condomínio na listagem da API Eu Preciso, sob pena de envio do anúncio para correção.</p>
</aside>

### Terrenos, sítios e fazendas

| Parâmetro               | Valor                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Tipo             | Obrigatório     | Descrição                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `size`                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Área do terreno (m²). Não aceita frações                                                                                   |
| `land_type`             | <p>1 para Terrenos e lotes</p><p>2 para Sítios e chácaras</p><p>3 para Fazendas</p><p>4 para outros</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | string           | Sim             | Tipo de terreno                                                                                                            |
| `land_features`         | <p>1 para Acesso asfaltado</p><p>2 para Água encanada</p><p>3 para Área verde</p><p>4 para Campo de futebol</p><p>5 para Casa sede</p><p>6 para Churrasqueira</p><p>7 para Energia elétrica</p><p>8 para Piscina</p><p>9 para Poço artesiano</p><p>10 para Pomar</p>                                                                                                                                                                                                                                                                                                      | array de strings | Não<sup>1</sup> | Detalhes do imóvel                                                                                                         |
| `land_complex_features` | <p>1 para Academia</p><p>2 para Banheira/Spa/Jacuzzi</p><p>3 para Campo de golf</p><p>4 para Condomínio fechado</p><p>5 para Coworking</p><p>6 para Elevador</p><p>7 para Energia fotovoltaica</p><p>8 para Permitido animais</p><p>9 para Piscina</p><p>10 para Portão eletrônico</p><p>11 para Portaria</p><p>12 para Quadra de tênis</p><p>13 para Quadra poliesportiva</p><p>14 para Reaproveitamento de água da chuva</p><p>15 para Salão de beleza</p><p>16 para Salão de festas</p><p>17 para Sala de Massagem</p><p>18 para Sauna</p><p>19 para Segurança 24h</p> | array de strings | Não<sup>1</sup> | Detalhes do condomínio                                                                                                     |
| `iptu`                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do IPTU                                                                                                       |
| `condominio`            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | integer          | Não<sup>1</sup> | Valor mensal do condomínio                                                                                                 |
| `condominioName`        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | String           | Não             | Nome do condomínio. Para verificar os nomes disponíveis use o serviço do Eu Preciso, conforme descrito nesta documentação. |
| `condoImages`           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Array de strings | Não             | Fotos do condomínio<sup>2</sup>.                                                                                           |
| `launch`                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Boolean          | Não             | Para imóveis em fase de lançamento.                                                                                        |
| `launch_modality`       | <p>1 para Pronto para morar ou construir</p><p>2 para na Planta</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | String           | Depende         | A modalidade de lançamento será obrigatória quando o parâmetro launch for true.                                            |
| `exchange`              | <p>1 para Sim</p><p>2 para Não</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | string           | Depende         | Aceita trocas pelo produto. Obrigatório quando a operação for “venda”.                                                     |

<aside class="notice" style="color:white">
<span style="font-weight: bold">Notas</span>
<p>1 Se você não quer enviar um parâmetro não-obrigatório, deixe de enviar o parâmetro no payload. Se você enviar o parâmetro com valor vazio ou 0, a operação vai falhar (a menos, é claro, que o valor 0 seja esperado para esse parâmetro).</p>
<p>2 Na categoria de imóveis, as fotos do condomínio devem ser enviadas este campo próprio, caso exista o condomínio na listagem da API Eu Preciso, sob pena de envio do anúncio para correção.</p>
</aside>

> Exemplo de resposta:

```json
{
  "status": "ok",
  "data": [
    {
      "nome": "VISION RESIDENCE",
      "cnpj": "46757764000127",
      "cep": "89221008",
      "logradouro": "Rua Dona Francisca",
      "numero": "2818",
      "bairro": "Saguaçu",
      "municipio": "Joinville",
      "state": "Santa Catarina"
    },
    {
      "nome": "MATISSE RESIDENCE HOME CLUB",
      "cnpj": "21439848000191",
      "cep": "89221008",
      "logradouro": "Rua Dona Francisca",
      "numero": "2666",
      "bairro": "Saguaçu",
      "municipio": "Joinville",
      "state": "Santa Catarina"
    }
  ]
}
```

### Listagem de Condomínios no Eu Preciso

Os endpoints disponíveis para consultar nomes de Condomínios no Eu Preciso são os seguintes:

| Descrição                                | Endpoint                                                                                         | Parâmetros     |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------- |
| Nomes de condomínio disponíveis por cep  | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/cep/${CEP}            | -              |
| Nomes de condomínio disponíveis por CNPJ | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/cnpj/${CNPJ}          | -              |
| Nomes de condomínio disponíveis por Nome | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/nome/${PARTE DO NOME} | cidade, bairro |

Nosso servidor deve receber a requisição com método do tipo POST, sendo que o formato do arquivo a ser enviado para nosso servidor deverá ser do tipo JSON.

<p style="font-weight:bold">Exemplo de consulta por CEP:</p>

`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/cep/89221008`

ou

`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/cep/89221-008`

<p style="font-weight:bold">Exemplo de consulta por nome:</p>

`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/ nome/Ouro?cidade=São+Paulo&bairro=Jardim+Paulista`

- O `access_token` deve ser fornecido no headers da requisição.
- O nome a ser preenchido no campo params.condominioName é o nome, primeiro campo da resposta.

<aside class="warning" style="color:white">
ATENÇÃO: Caso não localizado o condomínio, basta ignorar o campo params.condominioName.
</aside>

> Exemplo de resposta:

```json
{
  "status": "ok",
  "data": {
    "nome": "NOME_COMPLETO"
  }
}
```

### Inserção de Condomínios no Eu Preciso

O endpoint disponível para inserir um condomínio não localizado na base de dados do Eu Preciso é:

| Descrição                               | Endpoint                                                                          | Parâmetros |
| --------------------------------------- | --------------------------------------------------------------------------------- | ---------- |
| Nomes de condomínio disponíveis por cep | https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/insert | -          |

Nosso servidor deve receber a requisição com método do tipo `POST`, sendo que o formato do arquivo a ser enviado para nosso servidor deverá ser do tipo `JSON`. A requisição deve receber como parâmetro somente o cnpj do condomínio.

<p style="font-weight:bold">Exemplo de inserção de condomínio:</p>

`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/info/condo_info/insert?cnpj=46757764000127`

A inserção de condomínios pode resultar em erros de servidor (status 400, reason: “SERVER_ERROR_CEP”), em razão do CEP inválido cadastrado para o condomínio nas bases oficiais. Em caso de repetidos erros de servidor para a inserção de um mesmo condomínio, contate nosso suporte para inserção interna.

Também é possível que o erro retornado seja referente à impossibilidade de definição de zona e região para o condomínio. Nesse caso o servidor retornará uma mensagem: “Verifique com o suporte a inserção da zona e região para os seguintes dados. Bairro: ${ bairro }, Cidade: ${ cidade}/${ uf}.

Outros erros possíveis podem estar relacionados ao CNAE não referente a condomínio, CNPJ inativo ou empresa estrangeira.

# Status de Publicação

Durante a importação, os anúncios publicados por integradores são automaticamente validados e publicados, com exceção dos anúncios com erros de definição de região, que serão corrigidos no processo de validação.

É possível consultar o status da publicação, a confirmação de inserção do anúncio com sucesso, bem como informa e detalha um eventual erro no processo de importação de um anúncio.

## Requisição de Status

A URL usada para fazer a requisição do arquivo JSON: <p style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/import/{token}`</p>. O token a ser inserido no final da URL é o retorno mencionado na importação.

A requisição feita para esta URL deve conter o `access_token` de cada anunciante nos headers, usando o método `POST`.

> Exemplos de retorno:

> Para um anúncio inserido ou editado, que foi para processo de validação:

```json
{
  "autoupload_status": "pending",
  "ads": [
    {
      "id": "1111111111",
      "status": "queued",
      "operation": "insert",
      "list_id": "123456789",
      "message": []
    }
  ]
}
```

> Para um anúncio inserido com sucesso:

```json
{
  "autoupload_status": "done",
  "ads": [
    {
      "id": "1111111111",
      "status": "accepted",
      "operation": "insert",
      "message": [],
      "list_id": "123456789",
      "url": "http://www.eupreciso.com.br/santa-catarina/norte-de-santa-catarina/autos-e-pecas/anuncio/carro-novo-123456789"
    }
  ]
}
```

> Para um anúncio editado com sucesso:

```json
{
  "autoupload_status": "done",
  "ads": [
    {
      "id": "1111111111",
      "status": "accepted",
      "operation": "edit",
      "message": [],
      "list_id": "123456789",
      "url": "http://www.eupreciso.com.br/santa-catarina/norte-de-santa-catarina/autos-e-pecas/anuncio/carro-novo-123456789"
    }
  ]
}
```

> Para um anúncio removido com sucesso:

```json
{
  "autoupload_status": "done",
  "ads": [
    {
      "id": "1111111111",
      "status": "accepted",
      "operation": "delete",
      "message": [],
      "list_id": "123456789"
    }
  ]
}
```

> Para um anúncio que teve uma remoção recusada:

```json
{
  "autoupload_status": "done",
  "ads": [
    {
      "id": "1111111111",
      "status": "refused",
      "operation": "delete",
      "message": [
        {
          "error": "ID_NOT_FOUND"
        }
      ]
    }
  ]
}
```

## Retorno Esperado

O formato do retorno de nosso servidor será do tipo JSON, que contém a seguinte estrutura:

| Parâmetro           | Valores           | Descrição                                                                                                                                                      |
| ------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `autoupload_status` | `done`, `pending` | Retorna o status dos anúncios. `done`: todos os anúncios foram processados (inseridos ou deletados), `pending`: Pelo menos um anúncio ainda está em validação. |
| `ads`               | array[]           | Este parâmetro detalha o resultado de publicação de determinado anúncio, seguindo a descrição da tabela abaixo.                                                |

A tabela abaixo detalha o retorno do parâmetro ads, que de fato explica o que houve com o anúncio em si:

| Parâmetro   | Valores                                                                                                                                                                                                                                                                                                          | Descrição                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `status`    | <p>pending, error, queued, accepted, extra, refused</p>                                                                                                                                                                                                                                                          | Retorna o status dos anúncios. <br> - `pending`: o anúncio será processado. <br> - `error`: o anúncio está com erro(s). <br> - `queued`: o anúncio foi inserido e será ativado em breve, pois está em processo de validação. <br> - `accepted`: o anúncio foi ativado. Caso a operação seja de deleção, significa que o anúncio foi deletado. <br> - `refused`: o anúncio não foi ativado. |
| `operation` | <p>insert, delete</p>                                                                                                                                                                                                                                                                                            | Operação utilizada <br> - `insert`: inserção de anúncio <br> - `delete`: deleção de anúncio                                                                                                                                                                                                                                                                                                |
| `message`   | <p>Exemplos: </p><p>ERROR_IMAGE_TOO_SMALL,</p><p>ERROR_DOWNLOADING_IMAGE,</p><p>ERROR_UPLOADING_IMAGE,</p><p>NOT_ENOUGH_AD_SLOTS,</p><p>REFUSED_SUSPECT_CATEGORY,</p><p>REFUSED_SUSPECT_REGION,</p><p>REFUSED_DENOUNCE,</p><p>REFUSED_SUSPECT_AUTOS,</p><p>REFUSED_SUSPECT_DUPLICATES,</p><p>REFUSED_GENERIC</p> | Mensagens de aviso sobre erros ocorridos                                                                                                                                                                                                                                                                                                                                                   |
| `list_id`   | string                                                                                                                                                                                                                                                                                                           | Retorna o id do seu anúncio, caso ele tenha sido aprovado                                                                                                                                                                                                                                                                                                                                  |
| `url`       | string                                                                                                                                                                                                                                                                                                           | URL do anúncio gerada no Eu Preciso                                                                                                                                                                                                                                                                                                                                                        |

# Consulta de Anúncios Publicados

> Exemplo de retorno:

```json
[
  {
    "id": "55555555555555",
    "list_id": "6547d585616b107143a26488"
  }
]
```

Durante a importação, os anúncios publicados por integradores são automaticamente validados e publicados, com exceção dos anúncios com erros de definição de região, que serão corrigidos no processo de validação.

Esta consulta retorna todos os anúncios que já foram processados e estão ativos, ou seja, disponíveis para visualização em nosso site por qualquer usuário do Eu Preciso.

Nosso servidor deve receber a requisição com método do tipo POST e com um JSON no corpo da requisição.

Exemplo de chamada para a URL <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/published`</span>, sempre com o `access_token` incluso no headers da requisição.

O retorno da chamada trará o identificador usado pelo integrador/anunciante na inserção do anúncio e outro identificador do anúncio no Eu Preciso. Caso o anunciante não tenha anúncios ativos, será retornado um array vazio.

| Parâmetro | Valores                                     | Descrição                                      |
| --------- | ------------------------------------------- | ---------------------------------------------- |
| `id`      | Regular expression: `[A-Za-z0-9_{}-]{1,19}` | Identificador do anúncio definido pelo usuário |
| `list_id` | string                                      | ID do anúncio no Eu Preciso                    |

# Aplicação e Consulta de Destaques em Anúncios

Com o passar do tempo, um anúncio publicado no portal descerá algumas posições, seguindo uma ordenação natural de inserções. Para que o anúncio volte ao topo, oferecemos os destaques que são benefícios para que o anúncio ganhe mais visibilidade no Eu Preciso. Assim, seus anúncios podem voltar aos primeiros resultados como se fosse um anúncio novo e de forma destacada.

A requisição feita para esta URL deve conter o access_token de cada anunciante nos headers, usando o método `POST`.

Se o anunciante possui os Recursos PRO ativos e o destaque for aplicado, a requisição retorna um `status code 200` e um JSON no corpo da resposta com a estrutura abaixo.

## Aplicação de Destaque em Anúncios

A URL usada para fazer a requisição do arquivo JSON é <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/bump/ad/{ad_id}`</span>, método `POST`.

### Retorno de sucesso esperado

| Parâmetro    | Valores                        | Obrigatório | Descrição                                |
| ------------ | ------------------------------ | ----------- | ---------------------------------------- |
| `id`         | string                         | Sim         | Identificador do anúncio.                |
| `list_id`    | string                         | Não         | Identificador do anúncio no Eu Preciso.  |
| `date`       | string (ISO Datetime)          | Sim         | Data do bump.                            |
| `value`      | integer                        | Não         | Valor pago pelo destaque.                |
| `days`       | integer                        | Não         | Dias contratados do destaque.            |
| `last_bumps` | arrayOf[string (ISO Datetime)] | Não         | Últimas datas que o anúncio foi ao topo. |

Mesmo se um destaque estiver ativo, o sistema aceitará a solicitação e adicionará mais 7 dias ao destaque vigente.

### Retorno de erro esperado

Caso ocorra algum erro ou o anunciante não possua os Recursos PRO ativo, a consulta retorna um `status code > 400` e um JSON com o motivo e a mensagem do erro.

### Códigos e motivos de erros da requisição retornados

| Status Code | Descrição                                                                                 | Motivo                    | Mensagem                                                        |
| ----------- | ----------------------------------------------------------------------------------------- | ------------------------- | --------------------------------------------------------------- |
| 400         | Falta campo de authorization no header da requisição                                      | BAD_REQUEST               | Check the header field(s)                                       |
| 401         | Token inválido                                                                            | ACCESS_DENIED             | Check the client authentication token                           |
| 403         | Cliente não tem saldo disponível para aplicar o destaque                                  | FORBIDDEN                 | `{ "reason": "FORBIDDEN", "message": "Insufficient balance." }` |
| 404         | Anúncio não encontrado                                                                    | NOT_FOUND                 | `{ "reason": "NOT_FOUND", "message": "Ad not found." }`         |
| 429         | Rate Limit configurado quando o cliente fizer mais requisições por segundo do que deveria | RATE_LIMIT                | You have exceeded the X requests in X seconds limit!            |
| 500         | Erro interno inesperado                                                                   | UNEXPECTED_INTERNAL_ERROR | Unexpected internal error. Try again later                      |

> Exemplos de Retorno

> Ao aplicar destaque, o anúncio terá sua visibilidade aumentada indo para o topo da listagem de anúncios no período de 7 dias.

> Request

```shell
curl --location --request POST 'https://apps.eupreciso.com.br/v1.0/integradores/autoupload/bump/ad/456789123' \
--header 'Authorization: Bearer 5/4PXLrAv2gLZ6dqokH6xB' \
--header 'Content-Type: application/json' \
```

> Response

```json
{
  "status": "accepted",
  "bump_applied": {
    "id": "123456789123",
    "list_id": "5f9d7b7b9c9a9b0b3c9a9b0b",
    "date": "2023-12-04 00:00:00.00000",
    "value": 5,
    "days": 7,
    "lastBumps": [
      { "date": "2023-11-15 00:00:00.00000", "value": 5, "days": 7 }
    ]
  }
}
```

## Consulta de anúncios destacados

A URL usada para fazer a requisição do arquivo JSON é <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/bump/ads`</span>, método `POST`. Essa requisição deve conter em seu body `ad_ids` em formato JSON uma lista de até 10 identificadores de anúncios.

A requisição feita para esta URL deve conter o access_token de cada anunciante nos headers, usando o método `POST`.

```json
{
  "ad_ids": ["12345", "67891"]
}
```

| BODY    | Valores       | Obrigatório | Descrição                                                                                                                                              |
| ------- | ------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `ad_id` | array[string] | Sim         | Lista de até 10 identificadores de anúncios. <br><br> **Obs:** se for enviada uma lista com mais de 10 identificadores, o 11º em diante será ignorado. |

Se o anunciante possui anúncios destacados, a requisição retorna um `status code 200` e um JSON no corpo da resposta com a estrutura abaixo, podendo vir uma lista com até 10 itens.

### Retorno de sucesso esperado

| Parâmetro    | Valores                        | Obrigatório | Descrição                                                          |
| ------------ | ------------------------------ | ----------- | ------------------------------------------------------------------ |
| `id`         | string                         | Sim         | Identificador do anúncio.                                          |
| `list_id`    | string                         | Não         | Identificador do anúncio no Eu Preciso.                            |
| `date`       | string (ISO Datetime)          | Sim         | Data do bump.                                                      |
| `value`      | integer                        | Não         | Valor pago pelo destaque.                                          |
| `days`       | integer                        | Não         | Dias contratados do destaque.                                      |
| `last_bumps` | arrayOf[string (ISO Datetime)] | Não         | Últimas datas que o anúncio foi ao topo.                           |
| `reason`     | string                         | Não         | Motivo do erro que ocorreu ao obter dados do anúncio específico.   |
| `message`    | string                         | Não         | Mensagem de erro que ocorreu ao obter dados do anúncio específico. |

### Retorno de erro esperado

Caso ocorra algum erro ou o anunciante não possua os Recursos PRO ativo, a consulta retorna um `status code > 400` e um JSON com o motivo e a mensagem do erro.

| Status Code | Descrição                                                                                 | Motivo                    | Mensagem                                             |
| ----------- | ----------------------------------------------------------------------------------------- | ------------------------- | ---------------------------------------------------- |
| 400         | Falta campo de authorization no header da requisição                                      | BAD_REQUEST               | Check the header field(s)                            |
| 401         | Token inválido                                                                            | ACCESS_DENIED             | Check the client authentication token                |
| 429         | Rate Limit configurado quando o cliente fizer mais requisições por segundo do que deveria | RATE_LIMIT                | You have exceeded the X requests in X seconds limit! |
| 500         | Erro interno inesperado                                                                   | UNEXPECTED_INTERNAL_ERROR | Unexpected internal error. Try again later           |

### Códigos e motivos de erros da requisição retornados

| Anúncio | Condição do anúncio em relação a destaque                                                                                 |
| ------- | ------------------------------------------------------------------------------------------------------------------------- |
| B124    | Todos os destaques aplicados e destaque ativo, ou seja, no período de dias contratados a partir da aplicação do destaque. |
| D1234   | Anúncio com histórico de destaques aplicados, mas sem destaque ativo.                                                     |
| S995    | Sem destaque aplicado. Retornará "Anúncio não encontrado".                                                                |
| E4567   | Ocorreu uma indisponibilidade no momento da consulta deste anúncio. Por favor, tente mais tarde.                          |

> Exemplos de Retorno

> Consulta de 4 anúncios em situações diferentes, conforme condições abaixo:

> Request

```shell
curl --location --request POST 'https://apps.eupreciso.com.br/v1.0/integradores/autoupload/bump/ads' \
--header 'Authorization: Bearer 5/4PXLrAv2gLZ6dqokH6xB' \
--header 'Content-Type: application/json' \
--data-raw '{
"ad_ids": ["123456789123", “456789123”, “9874561”, “222222222”]
}'
```

> Response

```json
{
  "B124": [
    {
      "id": "123456789123",
      "list_id": "5f9d7b7b9c9a9b0b3c9a9b0b",
      "date": "2023-12-01 00:00:00.00000",
      "value": 5,
      "days": 7,
      "lastBumps": [
        { "date": "2023-11-15 00:00:00.00000", "value": 5, "days": 7 },
        { "date": "2023-11-01 00:00:00.00000", "value": 5, "days": 7 }
      ]
    }
  ],
  "D1234": [
    {
      "id": "456789123",
      "list_id": "3d9d7b7b9c9a9b0b3c9o1x7l",
      "lastBumps": [
        { "date": "2023-11-10 00:00:00.00000", "value": 5, "days": 7 },
        { "date": "2023-11-02 00:00:00.00000", "value": 5, "days": 7 }
      ]
    }
  ],
  "S995": [
    {
      "id": "9874561",
      "reason": "NOT_FOUND",
      "message": "Ad not found."
    }
  ],
  "E4567": [
    {
      "id": "222222222",
      "reason": "UNPROCESSABLE_AD",
      "message": "I couldn't get information for this ad. Please try again later"
    }
  ]
}
```

# Consulta de Saldos, Gastos e Renovações

A API abaixo disponibiliza informações para acompanhamento da quantidade de inserções que já foram realizadas, o saldo disponível para inserções de novos anúncios e as datas de renovação dos anúncios.

## Requisição de consulta de saldo e limite

A URL usada para fazer a requisição do arquivo JSON é <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/balance`</span>, método `POST`. Essa requisição deve conter o `access_token` de cada anunciante no header como: Authorization: Bearer `<access_token>`.

### Retorno de sucesso esperado

A consulta deve retornar `um status code 200` e um JSON no corpo da resposta com a estrutura:

| Parâmetro   | Valores | Obrigatório | Descrição                                                                                       |
| ----------- | ------- | ----------- | ----------------------------------------------------------------------------------------------- |
| `reference` | string  | Sim         | Data de referência (MM/AAAA)                                                                    |
| `ads`       | object  | Sim         | Estrutura contendo quantidade de inserções e destaques do cliente, bem como respectivos gastos. |
| `balance`   | object  | Sim         | Estrutura contendo quantidade de créditos, balanço e saldo bloqueado do usuário.                |

<p style="font-weight:bold">ADS</p>

| Parâmetro       | Valores | Obrigatório | Descrição                           |
| --------------- | ------- | ----------- | ----------------------------------- |
| `performed`     | integer | Sim         | Quantidade de anúncios publicados   |
| `adsAmount`     | integer | Sim         | Valor gasto com inserções           |
| `bumpsPerfomed` | integer | Sim         | Quantidade de destaques contratados |
| `bumpsAmount`   | integer | Sim         | Valor gasto com destaques           |

<p style="font-weight:bold">BALANCE</p>

| Parâmetro        | Valores | Obrigatório | Descrição                                                       |
| ---------------- | ------- | ----------- | --------------------------------------------------------------- |
| `credits`        | integer | Sim         | Saldo de créditos disponível para uso                           |
| `balance`        | integer | Sim         | Saldo em reais disponível para saque ou uso no site             |
| `blockedBalance` | Integer | Sim         | Saldo em reais bloqueado, referente a comissões pagas pelo site |
| `hasCreditCard`  | boolean | Sim         | Se o anunciante tem cartão de crédito cadastrado                |
| `limit`          | integer | Sim         | Limite mensal de gastos para fins de alerta                     |

### Retorno de erro esperado

Caso ocorra algum erro, a consulta retorna um `status code > 200` e um JSON com o motivo e a mensagem do erro.

### Códigos e motivos de erros da requisição retornados

| Status Code | Descrição                                                                                 | Motivo                    | Mensagem                                             |
| ----------- | ----------------------------------------------------------------------------------------- | ------------------------- | ---------------------------------------------------- |
| 400         | Falta campo de authorization no header da requisição                                      | BAD_REQUEST               | Check the header field(s)                            |
| 401         | Token inválido                                                                            | ACCESS_DENIED             | Check the client authentication token                |
| 429         | Rate Limit configurado quando o cliente fizer mais requisições por segundo do que deveria | RATE_LIMIT                | You have exceeded the X requests in X seconds limit! |
| 500         | Erro interno inesperado                                                                   | UNEXPECTED_INTERNAL_ERROR | Unexpected internal error. Try again later           |

> Exemplo de Retorno

> Requisição: https://apps.eupreciso.com.br/v1.0/integradores/autoupload/balance

```json
{
  "reference": "12/2023",
  "ads": {
    "performed": 1,
    "adsAmount": 0.95,
    "bumpsPerformed": 1,
    "bumpsAmount": 5
  },
  "balance": {
    "credits": 985.81,
    "balance": 294,
    "blockedBalance": 0.25,
    "hasCreditCard": false,
    "limit": 50
  }
}
```

## Requisição de consulta de renovações

A URL usada para fazer a requisição do arquivo JSON é <span style="font-weight:bold">`https://apps.eupreciso.com.br/v1.0/integradores/autoupload/renews`</span>, método `POST`. Essa requisição deve conter o `access_token` de cada anunciante no header como: Authorization: `Bearer <access_token>`.

### Retorno de sucesso esperado

A consulta deve retornar `um status code 200` e um JSON no corpo da resposta com a estrutura:

| Parâmetro   | Valores | Obrigatório | Descrição                                                         |
| ----------- | ------- | ----------- | ----------------------------------------------------------------- |
| `reference` | string  | Sim         | Data de referência (MM/AAAA)                                      |
| `ads`       | object  | Sim         | Estrutura contendo o ID do anúncio, data de renovação automática. |

<p style="font-weight:bold">ADS</p>

| Parâmetro | Valores               | Obrigatório | Descrição                              |
| --------- | --------------------- | ----------- | -------------------------------------- |
| `id`      | string                | Não         | Identificador do anúncio<sup>1</sup>.  |
| `list_id` | string                | Não         | Identificador do anúncio no Eu Preciso |
| `date`    | string (ISO Datetime) | Sim         | Data da renovação                      |

1 – Se o campo id não estiver presente, significa que o anúncio foi inserido manualmente pelo usuário na plataforma.

### Retorno de erro esperado

Caso ocorra algum erro, a consulta retorna um `status code > 200` e um JSON com o motivo e a mensagem do erro.

### Códigos e motivos de erros da requisição retornados

| Status Code | Descrição                                                                                 | Motivo                    | Mensagem                                             |
| ----------- | ----------------------------------------------------------------------------------------- | ------------------------- | ---------------------------------------------------- |
| 400         | Falta campo de authorization no header da requisição                                      | BAD_REQUEST               | Check the header field(s)                            |
| 401         | Token inválido                                                                            | ACCESS_DENIED             | Check the client authentication token                |
| 429         | Rate Limit configurado quando o cliente fizer mais requisições por segundo do que deveria | RATE_LIMIT                | You have exceeded the X requests in X seconds limit! |
| 500         | Erro interno inesperado                                                                   | UNEXPECTED_INTERNAL_ERROR | Unexpected internal error. Try again later           |

> Exemplo de Retorno

> Requisição: https://apps.eupreciso.com.br/v1.0/integradores/autoupload/renews

```json
[
  {
    "reference": "12/2023",
    "ads": [
      {
        "id": "123456789123",
        "list_id": "656cb3180b8aabb0ffe61856",
        "date": "2023-12-04T01:57:31.806Z"
      }
    ]
  },
  {
    "reference": "01/2024",
    "ads": [
      {
        "list_id": "648ba28c637bed93add5d359",
        "date": "2024-01-03T03:02:55.159Z"
      }
    ]
  },
  {
    "reference": "02/2024",
    "ads": [
      {
        "list_id": "656520bb0083e826147be25b",
        "date": "2024-02-25T23:05:33.501Z"
      },
      {
        "list_id": "65661bc390802a34a04dd46a",
        "date": "2024-02-26T16:56:37.804Z"
      }
    ]
  },
  {
    "reference": "03/2024",
    "ads": [
      {
        "list_id": "649dd532400912b6d430b724",
        "date": "2024-03-01T22:30:54.221Z"
      },
      {
        "list_id": "63dc46188a20792c14eca667",
        "date": "2024-03-15T04:57:58.477Z"
      }
    ]
  }
]
```

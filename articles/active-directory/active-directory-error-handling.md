<properties
	pageTitle="Tratamento de erro no OAuth 2.0 | Microsoft Azure"
	description="Este artigo descreve as classes comuns de erros no OAuth 2.0 com o Azure Active Directory e as práticas recomendadas para tratá-los."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Tratamento de erro no OAuth 2.0

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Neste artigo, veremos algumas práticas recomendadas para tratamento de classes comuns de erros que você pode encontrar ao autorizar o aplicativo com o Azure AD (Azure Active Directory). Para saber mais sobre o ponto de extremidade de autorização e o ponto de extremidade de emissão de token, confira [Fluxo de autenticação para um aplicativo no Azure AD](active-directory-protocols-oauth-code.md).

## Erros de ponto de extremidade de autorização

Na primeira etapa do processo de autorização, o aplicativo cliente envia uma solicitação ao ponto de extremidade`/authorize` do Azure AD, com a lista de permissões que precisa obter do usuário.

Os erros de ponto de extremidade de autorização se originam no ponto de extremidade `/authorize`. Eles são retornados em como erros HTTP 200 em uma página da Web ou usando um código de redirecionamento HTTP 302 quando um aplicativo cliente está disponível para tratar o erro.

Aqui está um exemplo de resposta de erro HTTP 302 do ponto de extremidade de autorização do Azure AD, quando uma solicitação não tem o parâmetro `response_type` necessário.

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
|-----------|-------------|
| error | Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](http://tools.ietf.org/html/rfc6749). A tabela a seguir descreve os códigos de erro retornados pelo Azure AD. |
| error\_description | Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o usuário final. |
| state | O valor de estado é um valor não reutilizado gerado aleatoriamente que é enviado na solicitação e retornado na resposta para evitar ataques de CSRF (solicitação intersite forjada). |

### Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código do Erro | Descrição | Ação do Cliente |
|------------|-------------|---------------|
| invalid\_request | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais.|
| unauthorized\_client | O aplicativo cliente não tem permissão para solicitar um código de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access\_denied | Consentimento negado pelo proprietário do recurso | O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported\_response\_type | O servidor de autorização não dá suporta ao tipo de resposta na solicitação. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais.|
|server\_error | O servidor encontrou um erro inesperado. | Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily\_unavailable | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid\_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente.| Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## Erros de ponto de extremidade de emissão de token

Depois que o aplicativo recebe um código de autorização do ponto de extremidade `/authorize`, passa-o ao ponto de extremidade `/token` para receber um token de acesso para o recurso especificado.

Os erros de ponto de extremidade de emissão de token se originam do ponto de extremidade `/token`. Esses são códigos de erro HTTP, pois o cliente chama o ponto de extremidade de emissão de token diretamente. Além do código de status HTTP, o ponto de extremidade de emissão de token do Azure AD também retorna um documento JSON com objetos que descrevem o erro.

Por exemplo, um erro se o parâmetro `client_id` na solicitação é inválido pode ser semelhante a:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### Códigos de status HTTP

A tabela a seguir lista os códigos de status HTTP que o ponto de extremidade de emissão de token retorna. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas, no caso de erros, você precisará analisar o documento JSON e correspondente e examinar seu código de erro.

| Código HTTP | Descrição |
|-----------|-------------|
| 400 | Código HTTP padrão. Usado na maioria dos casos e, normalmente, é devido a uma solicitação malformada. Corrija e reenvie a solicitação. |
| 401 | Falha na autenticação. Por exemplo, a solicitação não tem o parâmetro client\_secret.|
| 403 | Falha na autorização. Por exemplo, o usuário não tem permissão para acessar o recurso. |
| 500 | Erro interno no serviço. Tente novamente a solicitação. |

### Objetos de documento JSON na resposta de erro

| Objeto JSON | Descrição |
|-------------|-------------|
| error | Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](http://tools.ietf.org/html/rfc6749). A tabela a seguir descreve os códigos de erro retornados pelo Azure AD. |
| error\_description | Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o usuário final. |
| timestamp | A data e hora em que o erro ocorreu, em UTC (Horário Coordenado Universal). |
| trace\_id | Uma ID que identifica o rastreamento de erros. |
| correlation\_id | 	Um valor gerado pelo cliente. Essa ID é incluída no documento de erro JSON quando a solicitação ao ponto de extremidade de emissão de token inclui esse valor no cabeçalho `client-request-id`. A ID pode ser usada por outras chamadas na mesma sessão. |
| error\_codes | Contém uma lista de códigos de erro que são mapeados para diferentes condições do serviço. Não use esses códigos na lógica do aplicativo. No entanto, você pode usá-los para diagnosticar um problema e pesquisar informações online pelo código de erro. |

### Valores de código de erro de documento JSON

| Código do Erro | Descrição | Ação do Cliente |
|------------|-------------|---------------|
| invalid\_request | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação |
| invalid\_grant | A concessão de autorização (ou seus parâmetros) ou o token de atualização são inválidos, expiraram ou foram revogados. | Corrija e reenvie a solicitação. |
| unauthorized\_client | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| invalid\_client | Falha na autenticação de cliente. | As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais. |
| unsupported\_grant\_type | O servidor de autorização não dá suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| invalid\_resource | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| interaction\_required | A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. | Repita a solicitação com o mesmo recurso. |
| temporarily\_unavailable | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária.|

## Erros de recursos protegidos

Esses são os erros que podem ser retornados por um recurso protegido, como uma API Web, se ele implementa a especificação [RFC 6750](http://tools.ietf.org/html/rfc6750) da Estrutura de Autorização OAuth 2.0.

Recursos protegidos que implementam RFC 6750 emitem códigos de status HTTP. Se a solicitação não tem credenciais de autenticação ou o token está ausente, a resposta inclui um cabeçalho `WWW-Authenticate`. Quando uma solicitação falha, o servidor de recursos responde com um código de status HTTP e um código de erro.

Este é um exemplo de uma resposta malsucedida quando a solicitação do cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## Parâmetros de erro

| Parâmetro | Descrição |
|-----------|-------------|
| authorization\_uri | O URI (ponto de extremidade físico) do servidor de autorização. Esse valor também é usado como uma chave de pesquisa para obter mais informações sobre o servidor de um ponto de extremidade de descoberta. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso é protegido pelo Azure AD, é suficiente verificar se a URL começa com https://login.windows.net ou outro nome de host ao qual o Azure AD dá suporte. Um recurso específico de locatário sempre deve retornar um URI de autorização específico de locatário. |
| error | Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](http://tools.ietf.org/html/rfc6749).|
| error\_description | Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o usuário final.|
| resource\_id | Retorna o identificador exclusivo do recurso. O aplicativo cliente pode usar esse identificador como o valor do parâmetro `resource` ao solicitar um token para o recurso. <p><p> É muito importante que o aplicativo cliente verifique esse valor, caso contrário, um serviço mal-intencionado pode induzir um ataque de **elevação de privilégios** <p><p> A estratégia recomendada para evitar um ataque é verificar se o `resource_id` corresponde à base da URL da API Web que está sendo acessada. Por exemplo, se https://service.contoso.com/data está sendo acessado, o `resource_id` pode ser htttps://service.contoso.com/. O aplicativo cliente deve rejeitar um `resource_id` que não começa com a URL base, a menos que haja uma maneira alternativa confiável de verificar a id. |

## Códigos de erro de esquema de portador

A especificação RFC 6750 define os erros a seguir para recursos que usam o cabeçalho WWW-Authenticate e o esquema de Portador na resposta.

| Código de status HTTP | Código do Erro | Descrição | Ação do Cliente |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | A solicitação não está bem formada. Por exemplo, pode estar sem um parâmetro ou usando o mesmo parâmetro duas vezes. | Corrija o erro e repita a solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e deve ser detectado nos testes iniciais. |
| 401 | invalid\_token | O token de acesso está ausente, é inválido ou foi revogado. O valor do parâmetro error\_description fornece detalhes adicionais. | Solicite um novo token do servidor de autorização. Se o novo token falhar, isso indicará que ocorreu um erro inesperado. Envie uma mensagem de erro ao usuário e tente novamente após intervalos aleatórios. |
| 403 | insufficient\_scope | O token de acesso não tem as permissões de representação necessárias para acessar o recurso. | Envie uma nova solicitação de autorização ao ponto de extremidade de autorização. Se a resposta contiver o parâmetro de escopo, use o valor de escopo na solicitação para o recurso. |
| 403 | insufficient\_access | A entidade do token não tem as permissões necessárias para acessar o recurso. | Solicite que o usuário use uma conta diferente ou solicite permissões para o recurso especificado. |

<!---HONumber=AcomDC_0608_2016-->
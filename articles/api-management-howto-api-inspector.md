<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure

O Gerenciamento de API (em versão de visualização) oferece uma ferramenta de Inspetor de API para ajudá-lo a depurar e solucionar problemas de suas APIs. O Inspetor de API pode ser utilizado de forma programática por meio de seus aplicativos e pode também ser utilizado diretamente a partir do portal do desenvolvedor. Este guia fornece uma explicação da utilização do Inspetor de API.

## Neste tópico

-   [Usar o Inspetor de API para rastrear uma chamada][Usar o Inspetor de API para rastrear uma chamada]
-   [Inspecionar o rastreamento][Inspecionar o rastreamento]
-   [Próximas etapas][Próximas etapas]

## <a name="trace-call"> </a> Usar o Inspetor de API para rastrear uma chamada

Para usar o Inspetor de API, adicione um cabeçalho de solicitação **ocp-apim-trace: true** à chamada de operação, baixe e inspecione o rastreamento utilizando a URL indicada pelo cabeçalho de resposta **ocp-apim-trace-location**. Isso pode ser feito de forma programática ou diretamente a partir do portal do desenvolvedor.

Este tutorial mostra como usar o Inspetor de API para rastrear operações usando a API de ECO.

> Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. A API de ECO retorna qualquer entrada enviada a ela. Para usá-la, você pode invocar qualquer verbo HTTP e o valor retornado será simplesmente o que você tiver enviado.

Para começar, clique em **Portal de Desenvolvimento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Portal do desenvolvedor do Gerenciamento da API][Portal do desenvolvedor do Gerenciamento da API]

As operações podem ser chamadas diretamente do portal do desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do tutorial, você chamará o método de **Recurso GET** da **API de ECO**.

Clique em **APIs** no menu superior e depois clique em **API de ECO**.

![API de ECO][API de ECO]

> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.

Selecione a operação **Recurso GET** e clique em **Abri Console**.

![Abrir console][Abrir console]

Mantenha os valores de parâmetros padrão e selecione a chave de assinatura no menu suspenso **subscription-key**.

Digite **ocp-apim-trace: true** na caixa de texto **Cabeçalhos de solicitação** e clique em **HTTP Get**.

![HTTP Get][HTTP Get]

Nos cabeçalhos de resposta, haverá um **ocp-apim-trace-location** com um valor semelhante ao exemplo a seguir.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

O rastreamento pode ser baixado do local especificado e revisado, conforme demonstrado na etapa a seguir.

## <a name="inspect-trace"> </a>Inspecionar o rastreamento

Para revisar os valores no rastreamento, baixe o arquivo de rastreamento da URL **ocp-apim-trace-location**. Trata-se de um texto no formato JSON que contém entradas semelhantes ao exemplo a seguir.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Próximas etapas

-   Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][Introdução à configuração avançada de API].

  [Usar o Inspetor de API para rastrear uma chamada]: #trace-call
  [Inspecionar o rastreamento]: #inspect-trace
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Portal do desenvolvedor do Gerenciamento da API]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [API de ECO]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Abrir console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Introdução à configuração avançada de API]: ../api-management-get-started-advanced

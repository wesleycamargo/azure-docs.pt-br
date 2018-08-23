---
title: Equipe de desenvolvimento com Azure Dev Spaces usando .NET Core e VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 6b97baac6bb27166581c1dc6312e112a156ed6c3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920658"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em Equipe com Azure Dev Spaces

Neste tutorial, você aprenderá a usar vários espaços de desenvolvimento para trabalhar simultaneamente em ambientes de desenvolvimento diferentes, tendo o trabalho separado em espaços de desenvolvimento diferentes no mesmo cluster.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução em um contêiner separado

Nesta seção, você cria um segundo serviço, `mywebapi` e faz com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![Vários contêineres](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Baixar código de exemplo para *mywebapi*
Por economizar tempo, vamos baixar o código de exemplo de um repositório do GitHub. Vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub. O código para essa seção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Execute *mywebapi*
1. Abra a pasta `mywebapi` em uma *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto quando a barra de depuração do VS Code for exibida.
1. A URL de ponto de extremidade será algo parecido com http://localhost:\<portnumber\>. **Dica: A barra de status do VS Code exibirá uma URL clicável.** Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução no nosso espaço de desenvolvimento no Azure. O motivo para o endereço do localhost é porque `mywebapi` não definiu nenhum ponto de extremidade público e somente pode ser acessado de dentro da instância de Kubernetes. Para sua conveniência e para facilitar a interação com o serviço privado em sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contêiner em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra seu navegador para o endereço do localhost. Acrescente `/api/values` na URL para invocar a API GET padrão para `ValuesController`. 
1. Se todas as etapas foram bem-sucedidas, você poderá ver uma resposta do serviço `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`.
1. Alterne para a janela do VS Code para `webfrontend`.
1. *Substitua* o código pelo método Sobre:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá posteriormente como isso ajuda as equipes de desenvolvimento colaborativas.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Definir um ponto de interrupção no método `Get(int id)` que manipula solicitações GET `api/values/{id}`.
1. No projeto `webfrontend`, defina um ponto de interrupção antes que ele envie uma solicitação GET para `mywebapi/api/values`.
1. Pressione F5 no projeto `webfrontend`.
1. Invoque o aplicativo Web e percorra o código em ambos os serviços.
1. No aplicativo Web, a página Sobre exibirá uma mensagem concatenada pelos dois serviços: "Olá de webfrontend e Olá de mywebapi".


Muito bem! Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

[!INCLUDE[](includes/team-development-1.md)]

Vamos ver isso tudo em ação. Vá para a janela do VS Code para `mywebapi` e faça uma edição de código no método `string Get(int id)`, por exemplo:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```


[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

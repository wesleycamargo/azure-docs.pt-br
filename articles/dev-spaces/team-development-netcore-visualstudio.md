---
title: Equipe de desenvolvimento para Azure Dev Spaces usando .NET Core e Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 1b603db552edd14dab2c86e6b005184eb88e74bb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42154808"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em Equipe com Azure Dev Spaces

Neste tutorial, você aprenderá a usar vários espaços de desenvolvimento para trabalhar simultaneamente em ambientes de desenvolvimento diferentes, tendo o trabalho separado em espaços de desenvolvimento diferentes no mesmo cluster.

## <a name="call-another-container"></a>Chamar outro contêiner
Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Baixar código de exemplo para *mywebapi*
Por economizar tempo, vamos baixar o código de exemplo de um repositório do GitHub. Vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub. O código para essa seção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra o projeto `mywebapi` em uma *janela separada do Visual Studio*.
1. Selecione **Azure Dev Spaces** na lista suspensa de configurações de inicialização, conforme feito anteriormente no projeto `webfrontend`. Em vez de criar um novo cluster AKS desta vez, selecione o mesmo que você já criou. Assim como antes, deixe o espaço com o padrão `default` e clique em **OK**. Na janela de Saída, pode ser que você note que o Visual Studio inicia para “aquecer” esse novo serviço em seu espaço de desenvolvimento para acelerar as coisas ao iniciar a depuração.
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto quando a barra de status do Visual Studio ficar laranja
1. Anote o ponto de extremidade de URL exibido no painel **Azure Dev Spaces for AKS** na janela de **Saída**. Será semelhante a http://localhost:\<portnumber\>. Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução no espaço de desenvolvimento no Azure.
2. Quando `mywebapi` estiver pronto, abra seu navegador no endereço do localhost e acrescente `/api/values` à URL para invocar a API GET padrão para `ValuesController`. 
3. Se todas as etapas foram bem-sucedidas, você deverá ver uma resposta do serviço `mywebapi` que se parece com isto.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`. Alterne para a janela do Visual Studio que tem o projeto `webfrontend`. No arquivo `HomeController.cs`, *substitua* o código pelo método Sobre com o seguinte código:

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

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá mais tarde como isso facilita uma experiência de desenvolvimento mais produtiva em cenários de equipe.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no método `Get(int id)`, no arquivo `Controllers/ValuesController.cs`, que manipula solicitações GET `api/values/{id}`.
1. No projeto `webfrontend`, no qual você colou o código acima, defina um ponto de interrupção antes que ele envie uma solicitação GET para `mywebapi/api/values`.
1. Pressione F5 no projeto `webfrontend`. Novamente, o Visual Studio abrirá um navegador para a porta localhost apropriada, e o aplicativo Web será exibido.
1. Clique no link “**Sobre**” na parte superior da página para disparar o ponto de interrupção no projeto `webfrontend`. 
1. Pressione F10 para continuar. Agora o ponto de interrupção no projeto `mywebapi` será disparado.
1. Pressione F5 para continuar e você estará novamente no código no projeto `webfrontend`.
1. Pressionar F5 mais uma vez concluirá a solicitação e retornará uma página no navegador. No aplicativo Web, a página Sobre exibirá uma mensagem concatenada pelos dois serviços: "Olá de webfrontend e Olá de mywebapi".

Muito bem! Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

Até agora, você executou seu código do aplicativo como se fosse o único desenvolvedor trabalhando no aplicativo. Nesta seção, você aprenderá como o Azure Dev Spaces simplifica o desenvolvimento em equipe:
* Habilite uma equipe de desenvolvedores para trabalhar no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme o necessário.
* Dá suporte a cada desenvolvedor iterando em seu código em isolamento e sem o receio de interromper os outros.
* Testa o código de ponta a ponta, antes da confirmação, sem a necessidade de criar simulações ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios com o desenvolvimento de microsserviços
O aplicativo de exemplo não é muito complexo no momento. Porém, no desenvolvimento do mundo real, os desafios logo surgem à medida que você adiciona mais serviços e a equipe de desenvolvimento cresce.

Imagine-se trabalhando em um serviço que interage com dezenas de outros serviços.

- A execução de tudo localmente para o desenvolvimento pode se tornar irrealista. Seu computador de desenvolvimento pode não ter recursos suficientes para executar o aplicativo inteiro. Ou talvez o aplicativo tenha pontos de extremidade que precisam ser acessíveis publicamente (por exemplo, o aplicativo responde a um webhook por meio de um aplicativo SaaS).
- Você pode tentar executar somente os serviços dos quais depende, mas isso significa que você precisará saber o fechamento completo de dependências (por exemplo, as dependências de dependências). Ou é uma questão de não saber com facilidade como criar e executar as dependências porque você não trabalhou nelas.
- Alguns desenvolvedores recorrem à simulação de muitas de suas dependências de serviço. Isso pode ser útil às vezes, mas o gerenciamento dessas simulações logo pode competir com seu próprio esforço de desenvolvimento. Além disso, isso faz com que seu ambiente de desenvolvimento pareça diferente à produção, e bugs sutis podem afetá-lo.
- Entende-se que fazer qualquer tipo de teste de ponta a ponta se torna difícil. Os testes de integração só podem ocorrer de maneira realista após uma confirmação, o que significa que você terá problemas mais tarde no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar em um espaço de desenvolvimento compartilhado
Com o Azure Dev Spaces, você pode configurar um espaço de desenvolvimento *compartilhado* no Azure. Cada desenvolvedor pode se concentrar exatamente em sua parte do aplicativo e pode desenvolver iterativamente um *código de pré-confirmação* em um espaço que já contém todos os outros serviços e recursos de nuvem dos quais seus cenários dependem. As dependências estão sempre atualizadas, e os desenvolvedores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar em seu próprio espaço
À medida que você desenvolve o código para seu serviço, e antes de estar pronto para fazer check-in nele, em geral, o código não estará em um bom estado. Você ainda o está moldando iterativamente, testando e experimentando soluções. O Azure Dev Spaces apresenta o conceito de um **espaço**, permitindo que você trabalhe em isolamento e sem o receio de interromper os membros de sua equipe.

Faça o seguinte para garantir que ambos os serviços `webfrontend` e `mywebapi` estejam em execução **no `default` espaço de desenvolvimento**.
1. Feche todas as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas janelas do Visual Studio.
2. Alterne para a janela do Visual Studio com o projeto `mywebapi` e pressione Ctrl+F5 para executar o serviço sem o depurador anexado
3. Alterne para a janela do Visual Studio com o projeto `webfrontend` e pressione Ctrl+F5 para executá-lo também.

> [!Note]
> Às vezes, é necessário atualizar seu navegador depois que a página da Web é exibida inicialmente, seguindo um Ctrl+F5.

Qualquer pessoa que abre a URL pública e navega para o aplicativo Web invocará o caminho do código escrito, o qual é executado em ambos os serviços usando o espaço `default` padrão. Agora suponha que você deseja continuar desenvolvendo `mywebapi`: como você pode fazer isso sem interromper outros desenvolvedores que estiverem usando o espaço de desenvolvimento? Para fazer isso, você configurará seu próprio espaço.

### <a name="create-a-new-dev-space"></a>Criar um novo espaço de desenvolvimento
De dentro do Visual Studio, é possível criar espaços adicionais que serão usados ao aplicar o F5 ou Ctrl+F5 em seu serviço. Você pode chamar de espaço qualquer coisa que deseje, e pode ser flexível sobre o que seu significado (por ex. `sprint4` ou `demo`).

Faça o seguinte para criar um novo espaço:
1. Alterne para a janela do Visual Studio com o projeto `mywebapi`.
2. Clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Propriedades**.
3. Selecione a guia **Depurar** à esquerda para mostrar as configurações do Azure Dev Spaces.
4. A partir daqui, você pode alterar ou criar o cluster e/ou o espaço que será usado ao aplicar F5 ou Ctrl+F5. *Verifique se o Azure Dev Space criado anteriormente está selecionado*.
5. No menu suspenso do Espaço, selecione **<Criar novo espaço…>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Na caixa de diálogo **Adicionar espaço**, defina o espaço pai para **default**e digite um nome para seu novo espaço. Você pode usar seu nome (por exemplo, “scott”) para o novo espaço para que seus colegas possam identificar em qual espaço você está trabalhando. Clique em **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Agora você deve ver seu cluster AKS e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código para *mywebapi*

1. No projeto `mywebapi`, faça uma alteração no código para o método `string Get(int id)` no arquivo `Controllers/ValuesController.cs` da seguinte maneira:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco atualizado de código (talvez você já tenha um conjunto anterior).
3. Pressione F5 para iniciar o serviço `mywebapi`. Isso iniciará o serviço em seu cluster usando o espaço selecionado, que nesse caso é `scott`.

Aqui está um diagrama que ajudará você a entender como os diferentes espaços funcionam. O caminho roxo mostra uma solicitação por meio do espaço `default`, que é o caminho padrão usado se nenhum espaço for acrescentado à URL. O caminho rosa mostra uma solicitação por meio do espaço `default/scott`.

![](media/common/Space-Routing.png)

Essa funcionalidade interna do Azure Dev Spaces permite que você teste o código de ponta a ponta em um ambiente compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige que cabeçalhos de propagação sejam encaminhados em seu código de aplicativo, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-running-in-the-defaultscott-space"></a>Testar o código em execução no espaço `default/scott`
Para testar a nova versão de `mywebapi` em conjunto com `webfrontend`, abra seu navegador na URL de ponto de acesso público de `webfrontend` (por exemplo, http://webfrontend.123456abcdef.eastus.aksapp.io) e acesse a página Sobre. Você deve ver a mensagem original “Hello from webfrontend and Hello from mywebapi”.

Agora, adicione a parte “scott.s.” à URL para que ela leia algo como http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io e atualize o navegador. O ponto de interrupção que você definiu em seu projeto `mywebapi` deve será atingido. Clique em F5 para continuar e, em seu navegador, agora você deve ver a nova mensagem “Hello from webfrontend and mywebapi now says something new”. Isso ocorre porque o caminho para seu código atualizado em `mywebapi` está em execução no espaço `default/scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

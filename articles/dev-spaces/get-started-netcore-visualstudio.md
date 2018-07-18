---
title: Criar um ambiente de desenvolvimento Kubernetes na nuvem usando o .NET Core e o Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 012efcbd3fa87268f3a68fdac524ce8310d10120
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362049"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Introdução ao Azure Dev Spaces com .NET Core e Visual Studio

Neste guia, você aprenderá a:

- Crie um ambiente baseado em Kubernetes no Azure otimizado para desenvolvimento.
- Desenvolva o código em contêineres iterativamente usando o Visual Studio.
- Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
- Desenvolva e teste o código produtivamente em um ambiente de equipe.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Obter as ferramentas do Visual Studio
1. Instalar a versão mais recente do [Visual Studio 2017](https://www.visualstudio.com/vs/)
1. No instalador do Visual Studio, verifique se a Carga de Trabalho abaixo está selecionada:
    * Desenvolvimento Web e ASP.NET
1. Instale a [extensão do Visual Studio para o Azure Dev Spaces](https://aka.ms/get-azds-visualstudio)

Agora você está pronto para criar um aplicativo Web ASP.NET com o Visual Studio.

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP .NET

No Visual Studio 2017, crie um novo projeto. No momento, o projeto deve ser um **aplicativo Web do ASP.NET Core**. Nomeie o projeto '**webfrontend**'.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selecione o modelo **aplicativo Web (Model-View-Controller)** e verifique se você está usando **.NET Core** e **ASP.NET Core 2.0** nas duas listas suspensas na parte superior do diálogo. Clique em **OK** para criar o projeto.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Criar um ambiente de desenvolvimento no Azure

Com o Azure Dev Spaces, você pode criar ambientes de desenvolvimento baseados em Kubernetes que são totalmente gerenciados pelo Azure e otimizados para desenvolvimento. Depois que o projeto recém-criado estiver aberto, selecione **Azure Dev Spaces** na lista suspensa de configurações de inicialização, conforme mostrado abaixo.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Na caixa de diálogo que é exibida em seguida, verifique se você está conectado à conta apropriada e selecione um cluster Kubernetes existente.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Deixe a lista suspensa **Espaço** como `default` por enquanto. Posteriormente, você aprenderá mais sobre essa opção. Marque a caixa de seleção **Publicamente Acessível** para que o aplicativo Web possa ser acessado por meio de um ponto de extremidade público. Essa configuração não é necessária, mas será útil para demonstrar alguns conceitos deste passo a passo. Não se preocupe: em ambos os casos, você conseguirá depurar o site usando o Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Clique em **OK** para selecionar ou criar o cluster.

Se você escolher um cluster que não foi habilitado para trabalhar com Azure Dev Spaces, verá uma mensagem perguntando se deseja configurá-lo.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Escolha **OK**.

 Uma tarefa em segundo plano será iniciada para isso. Ela levará alguns minutos para ser concluída. Para ver se ele ainda está sendo criado, passe o ponteiro sobre o ícone **Tarefas em segundo plano** no canto inferior esquerdo da barra de status, conforme mostrado na imagem a seguir.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Só será possível depurar seu aplicativo depois que o ambiente de desenvolvimento for criado com êxito.

## <a name="look-at-the-files-added-to-project"></a>Examinar os arquivos adicionados ao projeto
Enquanto aguarda a criação do ambiente de desenvolvimento, examine os arquivos que foram adicionados ao projeto quando optar por usar um ambiente de desenvolvimento.

Primeiro, você pode ver que uma pasta chamada `charts` foi adicionada e, dentro dessa pasta, foi feito scaffold de um [gráfico Helm](https://docs.helm.sh) para seu aplicativo. Esses arquivos são usados para implantar seu aplicativo no ambiente de desenvolvimento.

Você verá que um arquivo chamado `Dockerfile` foi adicionado. Esse arquivo tem as informações necessárias para empacotar o aplicativo no formato padrão do Docker. Um arquivo `HeaderPropagation.cs` também é criado; vamos discutir esse arquivo mais adiante no passo a passo. 

Por fim, você verá um arquivo chamado `azds.yaml`, que contém informações de configuração necessárias para o ambiente de desenvolvimento, por exemplo, se o aplicativo pode ser acessado por meio de um ponto de extremidade público.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contêiner no Kubernetes
Depois que o ambiente de desenvolvimento for criado com êxito, você poderá depurar o aplicativo. Defina um ponto de interrupção no código, por exemplo, na linha 20 no arquivo `HomeController.cs` onde a variável `Message` é definida. Clique em **F5** para iniciar a depuração. 

O Visual Studio se comunicará com o ambiente de desenvolvimento para criar e implantar o aplicativo e abrirá um navegador com o aplicativo Web em execução. Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução no ambiente de desenvolvimento no Azure. O motivo do endereço do localhost é que o Azure Dev Spaces cria um túnel SSH temporário para o contêiner em execução no Azure.

Clique no link **Sobre** na parte superior da página para disparar o ponto de interrupção. Você tem acesso completo às informações de depuração exatamente como teria se o código fosse executado localmente, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção e muito mais.

## <a name="call-another-container"></a>Chamar outro contêiner
Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

## <a name="download-sample-code-for-mywebapi"></a>Baixar código de exemplo para *mywebapi*
Por economizar tempo, vamos baixar o código de exemplo de um repositório do GitHub. Vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub. O código para essa seção está em `samples/dotnetcore/getting-started/mywebapi`.

## <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra o projeto `mywebapi` em uma *janela separada do Visual Studio*.
1. Selecione **Azure Dev Spaces** na lista suspensa de configurações de inicialização, conforme feito anteriormente no projeto `webfrontend`. Em vez de criar um novo ambiente de desenvolvimento desta vez, selecione o mesmo que você já criou. Assim como antes, deixe o espaço com o padrão `default` e clique em **OK**. Na janela de Saída, pode ser que você note que o Visual Studio inicia para “aquecer” esse novo serviço em seu ambiente de desenvolvimento para acelerar as coisas ao iniciar a depuração.
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto quando a barra de status do Visual Studio ficar laranja
1. Anote o ponto de extremidade de URL exibido no painel **Azure Dev Spaces for AKS** na janela de **Saída**. Será semelhante a http://localhost:\<portnumber\>. Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução no ambiente de desenvolvimento no Azure.
2. Quando `mywebapi` estiver pronto, abra seu navegador no endereço do localhost e acrescente `/api/values` à URL para invocar a API GET padrão para `ValuesController`. 
3. Se todas as etapas foram bem-sucedidas, você deverá ver uma resposta do serviço `mywebapi` que se parece com isto.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

## <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`. Alterne para a janela do Visual Studio que tem o projeto `webfrontend`. No arquivo `HomeController.cs`, *substitua* o código pelo método Sobre com o seguinte código:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
      // headers in the incoming request to any outgoing requests
      using (var client = new HeaderPropagatingHttpClient(this.Request))
      {
          // Call *mywebapi*, and display its response in the page
          var response = await client.GetAsync("http://mywebapi/api/values/1");
          ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
      }

      return View();
   }
   ```

Observe como a descoberta de serviço DNS dos Kubernetes é utilizada para referir-se ao serviço como `http://mywebapi`. **O código em seu ambiente de desenvolvimento está em execução da mesma forma que ele será executado em produção**.

O exemplo de código acima também usa uma classe `HeaderPropagatingHttpClient`. Esta classe auxiliar é o arquivo `HeaderPropagation.cs` que foi adicionado ao seu projeto quando você o configurou para usar o Azure Dev Spaces. `HeaderPropagatingHttpClient` é derivada da famosa classe `HttpClient` e adiciona a funcionalidade para propagar cabeçalhos específicos de um objeto HttpRequest ASP .NET existente em um objeto HttpRequestMessage de saída. Você verá mais tarde como isso facilita uma experiência de desenvolvimento mais produtiva em cenários de equipe.

## <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no método `Get(int id)`, no arquivo `ValuesController.cs`, que manipula solicitações GET `api/values/{id}`.
1. No projeto `webfrontend`, no qual você colou o código acima, defina um ponto de interrupção antes que ele envie uma solicitação GET para `mywebapi/api/values`.
1. Pressione F5 no projeto `webfrontend`. Novamente, o Visual Studio abrirá um navegador para a porta localhost apropriada, e o aplicativo Web será exibido.
1. Clique no link “**Sobre**” na parte superior da página para disparar o ponto de interrupção no projeto `webfrontend`. 
1. Pressione F10 para continuar. Agora o ponto de interrupção no projeto `mywebapi` será disparado.
1. Pressione F5 para continuar e você estará novamente no código no projeto `webfrontend`.
1. Pressionar F5 mais uma vez concluirá a solicitação e retornará uma página no navegador. No aplicativo Web, a página Sobre exibirá uma mensagem concatenada pelos dois serviços: "Olá de webfrontend e Olá de mywebapi".

Muito bem! Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

Até agora, você executou seu código do aplicativo como se fosse o único desenvolvedor trabalhando no aplicativo. Nesta seção, você aprenderá como o Azure Dev Spaces simplifica o desenvolvimento em equipe:
* Permite que uma equipe de desenvolvedores trabalhe no mesmo ambiente de desenvolvimento.
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

### <a name="work-in-a-shared-development-environment"></a>Trabalhar em um ambiente de desenvolvimento compartilhado
Com o Azure Dev Spaces, você pode configurar um ambiente de desenvolvimento *compartilhado* no Azure. Cada desenvolvedor pode se concentrar exatamente em sua parte do aplicativo e pode desenvolver iterativamente um *código de pré-confirmação* em um ambiente que já contém todos os outros serviços e recursos de nuvem dos quais seus cenários dependem. As dependências estão sempre atualizadas, e os desenvolvedores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar em seu próprio espaço
À medida que você desenvolve o código para seu serviço, e antes de estar pronto para fazer check-in nele, em geral, o código não estará em um bom estado. Você ainda o está moldando iterativamente, testando e experimentando soluções. O Azure Dev Spaces apresenta o conceito de um **espaço**, permitindo que você trabalhe em isolamento e sem o receio de interromper os membros de sua equipe.

Faça o seguinte para garantir que ambos os serviços `webfrontend` e `mywebapi` estejam em execução no seu ambiente de desenvolvimento **e no espaço `default`**.
1. Feche todas as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas janelas do Visual Studio.
2. Alterne para a janela do Visual Studio com o projeto `mywebapi` e pressione Ctrl+F5 para executar o serviço sem o depurador anexado
3. Alterne para a janela do Visual Studio com o projeto `webfrontend` e pressione Ctrl+F5 para executá-lo também.

> [!Note]
> Às vezes, é necessário atualizar seu navegador depois que a página da Web é exibida inicialmente, seguindo um Ctrl+F5.

Qualquer pessoa que abre a URL pública e navega para o aplicativo Web invocará o caminho do código escrito, o qual é executado em ambos os serviços usando o espaço `default` padrão. Agora suponha que você deseja continuar desenvolvendo `mywebapi`: como você pode fazer isso sem interromper outros desenvolvedores que estiverem usando o ambiente de desenvolvimento? Para fazer isso, você configurará seu próprio espaço.

### <a name="create-a-new-space"></a>Criar um novo espaço
De dentro do Visual Studio, é possível criar espaços adicionais que serão usados ao aplicar o F5 ou Ctrl+F5 em seu serviço. Você pode chamar de espaço qualquer coisa que deseje, e pode ser flexível sobre o que seu significado (por ex. `sprint4` ou `demo`).

Faça o seguinte para criar um novo espaço:
1. Alterne para a janela do Visual Studio com o projeto `mywebapi`.
2. Clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Propriedades**.
3. Selecione a guia **Depurar** à esquerda para mostrar as configurações do Azure Dev Spaces.
4. A partir daqui, você pode alterar ou criar o cluster e/ou o espaço que será usado ao aplicar F5 ou Ctrl+F5. *Verifique se o Azure Dev Space criado anteriormente está selecionado*.
5. No menu suspenso do Espaço, selecione **<Criar novo espaço…>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Na caixa de diálogo **Adicionar Espaço**, digite um nome para o espaço e clique em **OK**. Você pode usar seu nome (por exemplo, “scott”) para o novo espaço para que seus colegas possam identificar em qual espaço você está trabalhando.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Agora você deve ver seu ambiente de desenvolvimento e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código para *mywebapi*

1. No projeto `mywebapi`, faça uma alteração no código para o método `string Get(int id)` no arquivo `ValuesController.cs` da seguinte maneira:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco atualizado de código (talvez você já tenha um conjunto anterior).
3. Pressione F5 para iniciar o serviço `mywebapi`. Isso iniciará o serviço em seu ambiente de desenvolvimento usando o espaço selecionado, que nesse caso é `scott`.

Aqui está um diagrama que ajudará você a entender como os diferentes espaços funcionam. O caminho azul mostra uma solicitação por meio do espaço `default`, que é o caminho padrão usado se nenhum espaço for acrescentado à URL. O caminho verde mostra uma solicitação por meio do espaço `scott`.

![](media/common/Space-Routing.png)

Essa funcionalidade interna do Azure Dev Spaces permite que você teste o código de ponta a ponta em um ambiente compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige que cabeçalhos de propagação sejam encaminhados em seu código de aplicativo, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-running-in-the-scott-space"></a>Testar o código em execução no espaço `scott`
Para testar a nova versão de `mywebapi` em conjunto com `webfrontend`, abra seu navegador na URL de ponto de acesso público de `webfrontend` (por exemplo, http://webfrontend-teamenv.123456abcdef.eastus.aksapp.io) e acesse a página Sobre. Você deve ver a mensagem original “Hello from webfrontend and Hello from mywebapi”.

Agora, adicione a parte “scott.s.” à URL para que ela leia algo como http://scott.s.webfrontend-teamenv.123456abcdef.eastus.aksapp.io e atualize o navegador. O ponto de interrupção que você definiu em seu projeto `mywebapi` deve será atingido. Clique em F5 para continuar e, em seu navegador, agora você deve ver a nova mensagem “Hello from webfrontend and mywebapi now says something new”. Isso ocorre porque o caminho para seu código atualizado em `mywebapi` está em execução no espaço `scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

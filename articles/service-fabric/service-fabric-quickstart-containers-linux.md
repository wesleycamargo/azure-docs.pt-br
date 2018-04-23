---
title: Criar um aplicativo de contêiner do Azure Service Fabric no Linux | Microsoft Docs
description: Neste início rápido, você cria seu primeiro aplicativo de contêiner do Linux no Azure Service Fabric.  Crie uma imagem do Docker com o seu aplicativo, envie a imagem para um registro de contêiner por push, crie e implante um aplicativo de contêiner do Service Fabric.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 65f048d67ef5f250691700a382e781814c57e8a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Guia de Início Rápido: Implantar um aplicativo de contêiner Linux do Azure Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis. 

Este guia de início rápido mostra como implantar contêineres do Linux em um cluster do Service Fabric. Uma vez concluído, você terá um aplicativo de votação que consiste em um front-end de Web em Python e um back-end em Redis em execução em um cluster do Service Fabric. Você também aprenderá a fazer o failover de um aplicativo e a dimensionar um aplicativo em seu cluster.

![Página da Web do aplicativo de votação][quickstartpic]

Neste início rápido, você usa o ambiente Bash no Azure Cloud Shell para executar comandos de CLI do Service Fabric. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se estiver executando o Cloud Shell pela primeira vez, você terá que configurar seu compartilhamento de arquivos `clouddrive`. Aceite os padrões ou anexe um compartilhamento de arquivo existente. Para saber mais, confira [Configurar um `clouddrive` compartilhamento de arquivos](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Obter o pacote de aplicativos
Para implantar os contêineres no Service Fabric, você precisa de um conjunto de arquivos de manifesto (a definição do aplicativo) que descreve os contêineres individuais e o aplicativo.

No Cloud Shell, use o Git para clonar uma cópia da definição do aplicativo; altere os diretórios para o diretório `Voting` em seu clone. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Para implantar o aplicativo no Azure, é necessário que um cluster do Service Fabric execute o aplicativo. Os party clusters facilitam a criação rápida de um cluster do Service Fabric. Party clusters são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure e executados pela equipe do Service Fabric. Você pode usar party clusters para implantar aplicativos e saber mais sobre a plataforma. O cluster usa um certificado único e autoassinado para segurança entre nós e entre cliente e nó.

Entre e ingresse em um [cluster Linux](http://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador clicando no link **PFX**. Clique no link **Leiame** para localizar a senha do certificado e as instruções sobre como configurar vários ambientes para usar o certificado. Mantenha ambas as páginas **Bem-vindo** e **Leiame** abertas. Você usará algumas das instruções nas etapas a seguir. 

> [!Note]
> Há um número limitado de party clusters disponíveis por hora. Se você receber um erro ao tentar se inscrever em um party cluster, poderá aguardar um período e tentar novamente, ou poderá seguir estas etapas em [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para criar um cluster em sua assinatura. 
> 
>Se você criar seu próprio cluster, lembre-se de que o serviço Web de front-end está configurado para escutar o tráfego de entrada na porta 80. Verifique se a porta está aberta no cluster. (Se você estiver usando um party cluster, essa porta estará aberta.)
>

## <a name="configure-your-environment"></a>Configurar seu ambiente
O Service Fabric fornece várias ferramentas que você pode usar para gerenciar um cluster e seus aplicativos:

- Service Fabric Explorer, uma ferramenta baseada no navegador.
- CLI (Interface de Linha de Comando) do Service Fabric, que é executada sobre a CLI 2.0 do Azure.
- Comandos do PowerShell. 

Neste início rápido você usa a CLI do Service Fabric no Cloud Shell e no Service Fabric Explorer. As seções a seguir mostram como instalar o certificado necessário para se conectar ao cluster seguro com essas ferramentas.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Configurar um certificado para a CLI do Service Fabric
Para usar a CLI no Cloud Shell, carregue o arquivo PFX de certificado para o Cloud Shell e use-o para criar um arquivo PEM.

1. Para carregar o certificado em seu diretório de trabalho atual do Cloud Shell, arraste o arquivo PFX de certificado da pasta onde ele foi baixado em seu computador e solte-o na janela do Cloud Shell.  

2. Para converter um arquivo PFX em um arquivo PEM, use o comando a seguir. (Para party clusters, você pode copiar um comando específico para o arquivo e senha PFX seguindo as instruções na página **Leiame**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Configurar certificado para o Service Fabric Explorer
Para usar o Service Fabric Explorer, você precisa importar o arquivo PFX do certificado baixado do site do Party Cluster para o repositório de certificados (Windows ou Mac) ou para o navegador propriamente dito (Ubuntu). Você precisa da senha de chave privada do PFX, que pode ser obtida na página **Leiame**.

Use o método com o qual você está mais familiarizado para importar o certificado em seu sistema. Por exemplo: 

- No Windows: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado em seu armazenamento pessoal, `Certificates - Current User\Personal\Certificates`. Como alternativa, você pode usar o comando do PowerShell nas instruções **Leiame**.
- No Mac: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado no conjunto de chaves.
- No Ubuntu: o Mozilla Firefox é o navegador padrão no Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu navegador e clique em **Opções**. Na página **Preferências**, use a caixa de pesquisa para procurar por "certificados". Clique em **Exibir Certificados**, selecione a guia **Seus Certificados**, clique em **Importar** e siga os prompts para importar o certificado.
 
   ![Instalar certificado no Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Implantar o aplicativo do Service Fabric 
1. No Cloud Shell, conecte-se ao cluster do Service Fabric no Azure usando a CLI. O ponto de extremidade é o ponto de extremidade de gerenciamento do cluster. Você criou o arquivo PEM na seção anterior. (Para party clusters, você pode copiar um comando específico para o arquivo PEM e para o ponto de extremidade de gerenciamento seguindo as instruções na página **Leiame**.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Use o script de instalação para copiar a definição do aplicativo de votação para o cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

2. Abra um navegador da Web e navegue até o ponto de extremidade do Service Fabric Explorer para seu cluster. O ponto de extremidade tem o seguinte formato: https://\<my-azure-service-fabric-cluster-url>:19080/Explorer; por exemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(Para party clusters, encontre o ponto de extremidade do Service Fabric Explorer para seu cluster na página **inicial**.) 

3. Expanda o nó **Aplicativos** para ver que agora há uma entrada para o tipo de aplicativo de votação e a instância que você criou.

    ![Service Fabric Explorer][sfx]

3. Para conectar-se ao contêiner em execução, abra um navegador da Web e navegue até a URL do seu cluster. Por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Você deve ver o aplicativo de votação no navegador.

    ![Página da Web do aplicativo de votação][quickstartpic]


> [!NOTE]
> Você também pode implantar aplicativos do Service Fabric com o Docker Compose. Por exemplo, o comando a seguir pode ser usado para implantar e instalar o aplicativo no cluster usando o Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Fazer failover de um contêiner em um cluster
O Service Fabric faz com que suas instâncias de contêiner sejam movidas automaticamente para outros nós no cluster em caso de falha. Também é possível drenar um nó para contêineres manualmente e movê-los normalmente para outros nós no cluster. O Service Fabric fornece várias maneiras de dimensionar seus serviços. Nas etapas a seguir, use o Service Fabric Explorer.

Para fazer failover do contêiner de front-end, execute as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster, por exemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Clique no nó **fabric:/Voting/azurevotefront** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID). Observe o nome do nó no modo de exibição de árvore, que mostra os nós onde o contêiner está em execução no momento, por exemplo, `_nodetype_4`.
3. Expanda o nó **Nós** no modo de exibição de árvore. Clique nas reticências (...) ao lado do nó que está executando o contêiner.
4. Escolha **Reiniciar** para reiniciar o nó e confirmar a ação de reinicialização. A reinicialização faz o contêiner fazer failover para outro nó no cluster.

    ![Modo de exibição de nó no Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster
Os serviços do Service Fabric podem ser facilmente dimensionados em um cluster para acomodar a carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster.

Para dimensionar o serviço de front-end da Web, realize as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster, por exemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/Voting/azurevotefront** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Início do serviço de escala do Service Fabric Explorer][containersquickstartscale]

  Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/azurevotefront** no modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de escala do Service Fabric Explorer concluído][containersquickstartscaledone]

    Agora é possível ver que o serviço tem duas instâncias. No modo de exibição de árvore, você pode ver em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="clean-up-resources"></a>Limpar recursos
1. Use o script de desinstalação (uninstall.sh) fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo. Esse script leva algum tempo para limpar a instância e, portanto, você não deve executar o script de instalação imediatamente após esse script. Você pode usar o Service Fabric Explorer para determinar quando a instância foi removida e o tipo de aplicativo teve o registro cancelado. 

    ```bash
    ./uninstall.sh
    ```

2. Se você concluiu o trabalho com o cluster, remova o certificado do repositório de certificados. Por exemplo: 
   - No Windows: use o [snap-in do MMC dos Certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Selecione **Minha conta de usuário** ao adicionar o snap-in. Navegue até `Certificates - Current User\Personal\Certificates` e remova o certificado.
   - No Mac: use o aplicativo de conjunto de chaves.
   - No Ubuntu: siga as etapas usadas para exibir certificados e remova o certificado.

3. Se você não quiser continuar usando o Cloud Shell, exclua a conta de armazenamento associada para evitar encargos. Feche a sessão do Cloud Shell. No portal do Azure, clique na conta de armazenamento associada ao Cloud Shell, clique em **Excluir** na parte superior da página e responda às solicitações.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você implantou um aplicativo de contêiner do Linux em um cluster do Service Fabric no Azure, realizou failover no aplicativo e dimensionou o aplicativo no cluster. Para saber mais sobre como trabalhar com contêineres do Linux no Service Fabric, prossiga para o tutorial sobre aplicativos de contêiner do Linux.

> [!div class="nextstepaction"]
> [Criar um aplicativo de contêiner do Linux](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png

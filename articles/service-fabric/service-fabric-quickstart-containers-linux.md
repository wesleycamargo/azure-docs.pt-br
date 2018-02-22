---
title: "Criar um aplicativo de contêiner do Azure Service Fabric no Linux | Microsoft Docs"
description: "Crie seu primeiro aplicativo de contêiner do Linux no Azure Service Fabric.  Crie uma imagem do Docker com o seu aplicativo, envie a imagem para um registro de contêiner por push, crie e implante um aplicativo de contêiner do Service Fabric."
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 6aec2146d83c18a1e1714843cd49890f178e4fb3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Implantar um aplicativo de contêiner Linux do Azure Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis. 

Este guia de início rápido mostra como implantar contêineres do Linux em um cluster do Service Fabric. Uma vez concluído, você terá um aplicativo de votação que consiste em um front-end de Web em Python e um back-end em Redis em execução em um cluster do Service Fabric. 

![quickstartpic][quickstartpic]

Neste guia de início rápido, você aprende a:
> [!div class="checklist"]
> * Implantar contêineres em um cluster do Service Fabric Linux do Azure
> * Contêineres de escala e failover no Service Fabric

## <a name="prerequisite"></a>Pré-requisito
1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

2. Se você optar por instalar e usar a CLI (interface de linha de comando) localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute az – version. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Obter pacote de aplicativos
Para implantar os contêineres no Service Fabric, você precisa de um conjunto de arquivos de manifesto (a definição do aplicativo) que descreve os contêineres individuais e o aplicativo.

No Cloud Shell, use o git para clonar uma cópia da definição do aplicativo.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o cluster do Azure Service Fabric
Para implantar o aplicativo em um cluster no Azure, crie seu próprio cluster.

Clusters de entidade são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure. Eles são executados pela equipe do Service Fabric, em que qualquer pessoa pode implantar aplicativos e conhecer a plataforma. Para obter acesso a um Cluster de Terceiros, [siga as instruções](http://aka.ms/tryservicefabric). 

Para executar operações de gerenciamento no cluster de entidade seguro, é possível usar o Service Fabric Explorer, a CLI ou o Powershell. Para usar o Service Fabric Explorer, você precisa baixar o arquivo PFX do site do cluster de terceiros e importar o certificado para o repositório de certificados (Windows ou Mac) ou para o navegador propriamente dito (Ubuntu). Não há nenhuma senha para os certificados autoassinados do cluster de entidade. 

Para executar operações de gerenciamento com o Powershell ou a CLI, você precisará do PFX (Powershell) ou PEM (CLI). Para converter o PFX em um arquivo PEM, execute o seguinte comando:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Para obter informações sobre como criar seu próprio cluster, consulte [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> O serviço de front-end da Web está configurado para escutar o tráfego de entrada na porta 80. Verifique se a porta está aberta no cluster. Se você estiver usando o Cluster de Entidade, essa porta estará aberta.
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Instalar a interface de linha de comando do Service Fabric e conectá-lo ao seu cluster

Conecte-se ao cluster do Service Fabric no Azure usando a CLI do Azure. O ponto de extremidade é o ponto de extremidade de gerenciamento do cluster, por exemplo, `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Implantar o aplicativo do Service Fabric 
Aplicativos de contêiner do Service Fabric podem ser implantados usando o pacote de aplicativos do Service Fabric descrito ou o Docker Compose. 

#### <a name="deploy-using-service-fabric-application-package"></a>Implantar usando o pacote de aplicativos do Service Fabric
Use o script de instalação fornecido para copiar a definição do aplicativo de votação para o cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Implantar o aplicativo usando o Docker Compose
Implantar e instalar o aplicativo no cluster do Service Fabric usando o Docker Compose com o comando a seguir.
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

Abra um navegador e vá para o Service Fabric Explorer em http://\<my-azure-service-fabric-cluster-url>:19080/Explorer – por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Expanda o nó de aplicativos para ver que agora há uma entrada para o tipo de aplicativo de votação e a instância que você criou.

![Service Fabric Explorer][sfx]

Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para a URL do cluster, por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Você deve ver o aplicativo de votação no navegador.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Fazer failover de um contêiner em um cluster
O Service Fabric certifica-se de que suas instâncias de contêiner sejam movidas automaticamente para outros nós no cluster caso ocorra uma falha. Também é possível drenar um nó para contêineres manualmente e movê-los normalmente para outros nós no cluster. Há vários modos de dimensionar seus serviços. Neste exemplo, estamos usando o Service Fabric Explorer.

Para fazer failover do contêiner de front-end, execute as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Clique no nó **fabric:/Voting/azurevotef** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID). Observe que o nome de nó no modo de exibição de árvore, que mostra os nós no qual o contêiner está em execução no momento, por exemplo, `_nodetype_4`
3. Expanda o nó **Nós** no modo de exibição de árvore. Clique nas reticências (três pontos) ao lado do nó, que está executando o contêiner.
4. Escolha **Reiniciar** para reiniciar o nó e confirmar a ação de reinicialização. A reinicialização faz o contêiner fazer failover para outro nó no cluster.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster
Os serviços do Service Fabric podem ser facilmente dimensionados em um cluster para acomodar a carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster.

Para dimensionar o serviço de front-end da Web, realize as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/Voting/azurevotefront** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![containersquickstartscale][containersquickstartscale]

  Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/VotingWeb** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Agora é possível ver que o serviço tem duas instâncias. No modo de exibição de árvore, você pode ver em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, dobramos o número de recursos disponíveis para nosso serviço de front-end para processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric garantirá que uma nova instância de serviço seja executada no cluster.

## <a name="clean-up"></a>Limpar
Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo. Esse comando leva algum tempo para limpar a instância e o comando 'install.sh' não deve ser executado imediatamente após esse script. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu a:
> [!div class="checklist"]
> * Implantar um aplicativo de contêiner do Linux no Azure
> * Failover de um contêiner em um cluster do Service Fabric
> * Dimensionar um contêiner em um cluster do Service Fabric

* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) no GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png

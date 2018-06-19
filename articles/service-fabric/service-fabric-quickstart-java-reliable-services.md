---
title: Como criar um aplicativo Java do Azure Service Fabric | Microsoft Docs
description: Neste guia de início rápido, você cria um aplicativo Java para o Azure usando um aplicativo de exemplo dos serviços confiáveis do Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 13d350950e91d771b7b4b2310a788537c4c36bd7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642386"
---
# <a name="quickstart-deploy-a-java-service-fabric-reliable-services-application-to-azure"></a>Início Rápido: implantar um aplicativo de serviços confiáveis do Service Fabric em Java no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços. 

Este guia de início rápido mostra como implantar seu primeiro aplicativo Java para o Service Fabric usando o IDE do Eclipse em um computador de desenvolvedor Linux. Ao terminar, você terá um aplicativo de votação com um front-end da Web em Java que salva os resultados da votação em um serviço de back-end com estado do cluster.

![Captura de tela do aplicativo](./media/service-fabric-quickstart-java/votingapp.png)

Neste guia de início rápido, você aprende a:

* Usar o Eclipse como uma ferramenta para seus aplicativos Java do Service Fabric
* Implantar o aplicativo no cluster local 
* Implantar o aplicativo em um cluster no Azure
* Expandir o aplicativo para vários nós

## <a name="prerequisites"></a>pré-requisitos
Para concluir este guia de início rápido:
1. [Instalar o SDK do Service Fabric e a CLI (interface de linha de comando) do Service Fabric (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instalar o Git](https://git-scm.com/)
3. [Instalar o Eclipse](https://www.eclipse.org/downloads/)
4. [Configurar o Ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), seguindo as etapas opcionais para instalar o plug-in do Eclipse 

## <a name="download-the-sample"></a>Baixar o exemplo
Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
1. Inicie seu cluster local executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A inicialização do cluster local leva algum tempo. Para confirmar que o cluster está totalmente funcional, acesse o Service Fabric Explorer em **http://localhost:19080**. Os cinco nós íntegros indicam que o cluster local está em execução. 
    
    ![Integridade do cluster local](./media/service-fabric-quickstart-java/localclusterup.png)

2. Abra o Eclipse.
3. Clique em Arquivo -> Abrir Projetos do Sistema de Arquivos… 
4. Clique em Diretório e escolha o diretório `Voting` na pasta `service-fabric-java-quickstart` clonada do Github. Clique em Concluir. 

    ![Caixa de diálogo Importação do Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. Agora você tem o projeto `Voting` no Explorador de Pacote para o Eclipse. 
6. Clique com o botão direito do mouse no projeto e selecione **Publicar Aplicativo…** na lista suspensa **Service Fabric**. Escolha **PublishProfiles/Local.json** como o perfil de destino e clique em Publicar. 

    ![Caixa de diálogo Publicar local](./media/service-fabric-quickstart-java/localjson.png)
    
7. Abra seu navegador da Web favorito e acesse o aplicativo em **http://localhost:8080**. 

    ![Front-end do aplicativo local](./media/service-fabric-quickstart-java/runninglocally.png)
    
Agora, você pode adicionar um conjunto de opções de votação e começar a votar. O aplicativo é executado e armazena todos os dados no cluster do Service Fabric, sem a necessidade de um banco de dados separado.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o cluster do Azure Service Fabric
Para implantar o aplicativo em um cluster no Azure, crie seu próprio cluster.

Party clusters são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure e executados pela equipe do Service Fabric. Você pode usar party clusters para implantar aplicativos e saber mais sobre a plataforma. O cluster usa um certificado único e autoassinado para segurança entre nós e entre cliente e nó.

Entre e ingresse em um [cluster Linux](http://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador clicando no link **PFX**. Clique no link **Leiame** para localizar a senha do certificado e as instruções sobre como configurar vários ambientes para usar o certificado. Mantenha ambas as páginas **Bem-vindo** e **Leiame** abertas. Você usará algumas das instruções nas etapas a seguir. 

> [!Note]
> Há um número limitado de party clusters disponíveis por hora. Se você receber um erro ao tentar se inscrever em um party cluster, poderá aguardar um período e tentar novamente, ou poderá seguir estas etapas em [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para criar um cluster em sua assinatura. 
>
> O serviço Spring Boot está configurado para escutar o tráfego de entrada na porta 8080. Verifique se a porta está aberta no cluster. Se você estiver usando o Cluster de Entidade, essa porta estará aberta.
>

O Service Fabric fornece várias ferramentas que você pode usar para gerenciar um cluster e seus aplicativos:

- Service Fabric Explorer, uma ferramenta baseada no navegador.
- CLI (Interface de Linha de Comando) do Service Fabric, que é executada sobre a CLI 2.0 do Azure.
- Comandos do PowerShell. 

Neste início rápido você usa a CLI do Service Fabric e o Service Fabric Explorer. 

Para usar a CLI, você precisa criar um arquivo PEM com base no arquivo PFX que você baixou. Para converter o arquivo, use o comando a seguir. (Para clusters de entidade, você pode copiar um comando específico para o arquivo PFX a partir das instruções na página **Leiame**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

Para usar o Service Fabric Explorer, você precisa importar o arquivo PFX do certificado que você baixou do site do Cluster de Entidade para o repositório de certificados (Windows ou Mac) ou para o navegador propriamente dito (Ubuntu). Você precisa da senha de chave privada do PFX, que pode ser obtida na página **Leiame**.

Use o método com o qual você está mais familiarizado para importar o certificado em seu sistema. Por exemplo: 

- No Windows: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado em seu armazenamento pessoal, `Certificates - Current User\Personal\Certificates`. Como alternativa, você pode usar o comando do PowerShell nas instruções **Leiame**.
- No Mac: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado no conjunto de chaves.
- No Ubuntu: o Mozilla Firefox é o navegador padrão no Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu navegador e clique em **Opções**. Na página **Preferências**, use a caixa de pesquisa para procurar por "certificados". Clique em **Exibir Certificados**, selecione a guia **Seus Certificados**, clique em **Importar** e siga os prompts para importar o certificado.
 
   ![Instalar certificado no Firefox](./media/service-fabric-quickstart-java/install-cert-firefox.png) 


### <a name="add-certificate-information-to-your-application"></a>Adicionar informações de certificado ao seu aplicativo

A impressão digital do certificado precisa ser adicionada ao seu aplicativo porque ele está usando modelos de programação do Service Fabric. 

1. Você precisará da impressão digital do certificado no arquivo ```Voting/VotingApplication/ApplicationManiest.xml``` ao executar em um cluster seguro. Execute o comando a seguir para extrair a impressão digital do certificado.

    ```bash
    openssl x509 -in [CERTIFICATE_PEM_FILE] -fingerprint -noout
    ```

2. Em ```Voting/VotingApplication/ApplicationManiest.xml```, adicione o trecho de código a seguir sob da marca **ApplicationManifest**. O **X509FindValue** deve ser a impressão digital da etapa anterior (sem ponto e vírgula). 

    ```xml
    <Certificates>
        <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
    </Certificates>   
    ```
    
### <a name="deploy-the-application-using-eclipse"></a>Implantar o aplicativo usando o Eclipse
Agora que o aplicativo e o cluster estão prontos, você pode implantá-los no cluster diretamente do Eclipse.

1. Abra o arquivo **Cloud.json** no diretório **PublishProfiles** e preencha os campos `ConnectionIPOrURL` e `ConnectionPort` adequadamente. É apresentado um exemplo: 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. Clique com o botão direito do mouse no projeto e selecione **Publicar Aplicativo…** na lista suspensa **Service Fabric**. Escolha **PublishProfiles/Cloud.json** como o perfil de destino e clique em Publicar. 

    ![Caixa de diálogo Publicar em nuvem](./media/service-fabric-quickstart-java/cloudjson.png)

3. Abra seu navegador da Web e acesse o aplicativo em: **http://\<ConnectionIPOrURL>:8080**. 

    ![Front-end do aplicativo em nuvem](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster
Os serviços do Service Fabric podem ser facilmente colocados em escala em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Existem várias maneiras de colocar seus serviços em escala, por exemplo, usando scripts ou comandos da CLI do Service Fabric (sfctl). Nas etapas a seguir, use o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP do cluster (19080), por exemplo, `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/Voting/VotingWeb** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Dimensionar Serviço do Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/VotingWeb** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de colocação em escala do Service Fabric Explorer concluído](./media/service-fabric-quickstart-java/servicescaled.png)

    Agora, você pode ver que o serviço tem duas instâncias e no modo de exibição de árvore, você vê em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu a:

* Usar o Eclipse como uma ferramenta para seus aplicativos Java do Service Fabric
* Implantar aplicativos Java no cluster local 
* Implantar aplicativos Java em um cluster no Azure
* Expandir o aplicativo para vários nós

Para saber mais sobre como trabalhar com aplicativos Java no Service Fabric, continue o tutorial para os aplicativos Java.

> [!div class="nextstepaction"]
> [Implantar um aplicativo Java](./service-fabric-tutorial-create-java-app.md)

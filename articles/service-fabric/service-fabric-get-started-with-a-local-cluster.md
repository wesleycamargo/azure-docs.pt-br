<properties
   pageTitle="Introdução à implantação e à atualização de aplicativos em seu cluster local | Microsoft Azure"
   description="Configure um cluster local do Service Fabric, implante um aplicativo existente nele e atualize esse aplicativo."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/20/2015"
   ms.author="seanmck"/>

# Introdução à implantação e à atualização de aplicativos em seu cluster local
O SDK do Service Fabric inclui um ambiente de desenvolvimento local completo e permite que você rapidamente comece a implantar e a gerenciar aplicativos em um cluster local. Neste artigo, você criará um cluster local, implantará um aplicativo existente nele e atualizará o aplicativo para uma nova versão, tudo do Windows PowerShell.

> [AZURE.NOTE]Este artigo pressupõe que você já tenha [configurado seu ambiente de desenvolvimento](service-fabric-get-started.md).

## Criar um cluster local
Um cluster do Service Fabric representa um conjunto de recursos de hardware em que você pode implantar aplicativos. Normalmente, um cluster é composto de cinco a milhares de computadores, mas o SDK do Service Fabric inclui uma configuração de cluster que pode ser executada em um único computador.

O cluster local do Service Fabric não é um simulador ou um emulador. Ele executa o mesmo código de plataforma encontrado em clusters com vários computadores. A única diferença é que ele executa os processos de plataforma normalmente espalhados por cinco computadores em um só.

O SDK fornece duas maneiras de configurar um cluster local: um script do Windows PowerShell e o aplicativo de bandeja do sistema Gerenciador de Cluster Local. Neste tutorial, usaremos o script do PowerShell.

> [AZURE.NOTE]Se você já tiver criado um cluster local ao implantar um aplicativo do Visual Studio, poderá ignorar esta seção.


1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de instalação do cluster da pasta do SDK:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    A instalação de cluster levará alguns minutos; depois disso, você verá uma saída parecida com esta:

    ![Saída da instalação do cluster][cluster-setup-success]

    Agora você está pronto para tentar implantar um aplicativo em seu cluster.

## Implantar um aplicativo
O SDK do Service Fabric inclui um conjunto avançado de estruturas e de ferramentas de desenvolvedor para a criação de aplicativos. Se você estiver interessado em aprender a criar aplicativos no Visual Studio, consulte [Criando seu primeiro aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Neste tutorial, usaremos um aplicativo de exemplo existente (chamado WordCount) para que possamos nos concentrar nos aspectos de gerenciamento da plataforma, incluindo a implantação, o monitoramento e a atualização.


1. Inicie uma nova janela do PowerShell como administrador.

2. Importe o módulo do PowerShell do SDK do Service Fabric.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Crie um diretório para armazenar o aplicativo que será baixado e implantado, como c:\\Service Fabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. Baixe o aplicativo WordCount [daqui](http://aka.ms/servicefabric-wordcountapp) no local que você criou.

5. Conecte-se ao cluster local:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Invoque o comando de implantação do SDK para criar um novo aplicativo, fornecendo um nome e um caminho para o pacote de aplicativos.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se tudo transcorrer bem, você verá uma saída como esta:

    ![Implantar um aplicativo no cluster local][deploy-app-to-local-cluster]

7. Para ver o aplicativo em ação, inicie o navegador e navegue até [http://localhost:8081/wordcount/index](http://localhost:8081/wordcount/index). Você deverá ver algo assim:

    ![IU do aplicativo implantado][deployed-app-ui]

    O aplicativo WordCount é muito simples. Ele inclui código JavaScript do lado do cliente para gerar "palavras", de cinco caracteres aleatórias, que serão transmitidas para o aplicativo por meio da WebAPI do ASP.NET. Um serviço com estado mantém o controle sobre o número de palavras contadas, particionadas com base no primeiro caractere da palavra. O aplicativo que foi implantado contém quatro partições e, portanto, as palavras de A a G são armazenadas na primeira partição, de H a N são armazenadas na segunda partição e assim por diante.

## Exibir detalhes e o status do aplicativo
Com o aplicativo implantado, vamos examinar alguns dos detalhes do aplicativo no PowerShell.

1. Consulte todos os aplicativos implantados no cluster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Supondo que tenha implantado somente o aplicativo WordCount, você verá algo assim:

    ![Consultar todos os aplicativos implantados no PowerShell][ps-getsfapp]

2. Vá para o próximo nível ao consultar o conjunto de serviços incluído no aplicativo WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Listar serviços para o aplicativo no PowerShell][ps-getsfsvc]

    Observe que o aplicativo é composto de dois serviços, o front-end da Web e o serviço com estado que gerencia as palavras.

3. Por fim, dê uma olhada na lista de partições para o WordCountService:

    ![Exibir as partições de serviço no PowerShell][ps-getsfpartitions]

    O conjunto de comandos que você acabou de usar, como todos os comandos do PowerShell do Service Fabric, estão disponíveis para qualquer cluster ao qual você queira se conectar, local ou remoto.

    Para obter uma maneira mais visual de interagir com o cluster, você poderá usar a ferramenta Service Fabric Explorer baseada na Web ao acessar [http://localhost:19080/Explorer](http://localhost:19080/Explorer) no navegador.

    ![Exibir detalhes do aplicativo no Service Fabric Explorer][sfx-service-overview]

    > [AZURE.NOTE]Para saber mais sobre o Service Fabric Explorer, consulte [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

## Atualizar um aplicativo
O Service Fabric oferece atualizações sem tempo de inatividade por meio do monitoramento da integridade do aplicativo à medida que ele percorre o cluster. Vamos fazer uma atualização simples do aplicativo WordCount.

A nova versão do aplicativo agora contará somente as palavras que comecem por vogal. À medida que a atualização for distribuída, veremos duas alterações no comportamento do aplicativo. Primeiro, a taxa na qual a contagem aumenta deve ficar mais lenta, já que menos palavras serão contadas. Em segundo lugar, como a primeira partição tem duas vogais (A e E) e todas as outras contêm apenas uma, sua contagem eventualmente deverá começar a ultrapassar as outras.

1. Baixe o pacote v2 [daqui](http://aka.ms/servicefabric-wordcountappv2) no mesmo local onde você baixou o pacote v1.

2. Volte para a janela do PowerShell e use o comando de atualização do SDK para registrar a nova versão do cluster e iniciar a atualização do fabric:/WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Você deverá ver uma saída no PowerShell semelhante a esta quando a atualização for iniciada.

    ![Andamento da atualização no PowerShell][ps-appupgradeprogress]

3. Durante a atualização, talvez você ache mais fácil monitorar seu status do Service Fabric Explorer. Inicie uma janela de navegador e navegue até [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Clique em **Aplicativos** na árvore à esquerda e escolha **Atualizações em Andamento**.

    ![Andamento da atualização no Service Fabric Explorer][sfx-upgradeprogress]

    Observe que o indicador Andamento da Atualização representa o estado da atualização nos domínios de atualização do cluster. À medida que a atualização prosseguir em cada domínio, serão executadas verificações de integridade para garantir que o aplicativo está se comportando adequadamente antes de prosseguir.

4. Se você executar novamente a consulta anterior para o conjunto de serviços incluídos no aplicativo fabric:/WordCount, observará que, enquanto a versão do WordCountService foi alterada, a versão do WordCountWebService não foi:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Consultar serviços de aplicativos após a atualização][ps-getsfsvc-postupgrade]

    Isso destaca como o Service Fabric gerencia atualizações de aplicativo, que é tocar apenas o conjunto de serviços (ou pacotes de código/configuração nesses serviços) que foram alterados, tornando o processo de atualização mais rápido e confiável.

5. Por fim, retorne ao navegador para observar o comportamento da nova versão do aplicativo. Conforme o esperado, a contagem é mais lenta e a primeira partição termina com um volume ligeiramente maior.

    ![Exibir a nova versão do aplicativo no navegador][deployed-app-ui-v2]

## Próximas etapas
- Agora que você implantou e atualizou alguns aplicativos pré-compilados, poderá [tentar compilar seu próprio aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas as ações executadas no cluster local neste artigo também podem ser executadas em um [cluster do Azure](service-fabric-cluster-creation-via-portal.md).
- A atualização executada neste artigo foi muito básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a eficiência e a flexibilidade das atualizações do Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_1203_2015-->
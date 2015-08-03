<properties
   pageTitle="Solucionar problemas de configuração do cluster local"
   description="Este artigo aborda um conjunto de sugestões para a solução de problemas do cluster de desenvolvimento local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="seanmck"/>

# Solucionar problemas de configuração do cluster de desenvolvimento local

Se você tiver um problema ao interagir com o cluster de desenvolvimento local, examine as sugestões a seguir para ver as possíveis soluções.

## Falhas de configuração do cluster

### Não é possível limpar os logs da Malha do Serviço

#### Problema

Ao executar o script DevClusterSetup, você vê um erro como este:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Solução

Feche a janela atual do Powershell e inicie uma nova janela como um Administrador. Agora você pode executar o script com êxito.

## Falhas de conexão do cluster

### Exceção de inicialização de tipo

#### Problema

Ao conectar-se com o cluster no PowerShell ou no Gerenciador da Malha do Serviço, você vê TypeInitializationException para System.Fabric.Common.AppTrace.

#### Solução

A variável do caminho não foi definida corretamente durante a instalação. Saia do Windows e faça logon novamente. Isso atualiza seu caminho.

### Falha de conexão do cluster com “O objeto está fechado”

#### Problema

Uma chamada para Connect-ServiceFabricCluster falha com um erro parecido com este:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Solução

Feche a janela atual do Powershell e inicie uma nova janela como um Administrador. Agora você pode se conectar com êxito.

### FabricConnectionDeniedException

#### Problema

Ao depurar no Visual Studio, você obtém FabricConnectionDeniedException.

#### Solução

Geralmente, esse erro ocorre quando você tenta iniciar um processo de host de serviço manualmente, em vez de permitir que o tempo de execução da Malha do Serviço inicie-o para você.

Verifique se você não possui um projeto de serviço definido como projeto de inicialização na sua solução. Somente projetos de aplicativo da Malha do Serviço devem ser definidos como projetos de inicialização.


## Próximas etapas

- [Entender e solucionar problemas de cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizando o cluster com o Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md)

<!---HONumber=July15_HO4-->
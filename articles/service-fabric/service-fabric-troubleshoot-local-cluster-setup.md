<properties
   pageTitle="Solucionar problemas de sua configuração de cluster do Service Fabric local | Microsoft Azure"
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
   ms.date="01/08/2016"
   ms.author="seanmck"/>

# Solucionar problemas de configuração do cluster de desenvolvimento local

Se você tiver um problema ao interagir com o cluster de desenvolvimento local do Service Fabric do Azure, examine as sugestões a seguir para ver as possíveis soluções.

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

Feche a janela atual do PowerShell e inicie uma nova janela como um administrador. Agora você pode executar o script com êxito.

## Falhas de conexão do cluster

### Cmdlets do PowerShell do Service Fabric não são reconhecidos no Azure PowerShell

#### Problema

Se você tentar executar qualquer um dos cmdlets do PowerShell do Service Fabric, como `Connect-ServiceFabricCluster` em uma janela do Azure PowerShell, ele falhará, dizendo que o cmdlet não é reconhecido. A razão para isso é que o Azure PowerShell usa a versão de 32 bits do Windows PowerShell (mesmo em versões de sistema operacional de 64 bits), enquanto os cmdlets do Service Fabric só funcionam em ambientes de 64 bits.

#### Solução

Sempre execute os cmdlets do Service Fabric diretamente do Windows PowerShell.

### Exceção de Inicialização de Tipo

#### Problema

Ao conectar-se com o cluster no PowerShell, você vê o erro TypeInitializationException para System.Fabric.Common.AppTrace.

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

Feche a janela atual do PowerShell e inicie uma nova janela como um administrador. Agora você pode se conectar com êxito.

### Exceção de Conexão ao Fabric Negada

#### Problema

Ao depurar no Visual Studio, você obtém um erro FabricConnectionDeniedException.

#### Solução

Geralmente, esse erro ocorre quando você tenta iniciar um processo de host de serviço manualmente, em vez de permitir que o tempo de execução da Malha do Serviço inicie-o para você.

Verifique se você não possui um projeto de serviço definido como projeto de inicialização na sua solução. Somente projetos de aplicativo da Malha do Serviço devem ser definidos como projetos de inicialização.


## Próximas etapas

- [Entender e solucionar problemas de cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizando o cluster com o Explorador do Service Fabric](service-fabric-visualizing-your-cluster.md)

<!---HONumber=AcomDC_0114_2016-->
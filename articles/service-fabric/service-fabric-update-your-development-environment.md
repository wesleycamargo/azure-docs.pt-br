<properties
   pageTitle="Atualize o ambiente de desenvolvimento da Malha do Serviço | Microsoft Azure"
   description="Atualize seu ambiente de desenvolvimento da Malha do Serviço para usar o tempo de execução, o SDK e as ferramentas mais recentes."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# Atualizar o ambiente de desenvolvimento da Malha do Serviço

 A Malha de Serviço fornece periodicamente novas versões do tempo de execução, SDK e das ferramentas para uso no desenvolvimento local. Manter seu ambiente de desenvolvimento local atualizado com essas versões garante que você sempre tenha acesso aos recursos, correções e melhorias de desempenho mais recentes ao compilar e testar seus aplicativos localmente.

## Limpar o cluster local

 Atualmente, a Malha de Serviço não dá suporte à atualização do tempo de execução da Malha de Serviço enquanto um cluster local está em execução. Para garantir uma atualização limpa, é importante limpar o cluster local primeiro.

 >[AZURE.NOTE]Limpar o cluster local removerá todos os aplicativos implantados e seus dados.

 Você pode limpar o cluster local da seguinte maneira:


 1. Feche todas as outras janelas do PowerShell e inicie uma nova como administrador.

 2. Navegue até o diretório de instalação de cluster com `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`

 3. Execute o `.\CleanCluster.ps1`


## Atualizar o tempo de execução, o SDK e as ferramentas

 Após ter limpado com êxito o cluster existente, você poderá prosseguir com a atualização da seguinte maneira:


 1. Inicie o Web Platform Installer para [atualizar para a nova versão][1].

 2. Após a conclusão, inicie uma nova janela do PowerShell como administrador e navegue até o diretório de instalação de cluster com `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"`.

 3. Execute `.\DevClusterSetup.ps1` para configurar o cluster local.

É isso! Agora você pode iniciar o Visual Studio e continuar a criação de aplicativos da Malha de Serviço.

>[AZURE.NOTE]A estrutura do projeto padrão foi alterada nesta versão. Você poderá abrir e executar projetos existentes no Visual Studio. No entanto, se você encontrar problemas com a criação, implantação ou depuração de seus aplicativos, considere criar um novo projeto e migrar seu código.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Link do WebPI"

<!---HONumber=Oct15_HO3-->
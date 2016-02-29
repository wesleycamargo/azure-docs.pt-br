<properties
   pageTitle="Configurar um cluster do Service Fabric usando um modelo do Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Configure um cluster do Service Fabric usando um modelo do Gerenciador de Recursos do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Configurar um cluster do Service Fabric usando um modelo do Gerenciador de Recursos do Azure

Esta página ajuda você a configurar um cluster do Service Fabric usando um modelo do Gerenciador de Recursos do Azure Para fazer isso, sua assinatura precisa ter uma quantidade suficiente de núcleos para implantar as VMs de IaaS que comporão esse cluster.

## Pré-requisitos

- Para configurar um cluster seguro, primeiro carregue um certificado X.509 em seu cofre de chave. Você precisará da URL do cofre de origem, da URL de certificado e da impressão digital do certificado.
- Confira [Segurança do cluster do Service Fabric](service-fabric-cluster-security.md) para obter mais detalhes sobre como configurar um cluster seguro.

## Obter um exemplo de modelo do Gerenciador de Recursos

Os exemplos de modelo do Gerenciador de Recursos estão disponíveis na [Galeria de modelos de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates). Todos os nomes de modelo do Service Fabric começam com "service-fabric...". Você pode pesquisar no repositório por "fabric" ou rolar a tela para baixo até o conjunto de modelos de exemplo. Para ajudar você a localizar rapidamente o que está procurando, os nomes dos modelos foram determinados da seguinte forma:

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) para indicar um modelo de cluster não seguro de cinco nós (nó simples).

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) para indicar um modelo de cluster seguro de cinco nós (nó simples) habilitado para WAD.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) para indicar um modelo de cluster seguro de dez nós (nós duplos) habilitado para WAD.

## Criar um modelo personalizado do Gerenciador de Recursos

Há duas maneiras de criar um modelo personalizado do Gerenciador de Recursos:

1. Você pode adquirir um exemplo de modelo na [Galeria de modelos de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates) e fazer alterações nele.

2. Entre no Portal do Azure e use as páginas do portal do Service Fabric para gerar o modelo que será personalizado. Para fazer isso, siga estas etapas:

    a. Entre no [Portal do Azure](https://portal.azure.com/).

    b. Siga o processo de criação do cluster, conforme descrito em [Criar um cluster do Service Fabric do Azure via portal](service-fabric-cluster-creation-via-portal.md), mas não clique em **Criar**. Em vez disso, acesse **Resumo** e baixe o modelo, conforme mostra a captura de tela a seguir.

 ![Captura de tela da página do Cluster do Service Fabric mostrando o link para baixar um modelo do Gerenciador de Recursos][DownloadTemplate]

## Implantar o modelo do Gerenciador de Recursos no Azure usando o Azure PowerShell

Confira [Implantar modelos de Gerenciador de Recursos usando o PowerShell](../resource-group-template-deploy.md) para obter diretrizes detalhadas sobre como implantar o modelo usando o PowerShell.

>[AZURE.NOTE] Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos em todos os momentos para manter a disponibilidade e preservar o estado - conhecido como "manter o quórum". Consequentemente, normalmente não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0218_2016-->
<properties
   pageTitle="Configurar um cluster do Service Fabric usando um modelo de ARM | Microsoft Azure"
   description="Configurar um cluster do Service Fabric usando um modelo de ARM."
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
   ms.date="11/13/2015"
   ms.author="chackdan"/>

# Configurar um cluster do Service Fabric usando um modelo de ARM

Esta página ajuda na configuração de um cluster do Service Fabric usando um modelo ARM. Supõe-se que a sua assinatura tem a quantidade de núcleos suficiente para implantar as VMs de IaaS que comporão esse cluster.

## Pré-requisitos

- Se deseja configurar um cluster seguro, verifique se você carregou um certificado X509 para o cofre da chave. Você precisará da URL do Cofre de Origem, da URL do Certificado e da Impressão Digital do Certificado.
-  Consulte [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md) para saber como fazer isso.

## Adquirir um exemplo de modelo de ARM

1. Os exemplos de modelo de ARM estão disponíveis em [Galeria de modelos de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates). Todos os modelos do Service Fabric começam com o nome "service-fabric-cluster-...". Você pode pesquisar "fabric" no repositório ou simplesmente rolar até o conjunto de exemplos de modelo.
2. Para ajudar você a localizar rapidamente o que está procurando, os nomes dos modelos foram determinados da seguinte forma
	1. [service-fabric-cluster-5-node-1-nodetype](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype) para indicar um modelo de cluster não seguro de 5 nós (nós simples).
	2. [service-fabric-cluster-5-node-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad) para indicar um modelo de cluster não seguro de 5 nós (nós simples) habilitado para WAD.
	3. [service-fabric-cluster-5-node-secure-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) para indicar um modelo de cluster seguro de 5 nós (nós simples) habilitado para WAD.
	4. [service-fabric-cluster-10-node-secure-2-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) para indicar um modelo de cluster seguro de 10 nós (nós duplos) habilitado para WAD.
	

## Criar um modelo personalizado de ARM

2. Você tem duas opções aqui 
	1. Você pode adquirir um exemplo de modelo na [Galeria de modelos de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates) e fazer alterações nele.
	2. Faça logon no Portal do Azure e use as páginas do portal do Service Fabric para gerar o modelo que será personalizado. O processo para fazer isso é descrito abaixo.
3. Faça logon no Portal do Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).
2. Siga o processo de criação do cluster, conforme descrito em [Criar um Cluster do Service Fabric via portal](service-fabric-cluster-creation-via-portal.md), mas não clique em ****criar**. Em vez disso, vá para Resumo e baixe o modelo. ![DownloadTemplate][DownloadTemplate]

## Implantar o modelo de ARM no Azure usando o Azure PS

Consulte [Implantar modelos de ARM usando o PS](https://azure.microsoft.com/pt-BR/documentation/articles/resource-group-template-deploy/) para obter orientações detalhadas sobre como fazer isso.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md) 
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade do Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=Nov15_HO4-->
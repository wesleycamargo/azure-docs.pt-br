<properties 
    pageTitle="Implantar QuickBooks no RemoteApp do Azure | Microsoft Azure" 
    description="Aprenda a compartilhar QuickBooks com o RemoteApp do Azure." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="elizapo" />



# Como implantar o QuickBooks no RemoteApp do Azure?

Use as seguintes informações para compartilhar QuickBooks como um aplicativo RemoteApp do Azure.


Você pode compartilhar o QuickBooks 2015 Enterprise com o RemoteApp do Azure em uma coleção híbrida ou de nuvem. O arquivo da empresa deve residir em uma VM que esteja executando o servidor de banco de dados do QuickBooks separado dos servidores do RemoteApp do Azure. Nunca armazene o arquivo da empresa em sua imagem do RemoteApp do Azure, pode haver perda de dados se você fizer isso. Somente o QuickBooks Enterprise dá suporte à hospedagem do arquivo QuickBooks em um compartilhamento externo com o servidor de banco de dados do QuickBooks acessível por meio de redes do Windows padrão.

> [AZURE.IMPORTANT]O servidor de banco de dados do QuickBooks que está hospedando o arquivo da empresa deve residir em uma VM separada na mesma VNET que a coleção do RemoteApp do Azure.

## Etapas para implantar o QuickBooks

1. Crie uma VM do Azure e instale o QuickBooks, o servidor de banco de dados do QuickBooks e coloque o arquivo da empresa em uma VM do Azure. Certifique-se de configurar corretamente as regras de firewall.
2. Instale o QuickBooks em uma [imagem personalizada](remoteapp-imageoptions.md) e crie uma [coleção do RemoteApp do Azure](remoteapp-collections.md), em nuvem ou híbrida, com a mesma VNET em que a VM que hospeda o servidor de banco de dados do QuickBooks com os arquivos da empresa está. 
3.	[Publicar](remoteapp-publish.md) o aplicativo QuickBooks aos usuários
4.	Inicie o cliente do QuickBooks hospedado no RemoteApp do Azure, navegue usando a rede padrão do Windows até a VM que hospeda o servidor de banco de dados do QuickBooks e abra o arquivo da empresa. 

## Referências de documentação

- [Configurações com suporte](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top) do QuickBooks
- [Opções de implantação](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) do QuickBooks

Você também pode conferir minha apresentação do Ignite, [Conceitos básicos de Gerenciamento e Administração do RemoteApp do Microsoft Azure](https://channel9.msdn.com/Events/Ignite/2015/BRK3868); avance até 1:02:45 para chegar à parte sobre QuickBooks.

## Arquitetura de implantação

![Implantação do QuickBooks + RemoteApp do Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=Oct15_HO2-->
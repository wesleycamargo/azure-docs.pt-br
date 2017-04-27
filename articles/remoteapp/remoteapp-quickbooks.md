---
title: Implantar QuickBooks no RemoteApp do Azure | Microsoft Docs
description: Aprenda a compartilhar QuickBooks com o RemoteApp do Azure.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c5d00753-77c0-4f0d-a5df-9451b46a31d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 17fbef55920c4f06f895739b89a0acc6ff08bd9d
ms.lasthandoff: 03/31/2017


---
# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Como implantar o QuickBooks no RemoteApp do Azure?
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Use as seguintes informações para compartilhar QuickBooks como um aplicativo RemoteApp do Azure.

Você pode compartilhar o QuickBooks 2015 Enterprise com o RemoteApp do Azure em uma coleção híbrida ou de nuvem. O arquivo da empresa deve residir em uma VM que esteja executando o servidor de banco de dados do QuickBooks separado dos servidores do RemoteApp do Azure. Nunca armazene o arquivo da empresa em sua imagem do RemoteApp do Azure, pode haver perda de dados se você fizer isso. Somente o QuickBooks Enterprise dá suporte à hospedagem do arquivo QuickBooks em um compartilhamento externo com o servidor de banco de dados do QuickBooks acessível por meio de redes do Windows padrão.   

> [!IMPORTANT]
> O servidor de banco de dados do QuickBooks que está hospedando o arquivo da empresa deve residir em uma VM separada na mesma VNET que a coleção do RemoteApp do Azure.  
> 
> 

## <a name="steps-to-deploy-quickbooks"></a>Etapas para implantar o QuickBooks
1. Crie uma VM do Azure e instale o QuickBooks, o servidor de banco de dados do QuickBooks e coloque o arquivo da empresa em uma VM do Azure.  Certifique-se de configurar corretamente as regras de firewall.
2. Instale o QuickBooks em uma [imagem personalizada](remoteapp-imageoptions.md) e crie uma [coleção do RemoteApp do Azure](remoteapp-collections.md), em nuvem ou híbrida, com a mesma VNET em que a VM que hospeda o servidor de banco de dados do QuickBooks com os arquivos da empresa está. 
3. [Publicar](remoteapp-publish.md) o aplicativo QuickBooks aos usuários
4. Inicie o cliente do QuickBooks hospedado no RemoteApp do Azure, navegue usando a rede padrão do Windows até a VM que hospeda o servidor de banco de dados do QuickBooks e abra o arquivo da empresa. 

## <a name="documentation-references"></a>Referências de documentação
* [Configurações com suporte](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
* [Opções de implantação](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Você também pode conferir minha apresentação do Ignite, [Conceitos básicos de Gerenciamento e Administração do RemoteApp do Microsoft Azure](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) ; avance até 1:02:45 para chegar à parte sobre QuickBooks.

## <a name="deployment-architecture"></a>Arquitetura de implantação
![Implantação do QuickBooks + RemoteApp do Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)



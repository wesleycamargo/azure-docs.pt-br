---
title: Guia de Publicação de Oferta de Modelo de Solução dos Aplicativos do Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057878"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicativos do Azure: Guia de Publicação de Oferta de Modelo de Solução

Os Modelos de Solução são uma das principais formas de publicar uma solução no Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

Use o tipo de oferta Aplicativo do Azure: modelo de solução quando a solução exigir mais automação de implantação e configuração do que uma VM simples. Você pode automatizar o provisionamento de uma ou mais VMs usando Aplicativos do Azure: modelos de solução. Você também pode provisionar recursos de rede e armazenamento. O tipo de oferta Aplicativos do Azure: modelos de solução oferece benefícios de automação para VMs únicas e soluções inteiras baseadas em IaaS.

## <a name="requirements-for-solution-templates"></a>Requisitos dos modelos de solução

|Requisitos |Detalhes  |
|---------|---------|
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. Máquinas virtuais pagas conforme o uso (PAYGO) serão negociadas com o cliente via Microsoft e cobradas por meio da assinatura do Azure do cliente (PAYGO) 
No caso do modelo traga sua própria licença, enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente        |
|VHD (disco rígido virtual) compatível com Azure    |   As VMs devem ser criadas em Windows ou Linux.<ul> <li>Para obter mais informações sobre como criar um VHD do Linux, visite a seção Criar um VHD compatível com o Azure (baseado em Linux) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, visite a seção Criar um VHD compatível com o Azure (baseado em Windows) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Próximas etapas
Caso ainda não tenha feito isso, 

- [registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta

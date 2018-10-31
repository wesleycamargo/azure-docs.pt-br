---
title: Oferta de máquina virtual no Azure Marketplace | Microsoft Docs
description: Visão geral do processo de publicação de uma oferta de VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638812"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Esta seção descreve os elementos da publicação de uma Máquina Virtual (VM) e destina-se como um guia para o publicador para o [Azure Marketplace](https://azuremarketplace.microsoft.com).  A partir desse ponto de vista, ele é dividido nas seguintes partes principais:

- [Pré-requisitos](./cpp-prerequisites.md) - lista os requisitos técnicos e comerciais antes de criar ou publicar uma oferta de VM
- [Criar oferta de VM](./cpp-create-offer.md) - lista as etapas necessárias para criar uma nova entrada de oferta de VMusando o [Portal do Microsoft Cloud Partner](https://cloudpartner.azure.com)
- [Criar ativos técnicos de VM](./cpp-create-technical-assets.md) - como criar ativos técnicos para uma solução de VM e como configurar esse pacote como uma VM da oferta no Azure Marketplace
- [Publicar oferta de VM](./cpp-publish-offer.md) - como enviar a oferta para publicar no Microsoft Azure Marketplace


## <a name="vm-publishing-process-flow"></a>Fluxo do processo de publicação

O diagrama a seguir ilustra as etapas de alto nível na publicação de uma oferta de VM. 

![Processo de publicação de VM](./media/publishvm_001.png)

1. Criar a oferta - todos os detalhes e informações sobre a oferta estão configurados, incluindo a descrição da oferta, informações dos materiais, jurídicas e de suporte e especificações do ativo.

2. Criar ativos técnicos e negócios - criar ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associado (aqui, as VMs e discos anexados). 

3. Criar a SKU – criar as SKUs associadas à oferta e enviá-las.  Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar. 
 
4. Certificar e publicar a oferta - depois de concluir a oferta e os recursos técnicos, envie-a. Isso iniciará o processo de publicação, no qual o modelo de solução é testado, validado, certificado e, em seguida vai “ao vivo” no marketplace.  

## <a name="next-steps"></a>Próximas etapas

Antes de considerar essas etapas, cumpra os [requisitos técnicos e comerciais](./cpp-prerequisites.md) para a publicação de uma VM no Microsoft Azure Marketplace. 

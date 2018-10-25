---
title: Atualizar uma oferta existente para o Azure Marketplace
description: Atualizar uma oferta existente para o Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805049"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Atualizar uma oferta existente para o Azure Marketplace 
==============================================

Há vários tipos de atualizações que você poderá fazer na oferta após a ativação.

1.  Adicionar novo \"Pacote\" a uma SKU existente
2.  Adicionar novas SKUs a uma Oferta existente
3.  Atualizar metadados do marketplace de SKUs/oferta

É necessário atualizar a oferta no Portal do Cloud Partner e republicar. Este artigo explica os diferentes aspectos da atualização da oferta do Aplicativo Azure.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Alterações não permitidas na oferta do Aplicativo Azure/SKU 
--------------------------------------------------

Há atributos de uma oferta do Aplicativo Azure/SKU que não poderão ser modificados depois que a oferta estiver ativa no Azure Marketplace.

1.  ID da oferta e ID do editor da oferta.
2.  ID de SKU de SKUs existentes.
3.  Atualize um pacote que tenha sido publicado.

<a name="adding-a-new-package-to-an-existing-sku"></a>Adicionar um novo pacote a uma SKU existente 
---------------------------------------

O editor talvez queira adicionar uma nova versão do pacote para atualizar um pacote existente. Isso pode ser feito, carregando um novo pacote com um número de versão diferente.

1.  Entre no [Portal do Cloud Partner](http://cloudpartner.azure.com)
2.  Em Todas as ofertas, localize a oferta que você quer atualizar
3.  No formulário de SKUs, clique na SKU\' cujo pacote você gostaria de atualizar
4.  Clique em \"Novo Pacote\" e forneça uma nova versão
5.  Carregar um novo arquivo .zip contendo o arquivo de modelo atualizado
6.  Clique em Publicar para iniciar o fluxo de trabalho de publicação para que a nova SKU seja ativada.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Adicionar uma nova SKU a uma oferta existente
-------------------------------------

É possível optar por disponibilizar um novo SKU para sua oferta existente. Para habilitar isso, siga as etapas abaixo.

1.  Faça logon no [Portal de Parceiros de Nuvem](http://cloudpartner.azure.com)
2.  Em Todas as ofertas, localize a oferta que você quer atualizar
3.  No formulário de SKUs, clique em Adicionar nova SKU e forneça uma ID de SKU no pop-up.
4.  Siga o restante das etapas conforme especificado [aqui](./cloud-partner-portal-managed-app-publish.md).
5.  Clique em Publicar para iniciar o fluxo de trabalho de publicação para que a nova SKU seja ativada.

<a name="updating-offer-marketplace-metadata"></a>Atualizar metadados do Marketplace de Oferta 
-----------------------------------

Talvez haja situações em que seja necessário atualizar os metadados de marketplace associados à sua oferta como atualizar seus logotipos da empresa, etc. Siga as etapas abaixo.

1.  Entre no Portal do Cloud Partner
2.  Em Todas as ofertas, localize a oferta que você quer atualizar
3.  Vá para o formulário do Marketplace e siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) para fazer as alterações.
4.  Clique em Publicar para iniciar o fluxo de trabalho de publicação para que as alterações sejam ativadas.

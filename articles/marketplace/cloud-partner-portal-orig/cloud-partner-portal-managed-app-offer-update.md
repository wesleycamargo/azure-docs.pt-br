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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073231"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Atualizar uma oferta existente para o Azure Marketplace 
==============================================

Há vários tipos de atualizações que você poderá fazer na oferta após a ativação, por exemplo:

1.  Adicionar novo \"Pacote\" a uma SKU existente
2.  Adicionar novas SKUs a uma Oferta existente
3.  Atualizar metadados do marketplace de SKUs/oferta

Você usará o Portal do Cloud Partner para implementar essas alterações e republicar sua oferta. Este artigo explica os diferentes aspectos da atualização de sua oferta de aplicativo do Azure.

<a name="unpermitted-changes-to-azure-application-offer"></a>Alterações não permitidas na oferta de aplicativo do Azure 
-----------------------------------------------

Há atributos de uma oferta do Aplicativo Azure/SKU que não poderão ser modificados depois que a oferta estiver ativa no Azure Marketplace.

* ID da oferta e ID do editor da oferta.
* ID de SKU de SKUs existentes.
* Atualize um pacote que tenha sido publicado.

<a name="adding-a-new-package-to-an-existing-sku"></a>Adicionar um novo pacote a uma SKU existente 
---------------------------------------

O editor talvez queira adicionar uma nova versão do pacote para atualizar um pacote existente. Esse acréscimo pode ser feito carregando um novo pacote com um número de versão diferente.

1.  Entre no [Portal do Cloud Partner](http://cloudpartner.azure.com)
2.  Em **Todas as ofertas**, localize a oferta que você quer atualizar
3.  No formulário de **SKUs**, clique na SKU cujo pacote você gostaria de atualizar
4.  Clique em \"Novo Pacote\" e forneça uma nova versão
5.  Carregar um novo arquivo .zip contendo o arquivo de modelo atualizado
6.  Clique em Publicar para iniciar o fluxo de trabalho de publicação para que a nova SKU seja ativada.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Adicionar uma nova SKU a uma oferta existente
-------------------------------------

Você pode optar por disponibilizar a nova SKU de sua oferta existente seguindo estas etapas:

1.  Faça logon no [Portal do Cloud Partner](http://cloudpartner.azure.com)
2.  Em **Todas as ofertas**, localize a oferta que você quer atualizar
3.  No formulário de **SKUs**, clique em Adicionar nova SKU e forneça uma ID de SKU no pop-up.
4.  Siga o restante das etapas conforme especificado [aqui](./cloud-partner-portal-managed-app-publish.md).
5.  Clique em **Publicar** para iniciar o fluxo de trabalho de publicação para que a nova SKU seja ativada.

<a name="updating-offer-marketplace-metadata"></a>Atualizar metadados do Marketplace de Oferta 
-----------------------------------

Talvez haja situações em que seja necessário atualizar os metadados de marketplace associados à sua oferta como atualizar seus logotipos da empresa, etc. Siga as etapas abaixo.

1.  Entre no Portal do Cloud Partner.
2.  Em **Todas as ofertas**, localize a oferta que você quer atualizar
3.  Vá para o formulário do Marketplace e siga as instruções [aqui](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md) para fazer as alterações.
4.  Clique em Publicar para iniciar o fluxo de trabalho de publicação para que as alterações sejam ativadas.

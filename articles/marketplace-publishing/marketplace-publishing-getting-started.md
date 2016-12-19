---
title: "Visão geral de como criar e implantar uma oferta no Marketplace | Microsoft Docs"
description: "Entender as etapas necessárias para se tornar um Desenvolvedor da Microsoft aprovado e criar e implantar uma imagem de máquina virtual, o modelo, serviço de dados ou o serviço de desenvolvedor no Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Como publicar e gerenciar uma oferta no Azure Marketplace
Este artigo é fornecido para ajudar um desenvolvedor a criar, implantar e gerenciar suas soluções listadas no Azure Marketplace para que outros clientes e parceiros do Azure as adquiram e utilizem.

## <a name="what-is-the-azure-marketplace"></a>O que é o Azure Marketplace?
O Azure Marketplace é onde um assinante do Azure pode localizar serviços para facilitar o desenvolvimento de aplicativos e soluções locais ou baseados na nuvem. Também é onde se usa os serviços do [Azure Certified](http://azure.com/certified) como blocos de construção para desenvolver rapidamente um aplicativo ou serviço inovador para sua linha de negócios e outros assinantes do Azure.

Como um editor do Azure, o Azure Marketplace é a maneira de distribuir e vender suas soluções ou seus serviços inovadores a outros desenvolvedores, ISVs e profissionais de TI que queiram desenvolver rapidamente seus aplicativos baseados em nuvem e soluções móveis.

## <a name="supported-types-of-offers"></a>Tipos de oferta com suporte
A primeira coisa que você gostaria de fazer como um publicador é definir que tipo de solução sua empresa está oferecendo. O Azure Marketplace dá suporte a três tipos de oferta:

* **Imagens de Máquina Virtual** são imagens pré-configuradas com um sistema operacional totalmente instalado e um ou mais aplicativos. Uma imagem da máquina virtual fornece as informações necessárias para criar e implantar máquinas virtuais no serviço de Máquinas Virtuais do Azure.

  > [!NOTE]
  > **Por exemplo,** como um editor do Azure, você criou e validou uma VM com um serviço de banco de dados inovador bem convincente, tanto é que outros assinantes do Azure estariam dispositivos a adquirir e implantar essa VM nos respectivos ambientes de serviço de nuvem.
  >
  >
* **Serviços de Desenvolvedor** são serviços totalmente gerenciados para uso no desenvolvimento de aplicativos ou no gerenciamento de sistemas. Eles apresentam funcionalidade que permitem rápido desenvolvimento de aplicativos em escala de nuvem no Azure.

  > [!NOTE]
  > **Por exemplo,** como um editor do Azure, você desenvolveu um serviço acessível por API (hospedado no Azure ou em qualquer outro lugar) que fornece previsões com base nos dados históricos. E esse é um serviço que outros assinantes do Azure que estão criando soluções podem querer utilizar. Você pode implantar esse serviço no Azure Marketplace para que outras pessoas o encontrem, adquiram e usem nos respectivos serviços.
  >
  >
* **modelo de solução** é uma estrutura de dados que pode fazer referência a um ou mais serviços distintos do Azure, incluindo serviços publicados por outros vendedores, de modo a permitir que assinantes do Azure implantem uma ou mais ofertas de maneira simples e coordenada.

  > [!NOTE]
  > **Por exemplo,** como um editor do Azure, você agrupou um conjunto de serviços do Azure que agiliza a implantação de um serviço de nuvem seguro e de alta disponibilidade com balanceamento de carga que é realizada com apenas alguns cliques. Outros assinantes do Azure podem aproveitar o valor para poupar tempo adquirindo esse modelo de solução em vez de identificar e configurar manualmente os mesmos serviços, ou semelhantes, do Azure.
  >
  >

Algumas etapas são compartilhadas entre os diferentes tipos de soluções. Este artigo fornece uma breve visão geral das etapas que você precisará concluir para qualquer tipo de solução.

## <a name="1-pre-requisites"></a>1. Pré-requisitos
> [!NOTE]
> Antes de começar qualquer trabalho no Azure Marketplace, você deve ser [pré-aprovado](http://azure.com/certified).
>
>

1. [Solicitar pré-aprovação do Microsoft Azure Certified](marketplace-publishing-azure-certification.md)
2. [Criar e registrar uma conta de Desenvolvedor da Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Cumprir os pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2. Publicação de sua oferta
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1 Concluir os pré-requisitos técnicos específicos da oferta
* [Pré-requisitos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Pré-requisitos técnicos de Modelo de Solução](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2 Criar sua oferta
1. Crie sua oferta usando os guias específicos de oferta abaixo.
   * [Criar sua oferta de VM](marketplace-publishing-vm-image-creation.md)
   * [Criar sua oferta de Modelo de Solução](marketplace-publishing-solution-template-creation.md)
2. [Criar sua oferta de conteúdo de marketing](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3 Testar sua oferta em preparo
* [Testar sua oferta de VM em preparo](marketplace-publishing-vm-image-test-in-staging.md)
* [Testar a oferta de Modelo de Solução em preparo](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4 Implantar a oferta no Marketplace
* [Implantar a oferta no Azure Marketplace](marketplace-publishing-push-to-production.md)
* [Solução de problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
* Para saber mais sobre os portais usados, veja [Portais necessários](marketplace-publishing-portals.md).

### <a name="virtual-machine-image-specific"></a>Imagem específica de máquina virtual
* [Criando uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Criar uma máquina virtual executando o Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual executando o Linux no Portal do Azure](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Solucionar problemas comuns encontrados durante a criação do VHD](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="3-post-publishing-management-of-your-offer"></a>3. Gerenciamento pós-publicação de sua oferta
* [Guia de pós-produção para ofertas de máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
* [Como atualizar os detalhes não técnicos de uma oferta ou uma SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Como atualizar os detalhes técnicos de uma oferta ou uma SKU](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Como adicionar uma nova SKU em uma oferta listada](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Como alterar a contagem de discos de dados para uma SKU listada](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Como excluir uma oferta listada do Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Como excluir uma SKU listada do Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Como excluir a versão atual de uma SKU listada do Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Como reverter o preço de listagem para os valores de produção](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Como reverter o modelo de cobrança para os valores de produção](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Como reverter a configuração de visibilidade de uma SKU listada para o valor de produção](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Como alterar seu incentivo ao revendedor de Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
* [Noções básicas sobre os relatórios de percepções do vendedor](marketplace-publishing-report-seller-insights.md)
* [Noções básicas sobre os relatórios de pagamento](marketplace-publishing-report-payout.md)
* [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Recursos adicionais
* [Configurando o PowerShell do Azure](marketplace-publishing-powershell-setup.md)



<!--HONumber=Nov16_HO3-->



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
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 49ffe988867ba70df5bd7757a64edc9aa813071d
ms.lasthandoff: 04/29/2017


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Como publicar e gerenciar uma oferta no Azure Marketplace
Este artigo é fornecido para ajudar um desenvolvedor a criar, implantar e gerenciar suas soluções listadas no Azure Marketplace para que outros clientes e parceiros do Azure as adquiram e utilizem.

## <a name="what-is-the-azure-marketplace"></a>O que é o Azure Marketplace?
Como um editor do Azure, o Azure Marketplace é a maneira de distribuir e vender suas soluções ou seus serviços inovadores a outros desenvolvedores, ISVs e profissionais de TI que queiram desenvolver rapidamente seus aplicativos baseados em nuvem e soluções móveis. Se sua solução tem como alvo usuários de negócios, pode ser útil considerar o marketplace do [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipos de soluções com suporte
A primeira coisa que você gostaria de fazer como um publicador é definir que tipo de solução sua empresa está oferecendo. O Azure Marketplace dá suporte aos seguintes tipos de oferta:

|Tipo de Solução|Máquina Virtual|Modelo de Solução|
|---|---|---|
|Definição|Imagens pré-configuradas com um sistema operacional totalmente instalado e um ou mais aplicativos. Uma imagem da máquina virtual fornece as informações necessárias para criar e implantar máquinas virtuais no serviço de Máquinas Virtuais do Azure.|Uma estrutura de dados que pode fazer referência a um ou mais serviços distintos do Azure, incluindo serviços publicados por outros vendedores, de modo a permitir que assinantes do Azure implantem uma ou mais ofertas de maneira simples e coordenada.|
|Exemplo|**Por exemplo,** como um editor do Azure, você criou e validou uma VM com um serviço de banco de dados inovador bem convincente, tanto é que outros assinantes do Azure estariam dispositivos a adquirir e implantar essa VM nos respectivos ambientes de serviço de nuvem.|**Por exemplo,** como um editor do Azure, você agrupou um conjunto de serviços do Azure que agiliza a implantação de serviços de nuvem com balanceamento de carga, seguro e com alta disponibilidade. Outros assinantes do Azure podem poupar tempo adquirindo o modelo de solução que atende aos seus objetivos em vez de localizar, adquirir, implantar e configurar manualmente serviços do Azure iguais ou semelhantes.|

> [!NOTE]
> Observe que algumas etapas são compartilhadas entre os diferentes tipos de soluções e outras são diferentes entre os respectivos tipos de solução. Este artigo fornece uma breve visão geral das etapas que você precisará concluir para qualquer tipo de solução.

## <a name="how-to-publish-a-solution"></a>Como publicar uma solução
![desenhar](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. Nomear sua solução para pré-aprovação
- Preencha o formulário de nomeação de solução do **Certificado do Microsoft Azure para Máquinas Virtuais** [aqui](https://createopportunity.azurewebsites.net)

>[!NOTE]
> Se você estiver trabalhando com um Gerente de Conta de Parceiro ou Gerente de Parceiro DX, peça para ele nomear sua solução para o programa Azure Certified OU acesse a página da Web [Microsoft Azure Certified](http://createopportunity.azurewebsites.net), preencha o formulário de inscrição e insira o email do seu Gerente de Conta de Parceiro ou Gerente de Parceiro DX no campo Contato Responsável da Microsoft.

Se os critérios de qualificação forem atendidos de acordo com as [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) e seu aplicativo for aprovado, começaremos a trabalhar com você para integrar sua solução ao Azure Marketplace.

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2. Registrar sua conta como um vendedor da Microsoft
- [Registrar sua conta da Microsoft como uma conta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. Publicar sua solução
1. Cumprir os pré-requisitos não técnicos
  - [Cumprir os pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md)
  - [Pré-requisitos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [Pré-requisitos técnicos de Modelo de Solução](marketplace-publishing-solution-template-creation-prerequisites.md)
2. Criar sua oferta
  - [Máquina virtual](marketplace-publishing-vm-image-creation.md)
  - [Modelo de Solução](marketplace-publishing-solution-template-creation.md)
3. [Criar sua oferta de conteúdo de marketing](marketplace-publishing-push-to-staging.md)
4. Testar sua oferta de preparo
  - [Testar sua oferta de VM em preparo](marketplace-publishing-vm-image-test-in-staging.md)
  - [Testar a oferta de Modelo de Solução em preparo](marketplace-publishing-solution-template-test-in-staging.md)
5. [Implantar a oferta no Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>Imagem específica de máquina virtual
* [Criando uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Criar uma máquina virtual executando o Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual executando o Linux no Portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Solucionar problemas comuns encontrados durante a criação do VHD](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>Como gerenciar sua solução
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
* [Noções básicas sobre os relatórios de pagamento](marketplace-publishing-report-payout.md)
* [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Recursos adicionais
* [Configurando o PowerShell do Azure](marketplace-publishing-powershell-setup.md)


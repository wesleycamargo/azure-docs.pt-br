<properties
   pageTitle="Visão geral de como criar e implantar uma oferta no Marketplace | Microsoft Azure"
   description="Entender as etapas necessárias para se tornar um Desenvolvedor da Microsoft aprovado e criar e implantar uma imagem de máquina virtual, o modelo, serviço de dados ou o serviço de desenvolvedor no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Como publicar e gerenciar uma oferta no Azure Marketplace
Este artigo é fornecido para ajudar um desenvolvedor a criar, implantar e gerenciar suas soluções listadas no Azure Marketplace para que outros clientes e parceiros do Azure as adquiram e utilizem.

A primeira coisa que você gostaria de fazer como um publicador é definir que tipo de solução sua empresa está oferecendo. O Azure Marketplace oferece suporte a várias soluções, e cada uma delas requer um conjunto ligeiramente diferente de trabalho para publicar com êxito no Marketplace.

## Tipos de Ofertas
|Tipo de oferta| Definição |
|---|---|
|Imagem de Máquina Virtual | Imagem de máquina virtual (VM) pré-configurada com um sistema operacional totalmente instalado e um ou mais aplicativos. Ofertas de Imagem de Máquina Virtual podem incluir uma ou várias imagens de VM reunidas por um Modelo de Solução. Uma imagem da máquina virtual ("Imagem") fornece as informações necessárias para criar e implantar máquinas virtuais no serviço de Máquinas Virtuais do Azure. Uma imagem abrange um disco rígido virtual do sistema operacional e zero ou mais discos rígidos virtuais do disco de dados. Os clientes podem implantar qualquer quantidade de máquinas virtuais por meio de uma única imagem.|
|Serviço do desenvolvedor| Serviço totalmente gerenciado para operadores de informações, analistas de negócios, desenvolvedores ou profissionais de TI usarem no desenvolvimento de aplicativos personalizados ou gerenciamento de sistemas. Os Serviços de Desenvolvedor fornecem funcionalidade para habilitar os clientes a desenvolver rapidamente aplicativos de escala de nuvem no Azure. Os clientes deverão ter uma assinatura do Azure para adquirir Serviços de Desenvolvedor. Os Editores são responsáveis por medir o uso dos clientes dos Serviços de Desenvolvedores e por relatar as informações de uso para a Microsoft, conforme detalhado no Contrato de Editor do Microsoft Azure Marketplace.|
|Modelo de Solução|Um "Modelo de Solução ARM (Azure Resource Manager)" é uma estrutura de dados que pode fazer referência a uma ou mais ofertas distintas, incluindo ofertas publicadas por outros fornecedores, para habilitar os clientes do Azure a implantar uma ou mais ofertas de forma única e coordenada.|

Algumas etapas são compartilhadas entre os diferentes tipos de soluções. Este artigo fornece uma breve visão geral das etapas que você precisará concluir para qualquer tipo de solução.

## 1\. Pré-requisitos

> [AZURE.NOTE] Antes de começar qualquer trabalho no Azure Marketplace, você deve ser pré-aprovado. Isso não é aplicável a editores de serviço de dados.

1. [Solicitar pré-aprovação do Microsoft Azure Certified](marketplace-publishing-azure-certification.md)
2. [Criar e registrar uma conta de Desenvolvedor da Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Cumprir os pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md)

## 2\. Publicação de sua oferta
### 2\.1 Concluir os pré-requisitos técnicos específicos da oferta
- [Pré-requisitos técnicos de VM](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Pré-requisitos técnicos de Modelo de Solução](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 Criar sua oferta
1. Crie sua oferta usando os guias específicos de oferta abaixo.
    - [Criar sua oferta de VM](marketplace-publishing-vm-image-creation.md)
    - [Criar sua oferta de Modelo de Solução](marketplace-publishing-solution-template-creation.md)
2. [Criar sua oferta de conteúdo de marketing](marketplace-publishing-push-to-staging.md)

### 2\.3 Testar sua oferta em preparo
- [Testar sua oferta de VM em preparo](marketplace-publishing-vm-image-test-in-staging.md)
- [Testar a oferta de Modelo de Solução em preparo](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Implantar a oferta no Marketplace
- [Implantar a oferta no Azure Marketplace](marketplace-publishing-push-to-production.md)

### Imagem específica de máquina virtual ###
- [Criando uma imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Criar uma máquina virtual que executa o Windows no Portal de Visualização do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [Solução de problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
- Para saber mais sobre os portais usados, veja [Portais necessários](marketplace-publishing-portals.md).


## 3\. Gerenciamento pós-publicação de sua oferta
- [Guia de pós-produção para ofertas de máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
- [Como atualizar os detalhes não técnicos de uma oferta ou uma SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Como excluir uma oferta ou SKU do Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [Como alterar seu incentivo ao revendedor de Provedor de Soluções na Nuvem](marketplace-publishing-csp-incentive.md)
- [Noções básicas sobre os relatórios de percepções do vendedor](marketplace-publishing-report-seller-insights.md)
- [Noções básicas sobre os relatórios de pagamento](marketplace-publishing-report-payout.md)
- [Obtenha suporte como um editor](marketplace-publishing-get-publisher-support.md)

## Recursos adicionais
- [Configurando o PowerShell do Azure](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->
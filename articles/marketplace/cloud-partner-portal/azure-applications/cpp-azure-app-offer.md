---
title: Oferta de Aplicativo Azure | Microsoft Docs
description: Visão geral do processo de publicação de uma oferta de Aplicativo Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195245"
---
# <a name="azure-application-offer"></a>Oferta de aplicativo do Azure

Esta seção explica como publicar uma nova oferta de aplicativo Azure no Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Cada aplicativo Azure contém um modelo do Azure Resource Manager que define todos os ativos técnicos usados pelo aplicativo e que normalmente inclui uma ou mais máquinas virtuais e outros serviços de suporte com base no Azure ou na Web. | ![Ícone de aplicativos do Azure](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Benefícios

Alguns dos benefícios de listar seus aplicativos em um mercado da Microsoft incluem:

* Alcance 100 milhões de usuários do Azure Active Directory no Office 365 e no Dynamics 365.

* Estenda sua equipe de vendas: alcance usuários corporativos em todo o mundo e ganhe um canal de vendas que envolva usuários finais, ajude a gerar clientes potenciais e inicie conversas com novos clientes em todos os setores.

* Receba insights acionáveis: compartilharemos insights sobre o desempenho do aplicativo no AppSource, sobre o que funciona bem e sobre como melhorar ainda mais seus procedimentos de vendas.

## <a name="types-of-azure-applications"></a>Tipos de aplicativos do Azure

Há dois tipos de aplicativos do Azure: um aplicativo gerenciado e um modelo de solução. Embora eles sejam semelhantes, há algumas diferenças notáveis.

### <a name="solution-template"></a>Modelo de solução

Os modelos de solução são uma das principais formas de publicar uma solução no Marketplace. Esse tipo de oferta é usado quando sua solução requer implantação adicional e automação de configuração de mais de uma única VM (máquina virtual). Você pode automatizar o fornecimento de mais de uma VM usando um modelo de solução. Isso inclui o provisionamento de recursos de rede e de armazenamento para fornecer soluções complexas de IaaS. Para obter uma visão geral dos requisitos de modelo de solução e o modelo de cobrança, confira [Aplicativos Azure: modelos de solução](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Aplicativo gerenciado

Um aplicativo gerenciado é semelhante a um modelo de solução no Marketplace, com uma diferença importante. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução. Use os aplicativos gerenciados do Azure para criar e entregar facilmente aplicativos totalmente gerenciados e prontos para uso aos clientes.

Além do Marketplace, você também pode oferecer aplicativos gerenciados em um catálogo de serviços. O catálogo de serviços é um catálogo interno das soluções aprovadas para os usuários em uma organização. Você pode usar o catálogo para atender aos padrões organizacionais ao oferecer soluções para grupos em uma organização. Os funcionários usam o catálogo para encontrar com facilidade aplicativos recomendados e aprovados pelos departamentos de TI.

Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, confira a [Visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Visão geral da publicação

O vídeo a seguir, [Construindo Modelos de Solução e Aplicativos Gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), é uma visão geral sobre como criar um modelo do Azure Resource Manager para definir uma solução de aplicativo do Azure e como publicar a oferta de aplicativo posteriormente o mercado do Azure.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Publicando o fluxo de trabalho do processo

O diagrama a seguir mostra o processo de alto nível para a publicação uma oferta de aplicativo Azure.

![Fluxo de trabalho para publicação de oferta](./media/new-offer-process.png)

## <a name="offer-components"></a>Componentes de oferta

Esta seção descreve os elementos de publicação de uma oferta de aplicativo gerenciado e funciona como um guia do Azure Marketplace para o editor. Publicação é dividida nas seguintes partes principais: 

* [Pré-requisitos](./cpp-prerequisites.md) – lista os requisitos técnicos e comerciais antes da criação ou da publicação de uma oferta de aplicativo gerenciado. 
* [Criar a oferta](./cpp-create-offer.md) – fornece as etapas necessárias para criar uma entrada de oferta de aplicativo gerenciado usando o Portal do Cloud Partner. 
* [Publicar a oferta](./cpp-publish-offer.md) – descreve como enviar a oferta para publicação no Azure Marketplace.

## <a name="steps-in-the-publishing-process"></a>Etapas no processo de publicação

As etapas de alto nível para publicar uma oferta de aplicativo Azure são:

1. Crie a oferta - forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.

2. Criar ativos técnicos e comerciais – criar ativos comerciais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada.

3. Criar o SKU - Crie o (s) SKU (s) associado (s) à oferta. Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar.

4. Certificar e publicar a oferta - Depois que a oferta e os recursos técnicos forem concluídos, você poderá enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "entra em operação" no Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

Antes de considerar essas etapas, você precisa cumprir os [requisitos técnicos e comerciais](./cpp-prerequisites.md) para publicar um aplicativo gerenciado no Microsoft Azure Marketplace.
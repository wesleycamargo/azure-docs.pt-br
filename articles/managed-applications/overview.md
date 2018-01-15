---
title: "Visão geral de aplicativos gerenciados do Azure | Microsoft Docs"
description: Descreve os conceitos de aplicativos gerenciados do Azure
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: a26cfc632dacb41435b3755409d3e91630f5f3eb
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="azure-managed-applications-overview"></a>Visão geral de aplicativos gerenciados do Azure

Os aplicativos gerenciados do Azure permitem que você ofereça soluções de nuvem fáceis de implantar e operar. Implemente a infraestrutura e forneça suporte contínuo. Para disponibilizar um aplicativo gerenciado para todos os clientes, publique-o no marketplace do Azure. Para disponibilizá-lo somente para usuários em sua organização, publique-o em um catálogo interno. 

Um aplicativo gerenciado é semelhante a um modelo de solução no Marketplace, com uma diferença importante. Em um aplicativo gerenciado, os recursos são provisionados para um grupo de recursos gerenciado pelo fornecedor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução.

## <a name="advantages-of-managed-applications"></a>Vantagens dos aplicativos gerenciados

Os aplicativos gerenciados reduzem as barreiras para os consumidores que usam suas soluções. Eles não precisam de conhecimento especializado sobre a infraestrutura de nuvem para usar sua solução. Os consumidores têm acesso limitado a recursos críticos. Eles não precisam se preocupar com erros durante o gerenciamento. 

Os aplicativos gerenciados permitem que você estabeleça uma relação contínua com seus consumidores. Defina as condições para o gerenciamento do aplicativo, e todos os encargos serão manipulados por meio da cobrança do Azure.

Embora os clientes implantem esses aplicativos gerenciados em suas assinaturas, eles não precisam fazer a manutenção, atualizar nem repará-las. Você pode garantir que todos os clientes estejam usando as versões aprovadas. Os clientes não precisam desenvolver conhecimento de domínio específico do aplicativo para gerenciarem esses aplicativos. Os clientes adquirem automaticamente atualizações do aplicativo sem precisar se preocupar com o diagnóstico e a solução de problemas com o aplicativo. 

Para equipes de TI, os aplicativos gerenciados permitem a oferta de soluções pré-aprovadas aos usuários na organização. Você garante que essas soluções sejam compatíveis com os padrões organizacionais.

## <a name="types-of-managed-applications"></a>Tipos de aplicativos gerenciados

Você pode publicar seu aplicativo gerenciado externamente ou internamente.

![Publicar interna ou externamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catálogo de serviços

O catálogo de serviços é um catálogo interno das soluções aprovadas para os usuários em uma organização. Use o catálogo para assegurar a conformidade com certos padrões organizacionais enquanto oferece soluções excelentes para as organizações. Os funcionários usam o catálogo para descobrir facilmente um conjunto sofisticado de aplicativos recomendados e aprovados pelos departamentos de TI. Eles veem os aplicativos gerenciados que outras pessoas na organização compartilham com eles.

Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, confira [Criar o aplicativo do catálogo de serviços](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Os fornecedores que desejam cobrar por seus serviços podem disponibilizar um aplicativo gerenciado no marketplace do Azure. Depois que o fornecedor publicar um aplicativo, ele ficará disponível para os usuários fora da organização. Com essa abordagem, os MSPs (provedores de serviços gerenciados), ISVs (fornecedores de software independentes) e SIs (integradores de sistema) podem oferecer suas soluções a todos os clientes do Azure.

Para obter informações sobre como publicar aplicativos gerenciados para o Marketplace, confira [Criar aplicativo do marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupos de recursos para aplicativos gerenciados

Normalmente, os recursos de um aplicativo gerenciado residem em dois grupos de recursos. O consumidor gerencia um grupo de recursos, e o fornecedor gerencia o outro grupo de recursos. Ao definir o aplicativo gerenciado, o fornecedor especifica os níveis de acesso. A imagem a seguir mostra um cenário no qual o fornecedor solicita a função de proprietário para o grupo de recursos gerenciados. O fornecedor aplicou um bloqueio somente leitura nesse grupo de recursos para o consumidor.

![Acesso ao grupo de recursos](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupo de recursos do aplicativo

Esse grupo de recursos armazena a instância do aplicativo gerenciado. Esse grupo de recursos pode conter apenas um recurso. O tipo de recurso do aplicativo gerenciado é **Microsoft.Solutions/applications**.

O consumidor tem acesso total ao grupo de recursos e o utiliza para gerenciar o ciclo de vida do aplicativo gerenciado.

### <a name="managed-resource-group"></a>Grupo de recursos gerenciado

Este grupo de recursos contém todos os recursos necessários ao aplicativo gerenciado. Por exemplo, esse grupo de recursos contém as máquinas virtuais, contas de armazenamento e redes virtuais para a solução. O consumidor tem acesso limitado a esse grupo de recursos, pois não gerencia os recursos individuais do aplicativo gerenciado. O acesso do fornecedor a este grupo de recursos corresponde à função especificada na definição do aplicativo gerenciado. Por exemplo, o fornecedor pode solicitar a função de Proprietário ou Colaborador para este grupo de recursos.

Quando o consumidor exclui o aplicativo gerenciado, o grupo de recursos gerenciado também é excluído.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à definição e implantação de um aplicativo gerenciado, confira [Criar e implantar um aplicativo gerenciado do Azure com a CLI do Azure](managed-apps-quickstart-cli.md)
* Para obter informações sobre como publicar um aplicativo interno, confira [Criar o aplicativo do catálogo de serviços](publish-service-catalog-app.md).
* Para obter informações sobre como publicar aplicativos gerenciados para o marketplace, confira [Criar aplicativo do marketplace](publish-marketplace-app.md).

---
title: Cliente de cobrança e estorno no Azure Stack | Microsoft Docs
description: Saiba como recuperar informações de uso de recursos do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337843"
---
# <a name="usage-and-billing-in-azure-stack"></a>Uso e cobrança do Azure Stack

Este artigo descreve como os usuários do Azure Stack são cobrados pelo uso de recursos e como as informações de cobrança são acessadas para análise e o encargo de volta.

O Azure Stack coleta e agrupa os dados de uso de recursos que são usados. Em seguida, o Azure Stack encaminha esses dados para comércio do Azure. Comércio do Azure cobra você para uso do Azure Stack da mesma forma que ele cobra você para uso do Azure.

Você também pode obter dados de uso e de exportação para seu próprio custo ou de cobrança de fazer o sistema por meio de um adaptador de cobrança ou exportá-lo para uma ferramenta de business intelligence, como o Microsoft Power BI.

## <a name="usage-pipeline"></a>Pipeline de uso

Cada provedor de recursos no Azure Stack posta dados de uso por uso de recursos. O serviço de uso periodicamente (por hora e diariamente) agrega dados de uso e o armazena no banco de dados de uso. Operadores de pilha e os usuários do Azure podem acessar os dados de uso armazenados através do uso de recursos do Azure Stack APIs.

Se você tiver [registrado sua instância do Azure Stack com o Azure](azure-stack-register.md), Azure Stack está configurado para enviar os dados de uso para comércio do Azure. Depois que os dados são carregados para o Azure, você pode acessá-lo por meio do portal de cobrança ou por meio de APIs de uso de recursos do Azure. Para obter mais informações sobre os dados de uso são relatados para o Azure, consulte [relatórios de dados de uso](azure-stack-usage-reporting.md).  

A imagem a seguir mostra os principais componentes no pipeline de uso:

![Pipeline de uso](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Quais informações de uso pode encontrar e como?

Provedores de recursos do Azure Stack (por exemplo, computação, armazenamento e rede) geram dados de uso em intervalos de horas para cada assinatura. Os dados de uso contém informações sobre o recurso usado como nome do recurso, a assinatura usada e a quantidade usada. Para saber mais sobre os recursos de identificação dos medidores, consulte o [perguntas frequentes sobre o uso da API](azure-stack-usage-related-faq.md).

Depois que foram coletados dados de uso, será [relatados para o Azure](azure-stack-usage-reporting.md) para gerar uma fatura, que pode ser exibida por meio do portal de cobrança do Azure.

> [!NOTE]  
> Relatórios de dados de uso não é necessário para o Azure Stack desenvolvimento ASDK (Kit) e para usuários do sistema integrado do Azure Stack que licenciam sob o modelo de capacidade. Para saber mais sobre o licenciamento no Azure Stack, consulte o [empacotamento e a folha de dados de preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

O portal de cobrança do Azure mostra dados de uso para os recursos passíveis de cobrança. Além dos recursos passíveis de cobrança, o Azure Stack captura dados de uso para um conjunto mais amplo de recursos, que pode ser acessado em seu ambiente do Azure Stack por meio de APIs REST ou cmdlets do PowerShell. Operadores do Azure Stack podem obter os dados de uso para todas as assinaturas do usuário. Usuários individuais só podem obter seus próprios detalhes de uso.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Relatório de uso do provedores de serviços de nuvem de multilocatário

Um locatário a provedor de serviços de nuvem (CSP) que tem muitos clientes que usam o Azure Stack talvez queira relatar o uso de cada cliente separadamente, para que o provedor pode cobrar o uso de diferentes assinaturas do Azure.

Cada cliente tem sua identidade representada por um locatário diferente do Azure Active Directory (Azure AD). O Azure Stack dá suporte à atribuição de uma assinatura de CSP para cada locatário do Azure AD. Você pode adicionar suas assinaturas e locatários para o registro do Azure Stack base. O registro de base é feito para todas as instâncias do Azure Stack. Se uma assinatura não está registrada para um locatário, o usuário ainda pode usar o Azure Stack e seu uso será enviado para a assinatura usada para o registro de base.

## <a name="next-steps"></a>Próximas etapas

- [Registrar com o Azure Stack](azure-stack-registration.md)
- [Relatar dados de uso do Azure Stack para o Azure](azure-stack-usage-reporting.md)
- [API de uso de recursos do provedor](azure-stack-provider-resource-api.md)
- [API de uso de recurso de locatário](azure-stack-tenant-resource-usage-api.md)
- [Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)
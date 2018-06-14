---
title: Cliente de cobrança e estorno na pilha do Azure | Microsoft Docs
description: Saiba como recuperar informações de uso do recurso da pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877476"
---
# <a name="usage-and-billing-in-azure-stack"></a>Uso e a cobrança na pilha do Azure

Este artigo descreve como os usuários de pilha do Azure são cobrados para uso de recursos. Você pode aprender como as informações de cobrança são acessadas para análise e chargeback.

Pilha do Azure coleta e agrupa os dados de uso para todos os recursos usados e encaminha esses dados para comércio do Azure. Comércio Azure cobra você para o uso da pilha do Azure da mesma forma como ele seria cobrar pelo uso do Azure.

Você também pode obter dados de uso e export-a para seu próprio cobrança ou custos backup do sistema usando um adaptador de cobrança ou exportá-lo para uma ferramenta de business intelligence, como o Microsoft Power BI e usá-lo para análise.


## <a name="usage-pipeline"></a>Pipeline de uso

Cada provedor de recursos na pilha do Azure emite os dados de uso por uso de recursos. Periodicamente, o serviço de uso (por hora e diariamente) agrega dados de uso e o armazena no banco de dados de uso. Os usuários e os operadores de pilha do Azure podem acessar os dados de uso armazenados por meio de APIs de uso de recursos de pilha do Azure. 

Se você tiver [registrado sua instância de pilha do Azure com o Azure](azure-stack-register.md), pilha do Azure está configurada para enviar os dados de uso para comércio do Azure. Depois que os dados são carregados no Azure, você pode acessá-lo por meio do portal de cobrança ou por meio de APIs de uso de recursos do Azure. Consulte o [relatórios de dados de uso](azure-stack-usage-reporting.md) tópico para saber mais sobre o uso de dados são relatados para o Azure.  

A imagem a seguir mostra os principais componentes do pipeline de uso: 

![Pipeline de uso](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>As informações de uso pode localizar e como?

Provedores de recursos da pilha do Azure, como computação, armazenamento e rede, geram dados de uso de hora em hora para cada assinatura. Os dados de uso contém informações sobre o recurso usado como nome de recurso, a assinatura usada e a quantidade usada. Para saber mais sobre os recursos de identificação de medidores, consulte o [perguntas frequentes sobre o uso da API](azure-stack-usage-related-faq.md) artigo.

Após a coleta de dados de uso é [relatados para o Azure](azure-stack-usage-reporting.md) para gerar uma lista, que pode ser visualizada por meio do portal de cobrança do Azure. 


> [!NOTE]
> Relatórios de dados de uso não é necessário para o Kit de desenvolvimento de pilha do Azure e para usuários do sistema de pilha do Azure integradas que no modelo de capacidade de licença. Para saber mais sobre licenciamento na pilha do Azure, consulte o [pacotes e preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) folha de dados.

O portal de cobrança do Azure mostra dados de uso para os recursos passíveis de cobrança. Além dos recursos passíveis de cobrança, a pilha do Azure captura dados de uso para um conjunto mais amplo de recursos, que pode ser acessado em seu ambiente de pilha do Azure por meio de APIs REST ou PowerShell. Operadores de pilha do Azure podem obter os dados de uso para todas as assinaturas de usuário. Usuários individuais só podem obter os detalhes de uso. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Relatório de uso do multilocatário provedores de serviços de nuvem

Multilocatário provedor de serviços de nuvem (CSP) que tem muitos clientes que usam a pilha do Azure talvez queira relatar o uso de cada cliente separadamente, para que o provedor pode cobrar o uso de diferentes assinaturas do Azure. 

Cada cliente vai ter sua identidade representada por um locatário diferente do Azure Active Directory (AD do Azure). A pilha do Azure dá suporte à atribuição de uma assinatura de CSP para cada locatário do AD do Azure. Você pode adicionar locatários e suas assinaturas para o registro do Azure pilha base. O registro base é feito para todas as pilhas do Azure. Se uma assinatura não está registrada para um locatário, o usuário ainda pode usar a pilha do Azure e seu uso será enviado para a assinatura usada para o registro de base. 


## <a name="next-steps"></a>Próximas etapas

[Registrar com a pilha do Azure](azure-stack-registration.md)

[Relatar dados de uso de pilha do Azure para o Azure](azure-stack-usage-reporting.md)

[Uso do provedor de recursos API](azure-stack-provider-resource-api.md)

[API de uso de recursos de locatário](azure-stack-tenant-resource-usage-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)
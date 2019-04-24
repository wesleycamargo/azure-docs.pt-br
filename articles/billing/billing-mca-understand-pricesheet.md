---
title: Compreender os termos na sua folha de preço para um contrato de cliente da Microsoft - Azure | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura da sua assinatura do Azure
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371352"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Compreender os termos na sua folha de preço para um contrato de cliente da Microsoft

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se você for um perfil de cobrança proprietário, colaborador, leitor ou Gerenciador de faturas você pode baixar a folha de preço da sua organização no portal do Azure. Ver [modo de exibição e download de sua organização de preço do](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Descrições em sua tabela de preços do contrato de cliente da Microsoft e termos detalhados

A seção a seguir descreve os termos importantes mostrados em sua tabela de preços do contrato de cliente da Microsoft.

| **Nome do Campo**   | **Descrição**   |
| --- | --- |
| billingAccountId  | Identificador exclusivo para a conta de cobrança.   |
| billingAccountName  | Nome da conta de cobrança.  |
| billingProfileId  | Identificador exclusivo para o perfil de cobrança.   |
| billingProfileName  | Nome do perfil de cobrança que está configurado para receber as faturas. Os preços na tabela de preços são associados este perfil de cobrança. |
| productOrderName  | Nome do plano de produto comprado. |
| serviceFamily  | Tipo de serviço do Azure. Por exemplo: Computação, análise, segurança |
| Produto  | Nome do produto acúmulo de encargos. Por exemplo: Banco de dados SQL básico banco de dados SQL Standard  |
| productId  | Identificador exclusivo para o produto cuja medidor é consumido. |
| unitOfMeasure  | Identifica as unidades de medida de cobrança para o serviço. Por exemplo, os serviços de computação são cobrados por hora. |
| meterId  | Identificador exclusivo para o medidor. |
| meterName  | Nome do medidor. O medidor representa o recurso implantável de um serviço do Azure. |
| meterCategory  | Nome da categoria de classificação para o medidor. Por exemplo, _serviços de nuvem_, _Networking_, etc. |
| meterType  |  Nome do tipo de medidor. |
| meterSubCategory  | Nome da categoria subclassificação medidor.  |
| meterRegion  | Nome da região em que o medidor para o serviço está disponível. Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter.    |
| tierId  | Identifica o tipo de preço quando aplicável. Isso funciona em conjunto com tierMinimumUnits para definir os preços em camadas quando os preços variam com base no número de unidades consumidas.    |
| tierMinimumUnits  | Define o limite inferior do intervalo de camada para os quais os preços são definidos. Por exemplo, se o intervalo é de 0 a 100, tierMinimumUnits seria 0.  |
| effectiveStartDate  | Quando o preço entra em vigor de data de início. |
| effectiveEndDate  | Data de término do preço em vigor. |
| unitPrice  | Preço por unidade no momento da cobrança (não o combinada preço efetivo) o mais específico para um nome de produto e o medidor de ordem.  Observação: O preço unitário não é o mesmo que o preço em vigor nos detalhes de uso downloads no caso de serviços com preços diferenciais entre camadas.  No caso de serviços com várias camadas de preços, o preço efetivo é uma taxa combinada em todas as camadas e não mostra um preço unitário específicos da camada. O preço combinado ou o preço efetivo é o preço líquido da abrangência de quantidade consumida em várias camadas (em que cada camada tem um preço de unidade específica). |
| basePrice  | O preço de mercado no momento em que o cliente fizer logon no ou o preço de mercado no momento o medidor de serviço é iniciado se ele for após o logon.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir e baixar o preço da sua organização](billing-ea-pricing.md)

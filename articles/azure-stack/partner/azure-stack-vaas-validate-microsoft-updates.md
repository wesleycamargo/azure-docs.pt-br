---
title: Validar as atualizações de software da Microsoft no Azure Stack validação como um serviço | Microsoft Docs
description: Aprenda a validar as atualizações de software da Microsoft com a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c90b81cdb6df5477d658d53dc93d0c3bbc47c1ff
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106436"
---
# <a name="validate-software-updates-from-microsoft"></a>Validar as atualizações de software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft lançará periodicamente atualizações para o software do Azure Stack. Essas atualizações são fornecidas para o Azure Stack coengineering parceiros. As atualizações são fornecidas com antecedência de publicamente disponíveis. Você pode verificar as atualizações em relação a sua solução e fornecer comentários à Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Aplicar atualização mensal

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Validações de atualização usam o mesmo fluxo de trabalho **validação de solução**.

## <a name="run-tests"></a>Executar testes

1. Validações de atualização usam o mesmo fluxo de trabalho **validação de solução**. 

2. Siga as instruções em [testes de executar a validação de solução](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Selecione os testes a seguir em vez disso:
    - Verificação de atualização mensal do Azure Stack
    - Mecanismo de simulação de nuvem

Você não precisa solicitar assinatura para validações de atualização de pacote.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
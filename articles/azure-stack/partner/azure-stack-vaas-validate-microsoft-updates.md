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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ad9d5057c18d316dcf3254dc57a3184c1b75fc50
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780824"
---
# <a name="validate-software-updates-from-microsoft"></a>Validar as atualizações de software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft lançará periodicamente atualizações para o software do Azure Stack. Essas atualizações são fornecidas para o Azure Stack coengineering parceiros. As atualizações são fornecidas com antecedência de publicamente disponíveis. Você pode verificar as atualizações em relação a sua solução e fornecer comentários à Microsoft.

Atualizações de software da Microsoft para o Azure Stack são designadas usando uma convenção de nomenclatura, por exemplo, 1803 indicando a atualização de março de 2018. Para obter informações sobre a política de atualização do Azure Stack, cadência e notas de versão estão disponíveis, consulte [política de manutenção do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Pré-requisitos

Antes de você executar o processo de atualização mensal em VaaS, você deve estar familiarizado com os seguintes itens:

- [Validação como um conceitos principais do serviço](azure-stack-vaas-key-concepts.md)
- [Teste de verificação do recurso interativo](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Testes necessários

Os testes a seguir devem ser executados na seguinte ordem para validação de software mensal:

1. Verificação de atualização mensal do Azure Stack
2. Mecanismo de simulação de nuvem

## <a name="validating-software-updates"></a>Validando atualizações de software

1. Criar um novo **validação de pacote** fluxo de trabalho.
1. Para os testes necessários acima, siga as instruções do [testes de validação do pacote executar](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consulte a seção abaixo para obter instruções adicionais sobre o **verificação de atualização de pilha do Azure mensais** de teste.

### <a name="apply-the-monthly-update"></a>Aplique a atualização mensal

1. Selecione um agente para executar testes em relação ao.
1. Agenda **verificação de atualização mensal do Azure Stack**.
1. Forneça o local para o pacote de extensão de OEM atualmente implantado no carimbo e o local para o pacote de extensão de OEM que será aplicado durante a atualização. Para configurar as URLs para esses pacotes, consulte [gerenciamento de pacotes para validação](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Siga as etapas na interface do usuário do agente selecionado.

Se você tiver perguntas ou dúvidas, entre em contato com [VaaS ajuda](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
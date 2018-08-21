---
title: Uma visão geral da validação como um serviço para o Azure Stack | Microsoft Docs
description: Uma visão geral da validação de pilha do Azure como um serviço, problemas conhecidos.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235180"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>O que é validação como um serviço para o Azure Stack?

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validação como um serviço (VaaS) é um serviço do Azure nativo, projetado para os parceiros de solução que são de engenharia conjunta ofertas do Azure Stack com a Microsoft. Parceiros de solução podem usar o serviço para verificar se suas soluções de atender aos requisitos da Microsoft e funcionam conforme o esperado com o Azure Stack.

O principal uso do VaaS é:

- Validar a novas soluções do Azure Stack
- Validar alterações no software do Azure Stack
- Obter parceiro de solução assinada digitalmente usados durante a implantação de pacotes
- Material de apoio para visualização do Azure Stack validação

## <a name="validate-new-azure-stack-solution"></a>Validar a nova solução do Azure Stack

Parceiros usam o fluxo de trabalho de validação de solução para verificar novas soluções do Azure Stack. A solução deve passar os testes de componentes de testes de Hardware Lab Kit (HKL) do Azure Stack necessários. Você só precisará executar o fluxo de trabalho duas vezes para cada nova solução: uma vez para a configuração de mínima e máxima.

Para obter mais informações, consulte [validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Validar alterações no software do Azure Stack

Parceiros usam o fluxo de trabalho de validação de pacote para verificar se sua solução funciona com a recente atualização de software do Azure Stack. No mínimo, o fluxo de trabalho de validação de pacote deve ser executado no ambiente de hardware recomendado pela Microsoft e, em uma solução em que o patch e atualização (P & U) eram usadas para aplicar a atualização. Você deve executar a validação do pacote na compilação da linha de base.

Para obter mais informações, consulte [validar as atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Obter solução assinada digitalmente os pacotes de parceiro

Além de validar as atualizações do Azure Stack, você pode usar o fluxo de trabalho de validação de pacote para verificar as atualizações para pacotes de personalização de OEM, que incluem os drivers de específicas de parceiro do Azure Stack, firmware e outro software usado durante a implantação do Azure Stack software. Implante o pacote que você está verificando a versão atual do Azure Stack software usando uma menos o mínimo dimensionados solução que terão suporte. O pacote atualizado deve ser carregado para o serviço antes de iniciar o teste. Se os testes forem bem-sucedidas, notificar vaashelp@microsoft.com. Informe o parceiro do Azure Stack que o pacote tiver concluído o teste e deve ser assinado digitalmente com a assinatura digital do Azure Stack. A Microsoft assina o pacote e notifica o parceiro do Azure Stack que o pacote está disponível para download no portal.

Para obter mais informações, consulte [pacotes de OEM validar](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Material de apoio para visualização do Azure Stack validação

Microsoft torna regularmente novos recursos disponíveis no Azure Stack. Como parte do processo de desenvolvimento para fornecer esses recursos de colocação no mercado, material de apoio do novo teste é disponibilizado no fluxo de trabalho de aprovação do teste. O fluxo de trabalho de aprovação de teste inclui material de apoio de teste dos outros fluxos de trabalho para permitir a execução de teste não oficial. Não use o fluxo de trabalho de aprovação do teste para enviar os resultados para aprovação. Use o fluxo de validações de validação e o pacote de solução obter aprovação oficial para sua solução.

## <a name="next-steps"></a>Próximas etapas

- Começar a usar, e [configurar sua validação como uma conta de serviço](azure-stack-vaas-validate-solution-new.md)

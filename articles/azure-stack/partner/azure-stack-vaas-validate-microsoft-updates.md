---
title: Validar as atualizações de software da Microsoft na validação do Azure Stack como um serviço | Microsoft Docs
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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235233"
---
# <a name="validate-software-updates-from-microsoft"></a>Validar as atualizações de software da Microsoft

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft lançará periodicamente atualizações para o software do Azure Stack. Essas atualizações são fornecidas para parceiros de engenharia conjunta do Azure Stack com antecedência sobre o que está sendo disponibilizado publicamente para que eles podem validar as atualizações em relação a suas soluções e fornecer comentários à Microsoft.

## <a name="test-an-existing-solution"></a>Testar uma solução existente

1. Entrar para o [portal de validação](https://azurestackvalidation.com).

2. Selecionar uma solução existente, em que a atualização da Microsoft foi implantada e selecione **iniciar** sobre o **validação de pacote** lado a lado.

    ![Validação de pacote](media/image3.png)

3. Insira o nome de validação.

4. Insira a URL para o pacote de OEM foi instalado na solução no momento da implantação. Use a URL para o pacote armazenado no serviço blob do Azure. Para obter mais informações, consulte [criar um blob de armazenamento do Azure para armazenar logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Selecione **carregar** para adicionar o arquivo de configuração de implantação. Consulte a [Validando uma nova solução de pilha do Azure](azure-stack-vaas-validate-solution-new.md) para obter informações sobre como carregar seu arquivo de configuração de implantação.

6. O arquivo de configuração de implantação, em seguida, deve ser personalizado com o arquivo de parâmetros de ambiente correto, consulte [parâmetros de ambiente](azure-stack-vaas-parameters.md#environment-parameters) para obter mais detalhes.

    > [!Note]   
    > O arquivo de configuração de implantação pode ser mais personalizado adicionando os parâmetros comuns de teste. Para obter mais informações, consulte [parâmetros comuns de fluxo de trabalho para a validação do Azure Stack como um serviço](azure-stack-vaas-parameters.md)

7. O nome de usuário e senha para o usuário do locatário, administrador de serviços e administrador de nuvem devem ser inseridos manualmente.

8. Forneça a URL para o blob de armazenamento do Azure para armazenar os logs de diagnóstico. Para obter mais informações, consulte [criar um blob de armazenamento do Azure para armazenar logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > Marcas descritivas podem ser inseridas para rotular o fluxo de trabalho.

10. Selecione **enviar** para salvar o fluxo de trabalho.

O fluxo de trabalho da solução é executado por aproximadamente 24 horas. Adicione um link para ou a instrução sobre a programação de testes. Desmarque na ferramenta.

Encontrar mais informações sobre como monitorar o progresso de uma validação de executar, consulte [monitore um teste ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).

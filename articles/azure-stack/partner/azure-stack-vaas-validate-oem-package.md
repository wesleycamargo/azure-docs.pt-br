---
title: Validar pacotes do fabricante original do equipamento (OEM) na validação do Azure Stack como um serviço | Microsoft Docs
description: Saiba como verificar pacotes do fabricante original do equipamento (OEM) com a validação como um serviço.
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
ms.openlocfilehash: cefa32c35df4e87d4d2b983ee8c4a16dc065e774
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160448"
---
# <a name="validate-oem-packages"></a>Validar pacotes de OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Você pode testar um novo pacote de OEM quando houve uma alteração no firmware ou drivers para uma validação de solução completa. Quando seu pacote tiver passado o teste, ele é assinado pela Microsoft. O teste deve conter o pacote de extensão de OEM atualizado com os drivers e firmware que passaram logotipo do Windows Server e testes PCS.

Todos os testes sejam concluídos dentro de 24 horas com resultado **bem-sucedida**. Se algum dos testes tiverem um resultado **falhou**, registre um bug no [Microsoft Collaborate](https://aka.ms/collaborate) e notificar a Microsoft enviando um email para [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Crie o OEM pacote assinado

1. Certifique-se de que a atualização mensal atual foi aplicada. Para a versão mais recente, consulte a versão mais recente no [documentação do Azure Stack operador > Visão geral > Notas de versão](https://docs.microsoft.com/azure/azure-stack/) .

    Atualizações de software da Microsoft para o Azure Stack são designadas usando uma convenção de nomenclatura, por exemplo, 1803 indicando a atualização de março de 2018. Para obter informações sobre a política de atualização do Azure Stack, cadência e notas de versão estão disponíveis, consulte [política de manutenção do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Verifique o status de integridade do sistema executando **AzureStack teste** conforme descrito em [executar um teste de validação para o Azure Stack. Corrija quaisquer erros e avisos antes de iniciar todos os testes.

2. No [portal de validação](https://azurestackvalidation.com), selecione uma solução existente. Se você não adicionou sua solução, consulte [adicionar uma nova solução](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Selecione **começar** sobre o **validações do pacote** lado a lado para iniciar um novo fluxo de trabalho.

    ![Validações do pacote](media/image3.png)

4.  Forneça um diagnóstico de cadeia de caracteres de conexão. Para obter instruções, consulte [configurar uma conta de armazenamento](azure-stack-vaas-set-up-account.md).

    Um pacote de extensão de OEM deve ser especificado para cada execução de validação de pacote. Especifique o pacote de OEM foi instalado na solução no momento da implantação do Azure Stack. Para obter instruções, consulte [criar um blob de armazenamento do Azure para armazenar logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Um arquivo JSON com as variáveis de ambiente deve ser usado para concluir a entrada para os campos obrigatórios para a execução evitar erros na entrada de dados. Para obter instruções, consulte [receber um arquivo de configuração em uma implantação do Azure Stack](azure-stack-vaas-parameters.md).

5. Execute os testes.

6. Quando todos os testes foi concluído com êxito, envie o nome da sua solução e pacote a validação para [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) para solicitar a assinatura do pacote.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).

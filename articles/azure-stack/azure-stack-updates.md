---
title: Gerenciar atualizações na visão geral do Azure Stack | Microsoft Docs
description: Saiba mais sobre o gerenciamento de atualizações para sistemas integrados do Azure Stack.
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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281566"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gerenciar atualizações na visão geral do Azure Stack

*Aplicável a Sistemas integrados do Azure Stack*

Pacotes de atualização da Microsoft para o Azure Stack integrado sistemas geralmente liberar em torno da quarta terça-feira de cada mês. Pergunte ao seu fabricante de equipamento original (OEM) sobre seu processo de notificação específica para garantir que as notificações de atualização atingir a sua organização. Você também pode verificar na biblioteca de documentação sob **visão geral** > **notas de versão** para obter informações sobre as versões que estão em suporte do Active Directory. 

Cada versão das atualizações de software da Microsoft é fornecido como um pacote de atualização única. Como um operador do Azure Stack, você pode importar, instalar e monitorar o progresso da instalação desses atualizar pacotes do portal do administrador. 

O fornecedor OEM também irá lançar atualizações, como atualizações de firmware e driver. Enquanto essas atualizações são entregues como pacotes separados por fornecedor, são importados, instalados e gerenciados da mesma forma como os pacotes de atualização da Microsoft.

Para manter seu sistema de suporte, você deve manter atualizado para um nível de versão específica do Azure Stack. Certifique-se de que você examine os [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> É possível aplicar os pacotes de atualização de pilha do Azure para o Kit de desenvolvimento do Azure Stack. Os pacotes de atualização são projetados para sistemas integrados. Para obter informações, consulte [reimplantar o ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>O provedor de recursos de atualização

O Azure Stack inclui um provedor de recursos de atualização que lida com a aplicação das atualizações de software da Microsoft. Esse provedor verifica que as atualizações são aplicadas a todos os hosts físicos, aplicativos do Service Fabric e tempos de execução e todas as máquinas virtuais de infraestrutura e seus serviços associados.

Como instalar atualizações, você pode exibir o status de alto nível como os destinos de processo de atualização de vários subsistemas no Azure Stack (por exemplo, hosts físicos e máquinas virtuais de infraestrutura).

## <a name="plan-for-updates"></a>Planejar atualizações de

É altamente recomendável que você notifica os usuários de qualquer operação de manutenção, e que você agende as janelas de manutenção normal durante o horário não comercial se possível. Operações de manutenção podem afetar a cargas de trabalho de locatário e operações do portal.

- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Usando o bloco de atualização para gerenciar atualizações

Gerenciar atualizações do portal do administrador. Como um operador do Azure Stack, você pode usar o bloco de atualização no painel:

- Exibir informações importantes, como a versão atual.
- instalar atualizações e monitorar o progresso.
- Examine o histórico de atualização para atualizações instaladas anteriormente.
- Exibir a versão atual do pacote OEM da nuvem
 
## <a name="determine-the-current-version"></a>Determinar a versão atual

Você pode exibir a versão atual do Azure Stack no quadro de atualização. Para abrir o bloco:

1. Abra o portal de administração do Azure Stack.
2. Selecione **Dashboard**. No **atualização** lado a lado, a versão atual é listada. 

    ![Atualizações de bloco no painel de controle padrão](./media/azure-stack-updates/image1.png)

    Por exemplo, na tela, a versão é 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalar atualizações e monitorar o progresso


1. Abra o portal de administração do Azure Stack.
2. Selecione **Dashboard**. Selecione o bloco de atualização.
3. Selecione **atualizar agora**.

    ![Detalhes da execução de atualização de pilha do Azure](media/azure-stack-updates/azure-stack-update-button.png)

4.  Você pode exibir o status de alto nível como o processo de atualização itera por meio de vários subsistemas no Azure Stack. Exemplo de subsistemas incluem hosts físicos, o Service Fabric, das máquinas virtuais de infraestrutura e serviços que fornecem os portais de administrador e o usuário. Durante o processo de atualização, o provedor de recursos de atualização relata detalhes adicionais sobre a atualização, como o número de etapas que tiveram êxito, bem como o número em andamento.

5. Selecione o **baixar logs completos** da atualização de folha de detalhes para baixar logs completos da execução.

    ![Detalhes da execução de atualização de pilha do Azure](media/azure-stack-updates/update-run-details.png)

6. Depois de concluído, o provedor de recursos de atualização fornece um **bem-sucedido** confirmação informando que o processo de atualização foi concluído e quanto tempo levou. A partir daí, você pode exibir informações sobre todas as atualizações, as atualizações disponíveis ou atualizações instaladas usando o filtro.

    ![Êxito de detalhes de execução de atualização de pilha do Azure](media/azure-stack-updates/update-success.png)

   Se a atualização falhar, a atualização de bloco relatórios **precisa de atenção**. Use o **baixar logs completos** para obter um status de alto nível em que a atualização pode ter falhado. A coleção de log do Azure Stack ajuda a facilitar o diagnóstico e solução de problemas.

## <a name="next-steps"></a>Próximas etapas

- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md) 
- [Gerenciamento de região no Azure Stack](azure-stack-region-management.md)

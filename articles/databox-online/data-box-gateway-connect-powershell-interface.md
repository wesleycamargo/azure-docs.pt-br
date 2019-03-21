---
title: Conectar e gerenciar o dispositivo de Gateway de caixa de dados do Microsoft Azure por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como conectar e, em seguida, gerenciar o Gateway da caixa de dados por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556508"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerenciar um dispositivo de Gateway de caixa de dados do Azure por meio do Windows PowerShell

Solução de Gateway da caixa de dados do Azure permite enviar dados pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo de Gateway da caixa de dados. Você pode usar o portal do Azure, interface do usuário da web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que usando a interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conectar-se à interface do PowerShell
- Iniciar uma sessão de suporte
- Criar um pacote de suporte
- Carregar um certificado
- Inicialize no ambiente de não-DHCP
- Exibir informações de dispositivo

> [!IMPORTANT]
> O Gateway de caixa de dados do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Iniciar uma sessão de suporte

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar um certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Inicialize no ambiente de não-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Exibir informações de dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Próximas etapas

- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.

---
title: Conectar e gerenciar o dispositivo de Gateway de caixa de dados do Microsoft Azure por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como conectar e, em seguida, gerenciar o Gateway da caixa de dados por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403486"
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

## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

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

---
title: Girar segredos na pilha do Azure | Microsoft Docs
description: Saiba como girar seus segredos na pilha do Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: 9b65a3cb5cdcc8a558e5c989026f2eee2f527bb5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Girar segredos na pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Atualize as senhas para os componentes da pilha do Azure em um ritmo regular.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Atualizando as senhas baseboard management controller (BMC)

Os controladores de gerenciamento da placa-base (BMC) monitoram o estado físico dos seus servidores. As especificações e instruções sobre como atualizar a senha do BMC variam com base no seu fornecedor de hardware do fabricante (OEM).

1. Atualize o BMC em seu servidor seguindo as instruções do OEM. A senha para cada BMC em seu ambiente deve ser o mesmo.
2. Abra um ponto de extremidade com privilégios em sessões de pilha do Azure. Para obter instruções, consulte [usando o ponto de extremidade com privilégios na pilha do Azure](azure-stack-privileged-endpoint.md).
3. Após o PowerShell prompt foi alterado para **[endereço IP ou ERCS VM name]: PS >** ou **[azs ercs01]: PS >**, dependendo do ambiente, execute `Set-BmcPassword` executando `invoke-command`. Passe a variável de sessão com privilégios de ponto de extremidade como um parâmetro.  
Por exemplo: 
    ```powershell
    $PEPSession = New-PSSession -ComputerName <ERCS computer name> -Credential <CloudAdmin credential> -ConfigurationName "PrivilegedEndpoint"  
    
    Invoke-Command -Session $PEPSession -ScriptBlock {
        param($password)
        set-bmcpassword -bmcpassword $password
    } -ArgumentList (<LatestPassword as a SecureString>) 
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança e a pilha do Azure, consulte [postura de segurança de infraestrutura do Azure pilha](azure-stack-security-foundations.md).
---
title: Conectar-se ao Azure Stack | Microsoft Docs
description: Saiba como se conectar ao Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993162"
---
# <a name="connect-to-azure-stack-development-kit"></a>Conectar ao Kit de desenvolvimento do Azure Stack

*Aplica-se a: Kit de desenvolvimento do Azure Stack*

Para gerenciar recursos, primeiro você deve conectar o Kit de desenvolvimento do Azure Stack. Neste artigo, descrevemos as etapas executadas para conectar-se para o kit de desenvolvimento. Você pode usar uma das seguintes opções de conexão:

* [Conexão de área de trabalho remota](#connect-with-remote-desktop). Quando você se conectar usando a Conexão de área de trabalho remota, um único usuário pode rapidamente se conectar o Kit de desenvolvimento.
* [Rede virtual privada (VPN)](#connect-with-vpn). Quando você se conectar usando uma VPN, vários usuários podem se conectar simultaneamente de clientes fora da infraestrutura do Azure Stack. Uma conexão VPN requer algumas configurações.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Conectar-se ao Azure Stack por meio de Conexão de área de trabalho remota

Um único usuário simultâneo pode gerenciar recursos no portal do operador ou o portal do usuário por meio de Conexão de área de trabalho remota.

1. Abrir Conexão de área de trabalho remota e conecte-se para o kit de desenvolvimento. O nome de usuário, insira **AzureStack\AzureStackAdmin**. Use a senha de operador que você especificou ao configurar o Azure Stack.  

2. No computador do kit de desenvolvimento, abra o Gerenciador do servidor. Selecione **servidor Local**, desmarque as **segurança reforçada do Internet Explorer** caixa de seleção e, em seguida, feche o Gerenciador do servidor.

3. Para abrir o [portal do usuário](azure-stack-key-features.md#portal), vá para https://portal.local.azurestack.external/. Entrar usando as credenciais do usuário. Para abrir o Azure Stack [portal do operador](azure-stack-key-features.md#portal), vá para https://adminportal.local.azurestack.external/. Entrar usando as credenciais do Azure Active Directory (Azure AD) que você especificou durante a instalação.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Conectar-se ao Azure Stack usando VPN

Você pode estabelecer um conexão VPN a um Kit de desenvolvimento do Azure Stack de túnel dividido. Você pode usar uma conexão VPN para acessar o portal de operador do Azure Stack, o portal do usuário e ferramentas instaladas localmente, como o Visual Studio e o PowerShell para gerenciar recursos do Azure Stack. Conectividade VPN é compatível com AD do Azure e implantações de serviços de Federação do Active Directory (AD FS). Conexões VPN tornam possível para vários clientes para se conectar ao Azure Stack ao mesmo tempo.

> [!NOTE]
> Uma conexão VPN não fornece conectividade a VM de infraestrutura do Azure Stack.

### <a name="prerequisites"></a>Pré-requisitos

1. Instale [do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md) no computador local.  
2. Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Configurar conectividade VPN

Para criar uma conexão VPN para o kit de desenvolvimento, abra o Windows PowerShell como administrador em seu computador local baseado em Windows. Em seguida, execute o script a seguir (o endereço IP e a senha valores para seu ambiente de atualização):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se a instalação for bem-sucedida, **azurestack** aparece na lista de conexões VPN.

![Conexões de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Conecte-se à instância do Azure Stack, usando um dos seguintes métodos:  

* Use o `Connect-AzsVpn ` comando:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando solicitado, o host do Azure Stack de confiança e instalar o certificado da **AzureStackCertificateAuthority** no repositório de certificados do computador local. (O prompt pode estar oculto por janela do PowerShell).

* No computador local, selecione **as configurações de rede** > **VPN** > **azurestack** > **conectar**. No prompt de logon, insira o nome de usuário (**AzureStack\AzureStackAdmin**) e sua senha.

### <a name="test-vpn-connectivity"></a>Testar Conectividade VPN

Para testar a conexão do portal, abra um navegador da web e, em seguida, vá para o portal do usuário (https://portal.local.azurestack.external/) ou o portal de operador (https://adminportal.local.azurestack.external/). Entrar e criar recursos.  

## <a name="next-steps"></a>Próximas etapas

[Tornar as máquinas virtuais disponíveis para seus usuários do Azure Stack](azure-stack-tutorial-tenant-vm.md)

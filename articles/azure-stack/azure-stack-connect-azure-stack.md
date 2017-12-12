---
title: Conecte-se a pilha do Azure | Microsoft Docs
description: Saiba como conectar-se a pilha do Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Para gerenciar recursos, primeiro você deve conectar o Kit de desenvolvimento de pilha do Azure. Neste artigo, descreveremos as etapas executadas para conectar-se para o kit de desenvolvimento. Você pode usar uma das seguintes opções de conexão:

* [Conexão de área de trabalho remota](#connect-with-remote-desktop). Quando você se conectar usando a Conexão de área de trabalho remota, um único usuário pode conectar-se rapidamente do kit de desenvolvimento.
* [Rede virtual privada (VPN)](#connect-with-vpn). Quando você se conectar usando uma VPN, vários usuários podem se conectar simultaneamente de clientes fora da infraestrutura do Azure pilha (requer a instalação).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Conecte-se a pilha do Azure usando a Conexão de área de trabalho remota
Um único usuário simultâneo pode gerenciar recursos no portal do operador ou o portal do usuário por meio de Conexão de área de trabalho remota.

1. Abrir Conexão de área de trabalho remota e conecte-se para o kit de desenvolvimento. O nome de usuário, digite **AzureStack\AzureStackAdmin**. Use a senha de operador que você especificou ao configurar a pilha do Azure.  

2. No computador do kit de desenvolvimento, abra o Gerenciador do servidor. Selecione **servidor Local**, limpe o **segurança reforçada do Internet Explorer** caixa de seleção e, em seguida, feche o Gerenciador do servidor.

3. Para abrir o [portal do usuário](azure-stack-key-features.md#portal), vá para https://portal.local.azurestack.external/. Entrar usando as credenciais do usuário. Para abrir a pilha do Azure [portal operador](azure-stack-key-features.md#portal), vá para https://adminportal.local.azurestack.external/. Entrar usando as credenciais do Azure Active Directory (AD do Azure) que você especificou durante a instalação.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Conectar a pilha do Azure por meio de VPN

Você pode estabelecer um túnel dividido conexão VPN para um Kit de desenvolvimento de pilha do Azure. Você pode usar uma conexão VPN para acessar o portal de operador de pilha do Azure, o portal do usuário e as ferramentas instaladas localmente, como Visual Studio e do PowerShell para gerenciar recursos da pilha do Azure. Conectividade VPN tem suporte no AD do Azure e em implantações de serviços de Federação do Active Directory (AD FS). Conexões VPN possibilitam que vários clientes conectem-se a pilha do Azure ao mesmo tempo. 

> [!NOTE] 
> Uma conexão VPN não fornece conectividade à infraestrutura do Azure pilha VMs. 

### <a name="prerequisites"></a>Pré-requisitos

1. Instalar [Azure pilha-compatível com o Azure PowerShell](azure-stack-powershell-install.md) no computador local.  
2. Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Configurar conectividade VPN

Para criar uma conexão VPN para o kit de desenvolvimento, abra o Windows PowerShell como administrador no computador local com base em Windows. Em seguida, execute o script a seguir (o endereço IP e a senha valores para o seu ambiente de atualização):

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

Conecte-se à instância de pilha do Azure usando um dos seguintes métodos:  

* Use o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando solicitado, o host do Azure pilha de confiança e instalar o certificado do **AzureStackCertificateAuthority** no repositório de certificados do computador local. (O prompt pode estar oculto por na janela do PowerShell.) 

* No computador local, selecione **as configurações de rede** > **VPN** > **azurestack** > **conectar**. No prompt de logon, digite o nome de usuário (**AzureStack\AzureStackAdmin**) e sua senha.

### <a name="test-vpn-connectivity"></a>Testar Conectividade VPN

Para testar a conexão do portal, abra um navegador da web e, em seguida, vá para o portal do usuário (https://portal.local.azurestack.external/) ou o portal de operador (https://adminportal.local.azurestack.external/). Entrar e criar recursos.  

## <a name="next-steps"></a>Próximas etapas

[Tornar as máquinas virtuais disponíveis para seus usuários de pilha do Azure](azure-stack-tutorial-tenant-vm.md)


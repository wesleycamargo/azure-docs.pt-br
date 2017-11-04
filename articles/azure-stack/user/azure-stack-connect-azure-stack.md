---
title: Conecte-se a pilha do Azure | Microsoft Docs
description: Saiba como conectar-se a pilha do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: sngun
ms.openlocfilehash: 914f2e5d10aa341cea5eba8c24c7c37610e6b626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Para gerenciar recursos, você deve conectar o Kit de desenvolvimento de pilha do Azure. Este tópico detalha as etapas necessárias para conectar-se para o kit de desenvolvimento. Você pode usar qualquer uma das seguintes opções de conexão:

* [Área de trabalho remota](#connect-with-remote-desktop): permite que um único usuário simultâneo conectar-se rapidamente do kit de desenvolvimento.
* [Rede virtual privada (VPN)](#connect-with-vpn): permite que vários usuários simultâneos se conectem de clientes fora da infraestrutura do Azure pilha (requer configuração).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Conecte-se a pilha do Azure com área de trabalho remota
Com uma conexão de área de trabalho remota, um único usuário simultâneo pode trabalhar com o portal para gerenciar recursos.

1. Abrir uma Conexão de área de trabalho remota e conecte-se para o kit de desenvolvimento. Digite **AzureStack\AzureStackAdmin** como o nome de usuário e a senha administrativa que você forneceu durante a instalação da pilha do Azure.  

2. No computador do kit de desenvolvimento, abra o Gerenciador de servidor, clique em **servidor Local**, desative a segurança aprimorada do Internet Explorer e, em seguida, feche o Gerenciador do servidor.

3. Para abrir o portal, navegue até (https://portal.local.azurestack.external/) e entre usando as credenciais do usuário.


## <a name="connect-to-azure-stack-with-vpn"></a>Conecte-se a pilha do Azure com VPN

Você pode estabelecer um túnel dividido conexão de rede Virtual privada (VPN) para um Kit de desenvolvimento de pilha do Azure. Por meio da conexão VPN, você pode acessar o portal do administrador, o portal do usuário e instaladas localmente ferramentas como o Visual Studio e do PowerShell para gerenciar recursos da pilha do Azure. Conectividade VPN tem suporte em ambos os Directory(AAD) ativa do Azure e Active Directory Federation Services(AD FS) com base em implantações. As conexões VPN permitem que vários clientes conectem-se a pilha do Azure ao mesmo tempo. 

> [!NOTE] 
> Essa conexão VPN não fornece conectividade à infraestrutura do Azure pilha VMs. 

### <a name="prerequisites"></a>Pré-requisitos

* Instalar [Azure pilha compatível do Azure PowerShell](azure-stack-powershell-install.md) no computador local.  
* Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurar conectividade VPN

Para criar uma conexão VPN para o kit de desenvolvimento, abra uma sessão do PowerShell com privilégios elevados no computador local com base em Windows e execute o script a seguir (certifique-se de atualizar os valores de endereço e a senha de IP para o seu ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se o conjunto de backup for bem-sucedida, você deve ver **azurestack** na sua lista de conexões VPN.

![Conexões de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Conecte-se à instância do Azure pilha usando qualquer um dos dois métodos a seguir:  

* Usando o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando solicitado, o host do Azure pilha de confiança e instalar o certificado do **AzureStackCertificateAuthority** no repositório de certificados do computador local. (o prompt pode aparecer por trás da janela de sessão do PowerShell). 

* Abra o seu computador local **as configurações de rede** > **VPN** > clique **azurestack** > **conectar**. No prompt de logon, digite o nome de usuário (AzureStack\AzureStackAdmin) e a senha.

### <a name="test-the-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a conexão do portal, abra um navegador da Internet e navegue até o portal do usuário (https://portal.local.azurestack.external/), entrar e criar recursos.  

## <a name="next-steps"></a>Próximas etapas




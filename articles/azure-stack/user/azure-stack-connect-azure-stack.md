---
title: Conectar-se ao Azure Stack | Microsoft Docs
description: Saiba como conectar o Azure Stack
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 21015d31a738d3ad57048fe4a703bf78dda7e40c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865763"
---
# <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Para gerenciar recursos, você deve conectar o Kit de desenvolvimento do Azure Stack. Este artigo detalha as etapas necessárias para conectar-se para o kit de desenvolvimento. Você pode usar qualquer uma das seguintes opções de conexão:

* [Área de trabalho remota](#connect-with-remote-desktop): permite que um único usuário simultâneo conectar-se rapidamente no kit de desenvolvimento.
* [Rede virtual privada (VPN)](#connect-with-vpn): permite que vários usuários simultâneos se conecta de clientes fora da infraestrutura do Azure Stack (requer configuração).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Conectar-se ao Azure Stack com área de trabalho remota
Com uma conexão de área de trabalho remota, um único usuário simultâneo pode trabalhar com o portal para gerenciar recursos.

1. Abra uma Conexão de área de trabalho remota e conecte-se para o kit de desenvolvimento. Insira **AzureStack\AzureStackAdmin** como o nome de usuário e a senha administrativa que você forneceu durante a instalação do Azure Stack.  

2. No computador do kit de desenvolvimento, abra o Server Manager, clique em **servidor Local**, desativar a segurança reforçada do Internet Explorer e, em seguida, feche o Gerenciador do servidor.

3. Para abrir o portal, navegue até (https://portal.local.azurestack.external/) e entre usando as credenciais do usuário.


## <a name="connect-to-azure-stack-with-vpn"></a>Conectar-se ao Azure Stack com VPN

Você pode estabelecer um conexão de rede Virtual privada (VPN) para um Kit de desenvolvimento do Azure Stack de túnel dividido. Por meio da conexão VPN, você pode acessar o portal de administrador, o portal do usuário e instalado localmente ferramentas como o Visual Studio e o PowerShell para gerenciar recursos do Azure Stack. Conectividade VPN tem suporte em ambos os Azure de Active Directory (AAD) e Active Directory Federation Services(AD FS) com base em implantações. As conexões de VPN permitem que vários clientes para se conectar ao Azure Stack ao mesmo tempo. 

> [!NOTE] 
> Essa conexão VPN não fornece conectividade a VM de infraestrutura do Azure Stack. 

### <a name="prerequisites"></a>Pré-requisitos

* Instale [compatível com Azure PowerShell do Azure Stack](azure-stack-powershell-install.md) no computador local.  
* Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurar conectividade VPN

Para criar uma conexão VPN para o kit de desenvolvimento, abra uma sessão do PowerShell com privilégios elevados do seu computador local baseado em Windows e execute o script a seguir (certifique-se de atualizar os valores de endereço e a senha IP para o seu ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

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

Se a instalação for bem-sucedida, você deve ver **azurestack** na sua lista de conexões VPN.

![Conexões de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

Conecte-se à instância do Azure Stack usando qualquer um dos dois métodos a seguir:  

* Usando o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando solicitado, o host do Azure Stack de confiança e instalar o certificado da **AzureStackCertificateAuthority** no repositório de certificados do computador local. (o prompt pode aparecer por trás da janela de sessão do PowerShell). 

* Abra o seu computador local **as configurações de rede** > **VPN** > clique em **azurestack** > **conectar**. No prompt de logon, insira o nome de usuário (AzureStack\AzureStackAdmin) e a senha.

### <a name="test-the-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a conexão do portal, abra um navegador da Internet e navegue até o portal do usuário (https://portal.local.azurestack.external/), entrar e criar recursos.  

## <a name="next-steps"></a>Próximas etapas




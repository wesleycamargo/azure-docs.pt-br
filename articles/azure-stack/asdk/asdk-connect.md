---
title: Conectar-se ao Azure Stack | Microsoft Docs
description: Saiba como conectar-se para o ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 42b67ae9b715670f1a0c6e0fed004487a52817d3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206456"
---
# <a name="connect-to-the-asdk"></a>Conectar-se para o ASDK

Para gerenciar recursos, você deve primeiro se conectar para o Azure Stack desenvolvimento ASDK (Kit). Neste artigo, descrevemos as etapas que você tomar para se conectar ao ASDK usando as opções de conexão a seguir:

* [Conexão de área de trabalho remota (RDP)](#connect-with-rdp). Quando você se conectar usando a Conexão de área de trabalho remota, um único usuário pode rapidamente se conectar o Kit de desenvolvimento.
* [Rede virtual privada (VPN)](#connect-with-vpn). Quando você se conectar usando uma VPN, vários usuários podem se conectar simultaneamente para os portais do Azure Stack de clientes fora da infraestrutura do Azure Stack. Uma conexão VPN requer algumas configurações.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Conectar-se ao Azure Stack usando o RDP

Um único usuário simultâneo pode gerenciar recursos no portal de administração do Azure Stack ou o portal do usuário por meio de Conexão de área de trabalho remota diretamente do computador host ASDK. 

> [!TIP]
> Essa opção também permite que você use RDP novamente enquanto estiver conectado ao computador host ASDK para entrar em máquinas virtuais criadas no computador host ASDK. 

1. Abrir Conexão de área de trabalho remota (mstc.exe) e conecte-se para o desenvolvimento kit computador IP endereço do host usando uma conta autorizada para fazer logon remotamente no computador de host ASDK. Por padrão, **AzureStack\AzureStackAdmin** tem permissões para a conexão remota com o computador host ASDK.  

2. No computador host do kit de desenvolvimento, abra o Gerenciador do servidor (ServerManager.exe). Selecione **servidor Local**, desative **configuração de segurança reforçada do IE**e feche o Gerenciador do servidor.

3. Entrar no portal de administração como **AzureStack\CloudAdmin** ou usar outras credenciais de operador do Azure Stack. É o endereço do portal de administração ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Entrar no portal do usuário, como **AzureStack\CloudAdmin** ou usar outras credenciais de usuário do Azure Stack. É o endereço do portal usuário ASDK [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Para obter mais informações sobre quando usar qual conta, consulte [Noções básicas de administração de ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Conectar-se ao Azure Stack usando VPN

Você pode estabelecer um conexão VPN a um computador de host ASDK para acessar os portais do Azure Stack e ferramentas instaladas localmente, como o Visual Studio e do PowerShell de túnel dividido. Usando conexões VPN, vários usuários podem se conectar ao mesmo tempo a recursos do Azure Stack hospedado pelo ASDK.

Conectividade VPN tem suporte para o AD do Azure e Active Directory implantações Directory Federation Services (AD FS).

> [!NOTE]
> Uma conexão VPN *não* fornecer conectividade para VMs do Azure Stack. Você não poderá RDP em VMs do Azure Stack enquanto conectados via VPN.

### <a name="prerequisites"></a>Pré-requisitos
Antes de configurar uma conexão VPN para o ASDK, verifique se que os seguintes pré-requisitos foram atendidos.

- Instale [do Azure Stack-compatível com o Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) no computador local.  
- Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Configurar conectividade VPN

Para criar uma conexão VPN para o ASDK, abra o PowerShell como administrador no seu computador local baseado em Windows. Em seguida, execute o script a seguir (o endereço IP e a senha valores para seu ambiente de atualização):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![Conexões de rede](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Conectar-se ao Azure Stack

  Conecte-se à instância do Azure Stack, usando um dos seguintes métodos:  

  * Use o `Connect-AzsVpn ` comando:
      
    ```PowerShell
    Connect-AzsVpn `
      -Password $Password
    ```

  * No computador local, selecione **as configurações de rede** > **VPN** > **azurestack** > **conectar**. No prompt de logon, insira o nome de usuário (**AzureStack\AzureStackAdmin**) e sua senha.

Na primeira vez que você se conectar, você será solicitado a instalar o certificado de raiz do Azure Stack da **AzureStackCertificateAuthority** no repositório de certificados do computador local. Esta etapa adiciona a autoridade de certificação (CA) ASDK à lista de hosts confiáveis. Clique em **Sim** para instalar o certificado.

![Certificado raiz](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > O prompt pode estar oculto por outros aplicativos ou na janela do PowerShell.

### <a name="test-vpn-connectivity"></a>Testar Conectividade VPN

Para testar a conexão do portal, abra um navegador da web e, em seguida, vá para o portal do usuário (https://portal.local.azurestack.external/) ou o portal de administração (https://adminportal.local.azurestack.external/). 

Entre com as credenciais de assinatura apropriada para criar e gerenciar recursos.  

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas](asdk-troubleshooting.md)

---
title: 'Tutorial: Integrar uma única floresta do AD ao Azure usando a senha hash sync (PHS) | Microsoft Docs'
description: Demonstra como configurar um ambiente de identidade híbrido usando a sincronização de senha.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 81c88c90ff24cb5e0ab143237fb08678067ea993
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46305535"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Tutorial: Integrar uma única floresta do AD usando a senha hash sync (PHS)

![Criar](media/tutorial-password-hash-sync/diagram.png)

O tutorial a seguir guiará você pela criação de um ambiente de identidade híbrido usando a sincronização de senha hash.  Esse ambiente pode ser usado para testes ou para se familiarizar mais com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
A seguir estão os pré-requisitos necessários para concluir este tutorial
- Um computador com [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  É aconselhável fazer isso em qualquer um uma [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou um [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) computador.
- Uma [adaptador de rede externo](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual para se comunicar com a internet.
- Uma [assinatura do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016

> [!NOTE]
> Este tutorial usa scripts do PowerShell para que você possa criar o ambiente do tutorial no menor tempo possível.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Você pode e deve alterar as variáveis para refletir seu ambiente.
>
>Os scripts usados criam um ambiente geral do Active Directory antes de instalar o Azure AD Connect.  Eles são relevantes para todos os tutoriais.
>
> Copia os scripts do PowerShell que são usados neste tutorial está disponíveis no Github [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que precisamos fazer para colocar o nosso ambiente de identidade híbrida em funcionamento é criar uma máquina virtual que será usada como nosso servidor local do Active Directory.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Concluir a implantação de sistema operacional
Para concluir a construção da máquina virtual, você precisa concluir a instalação do sistema operacional.

1. Gerenciador Hyper-V, clique duas vezes na máquina virtual
2. Clique no botão Iniciar.
3.  Você será solicitado a "Pressionar qualquer tecla para inicializar a partir de um CD ou DVD". Vá em frente e faça isso.
4. Na tela de inicialização do Windows Server, selecione seu idioma e clique em **Próximo**.
5. Clique em **instalar agora**.
6. Insira sua chave de licença e clique em **próxima**.
7. Marque * * aceitar os termos de licença e clique em **próxima**.
8. Selecione **personalizada: instalar somente o Windows (Avançado)**
9. Clique em **Avançar**
10. Quando a instalação estiver concluída, reinicie a máquina virtual, faça o login e execute as atualizações do Windows para garantir que a VM seja a mais atualizada.  Instale as atualizações mais recentes.

## <a name="install-active-directory-prerequisites"></a>Instalar os pré-requisitos do Active Directory
Agora que temos uma máquina virtual ativa, precisamos fazer algumas coisas antes de instalar o Active Directory.  Ou seja, precisamos renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de Administração de Servidor Remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Crie um ambiente do Windows Server AD
Agora que temos a VM criada e ela foi renomeada e tem um endereço IP estático, podemos instalar e configurar os Serviços de Domínio Active Directory.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Crie um usuário do Windows Server AD
Agora que temos nosso ambiente do Active Directory, precisamos de uma conta de teste.  Essa conta será criada em nosso ambiente de AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Criar um locatário do Azure AD
Agora, precisamos criar um locatário do Azure AD para que possamos sincronizar nossos usuários com a nuvem.  Para criar um novo locatário do Azure AD, faça o seguinte.

1. Navegue até o [Portal do Azure](https://portal.azure.com) e faça login com uma conta que tenha uma assinatura do Azure.
2. Selecione o **adição (+)** e pesquise **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados da pesquisa.
4. Selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/create1.png)</br>
5. Fornecer um **nome da organização** juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Isto irá criar o seu diretório.
6. Depois que isso for concluído, clique no link **aqui** para gerenciar o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que temos um locatário do Azure AD, criaremos uma conta de administrador global.  Essa conta é usada para criar a conta do Azure AD Connector durante a instalação do Azure AD Connect.  A conta do Azure AD Connector é usada para gravar informações no Azure AD.   Para criar a conta de administrador global, faça o seguinte.

1.  Em **Gerenciar**, selecione **Usuários**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **Todos os usuários** e, em seguida, selecione **+ Novo usuário**.
3.  Forneça um nome e um nome de usuário para este usuário. Este será seu administrador global para o locatário. Você também desejará alterar a **função de Diretório** para **Administrador global.** Você também pode mostrar a senha temporária. Quando você terminar, selecione **criar**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Depois que isso for concluído, abra um novo navegador da Web e faça login em myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a senha do administrador global para algo que você irá lembrar.

## <a name="download-and-install-azure-ad-connect"></a>Baixe e instale o Azure AD Connect
Agora é hora de baixar e instalar o Azure AD Connect.  Uma vez instalado, vamos executar a instalação expressa.  Faça o seguinte:

1. Baixar [do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi**e clique duas vezes nessa opção.
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.  
4. Na tela de configurações expressas, clique em **Usar configurações expressas**.</br>  
![Criar](media/tutorial-password-hash-sync/express1.png)</br>
5. Na tela do Azure AD a conectar, digite o nome de usuário e a senha do administrador global do Azure AD. Clique em **Próximo**.  
6. Na tela Conectar AD DS, digite o nome de usuário e senha para uma conta de administrador corporativa. Clique em **Próximo**.  
7. Na tela Pronto para configurar, clique em **Instalar**.
8. Quando a instalação for concluída, clique em **Sair**.
9. Após a conclusão da instalação, saia e entre novamente antes de usar o Gerenciador do Serviço de Sincronização ou o Editor de Regras de Sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificar se os usuários foram criados e a sincronização está ocorrendo
Verificamos agora que os usuários que tínhamos em nosso diretório local foram sincronizados e agora existem no locatário do Azure AD.  Lembre-se de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários estiverem sincronizados, faça o seguinte.


1. Navegue até o [Portal do Azure](https://portal.azure.com) e faça login com uma conta que tenha uma assinatura do Azure.
2. À esquerda, selecione **do Active Directory do Azure**
3. Em **Gerenciar**, selecione **Usuários**.
4. Verifique se você vê os novos usuários em nosso inquilino</br>
![Sincronização](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Teste fazendo login com um de nossos usuários

1.  Navegue até [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Faça login com uma conta de usuário que foi criada em nosso novo locatário.  Você precisará fazer login usando o seguinte formato: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para fazer login no local.</br>
![Verificar](media/tutorial-password-hash-sync/verify1.png)</br>

Agora você configurou com êxito um ambiente de identidade híbrido que pode ser usado para testar e se familiarizar com o que o Azure tem a oferecer.

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|

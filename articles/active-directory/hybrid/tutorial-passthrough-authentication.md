---
title: 'Tutorial: Integrar uma única floresta do AD ao Azure usando a autenticação de passagem (PTA) | Microsoft Docs'
description: Demonstra como configurar um ambiente de identidade híbrido usando a autenticação de passagem.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6154164aadd8ed508bfae8de8b6f3c8255111a73
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46308065"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-pass-through-authentication-pta"></a>Tutorial: Integrar uma única floresta do AD usando a autenticação de passagem (PTA)

![Criar](media/tutorial-passthrough-authentication/diagram.png)

O tutorial a seguir o orientará na criação de um ambiente de identidade híbrido usando a autenticação de passagem.  Esse ambiente pode ser usado para testes ou para se familiarizar mais com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
A seguir estão os pré-requisitos necessários para concluir este tutorial
- Um computador com o [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  Sugere-se fazer isso em um computador [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou do [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Uma [assinatura do Azure](https://azure.microsoft.com/free)
- - Um [adaptador de rede externa](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual se comunique com a Internet.
- Uma cópia do Windows Server 2016
- Um [domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md) que pode ser verificado

> [!NOTE]
> Este tutorial usa scripts do PowerShell para que você possa criar o ambiente do tutorial no menor tempo possível.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Você pode e deve alterar as variáveis para refletir seu ambiente.
>
>Os scripts usados criam um ambiente geral do Active Directory antes de instalar o Azure AD Connect.  Eles são relevantes para todos os tutoriais.
>
> Cópias dos scripts do PowerShell que são usados neste tutorial estão disponíveis no Github [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que precisamos fazer para colocar o nosso ambiente de identidade híbrida em funcionamento é criar uma máquina virtual que será usada como nosso servidor local do Active Directory.  

>[!NOTE]
>Se você nunca executou um script no PowerShell em sua máquina host, precisará executar `Set-ExecutionPolicy remotesigned` e dizer sim no PowerShell, antes de executar os scripts.

Faça o seguinte:

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
4. Na tela de inicialização da Windows Server selecione seu idioma e clique em **próxima**.
5. Clique em **instalar agora**.
6. Digite sua chave de licença e clique em **Próximo**.
7. Marque * * aceitar os termos de licença e clique em **próxima**.
8. Selecione **Personalizado: Instalar Somente no Windows (Avançado)**
9. Clique em **Avançar**
10. Quando a instalação estiver concluída, reinicie a máquina virtual, faça o login e execute as atualizações do Windows para garantir que a VM seja a mais atualizada.  Instale as atualizações mais recentes.

## <a name="install-active-directory-prerequisites"></a>Instale os pré-requisitos do Active Directory
Agora que temos uma máquina virtual ativa, precisamos fazer algumas coisas antes de instalar o Active Directory.  Ou seja, precisamos renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de Administração de Servidor Remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Executar `Set-ExecutionPolicy remotesigned` e dizer Sim para todos os [A].  Pressione Enter.
3. Execute o seguinte script.

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

## <a name="create-a-windows-server-ad-user"></a>Criar um usuário do AD do Windows Server
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
2. Selecione o ícone **plus (+)** e procure **Active Directory do Azure**.
3. Selecione **Azure Active Directory** nos resultados da pesquisa.
4. Selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/create1.png)</br>
5. Forneça um **nome para a organização** junto com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Isto irá criar o seu diretório.
6. Depois que isso for concluído, clique no link **aqui** para gerenciar o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que temos um locatário do Azure AD, criaremos uma conta de administrador global.  Essa conta é usada para criar a conta do Azure AD Connector durante a instalação do Azure AD Connect.  A conta do Azure AD Connector é usada para gravar informações no Azure AD.   Para criar o conta de administrador global, faça o seguinte.

1.  Em **Gerenciar**, selecione **Usuários**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **Todos os usuários** e, em seguida, selecione **+ Novo usuário**.
3.  Forneça um nome e um nome de usuário para este usuário. Este será seu administrador global para o locatário. Você também desejará alterar a **função de Diretório** para **Administrador global.** Você também pode mostrar a senha temporária. Quando você terminar, selecione **criar**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Depois que isso for concluído, abra um novo navegador da Web e faça login em myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a senha do administrador global para algo que você irá lembrar.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Adicionar o nome de domínio personalizado ao diretório
Agora que temos um locatário e um administrador global, precisamos adicionar nosso domínio personalizado para que o Azure possa verificá-lo.  Faça o seguinte:

1. De volta ao [portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview), certifique-se de fechar o blade **All Users**.
2. À esquerda, selecione **Personalizar nomes de domínio**.
3. Selecione **Adicionar domínio personalizado**.</br>
![Personalizado](media/tutorial-federation/custom1.png)</br>
4. Em **nomes de domínio personalizados**, insira o nome do seu domínio personalizado na caixa e clique em **Adicionar domínio**.
5. Na tela de nome de domínio personalizado, você será fornecido com informações TXT ou MX.  Essas informações devem ser adicionadas às informações de DNS do registrador de domínios em seu domínio.  Portanto, você precisa acessar o registrador de domínios e inserir as informações de TXT ou MX nas configurações de DNS do seu domínio.  Isso permitirá que o Azure verifique seu domínio.  Isso pode levar até 24 horas para o Azure verificar isso.  Para obter mais informações, consulte a documentação [adicionar um domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md).</br>
![Personalizado](media/tutorial-federation/custom2.png)</br>
6. Para garantir que ele é verificado, clique no botão Verificar.</br>
![Personalizado](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Baixe e instale o Azure AD Connect
Agora é hora de baixar e instalar o Azure AD Connect.  Uma vez instalado, vamos executar a instalação expressa.  Faça o seguinte:

1. Baixar [do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi**e clique duas vezes nessa opção.
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.  
4. Na tela Configurações Express, clique em **Personalizar**.  
5. Na tela Instalar componentes necessários. Clique em **Instalar**.  
6. Na tela Login do usuário, selecione **Autenticação de passagem** e **Habilite o logon único** e clique em **Próximo**.</br>
![PTA](media/tutorial-passthrough-authentication/pta1.png)</b>
7. Na tela Conectar-se ao Azure AD, insira o nome de usuário e a senha do administrador global que criamos acima e clique em **Avançar**.
2. Na tela diretórios conectar, clique em **Adicionar diretório**.  Em seguida, selecione **Criar nova conta do AD**, insira o nome de usuário e senha contoso \ Administrator e clique em **OK**.
3. Clique em **Próximo**.
4. Na tela de configuração de login do AD do Azure, selecione **Continuar sem corresponder a todos os sufixos UPN aos domínios verificados** e clique em **Avançar.**
5. Na tela de filtragem de UO e domínio, clique em **próxima**.
6. Na tela Identificando exclusivamente seus usuários, clique em **Próximo**.
7. Na tela Filtrar usuários e dispositivos, clique em **Próximo**.
8. Na tela de recursos opcionais, clique em **próxima**.
9. Na página Habilitar credenciais de logon único, insira o nome de usuário e senha contoso \ Administrator e clique em **Avançar.**
10. Na tela Pronto para configurar, clique em **Instalar**.
11. Quando a instalação for concluída, clique em **Sair**.
12. Após a conclusão da instalação, saia e entre novamente antes de usar o Gerenciador do Serviço de Sincronização ou o Editor de Regras de Sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificar se os usuários foram criados e a sincronização está ocorrendo
Verificamos agora que os usuários que tínhamos em nosso diretório local foram sincronizados e agora existem no locatário do Azure AD.  Esteja ciente de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários estão sincronizados, faça o seguinte.


1. Navegue até o [Portal do Azure](https://portal.azure.com) e faça login com uma conta que tenha uma assinatura do Azure.
2. À esquerda, selecione **do Active Directory do Azure**
3. Em **Gerenciar**, selecione **Usuários**.
4. Verifique se você vê os novos usuários em nosso locatário ![Synch](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Conectando-se com um dos nossos usuários de teste

1.  Navegue até [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Faça login com uma conta de usuário que foi criada em nosso novo locatário.  Você precisará fazer login usando o seguinte formato: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para fazer login no local.
![Verificar](media/tutorial-password-hash-sync/verify1.png)

Agora você configurou com êxito um ambiente de identidade híbrido que pode ser usado para testar e se familiarizar com o que o Azure tem a oferecer.

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Autenticação de passagem](how-to-connect-pta.md)

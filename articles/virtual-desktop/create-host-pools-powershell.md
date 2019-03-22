---
title: Criar um pool de host com o PowerShell (visualização) – Azure
description: Como criar um pool de host na área de trabalho Virtual do Windows com os cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 4b65d7614db94a9cc3fdca3f4b784c2c84ebaef8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318533"
---
# <a name="create-a-host-pool-with-powershell-preview"></a>Criar um pool de host com o PowerShell (versão prévia)

Pools de host são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de locatário (visualização) da área de trabalho Virtual do Windows. Cada pool de host pode conter um grupo de aplicativos que os usuários podem interagir com o que seria em uma área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Use seu cliente do PowerShell para criar um pool de host

Primeiro, [Baixe e importe o módulo do Windows PowerShell de área de trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) usar em sua sessão do PowerShell se você ainda não fez isso.

Execute o seguinte cmdlet para entrar no ambiente de área de trabalho Virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Depois disso, execute o seguinte cmdlet para definir o contexto para o grupo de locatário. Se você não tiver o nome do grupo de locatário, seu locatário é mais provável em "Grupo de locatário padrão", portanto, você pode ignorar esse cmdlet.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Em seguida, execute este cmdlet para criar um novo pool de host em seu locatário de área de trabalho Virtual do Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o cmdlet seguinte para criar um token de registro para autorizar um host de sessão ingressam no pool de host e salvá-lo para um novo arquivo no computador local. Você pode especificar quanto tempo o token de registro é válido usando o parâmetro - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar usuários do Active Directory do Azure para o grupo de aplicativo da área de trabalho padrão para o pool de host.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O **RdsAppGroupUser adicionar** cmdlet não oferece suporte a adição de grupos de segurança e só adiciona um usuário por vez para o grupo de aplicativos. Se você quiser adicionar vários usuários para o grupo de aplicativos, execute novamente o cmdlet com os nomes de entidade de usuário apropriado.

Execute o seguinte cmdlet para exportar o token de registro para uma variável, que você usará posteriormente no [registrar as máquinas virtuais ao pool de área de trabalho Virtual do Windows de host](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de host

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao pool de host de área de trabalho Virtual do Windows.

Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual de uma imagem de galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual de uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual a partir de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Prepare as máquinas virtuais de instalações de agentes da área de trabalho Virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes da área de trabalho Virtual do Windows e registrar as máquinas virtuais ao pool de host de área de trabalho Virtual do Windows:

- Faça o ingresso no domínio o computador. Isso permite que os usuários recebidos de área de trabalho Virtual do Windows a ser mapeado da sua conta do Active Directory do Azure para sua conta do Active Directory e com êxito o acesso permitido para a máquina virtual.
- Você deve instalar a função de Host de sessão de área de trabalho remota (RDSH) (versão prévia) se a máquina virtual estiver executando um sistema operacional do Windows Server. A função RDSH permite que os agentes da área de trabalho Virtual do Windows seja instalado corretamente.

Para com êxito ingresso no domínio, faça o seguinte em cada máquina virtual:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Na máquina virtual, inicie **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar as configurações**e, em seguida, selecione **alteração...**
4. Selecione **domínio** e, em seguida, insira o domínio do Active Directory em uma rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para ingressar computadores em domínio.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registre as máquinas virtuais ao pool de host de área de trabalho Virtual do Windows

Registrar as máquinas virtuais a um pool de host de área de trabalho Virtual do Windows é tão simple quanto instalar os agentes da área de trabalho Virtual do Windows.

Para registrar os agentes da área de trabalho Virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Baixe e instale o agente de área de trabalho Virtual do Windows.
   - Baixe o [agente da área de trabalho Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - O instalador baixado com o botão direito, selecione **propriedades**, selecione **Unblock**, em seguida, selecione **Okey**. Isso permitirá que seu sistema para o instalador de confiança.
   - Execute o instalador. Quando o instalador solicita o token de registro, insira o valor que você obteve o **RdsRegistrationInfo exportação** cmdlet.
3. Baixe e instale o Windows Virtual Desktop agente carregador de inicialização.
   - Baixe o [carregador de inicialização de agente da área de trabalho Virtual Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - O instalador baixado com o botão direito, selecione **propriedades**, selecione **Unblock**, em seguida, selecione **Okey**. Isso permitirá que seu sistema para o instalador de confiança.
   - Execute o instalador.
4. Instalar ou ativar a pilha de área de trabalho Virtual do Windows lado a lado. As etapas serão diferentes dependendo de qual versão do sistema operacional da máquina virtual usa.
   - Se SO do sua máquina virtual Windows Server 2016:
     - Baixe o [pilha de lado a lado da área de trabalho Virtual do Windows](https://go.microsoft.com/fwlink/?linkid=2084270).
     - O instalador baixado com o botão direito, selecione **propriedades**, selecione **Unblock**, em seguida, selecione **Okey**. Isso permitirá que seu sistema para o instalador de confiança.
     - Execute o instalador.
   - Se o sistema de operacional da máquina virtual for Windows 10 1809 ou posterior ou Windows Server 2019 ou posterior:
     - Baixe o [script](https://go.microsoft.com/fwlink/?linkid=2084268) para ativar a pilha de lado a lado.
     - O script baixado com o botão direito, selecione **propriedades**, selecione **Unblock**, em seguida, selecione **Okey**. Isso permitirá que seu sistema para o script de confiança.
     - Dos **inicie** menu, procure o Windows PowerShell ISE, clique duas vezes e selecione **executar como administrador**.
     - Selecione **arquivo**, em seguida, **abra...** e, em seguida, localize o script do PowerShell no arquivo de download e abri-lo.
     - Selecione o botão verde play para executar o script.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um pool de host, é hora de preenchê-lo com RemoteApps (visualização). Para saber mais sobre como gerenciar aplicativos na área de trabalho Virtual do Windows, consulte o tutorial de grupos de aplicativo de gerenciar.

> [!div class="nextstepaction"]
> [Gerenciar o tutorial de grupos de aplicativo](./manage-app-groups.md)

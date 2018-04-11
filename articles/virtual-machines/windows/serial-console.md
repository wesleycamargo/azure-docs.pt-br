---
title: Console serial da máquina virtual do Azure | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 2ff0dcba0912461d8528fc76c7c67d90febc0324
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Console serial da máquina virtual (visualização) 


O console serial da máquina virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais Linux e Windows. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornece acesso à máquina virtual e não está relacionada à rede da máquina virtual/estado do sistema operacional. No momento, o acesso ao console serial para uma máquina virtual pode ser obtido somente por meio do portal do Azure e é permitido apenas para os usuários que têm acesso de Colaborador de VM ou superior à máquina virtual. 

> [!Note] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para saber mais, confira [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Atualmente, este serviço está em **visualização pública**, e o acesso ao console serial para máquinas virtuais está disponível para regiões globais do Azure. Neste ponto, o console serial não está disponível por meio de Azure Government, Azure Germany e Azure China cloud.

 

## <a name="prerequisites"></a>pré-requisitos 

* A máquina virtual deve ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados 
* A conta que usa o console serial deve ter a [função de Colaborador](../../active-directory/role-based-access-built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Abra o console serial
o console serial de máquinas virtuais só pode ser acessado por meio do [portal do Azure](https://portal.azure.com). A seguir, as etapas para acessar o console serial para máquinas virtuais por meio do portal 

  1. Abrir o portal do Azure
  2. No menu à esquerda, selecione as máquinas virtuais.
  3. Clique na VM na lista. A página de visão geral para a VM será aberta.
  4. Role para baixo até a seção Suporte + Solução de Problemas e clique na opção de console serial (visualização). Um novo painel com o console serial será aberto e iniciará a conexão.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Desabilitar recurso
A funcionalidade do console serial pode ser desativada para VMs específicas desabilitando a configuração de diagnóstico de inicialização da VM.

## <a name="serial-console-security"></a>Segurança de console serial 

### <a name="access-security"></a>Segurança de acesso 
O acesso ao console serial é limitado aos usuários que têm acesso de [Colaboradores de VM](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) ou superior para a máquina virtual. Se o locatário do AAD exigir a Autenticação Multifator, o acesso ao console serial também exigirá MFA, pois seu acesso é por meio do [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados são enviados criptografados durante a transmissão.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.  

>[!CAUTION] 
Embora nenhuma senha de acesso para o console seja registrada, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuários ou qualquer outra forma de PII (Informações de Identificação Pessoal), eles serão gravados nos logs de diagnóstico de inicialização da máquina virtual, juntamente com todo o outro texto visível, como parte da implementação da funcionalidade de scrollback do console serial. Esses logs são circulares, e somente pessoas com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, recomendamos que você siga a prática recomendada de usar o console do Desktop Remoto para qualquer item que envolva segredos e/ou PII. 

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito o acesso à mesma máquina virtual, o primeiro usuário será desconectado, e o segundo usuário será conectado de maneira semelhante ao primeiro usuário, aguardando e saindo do console físico, com um novo usuário aguardando.

>[!CAUTION] 
Isso significa que o usuário que é desconectado não será fará logoff! A capacidade de impor um logoff após a desconexão (via SIGHUP ou um mecanismo semelhante) ainda está em nossos planos. Para o Windows, há um tempo limite automático habilitado no SAC. Porém, para Linux, você pode definir a configuração de tempo limite de terminal. 


## <a name="accessing-serial-console-for-windows"></a>Acessar o console serial para Windows 
As imagens mais recentes do Windows Server no Azure terão o [Console de Administração Especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC tem suporte a versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7). Para habilitar o console Serial para máquinas virtuais do Windows criado usando Feb2018 ou imagens inferiores, use as etapas a seguir: 

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Reinicie o sistema para que o console do SAC esteja habilitado

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário SAC pode ser habilitado offline também, 

1. Anexe o disco do Windows que você deseja o SAC configurado para um disco de dados em VM existente. 
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Como saber se o SAC está habilitado ou não 

Se o [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver habilitado, o console serial não mostrará o prompt do SAC. Ele pode mostrar uma informações de integridade da VM em alguns casos ou estará em branco.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Habilitar menu de inicialização para exibir no console serial 

Se você precisar habilitar os prompts do carregador de inicialização do Windows para exibição no console serial, poderá adicionar as seguintes opções adicionais para o carregador de inicialização do Windows.

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie o sistema para que o menu de inicialização seja habilitado

> [!NOTE] 
> Nesse suporte de ponto para a funções de chave não está habilitado, se você precisar de opções de inicialização avançadas use bcdedit /set {current} onetimeadvancedoptions, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para obter mais detalhes

## <a name="windows-commands---cmd"></a>Comandos do Windows - CMD 

Esta seção inclui exemplos de comandos para executar tarefas comuns em cenários nos quais são necessários utilizar o SAC para acessar a VM, por exemplo, quando precisar solucionar falhas de conexão do RDP.

O SAC foi incluído em todas as versões do Windows desde o Windows Server 2003, mas é desabilitado por padrão. O SAC depende de `sacdrv.sys` driver do kernel, o `Special Administration Console Helper` serviço (`sacsvr`) e o processo `sacsess.exe`. Para obter mais informações, consulte [Ferramentas e configurações dos Serviços de Gerenciamento de Emergência](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

O SAC permite conectar-se ao SO via porta serial. Ao inicializar o CMD a partir do SAC, `sacsess.exe` inicializa `cmd.exe` no SO em execução. É possível ver que no Gerenciador de Tarefas, se o RDP para a VM for ao mesmo tempo você será conectado ao SAC via recurso de console serial. O CMD que você acessa via SAC é o mesmo `cmd.exe` usado quando conectado via RDP. Todos os mesmos comandos e ferramentas estão disponíveis, incluindo a capacidade de inicializar o PowerShell a partir dessa instância do CMD. Essa é uma grande diferença entre o SAC e o WinRE (Ambiente de Recuperação do Windows) em que o SAC permite gerenciar o SO em execução, onde o WinRE é inicializado em um SO mínimo e diferente. Embora as VMs do Azure não forneçam suporte à capacidade de acessar o WinRE, com o recurso do console serial, as VMs do Azure podem ser gerenciadas por meio do SAC.

Como o SAC está limitado a um buffer de tela de 80 x 24 sem scrollback, adicione `| more` aos comandos para exibir a saída de uma página por vez. Use `<spacebar>` para ver a próxima página ou `<enter>` para ver a próxima linha.  

`SHIFT+INSERT` é o atalho de pasta para a janela do console serial.

Por causa do buffer de tela limitado do SAC, comandos mais longos podem ser mais fáceis de serem digitados em um editor de texto local e depois colados no SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar as configurações do registro do Windows
#### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A segunda chave (em \Policies) só existirá se a configuração da política de grupo relevante estiver configurada.

#### <a name="enable-rdp"></a>Habilitar o RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A segunda chave (em \Policies) só seria necessária se a configuração da política de grupo relevante tivesse sido configurada. O valor será regravado na próxima atualização de política de grupo, se estiver configurado na política de grupo.

### <a name="manage-windows-services"></a>Gerenciar Serviços do Windows

#### <a name="view-service-state"></a>Exibir estado do serviço
`sc query termservice`
####  <a name="view-service-logon-account"></a>Exibir conta de logon do serviço
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Definir conta de logon do serviço 
`sc config termservice obj= "NT Authority\NetworkService"`

Um espaço é necessário após o sinal de igual.
#### <a name="set-service-start-type"></a>Definir o tipo de inicialização do serviço
`sc config termservice start= demand` 

Um espaço é necessário após o sinal de igual. Valores de inicialização possíveis incluem `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Definir dependências de serviço
`sc config termservice depend= RPCSS`

Um espaço é necessário após o sinal de igual.
#### <a name="start-service"></a>Iniciar serviço
`net start termservice`

ou o

`sc start termservice`
#### <a name="stop-service"></a>Parar serviço
`net stop termservice`

ou o

`sc stop termservice`
### <a name="manage-networking-features"></a>Gerenciar recursos de Rede
#### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Mostrar configuração do IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Habilitar NIC
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

VMs do Azure sempre devem ser configuradas no SO convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa DHCP para fornecer o IP estático à VM.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Ping de porta  
Instalar o cliente Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testar a conectividade

`telnet bing.com 80`

Para remover o cliente Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Quando limitado a métodos disponíveis no Windows por padrão, o PowerShell pode ser uma abordagem melhor para testar a conectividade da porta. Veja a seção PowerShell abaixo para exemplos.
#### <a name="test-dns-name-resolution"></a>Testar a resolução de nomes DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Mostrar regra de Firewall do Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Desabilitar Firewall do Windows
`netsh advfirewall set allprofiles state off`

É possível usar esse comando ao solucionar problemas para excluir temporariamente o Firewall do Windows. Ele será habilitado na próxima reinício ou quando habilitar usando o comando abaixo. Não pare o serviço do Firewall do Windows (MPSSVC) ou o BFE (Mecanismo de Filtragem Base) como forma de excluir o Firewall do Windows. Parar o MPSSVC ou BFE resultará no bloqueio de toda a conectividade.
#### <a name="enable-windows-firewall"></a>Habilitar Firewall do Windows
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
#### <a name="create-local-user-account"></a>Criar conta de usuário local
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Adicionar usuário local a grupo local
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Verificar se a conta do usuário está habilitada
`net user <username> | find /i "active"`

As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
#### <a name="enable-user-account"></a>Habilitar conta de usuário
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Exibir propriedades da conta de usuário
`net user <username>`

Exemplo de linhas de interesse de uma conta de administrador local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Exibir grupos locais
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
#### <a name="query-event-log-errors"></a>Consultar erros de log de eventos
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos correspondentes ao filtro.
#### <a name="query-event-log-by-event-id"></a>Consultar log de eventos por ID do evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Consultar log de eventos por ID do evento e provedor
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar log de eventos por ID do evento e provedor nas últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para voltar 7 dias em vez de 24 horas.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e dados do evento nos últimos 7 dias
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
#### <a name="list-installed-applications"></a>Listar aplicativos instalados
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` classifica em ordem decrescente a data de instalação para facilitar a visualização do que foi instalado recentemente. Use `<spacebar>` para avançar para a próxima página de saída ou `<enter>` para avançar uma linha.
#### <a name="uninstall-an-application"></a>Desinstalar um aplicativo
`wmic path win32_product where name="<name>" call uninstall`

Substitua `<name>` pelo nome retornado no comando acima para o aplicativo que você deseja remover.

### <a name="file-system-management"></a>Gerenciamento de sistema de arquivos
#### <a name="get-file-version"></a>Obter versão do arquivo
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Este exemplo retorna a versão do arquivo do driver NIC virtual, que é netvsc.sys, netvsc63.sys ou netvsc60.syss, dependendo da versão do Windows.
#### <a name="scan-for-system-file-corruption"></a>Verificar corrupção de arquivo do sistema
`sfc /scannow`

Consulte também [Reparar uma imagem do Windows](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Verificar corrupção de arquivo do sistema
`dism /online /cleanup-image /scanhealth`

Consulte também [Reparar uma imagem do Windows](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Exportar permissões de arquivo para o arquivo de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Salvar permissões de arquivo para arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Restaurar permissões de arquivo do arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

O caminho ao utilizar `/restore` precisa ser a pasta pai da pasta que você especificou ao utilizar `/save`. Neste exemplo, `\RSA` é o pai da pasta `\MachineKeys`especificada no exemplo `/save` acima.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Assumir propriedade NTFS de uma pasta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permissões NTFS para uma pasta recursivamente
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Gerenciar dispositivos
#### <a name="remove-non-present-pnp-devices"></a>Remover dispositivos PNP não presentes
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Gerenciar Política de Grupo
#### <a name="force-group-policy-update"></a>Forçar atualização da política de grupo
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Ferramentas diversas
#### <a name="show-os-version"></a>Exibir versão do SO
`ver`

ou o 

`wmic os get caption,version,buildnumber /format:list`

ou o 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Exibir data de instalação do SO
`systeminfo | find /i "original"`

ou o 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Exibir última hora de inicialização
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Exibir fuso horário
`systeminfo | find /i "time zone"`

ou o

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Reiniciar o Windows
`shutdown /r /t 0`

Adicionar `/f` forçará o fechamento dos aplicativos em execução sem avisar os usuários.
#### <a name="detect-safe-mode-boot"></a>Detectar modo de inicialização segura
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Comandos do Windows - PowerShell

Para executar o PowerShell no SAC, depois de chegar a um prompt do CMD, digite:

`powershell <enter>`

>[!CAUTION]
Remova o módulo PSReadLine da sessão do PowerShell antes de executar qualquer outro comando do PowerShell. Há um problema conhecido em que caracteres adicionais poderão ser introduzidos no texto colado da área de transferência, se o PSReadLine estiver sendo executado em uma sessão do PowerShell no SAC.

Primeiro verifique se PSReadLine está carregado. Ele é carregado por padrão no Windows Server 2016, Windows 10 e versões mais recentes do Windows. Ele somente estará presente nas versões anteriores do Windows, se tiver sido instalado manualmente. 

Se esse comando retornar a um prompt sem saída, o módulo não foi carregado e você poderá continuar usando a sessão do PowerShell no SAC normalmente.

`get-module psreadline`

Se o comando acima retornar a versão do módulo PSReadLine, execute o comando a seguir para descarregá-lo. Esse comando não exclui nem desinstala o módulo, apenas o descarrega da sessão atual do PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar as configurações do registro do Windows
#### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A segunda chave (em \Policies) só existirá se a configuração da política de grupo relevante estiver configurada.
#### <a name="enable-rdp"></a>Habilitar o RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A segunda chave (em \Policies) só seria necessária se a configuração da política de grupo relevante tivesse sido configurada. O valor será regravado na próxima atualização de política de grupo, se estiver configurado na política de grupo.
### <a name="manage-windows-services"></a>Gerenciar Serviços do Windows
#### <a name="view-service-details"></a>Exibir detalhes do serviço
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` pode ser usado, mas não inclui a conta de logon do serviço. `Get-WmiObject win32-service` faz.
#### <a name="set-service-logon-account"></a>Definir conta de logon do serviço
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ao usar uma conta de serviço diferente de `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, ou `LocalSystem`, especifique a senha da conta como o último (oitavo) argumento após o nome da conta.
#### <a name="set-service-startup-type"></a>Definir o tipo de inicialização do serviço
`set-service termservice -startuptype Manual`

`Set-service` aceita `Automatic`, `Manual` ou `Disabled` para o tipo de inicialização.
#### <a name="set-service-dependencies"></a>Definir dependências de serviço
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Iniciar serviço
`start-service termservice`
#### <a name="stop-service"></a>Parar serviço
`stop-service termservice`
### <a name="manage-networking-features"></a>Gerenciar recursos de Rede
#### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

ou o 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` está disponível em 2012+, para uso em 2008R2 `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Habilitar NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

ou o

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` está disponível em 2012+, para uso em 2008R2 `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. VMs do Azure sempre devem ser configuradas no SO convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa o DHCP para fornecer o IP à VM.
#### <a name="ping"></a>Ping
`test-netconnection`

ou o

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sem nenhum parâmetro, tentará executar ping `internetbeacon.msedge.net`. Está disponível em 2012+. Para 2008R2 use `Get-WmiObject` como no segundo exemplo.
#### <a name="port-ping"></a>Ping de porta
`test-netconnection -ComputerName bing.com -Port 80`

ou o

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` está disponível em 2012+. Para 2008R2 use `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Testar a resolução de nomes DNS
`resolve-dnsname bing.com` 

ou o 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` está disponível em 2012+. Para 2008R2 use `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Mostrar regra de firewall do Windows por nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Mostrar regra de firewall do Windows por porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

ou o

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` está disponível em 2012+. Para 2008R2, use o objeto COM `hnetcfg.fwpolicy2`. 
#### <a name="disable-windows-firewall"></a>Desabilitar o firewall do Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` está disponível em 2012+. Para 2008R2 use `netsh advfirewall` como mencionado na seção CMD acima.
### <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
#### <a name="create-local-user-account"></a>Criar conta de usuário local
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Verificar se a conta do usuário está habilitada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

ou o 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Adicionar usuário local a grupo local
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Habilitar conta de usuário local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Então, normalmente não será `Administrator`.
#### <a name="view-user-account-properties"></a>Exibir propriedades da conta de usuário
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

ou o 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Esse exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Exibir grupos locais
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
#### <a name="query-event-log-errors"></a>Consultar erros de log de eventos
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos correspondentes ao filtro.
#### <a name="query-event-log-by-event-id"></a>Consultar log de eventos por ID do evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Consultar log de eventos por ID do evento e provedor
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar log de eventos por ID do evento e provedor nas últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para voltar 7 dias em vez de 24 horas. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e dados do evento nos últimos 7 dias
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
#### <a name="list-installed-software"></a>Listar software instalado
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Desinstalar software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Gerenciamento de sistema de arquivos
#### <a name="get-file-version"></a>Obter versão do arquivo
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Este exemplo retorna a versão do arquivo do driver NIC virtual, que é nomeado netvsc.sys, netvsc63.sys ou netvsc60.syss, dependendo da versão do Windows.
#### <a name="download-and-extract-file"></a>Baixar e extrair aplicativo
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Este exemplo cria uma pasta `c:\bin` e, em seguida, baixa e extrai o conjunto de ferramentas Sysinternals para `c:\bin`.
### <a name="miscellaneous-tasks"></a>Ferramentas diversas
#### <a name="show-os-version"></a>Exibir versão do SO
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Exibir data de instalação do SO
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Exibir última hora de inicialização
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Exibir tempo de atividade do Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retorna o tempo de atividade como `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, por exemplo `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Reiniciar o Windows
`restart-computer`

Adicionar `-force` forçará o fechamento dos aplicativos em execução sem avisar os usuários.
### <a name="instance-metadata"></a>Metadados da instância

É possível consultar os metadados da instância do Azure na VM do Azure para exibir detalhes como osType, Local, vmSize, vmId, nome, resourceGroupName, subscriptionId, privateIpAddress e publicIpAddress.

A consulta de metadados da instância exige conectividade de rede do convidado íntegra, porque faz uma chamada REST por meio do host do Azure para o serviço de metadados da instância. Portanto, se você conseguir consultar metadados da instância, isso indica que o convidado pode comunicar-se pela rede com um serviço hospedado pelo Azure.

Para obter mais informações, consulte [Serviço de metadados da instância do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Metadados da instância
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Tipo de SO (metadados da instância)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Local (metadados da instância)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Tamanho (metadados da instância)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>ID da VM (metadados da instância)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Nome da VM (metadados da instância)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Nome do grupo de recursos (metadados da instância)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>ID da assinatura (metadados da instância)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Marcas (metadados da instância)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>ID do grupo de posicionamento (metadados da instância)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Domínio de falha da plataforma (metadados da instância)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Domínio de atualização da plataforma (metadados da instância)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Endereço IP privado IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Endereço IP público IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Prefixo/endereço de sub-rede IPv4 (metadados da instância)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>Endereço IP IPv6 (metadados da instância)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Endereço MAC (metadados da instância)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
A maioria dos erros é temporária, e é resolvida quando você tenta estabelecer novamente a conexão. A tabela abaixo mostra uma lista de erros e mitigação 

Erro                            |   Redução 
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para '<VMNAME>'. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados. 
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A máquina virtual deve estar em um estado iniciado para acessar o console serial
Você não tem as permissões necessárias para usar essa VM por meio do console serial. Verifique se você tem pelo menos permissões de função de Colaborador de VM.| O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização '<STORAGEACCOUNTNAME>'. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes

## <a name="known-issues"></a>Problemas conhecidos 
Como ainda estamos na fase de visualização para acesso ao console serial, estamos lidando com alguns problemas conhecidos. Abaixo está a lista deles, com possíveis soluções alternativas 

Problema                           |   Redução 
:---------------------------------|:--------------------------------------------|
Não há uma opção com o console serial de instância de conjunto de escala de máquina virtual | No momento da visualização, não há suporte para acesso ao console serial para instâncias de conjunto de escala de máquina virtual.
Pressionar enter após a faixa de conexão não mostra um log no prompt | [O pressionamento de enter não executa nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Apenas as informações de integridade são mostradas ao se conectar a uma VM do Windows| [Sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Não será possível digitar no prompt do SAC se a depuração de kernel estiver habilitada | RDP para VM e execute `bcdedit /debug {current} off` a partir de um prompt de comandos com privilégios elevados. Se não for possível o RDP, você poderá anexar o disco de SO a outra VM do Azure e modificá-lo enquanto estiver conectado como um disco de dados usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` e, em seguida, trocar o disco de volta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com 

**P. Recebo um erro "Console existente tem um tipo de sistema operacional conflitante"Windows"com o tipo solicitado do sistema operacional do Linux?**

a. Esse é um problema conhecido. Para corrigi-lo, basta abrir o [Shell de Nuvem do Azure](https://docs.microsoft.com/azure/cloud-shell/overview) no modo de bash e tentar novamente.

**P. Não consigo acessar o console serial. Onde posso registrar um caso de suporte?**

a. Esse recurso de visualização é abrangido por meio de Termos de Visualização do Azure. O suporte para isso é manipulado melhor por meio dos canais mencionados acima. 

## <a name="next-steps"></a>Próximas etapas
* O console serial também está disponível para VMs do [Linux](../linux/serial-console.md)
* Saiba mais sobre [bootdiagnostics](boot-diagnostics.md)

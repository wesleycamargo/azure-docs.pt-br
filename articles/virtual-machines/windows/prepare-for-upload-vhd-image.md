---
title: Preparar um VHD do Windows para carregar no Azure | Microsoft Docs
description: Como preparar um VHD ou VHDX do Windows antes de carregar no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/10/2018
ms.author: genli
ms.openlocfilehash: f9b950b1d85f50331d556a54b4237d78ec5c07ac
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388138"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure
Antes de carregar uma VM (máquina virtual) Windows do local para o Microsoft Azure, você deve preparar o VHD (disco rígido virtual) ou VHDX. O Azure oferece suporte a **somente VMs de geração 1** que estão no formato de arquivo VHD e possuem um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1.023 GB. Você pode converter uma VM de geração 1 do sistema de arquivos VHD para VHDX e de um disco de expansão dinâmica para um disco de tamanho fixo. No entanto, não é possível alterar a geração de uma VM. Para obter mais informações, consulte [Devo criar uma VM de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Para obter mais informações sobre a política de suporte para a VM do Azure, consulte [Suporte de software para servidores da Microsoft para VMs do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> As instruções deste artigo se aplicam à versão de 64 bits do Windows Server 2008 R2 e sistema operacional Windows posterior. Para obter informações sobre como executar a versão de 32 bits do sistema operacional no Azure, consulte [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Converter o disco virtual em VHD e disco de tamanho fixo 
Se precisar converter o disco virtual em um formato necessário para o Azure, use um dos métodos desta seção. Faça backup da VM antes de executar o processo de conversão do disco virtual e verifique se o VHD do Windows funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

Depois de converter o disco, crie uma VM que use o disco convertido. Inicie e entre na VM a fim de concluir a preparação da VM para carregamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Converter disco usando o Gerenciador do Hyper-V
1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima da lista de computadores, clique em **Ação** > **Editar Disco**.
2. Na tela **Localizar Disco Rígido Virtual**, localize e selecione o disco virtual.
3. Na tela **Escolher Ação**, selecione **Converter** e **Avançar**.
4. Se você precisar converter de VHDX, selecione **VHD** e clique em **Próximo**.
5. Se você precisar converter de um disco de expansão dinâmica, selecione **tamanho fixo** e, em seguida, clique em **próxima**.
6. Localize e selecione um caminho no qual salvar o novo arquivo VHD.
7. Clique em **Concluir**.

>[!NOTE]
>Os comandos deste artigo devem ser executados em uma sessão do PowerShell com privilégios elevados.

### <a name="convert-disk-by-using-powershell"></a>Converter disco usando o PowerShell
Você pode converter um disco virtual usando o comando [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) no Windows PowerShell. Escolha **Executar como administrador** ao iniciar o PowerShell. 

O seguinte comando de exemplo faz a conversão de um VHDX em VHD e de um disco de expansão dinâmica em um de tamanho fixo:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Nesse comando, substitua o valor de “-Path” pelo caminho do disco rígido virtual que você deseja converter e “-DestinationPath” pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem de VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a em um VHD usando o [Conversor de VM da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Para obter mais informações, consulte o artigo no blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK do VMware em um VHD do Hyper-V).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

Na VM que você pretende carregar no Azure, execute todos os comandos nas seguintes etapas em uma [janela do Prompt de Comando com privilégios elevados](https://technet.microsoft.com/library/cc947813.aspx):

1. Remova qualquer rota persistente estática na tabela de roteamento:
   
   * Para exibir a tabela de rotas, execute `route print` no prompt de comando.
   * Verifique as seções **Rotas de Persistência** . Se houver uma rota persistente, use o comando **route delete** para removê-la.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Defina a política de SAN do disco para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    Na janela do Prompt de Comando aberta, digite os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Defina a hora UTC (Tempo Universal Coordenado) para o Windows e o tipo de inicialização do serviço Horário do Windows (w32time) como **Automaticamente**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Defina o perfil de energia para o **Alto Desempenho**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Certifique-se de que as variáveis ambientais **TEMP** e **TMP** são definidos para seus valores padrão:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Verificar os serviços Windows
Verifique se cada um dos seguintes serviços do Windows está definido como os **valores padrão do Windows**. Esse é o número mínimo de serviços que deve ser configurado para garantir que a VM tenha conectividade. Para redefinir as configurações de inicialização, execute os seguintes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar configurações de registro da Área de Trabalho Remota
Verifique se as seguintes configurações estão configuradas corretamente para a conexão de área de trabalho remota:

>[!Note] 
>Você pode receber uma mensagem de erro quando você executa o **Set-ItemProperty-caminho ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal serviços - nome &lt;nome do objeto&gt; -valor &lt;valor&gt;** nessas etapas. A mensagem de erro pode ser ignorada. Isso significa apenas que o domínio não está enviando a configuração por push por meio de um objeto de Política de Grupo.
>
>

1. O protocolo RDP está habilitado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. A porta do RDP está configurada corretamente (porta 3389 padrão):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Ao implantar uma VM, as regras padrão são criadas em relação à porta 3389. Se desejar alterar o número da porta, faça isso depois que a VM for implantada no Azure.

3. O ouvinte escuta em todos os adaptadores de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configure o modo de Autenticação no Nível da Rede para as conexões RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Defina o valor de keep alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Reconectar-se:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limite o número de conexões simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Se houver certificados autoassinados vinculados ao ouvinte do RDP, remova-os:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Isso serve para garantir que você pode se conectar no começo, durante a implantação da VM. Você também poderá examinar isso em um estágio posterior depois que a VM for implantada no Azure, se necessário.

9. Se a VM for fazer parte de um Domínio, verifique todas as configurações a seguir para garantir que as configurações anteriores não sejam revertidas. As políticas que devem ser verificadas são as seguintes:
    
    | Objetivo                                     | Política                                                                                                                                                       | Valor                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP está habilitado                           | Configuração do Computador\Diretivas\Configurações do Windows\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Conexões         | Permitir que os usuários se conectem remotamente usando a Área de Trabalho Remota                                  |
    | Diretiva de grupo do NLA                         | Configurações\Modelos Administrativos\Componentes\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Segurança                                                    | Exigir autenticação do usuário para conexões remotas usando a autenticação no nível de rede |
    | Configurações de Keep Alive                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Configurar o intervalo de conexão keep-alive                                                 |
    | Configurações de reconexão                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Reconexão automática                                                                   |
    | Limitar o número de configurações de conexões | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Componentes do Windows\ Serviços de Área de Trabalho Remota\Host da Sessão da Área de Trabalho Remota\Conexões | Limitar o número de conexões                                                              |

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
1. Ative o Firewall do Windows nos três perfis (Domínio, Padrão e Público):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Execute o seguinte comando no PowerShell para permitir o WinRM por meio dos três perfis de firewall (Domínio, Privado e Público) e habilite o serviço Remoto do PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Ative as seguintes regras de firewall para permitir o tráfego RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Habilite a regra de Compartilhamento de Arquivo e Impressora para que a VM possa responder a um comando ping dentro da Rede Virtual:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Se a VM for fazer parte de um Domínio, verifique as configurações a seguir para garantir que as configurações anteriores não sejam revertidas. As políticas do AD que devem ser verificadas são as seguintes:

    | Objetivo                                 | Política                                                                                                                                                  | Valor                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Habilitar os perfis do Firewall do Windows | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Proteger todas as conexões de rede         |
    | Habilitar o RDP                           | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções de área de trabalho remota de entrada |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções de área de trabalho remota de entrada |
    | Habilitar o ICMP-V4                       | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil de Domínio\Firewall do Windows   | Permitir exceções do ICMP                   |
    |                                      | Configuração do Computador\Políticas\Configurações do Windows\Modelos Administrativos\Rede\Conexão de Rede\Firewall do Windows\Perfil Padrão\ Firewall do Windows | Permitir exceções do ICMP                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Verifique se a VM está em estado íntegro, segura e pode ser acessada com RDP 
1. Para verificar se o disco está íntegro e consistente, execute uma operação de verificação de disco na próxima reinicialização da VM:

    ```PowerShell
    Chkdsk /f
    ```
    Verifique se o relatório mostra um disco íntegro e limpo.

2. Defina as configurações de BCD (Dados de Configuração da Inicialização). 

    > [!Note]
    > Certifique-se de executar esses comandos em uma janela elevada do PowerShell.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. O log de Despejo pode ser útil para solucionar problemas de falha do Windows. Habilite a coleta do log de Despejo:

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Confirme se o repositório da Instrumentação de Gerenciamento do Windows é consistente. Para fazer isso, execute o seguinte comando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório estiver corrompido, consulte [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: o repositório está corrompido ou não?).

5. Verifique se outro aplicativo não está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Execute **netstat -anob** para ver quais portas estão sendo usadas na VM:

    ```PowerShell
    netstat -anob
    ```

6. Se o VHD do Windows que você deseja carregar for um controlador de domínio, siga estas etapas:

    1. Siga [estas etapas extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

    1. Verifique se você sabe a senha do DSRM, caso precise iniciar a VM no DSRM em algum momento. Talvez você deseje consultar este link para definir a [senha do DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Verifique se você sabe a conta de Administrador Interno e a senha. Talvez você deseje redefinir a senha do administrador local atual e verificar se pode usar essa conta para entrar no Windows por meio da conexão RDP. Essa permissão de acesso é controlada pelo objeto de Política de Grupo "Permitir logon pelos Serviços de Área de Trabalho Remota". Exiba esse objeto no Editor de Política de Grupo Local em:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Confira as políticas de AD a seguir para ter certeza de que você não está bloqueando o acesso de RDP através de RDP e não da rede:

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar acesso a este computador pela rede

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Negar logon pelos Serviços de Área de Trabalho Remota


9. Verifique a seguinte política do AD para se certificar de que você não esteja removendo nenhuma das seguintes contas de acesso necessárias:

    - Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário\Acessar este computador pela rede

    Os grupos a seguir devem ser listados nesta política:

    - Administradores
    - Operadores de Backup
    - Todos
    - Usuários

10. Reinicie a VM para garantir que o Windows ainda está íntegro e pode ser acessado usando a conexão RDP. Neste ponto, talvez você deseje criar uma VM no Hyper-V local para verificar se a VM está iniciando por completo e, em seguida, testar se ela é acessível por RDP.

11. Remova os filtros extras da Interface do Driver de Transporte, como software que analisa pacotes TCP ou firewalls extras. Você também poderá examinar isso em um estágio posterior depois que a VM for implantada no Azure, se necessário.

12. Desinstale outros softwares e drivers de terceiros relacionados a componentes físicos ou a qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
A configuração ideal é **ter o último nível de patch do computador**. Se isso não for possível, verifique se as seguintes atualizações estão instaladas:

| Componente               | Binário         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1,Windows Server 2012 R2 | Windows 10 Version 1607Windows Server 2016 Versão 1607 | Windows 10, versão 1703    | Windows 10 1709 Windows Server 2016 versão 1709 | Windows 10 1803Windows Server 2016 versão 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Armazenamento                 | disk.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17638/6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17188/6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17623/6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 – KB3125574                | 6.2.9200.16384 – KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17061/6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614         | 10.0.14393.953 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17047/6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726         | 10.0.14393.1066 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 – KB3125574                | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726         | 10.0.14393.1198 – KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614         | 10.0.14393.576 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614         | 10.0.14393.206 – KB4022715                              | -                          | -                                               | -                                               |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 – KB4022715                             | 10.0.15063.250 – KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726         | 10.0.14393.479 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 – KB4022722                | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 – KB4022722                | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726         | 10.0.14393.251 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 – KB4022719                | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Núcleo                    | ntoskrnl.exe   | 6.1.7601.23807 – KB4022719                | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Serviços da Área de Trabalho Remota | rdpcorets.dll  | 6.2.9200.21506 – KB4022719                | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 – KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 – KB4022719                | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726         | 10.0.14393.594 – KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Quando usar o Sysprep <a id="step23"></a>    

O Sysprep é um processo que pode ser executado em uma instalação do Windows, que redefinirá a instalação do sistema e fornecerá uma “experiência pronta para uso”, com a remoção de todos os dados pessoais e a redefinição de vários componentes. Normalmente, isso é feito se você deseja criar um modelo com base no qual você pode implantar várias outras VMs que têm uma configuração específica. Isso é chamado de uma **imagem generalizada**.

Se, em vez disso, você desejar apenas criar uma VM com base em um disco, não precisará usar o Sysprep. Nessa situação, basta criar a VM com base no que é conhecido como um **imagem especializada**.

Para obter mais informações sobre como criar uma VM com base em um disco especializado, consulte:

- [Criar uma VM com base em um disco especializado](create-vm-specialized.md)
- [Criar uma VM com base em um disco VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se você desejar criar uma imagem generalizada, precisará executar o Sysprep. Para obter mais informações sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx). 

Nem toda função ou aplicativo que é instalado em um computador baseado no Windows dá suporte a essa generalização. Portanto, antes de executar esse procedimento, consulte o artigo a seguir para verificar se há suporte para a função do computador no Sysprep. Para obter mais informações, consulte [Suporte do Sysprep em funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Etapas para generalizar um VHD

>[!NOTE]
> Após a execução do sysprep.exe, conforme especificado nas etapas a seguir, desligue a VM e não a ligue novamente até que você crie uma imagem dela no Azure.

1. Entre na VM Windows.
2. Execute o **Prompt de Comando** como administrador. 
3. Altere o diretório para **%windir%\system32\sysprep** e, em seguida, execute **sysprep.exe**.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.

    ![Ferramenta de Preparação do Sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
6. Após a conclusão do Sysprep, desligue a VM. Não use **Reiniciar** para desligar a VM.
7. Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM com base em um disco generalizado, consulte [Carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Conclua as configurações recomendadas
As seguintes configurações não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

* Instale o [Agente de VMs do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, você pode habilitar as extensões de VM. As extensões de VM implementam a maioria da funcionalidade crítica que você pode desejar usar com as VMs, como redefinição de senhas, configuração do RDP e assim por diante. Para obter mais informações, consulte:

    - [Agente de VM e extensões – parte 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agente de VM e extensões – parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)

*  Depois que a VM for criada no Azure, recomendamos colocar o arquivo de paginação no volume “Unidade temporal” para melhorar o desempenho. Configure isso da seguinte maneira:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile" -Type MultiString -force
    ```
Se houver um disco de dados anexado à VM, a letra da unidade do volume da Unidade temporal geralmente será “D”. Essa designação poderá ser diferente, dependendo do número de unidades disponíveis e das configurações feitas.

## <a name="next-steps"></a>Próximas etapas
* [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](upload-generalized-managed.md)
* [Solucionar problemas de ativação de máquina virtual do Microsoft Azure](troubleshoot-activation-problems.md)


---
title: Preparar um VHD do Windows para carregar no Azure | Microsoft Docs
description: Como preparar um VHD ou VHDX do Windows antes de carregar no Azure
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 5d8274f61c3de178c9d418adb9be1efe0fe62bc1
ms.openlocfilehash: 6fbfc74cb1cce744b51345c0732b40b95be21c94


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou VHDX do Windows para carregar no Azure
Para carregar uma VM do Windows do local no Azure, você deve preparar corretamente o VHD ou VHDX (disco rígido virtual). O Azure dá suporte somente a máquinas virtuais de geração 1 que estão no formato de arquivo VHD e têm um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1.023 GB. Você pode converter uma máquina virtual de geração 1 do formato de arquivo VHDX em VHD e de expansão dinâmica em um disco de tamanho fixo. Mas não é possível alterar a geração de uma máquina virtual. Para saber mais, confira [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Converter o disco virtual em VHD e disco de tamanho fixo 
Se precisar converter o disco virtual em um formato necessário para o Azure, use um dos métodos desta seção. Faça backup da VM antes de executar o processo de conversão do disco virtual e verifique se o VHD do Windows funciona corretamente no servidor local. Resolva todos os erros na própria VM antes de tentar convertê-la ou carregá-la no Azure.

Depois de converter o disco, crie uma VM que use o disco convertido. Inicie e entre na VM a fim de concluir a preparação da VM para carregamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Converter disco usando o Gerenciador do Hyper-V
1. Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima dele, clique em **Ação** > **Editar Disco**.
2. Na tela **Localizar Disco Rígido Virtual** , navegue até o disco virtual e selecione-o.
3. Na tela **Escolher Ação**, selecione **Converter** e **Avançar**.
4. Se você precisar converter de VHDX, selecione **VHD** e clique em **Avançar**
5. Se você precisar fazer a conversão de disco de expansão dinâmica, selecione **Tamanho fixo** e clique em **Avançar**
6. Navegue até um caminho e selecione-o para salvar o novo arquivo VHD.
7. Clique em **Concluir** para fechar.

### <a name="convert-disk-using-powershell"></a>Converter disco usando o PowerShell
Você pode converter um disco virtual usando o cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) no Windows PowerShell. Escolha **Executar como administrador** ao iniciar o PowerShell. O seguinte exemplo mostra como fazer a conversão de um VHDX em VHD e de um disco de expansão dinâmica em um de tamanho fixo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Substitua os valores de -Path pelo caminho para o disco rígido virtual que você quer converter e -DestinationPath pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem da VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a no formato VHD usando o [Conversor de Máquina Virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Para saber mais, confira o blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK da VMware em VHD do Hyper-V).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

Na máquina virtual que você planeja carregar no Azure, execute todos os comandos a seguir na janela do prompt de comando com [privilégios administrativos](https://technet.microsoft.com/library/cc947813.aspx).

1. Remova qualquer rota persistente estática na tabela de roteamento:
   
   * Para exibir a tabela de rotas, execute `route print` na janela de prompt de comando.
   * Verifique as seções **Rotas de Persistência** . Se houver uma rota persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para removê-la.
2. Remova o proxy de WinHTTP:
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. Defina a política de SAN do disco como [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Defina a hora UTC (Tempo Universal Coordenado) para o Windows e o tipo de inicialização do serviço Horário do Windows (w32time) como **Automaticamente**:
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>Definir a inicialização dos serviços para valores padrão do Windows
Verifique se cada um dos seguintes serviços do Windows está definido como os **valores padrão do Windows**. Para redefinir as configurações de inicialização, execute os seguintes comandos:
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar configurações de registro da Área de Trabalho Remota
1. Se houver certificados autoassinados associados ao ouvinte do RDP (protocolo RDP), remova-os:
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Para obter mais informações sobre como configurar certificados para o ouvinte do RDP, consulte [Configurações de certificado de ouvinte no Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. Configure os valores de [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) para o serviço do RDP:
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Configure o modo de autenticação para o serviço do RDP:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Habilite o serviço do RDP adicionando as seguintes subchaves ao Registro:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
1. Execute o seguinte comando no PowerShell para permitir o WinRM por meio de três perfis de firewall (Domínio, Privado e Público) e habilite o serviço remoto do PowerShell:
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. Execute os seguintes comandos na janela do prompt de comando para verificar se as seguintes regras de firewall do sistema operacional convidado estão definidas:
   
   * Entrada
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * Entrada e saída
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * Saída
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Verifique se a VM está em estado íntegro, segura e pode ser acessada com RDP 
1. Na janela do prompt de comando, execute `winmgmt /verifyrepository` para confirmar se o repositório da WMI (Instrumentação de Gerenciamento do Windows) é consistente. Se o repositório estiver corrompido, leia a postagem no blog [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: o repositório está corrompido ou não?)
2. Defina as configurações de BCD (Dados de Configuração da Inicialização):
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Remova quaisquer filtros de Interface do Driver de Transporte extra, como o software que analisa pacotes de TCP.
4. Para verificar se o disco está íntegro e consistente, execute o comando `CHKDSK /f` na janela do prompt de comando. Digite "Y" para agendar a verificação e reinicie a VM.
5. Desinstale qualquer outro software e driver de terceiros relacionados a componentes físicos ou qualquer outra tecnologia de virtualização.
6. Verifique se um aplicativo de terceiros não está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Você pode executar `netstat -anob` na janela do prompt de comando para ver as portas que são usadas pelos aplicativos.
7. Se o VHD do Windows que você deseja carregar for um controlador de domínio, execute [estas etapas extra](https://support.microsoft.com/kb/2904015) para preparar o disco.
8. Reinicialize a VM para garantir que o Windows ainda esteja íntegro e possa ser alcançado usando a conexão de RDP.
9. Redefina a senha do administrador local atual e verifique se você pode usar essa conta para entrar no Windows por meio da conexão de RDP. Essa permissão de acesso é controlada pelo objeto de Política de Grupo "Permitir logon pelos Serviços de Área de Trabalho Remota". É possível exibir esse objeto no Editor de Política de Grupo Local em "Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de Direitos de Usuário".

## <a name="install-windows-updates"></a>Instalar atualizações do Windows
Instale as atualizações mais recentes para o Windows. Se isso não for possível, verifique se as seguintes atualizações estão instaladas:
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) VMs do Microsoft Azure não se recuperam de uma interrupção de rede e ocorrem problemas de dados corrompidos
   * [KB3115224](https://support.microsoft.com/kb/3115224) Melhorias de confiabilidade para VMs em execução em um host do Windows Server 2012 R2 ou o Windows Server 2012
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: atualização de segurança do Microsoft Windows para tratar da elevação de privilégio: 8 de março de 2016
   * [KB3063075](https://support.microsoft.com/kb/3063075) Muitos eventos com ID 129 são registrados quando você executa uma máquina virtual do Windows Server 2012 R2 no Microsoft Azure
   * [KB3137061](https://support.microsoft.com/kb/3137061) VMs do Microsoft Azure não se recuperam de uma interrupção de rede e ocorrem problemas de dados corrompidos
   * [KB3114025](https://support.microsoft.com/kb/3114025) Desempenho lento ao acessar o armazenamento de arquivos do Azure do Windows 8.1 ou Server 2012 R2
   * [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix aumenta o limite de 64K de buffers de RIO por processo para o serviço do Azure no Windows
   * [KB3004545](https://support.microsoft.com/kb/3004545) Não é possível acessar máquinas virtuais hospedadas em serviços de hospedagem do Azure por meio de uma conexão VPN no Windows
   * [KB3082343](https://support.microsoft.com/kb/3082343) A conectividade VPN entre locais é perdida quando túneis de VPN site a site do Azure usam o RRAS do Windows Server 2012 R2
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: atualização de segurança do Microsoft Windows para tratar da elevação de privilégio: 8 de março de 2016
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: descrição da atualização de segurança para CSRSS: 12 de abril de 2016
   * [KB2904100](https://support.microsoft.com/kb/2904100) O sistema congela durante a E/S do disco no Windows
     
## <a name="run-sysprep--a-idstep23a"></a>Executar Sysprep  <a id="step23"></a>    
Se quiser criar uma imagem para implantação em várias VMs, você precisará [generalizar a imagem executando Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de carregar o VHD no Azure. Não é preciso executar Sysprep para usar um VHD especializado. Para obter mais informações, consulte os seguintes artigos:
   
   * [Generalizar uma máquina virtual do Windows usando Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>Conclua as configurações recomendadas
As seguintes configurações não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

* Instalar o [Agente de máquinas virtuais do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Depois de instalar o agente, você pode habilitar extensões de VM. As extensões de VM implementam a maioria dos recursos essenciais que você deseja usar com suas VMs, incluindo a redefinição de senhas, configuração do RDP e muitos outros.
* O log de Despejo pode ser útil para solucionar problemas de falha do Windows. Habilite a coleta do log de Despejo:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* Após a VM ser criada no Azure, configure o arquivo de paginação com tamanho definido pelo sistema na unidade D:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Próximas etapas
* [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->



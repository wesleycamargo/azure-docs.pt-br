<properties
    pageTitle="Preparar um VHD do Windows para upload no Azure | Microsoft Azure"
    description="Práticas recomendadas para preparar um VHD do Windows antes de carregá-lo no Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>


# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparar um VHD do Windows para upload no Azure
Para carregar uma VM do Windows do local para o Azure, você deve preparar corretamente o VHD (disco rígido virtual). Você pode concluir várias etapas recomendadas antes de carregar um VHD no Azure. Este artigo mostra como preparar um VHD do Windows para upload no Microsoft Azure e também explica [usar o Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparar o disco virtual

>[AZURE.NOTE] 
> O Azure dá suporte somente para [máquinas virtuais de geração 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) no formato de arquivo VHD. Não há suporte para o formato VHDX mais recente no Azure. 
>
> O VHD deve ter um tamanho fixo, não dinâmico. Se necessário, as instruções a seguir detalham a conversão de discos VHDX ou dinâmicos. O tamanho máximo permitido para o VHD é 1.023 GB.


Verifique se o VHD do Windows está funcionando corretamente no servidor local. Resolva quaisquer erros na própria VM antes de tentar converter ou carregar no Azure.

Se precisar converter o disco virtual para o formato necessário para o Azure, use um dos métodos indicados nas seções a seguir. Faça backup da VM antes de executar qualquer processo de conversão do disco virtual ou o Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Converter usando o Gerenciador do Hyper-V
- Abra o Gerenciador do Hyper-V e selecione o computador local à esquerda. No menu acima dele, clique em **Ação** > **Editar Disco**.
    - Na tela **Localizar Disco Rígido Virtual** , navegue até o disco virtual e selecione-o.
    - Selecione **Converter** na tela seguinte
        - Se você precisar converter de VHDX, selecione **VHD** e clique em **Avançar**
        - Se você precisar converter de disco Dinâmico, selecione **Tamanho fixo** e clique em **Avançar**

    - Navegue até o **Caminho para o novo arquivo VHD**e selecione-o.
    - Clique em **Concluir** para fechar.

### <a name="convert-using-powershell"></a>Converter usando o PowerShell
Você pode converter um disco virtual usando o [cmdlet do PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). No exemplo a seguir, estamos convertendo de VHDX para VHD e convertendo do tipo Dinâmico para Fixo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem da VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a no formato VHD usando o [Conversor de Máquina Virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Leia o blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK do VMware para VHD do Hyper-V) para obter mais informações.

## <a name="prepare-windows-configuration-for-upload"></a>Prepare a configuração do Windows para o upload

> [AZURE.NOTE] Execute todos os comandos a seguir com [privilégios administrativos](https://technet.microsoft.com/library/cc947813.aspx).

1. Remova qualquer rota persistente estática na tabela de roteamento:

    - Para exibir a tabela de roteamento, execute `route print`.
    - Verifique as seções **Rotas de Persistência** . Se houver uma rota persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para removê-la.

2. Remova o proxy de WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configure a política de SAN do disco para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Use a hora no UTC (Tempo Universal Coordenado) para o Windows e defina o tipo de inicialização do Horário do Windows (w32time) para **Automaticamente**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configure os serviços do Windows
5. Verifique se cada um dos seguintes serviços do Windows está definido como os **valores padrão do Windows**. Eles são configurados com as configurações de inicialização observadas na lista a seguir. Você pode executar esses comandos para redefinir as configurações de inicialização:

    ```
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


## <a name="configure-remote-desktop-configuration"></a>Defina a configuração da Área de Trabalho Remota
6. Se houver certificados autoassinados associados ao ouvinte do RDP (protocolo RDP), remova-os:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Para obter mais informações sobre como configurar certificados para o ouvinte do RDP, consulte [Configurações de certificado de ouvinte no Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configure os valores de [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) para o serviço do RDP:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configure o modo de autenticação para o serviço do RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Habilite o serviço do RDP adicionando as seguintes subchaves ao Registro:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configure as regras do Firewall do Windows
10. Permita o WinRM por meio de três perfis de firewall (Domínio, Privado e Público) e habilite o serviço remoto do PowerShell:

    ```
    Enable-PSRemoting -force
    ```

11. Verifique se as seguintes regras de firewall de sistema operacional convidado estão em vigor:

    - Entrada

    ```
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

    - Entrada e saída

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Saída

    ```
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


## <a name="additional-windows-configuration-steps"></a>Etapas adicionais de configuração do Windows
12. Execute `winmgmt /verifyrepository` para confirmar se o repositório da WMI (Instrumentação de Gerenciamento do Windows) é consistente. Se o repositório estiver corrompido, consulte [esta postagem de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Verifique se as configurações de BCD (Dados de Configuração da Inicialização) corresponderem ao seguinte:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Remova quaisquer filtros de Interface do Driver de Transporte extra, como o software que analisa pacotes de TCP.
15. Para verificar se o disco está íntegro e consistente, execute o comando `CHKDSK /f` .
16. Desinstale qualquer outro software e driver de terceiros relacionados a componentes físicos ou qualquer outra tecnologia de virtualização.
17. Verifique se um aplicativo de terceiros não está usando a porta 3389. Esta porta é usada para o serviço de RDP no Azure. Você pode usar o comando `netstat -anob` para verificar as portas usadas pelos aplicativos.
18. Se o VHD do Windows que você deseja carregar for um controlador de domínio, execute [estas etapas extra](https://support.microsoft.com/kb/2904015) para preparar o disco.
19. Reinicialize a VM para garantir que o Windows ainda esteja íntegro e possa ser alcançado usando a conexão de RDP.
20. Redefina a senha do administrador local atual e verifique se você pode usar essa conta para entrar no Windows por meio da conexão de RDP.  Essa permissão de acesso é controlada pelo objeto de política "Permitir logon pelos Serviços de Área de Trabalho Remota". O objeto está localizado em "Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Atribuição de direitos de usuário".


## <a name="install-windows-updates"></a>Instalar atualizações do Windows
22. Instale as atualizações mais recentes para o Windows. Se isso não for possível, verifique se as seguintes atualizações estão instaladas:

    - [KB3137061](https://support.microsoft.com/kb/3137061) VMs do Microsoft Azure não se recuperam de uma interrupção de rede e ocorrem problemas de dados corrompidos

    - [KB3115224](https://support.microsoft.com/kb/3115224) Melhorias de confiabilidade para VMs em execução em um host do Windows Server 2012 R2 ou o Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: atualização de segurança do Microsoft Windows para tratar da elevação de privilégio: 8 de março de 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Muitos eventos com ID 129 são registrados quando você executa uma máquina virtual do Windows Server 2012 R2 no Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) VMs do Microsoft Azure não se recuperam de uma interrupção de rede e ocorrem problemas de dados corrompidos

    - [KB3114025](https://support.microsoft.com/kb/3114025) Desempenho lento ao acessar o armazenamento de arquivos do Azure do Windows 8.1 ou Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix aumenta o limite de 64K de buffers de RIO por processo para o serviço do Azure no Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) Não é possível acessar máquinas virtuais hospedadas em serviços de hospedagem do Azure por meio de uma conexão VPN no Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) A conectividade VPN entre locais é perdida quando túneis de VPN site a site do Azure usam o RRAS do Windows Server 2012 R2

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: atualização de segurança do Microsoft Windows para tratar da elevação de privilégio: 8 de março de 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: descrição da atualização de segurança para CSRSS: 12 de abril de 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) O sistema congela durante a E/S do disco no Windows <a id="step23"></a>
23. Se quiser criar uma imagem para implantar várias máquinas dela, você precisará generalizar a imagem executando `sysprep` antes de carregar o VHD no Azure. Você não precisa executar `sysprep` para usar um VHD especializado. Para obter mais informações sobre como criar uma imagem generalizada, consulte os seguintes artigos:

    - [Create a VM image from an existing Azure VM using the Resource Manager deployment model (Criar uma imagem de VM de uma VM do Azure existente usando o modelo de implantação do Gerenciador de Recursos)](virtual-machines-windows-capture-image.md)
    - [Create a VM image from an existing Azure VM using the Classic deployment modem (Criar uma imagem de VM de uma VM do Azure existente usando o modelo de implantação clássico)](virtual-machines-windows-classic-capture-image.md)
    - [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurações adicionais sugeridas

As seguintes configurações não afetam o carregamento do VHD. No entanto, é altamente recomendável que você as configure.

- Instalar o [Agente de máquinas virtuais do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Depois de instalar o agente, você pode habilitar extensões de VM. As extensões de VM implementam a maioria dos recursos essenciais que você deseja usar com suas VMs, incluindo a redefinição de senhas, configuração do RDP e muitos outros.

- O log de Despejo pode ser útil para solucionar problemas de falha do Windows. Habilite a coleta do log de Despejo:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Após a VM ser criada no Azure, configure o arquivo de paginação com tamanho definido pelo sistema na unidade D:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Próximas etapas

- [Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager](virtual-machines-windows-upload-image.md)



<!--HONumber=Oct16_HO2-->



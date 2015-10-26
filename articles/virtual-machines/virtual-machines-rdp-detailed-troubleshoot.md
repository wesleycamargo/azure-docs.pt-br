<properties
	pageTitle="Solução de problemas detalhada da Área de Trabalho Remota | Microsoft Azure"
	description="Etapas da solução de problemas detalhada de conexões de RDP ou Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Solução de problemas detalhada de conexões de Área de Trabalho Remota para máquinas virtuais do Azure baseadas em Windows

Este artigo fornece as etapas detalhadas de solução de problemas de erros complexos de Área de Trabalho Remota.

> [AZURE.IMPORTANT]Para eliminar os erros mais comuns de Área de Trabalho Remota, certifique-se de ler [a solução de problemas básica para Área de Trabalho Remota](virtual-machines-troubleshoot-remote-desktop-connections.md) antes de continuar.

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](http://azure.microsoft.com/support/forums/).

Como alternativa, você também pode registrar um incidente de suporte do Azure. Para enviar um incidente, vá para o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Mensagem de erro genérica da Área de Trabalho Remota

Algumas vezes você pode ver essa mensagem de erro na janela de mensagem de Conexão de Área de Trabalho Remota: _A Área de Trabalho Remota não pode se conectar ao computador remoto por um destes motivos..._

Esse erro quando o cliente da Área de Trabalho Remota não pode acessar os Serviço de Área de Trabalho Remota na máquina virtual. Pode haver vários motivos para esse erro.

Aqui está o conjunto de componentes envolvidos.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

Antes de mergulhar em um processo passo a passo de solução de problemas, é útil examinar mentalmente o que foi alterado desde que você conseguiu criar com êxito conexões de Área de Trabalho Remota e usar essa alteração como base para corrigir o problema. Por exemplo:

- Se você conseguiu criar conexões de Área de Trabalho Remota e alterou o endereço IP público da máquina virtual ou do serviço de nuvem que contém sua máquina virtual (também conhecido como endereço IP virtual, ou [VIP]), o cache do cliente DNS deve ter uma entrada para o nome DNS e o *endereço IP antigo*. Libere o cache do cliente DNS e tente novamente. Como alternativa, tente estabelecer a conexão usando o novo VIP.
- Se você deixou de usar o portal do Azure ou o portal de visualização do Azure e passou a usar um aplicativo para gerenciar suas conexões de Área de Trabalho Remota, verifique se a configuração do aplicativo inclui a porta TCP determinada de forma aleatória para o tráfego de Área de Trabalho Remota.

As seções a seguir explicam como isolar e determinar as várias causas raiz para esse problema e fornecem soluções e soluções alternativas.


## Etapas preliminares

Execute estas etapas antes de prosseguir para a solução de problemas detalhada.

- Verificar o status da máquina virtual no portal do Azure ou no portal de visualização do Azure
- Reiniciar a máquina virtual
- [Redimensionar a máquina virtual](virtual-machines-size-specs.md)

Depois dessas etapas, tente estabelecer novamente a conexão de Área de Trabalho Remota.


## Solução de problemas detalhada

O cliente da Área de Trabalho Remota pode não conseguir acessar os Serviços de Área de Trabalho Remota na máquina virtual do Azure devido a problemas ou configurações incorretas nas seguintes fontes:

- Computador cliente de Área de Trabalho Remota
- Dispositivo de borda de intranet da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- Máquina virtual baseada em Microsoft Azure

### Fonte 1: computador cliente de Área de Trabalho Remota

Para eliminar seu computador como a fonte dos problemas ou erros de configuração, verifique se o computador pode estabelecer conexões de Área de Trabalho Remota com outro computador local baseado no Windows.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego de Área de Trabalho Remota
- Um software de proxy cliente instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Um software de monitoramento de rede instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/não permitem tipos específicos de tráfego e que estão impedindo conexões de Área de Trabalho Remota

Em todos esses casos, tente desabilitar temporariamente o software e tente estabelecer uma conexão de Área de Trabalho Remota com um computador local para descobrir a causa raiz. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software para permitir as conexões de Área de Trabalho Remota.

### Fonte 2: dispositivo de borda de intranet da organização

Para eliminar o dispositivo de borda de intranet de sua organização como a fonte dos problemas ou erros de configuração, verifique se um computador conectado diretamente à Internet pode estabelecer conexões de Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova máquina virtual do Azure em seu próprio grupo de recursos ou serviço de nuvem e usá-la. Para obter mais informações, consulte [Criar uma máquina virtual que execute o Windows no Azure](virtual-machines-windows-tutorial.md). Exclua o grupo de recursos ou a máquina virtual e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão de Área de Trabalho Remota com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda de intranet da organização:

- Um firewall interno que está bloqueando conexões HTTPS com a Internet
- Seu servidor proxy que está impedindo conexões de Área de Trabalho Remota
- Software de detecção de intrusão ou monitoramento de rede executado em dispositivos em sua rede de borda que está impedindo conexões de Área de Trabalho Remota

Trabalhe com o administrador da rede para corrigir as configurações do dispositivo de borda de intranet da organização para permitir as conexões de Área de Trabalho Remota baseadas em HTTPS com a Internet.

### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

Para eliminar o ponto de extremidade do serviço de nuvem e a ACL como a fonte dos problemas ou má configuração das máquinas virtuais criadas usando a API de Gerenciamento de Serviços, verifique se outra máquina virtual do Azure que está no mesmo serviço de nuvem ou rede virtual pode estabelecer conexões da Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]Para máquinas virtuais criadas no Gerenciador de Recursos, vá para [Fonte 4: Grupos de Segurança de Rede](#nsgs).

Se não houver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, você poderá criar facilmente uma nova. Para obter mais informações, consulte [Criar uma máquina virtual que execute o Windows no Azure](virtual-machines-windows-tutorial.md). Exclua a máquina virtual extra ao concluir o teste.

Se você puder criar uma Conexão de Área de Trabalho Remota com uma máquina virtual no mesmo serviço de nuvem ou rede virtual, verifique o seguinte:

- A configuração de ponto de extremidade para o tráfego de Área de Trabalho Remota na máquina virtual de destino. A porta TCP privada do ponto de extremidade deve corresponder à porta TCP em que os Serviços de Área de Trabalho Remota na máquina virtual estão escutando, que, por padrão, é 3389.
- A ACL para o ponto de extremidade de tráfego de Área de Trabalho Remota na máquina virtual de destino. As ACLs permitem que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de Área de Trabalho Remota para o ponto de extremidade. Examine suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou outro servidor de borda é permitido. Para obter mais informações, veja [O que é uma ACL (Lista de Controle de Acesso) à Rede?](../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto de extremidade como a fonte do problema, remova o ponto de extremidade atual e crie um novo ponto de extremidade, escolhendo uma porta aleatória no intervalo 49152-65535 para o número da porta externa. Para obter mais informações, veja [Como configurar pontos de extremidade para uma máquina virtual](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego de Área de Trabalho Remota da Internet é permitido.

Para obter mais informações, consulte [O que é um NSG (Grupo de Segurança de Rede)?](../virtual-network/virtual-networks-nsg.md).

### Fonte 5: máquina virtual do Azure baseada em Windows

O último conjunto de problemas possíveis está na máquina virtual do Azure em si.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

O [artigo básico de solução de problemas da Área de Trabalho Remota](virtual-machines-troubleshoot-remote-desktop-connections.md) descreve como usar o [pacote de diagnóstico de IaaS (Windows) do Azure](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Se com este pacote de diagnóstico você não conseguir resolver o problema de **conectividade do RDP a uma VM do Azure (Reinicialização Necessária)**, siga as instruções [neste artigo](virtual-machines-windows-reset-password.md) para redefinir o serviço dos Serviços de Área de Trabalho Remota na máquina virtual. Isso vai:

- Habilitar a regra de firewall padrão do Windows de "Área de Trabalho Remota" (porta TCP 3389).
- Habilitar conexões de Área de Trabalho Remota definindo o valor do Registro HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections como 0.

Tente estabelecer a conexão de seu computador novamente. Se você não tiver êxito, estes são alguns dos problemas possíveis:

- Os Serviços de Área de Trabalho Remota não estão em execução na máquina virtual de destino.
- Os Serviços de Área de Trabalho Remota não estão escutando na porta TCP 3389.
- O Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de Área de Trabalho Remota.
- Um software de detecção de intrusão ou monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de Área de Trabalho Remota.

Para corrigir esses possíveis problemas de máquinas virtuais criadas na API de Gerenciamento de Serviços, você pode usar uma sessão remota do Azure PowerShell com a máquina virtual do Azure. Primeiro, você precisa instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Vá para [Configurar o acesso remoto seguro do PowerShell para máquinas virtuais do Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM.ps1** para uma pasta no computador local.

Em seguida, instale o Azure PowerShell, se ainda não tiver feito isso. Consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).

Em seguida, abra um prompt de comando do Azure PowerShell e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM.ps1**. Para executar um script do Azure PowerShell, é preciso definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome de sua assinatura do Azure, o nome do serviço de nuvem e o nome da máquina virtual (removendo os caracteres < and >) e execute estes comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Você pode obter o nome da assinatura correto na propriedade _SubscriptionName_ da exibição do comando **Get-AzureSubscription**. Você pode obter o nome do serviço de nuvem para a máquina virtual na coluna _ServiceName_ da exibição do comando **Get-AzureVM**.

Para comprovar que você tem esse novo certificado, abra um snap-in de Certificados para o usuário atual e examine a pasta **Trusted Root Certification Authorities\\Certificates**. Você deve ver um certificado com o nome DNS de seu serviço de nuvem na coluna Emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do Azure PowerShell usando estes comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Depois de inserir credenciais de administrador válidas, você verá algo semelhante ao seguinte exemplo como o prompt do Azure PowerShell:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

A primeira parte do prompt indica que agora você está emitindo comandos do Azure PowerShell para o serviço de nuvem que contém a máquina virtual de destino. O nome do serviço de nuvem será diferente de "cloudservice4testing.cloudapp.net".

Agora é possível emitir comandos do Azure PowerShell para investigar os problemas adicionais citados acima e fazer correções de configuração.

### Para corrigir manualmente os Serviços de Área de Trabalho Remota escutando a porta TCP

Se você não conseguiu executar o [pacote de diagnóstico de IaaS (Windows) do Azure](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema de **conectividade do RDP a uma VM do Azure (Reinicialização Necessária)**, no prompt de sessão remota do Azure PowerShell, execute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

A propriedade PortNumber mostra o número de porta atual. Se necessário, altere o número de porta da Área de Trabalho Remota de volta para seu valor padrão (3389) usando este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verifique se a porta foi alterada para 3389 usando este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Saia da sessão remota do Azure PowerShell usando este comando.

	Exit-PSSession

Verifique se o ponto de extremidade da Área de Trabalho Remota para a máquina virtual do Azure também está usando a porta TCP 3398 como sua porta interna. Em seguida, reinicie a máquina virtual do Azure e tente estabelecer novamente a conexão de Área de Trabalho Remota.


## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows](virtual-machines-windows-reset-password.md)

[Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO3-->
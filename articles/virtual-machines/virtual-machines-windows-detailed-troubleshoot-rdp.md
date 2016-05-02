<properties
	pageTitle="Solução de problemas detalhada da Área de Trabalho Remota | Microsoft Azure"
	description="Etapas da solução de problemas detalhada de conexões de RDP ou Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/06/2016"
	ms.author="iainfou"/>

# Solução de problemas detalhada de conexões de Área de Trabalho Remota para máquinas virtuais do Azure baseadas em Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo fornece etapas detalhadas de solução de problemas a fim de diagnosticar e corrigir erros complexos de Área de Trabalho Remota para máquinas virtuais do Azure baseadas no Windows.

> [AZURE.IMPORTANT] Para eliminar os erros mais comuns da Área de Trabalho Remota, não deixe de ler [o artigo de solução de problemas básicos da Área de Trabalho Remota](virtual-machines-windows-troubleshoot-rdp-connection.md) antes de continuar.

Caso você receba uma mensagem de erro da Área de Trabalha Remota que não se assemelha a nenhuma das mensagens de erro específicas abordadas em [Guia de solução de problemas básicos da Área de Trabalho Remota](virtual-machines-windows-troubleshoot-rdp-connection.md), siga estas etapas e tente descobrir por que o cliente da [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (ou Área de Trabalho Remota) não consegue se conectar ao serviço RDP na VM do Azure.

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Para enviar um incidente, vá para o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes do Suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).


## Componentes de uma conexão da Área de Trabalho Remota

Estes são os componentes envolvidos em uma conexão de RDP:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, talvez seja útil revisar mentalmente o que mudou desde a última conexão bem-sucedida da Área de Trabalho Remota com a VM. Por exemplo:

- Se o endereço IP público da VM ou do serviço de nuvem que contém a VM (também chamado do endereço IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) tiver sido alterado, talvez a RDP tenha falhado porque cache do cliente DNS ainda tem o *endereço IP antigo* registrado para o nome DNS. Libere o cache do cliente DNS e tente conectar-se novamente à VM. Ou tente conectar-se diretamente com o novo VIP.
- Se você estiver usando um aplicativo de terceiro para gerenciar suas conexões de Área de Trabalho Remota em vez de usar qualquer um dos portais do Azure, verifique se a configuração do aplicativo inclui a porta TCP atual para o tráfego da Área de Trabalho Remota. Você pode verificar essa porta para uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), clicando em Configurações > Pontos de Extremidade da VM.


## Etapas preliminares

Antes de prosseguir para a solução de problemas detalhada,

- verifique o status da máquina virtual no Portal Clássico do Azure ou no Portal do Azure em busca de problemas óbvios
- Siga as [etapas de correção rápida para erros comuns de RDP no guia de solução de problemas básicos](virtual-machines-windows-troubleshoot-rdp-connection.md#quickfixrdp)


Tente reconectar-se à VM por meio da Área de Trabalho Remota após estas etapas.


## Solução de problemas detalhada

Talvez o cliente da Área de Trabalho Remota não consiga acessar o serviço da Área de Trabalho Remota na VM do Azure devido a problemas nas seguintes fontes:

- Computador cliente de Área de Trabalho Remota
- Dispositivo de borda de intranet da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- Máquina virtual baseada em Microsoft Azure

### Fonte 1: computador cliente de Área de Trabalho Remota

Verifique se o computador pode estabelecer conexões de Área de Trabalho Remota com outro computador local baseado em Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Se isso não for possível, verifique o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego de Área de Trabalho Remota
- Um software de proxy cliente instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Um software de monitoramento de rede instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/não permitem tipos específicos de tráfego e que estão impedindo conexões de Área de Trabalho Remota

Em todos esses casos, desabilite temporariamente o software e tente estabelecer uma conexão com um computador local por meio da Área de Trabalho Remota. Se você puder encontrar a causa real dessa forma, trabalhe com o administrador de rede para corrigir as configurações do software a fim de permitir as conexões de Área de Trabalho Remota.

### Fonte 2: dispositivo de borda de intranet da organização

Verifique se um computador conectado diretamente à Internet pode estabelecer conexões de Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que esteja diretamente conectado à Internet, crie e teste com uma nova máquina virtual do Azure em um grupo de recursos ou serviço de nuvem. Para obter mais informações, consulte [Criar uma máquina virtual que execute o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Após o teste, você pode excluir a máquina virtual e o grupo de recursos ou o serviço de nuvem.

Se você puder criar uma conexão de Área de Trabalho Remota com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda de intranet da organização:

- Um firewall interno bloqueando conexões HTTPS com a Internet
- Um servidor proxy impedindo conexões de Área de Trabalho Remota
- Software de detecção de intrusão ou monitoramento de rede executado em dispositivos em sua rede de borda que está impedindo conexões de Área de Trabalho Remota

Trabalhe com o administrador da rede para corrigir as configurações do dispositivo de borda de intranet da organização para permitir as conexões de Área de Trabalho Remota baseadas em HTTPS com a Internet.

### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

Para máquinas virtuais criadas usando o modelo de implantação clássico, verifique se outra máquina virtual do Azure que está no mesmo serviço de nuvem ou rede virtual pode estabelecer conexões da Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Para máquinas virtuais criadas no Gerenciador de Recursos, vá para [Fonte 4: Grupos de segurança de rede](#nsgs).

Se não houver outra máquina virtual no mesmo serviço de nuvem ou na mesma rede virtual, você poderá criar uma nova usando as etapas em [Criar uma máquina virtual que executa o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Exclua a máquina virtual extra após a conclusão do teste.

Se você puder se conectar a uma máquina virtual por meio da Área de Trabalho Remota no mesmo serviço de nuvem ou rede virtual, verifique o seguinte:

- A configuração do ponto de extremidade para o tráfego de Área de Trabalho Remota na VM de destino: a porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço de Área de Trabalho Remota da VM está escutando (o padrão é 3389).
- A ACL para o ponto de extremidade do tráfego da Área de Trabalho Remota na VM de destino: as ACLs permitem que você especifique o tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de Área de Trabalho Remota para o ponto de extremidade. Verifique suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou outro servidor de borda é permitido. Para saber mais, consulte [O que é uma ACL (Lista de Controle de Acesso)?](../virtual-network/virtual-networks-acl.md).

Para verificar se o ponto de extremidade é a fonte do problema, remova o ponto de extremidade atual e crie um novo, escolhendo uma porta aleatória no intervalo 49152-65535 para o número da porta externa. Para saber mais, consulte [Como configurar pontos de extremidade para uma máquina virtual](virtual-machines-windows-classic-setup-endpoints.md).

### <a id="nsgs"></a>Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Verifique as regras do Grupo de Segurança de Rede para verificar se o tráfego de Área de Trabalho Remota da Internet é permitido.

Para saber mais, confira [O que é um NSG (Grupo de Segurança de Rede)?](../virtual-network/virtual-networks-nsg.md).

### Fonte 5: máquina virtual do Azure baseada em Windows

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Use o [pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para ver se a falha ocorreu devido à própria máquina virtual do Azure. Caso você não consiga resolver o problema de **conectividade RDP a uma VM do Azure (Reinicialização Necessária)** com este pacote de diagnóstico, siga as instruções [neste artigo](virtual-machines-windows-reset-rdp.md) para redefinir o serviço da Área de Trabalho Remota na máquina virtual. Isso vai:

- Habilitar a regra de firewall padrão do Windows de "Área de Trabalho Remota" (porta TCP 3389).
- Habilitar conexões de Área de Trabalho Remota definindo o valor do Registro HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections como 0.

Tente estabelecer a conexão de seu computador novamente. Se você ainda não conseguir conectar-se via Área de Trabalho Remota, verifique os possíveis problemas a seguir:

- O serviço de Área de Trabalho Remota não está em execução na VM de destino.
- O serviço de Área de Trabalho Remota não está escutando na porta TCP 3389.
- O Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de Área de Trabalho Remota.
- Um software de detecção de intrusão ou monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de Área de Trabalho Remota.

Para VMs criadas usando o modelo de implantação clássico, você pode usar uma sessão remota do Azure PowerShell para a máquina virtual do Azure. Primeiro, você precisa instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Acesse [Configure Secure Remote PowerShell Access to Azure Virtual Machines](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM.ps1** no computador local.

Em seguida, instale o Azure PowerShell, se ainda não tiver feito isso. Consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Em seguida, abra um prompt de comando do Azure PowerShell e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM.ps1**. Para executar um script do Azure PowerShell, é preciso definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome de sua assinatura do Azure, o nome do serviço de nuvem e o nome da máquina virtual (removendo os caracteres < and >) e execute estes comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Você pode obter o nome da assinatura correto na propriedade _SubscriptionName_ da exibição do comando **Get-AzureSubscription**. Você pode obter o nome do serviço de nuvem para a máquina virtual na coluna _ServiceName_ da exibição do comando **Get-AzureVM**.

Verifique se você tem o novo certificado, abra um snap-in de Certificados para o usuário atual e examine a pasta **Autoridades de Certificação Confiáveis\\Certificados**. Você deve ver um certificado com o nome DNS de seu serviço de nuvem na coluna Emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do Azure PowerShell usando estes comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Depois de inserir credenciais de administrador válidas, você verá algo semelhante ao seguinte exemplo como o prompt do Azure PowerShell:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

A primeira parte desse prompt é seu nome de serviço de nuvem que contém a VM de destino, que pode ser diferente de "cloudservice4testing.cloudapp.net". Agora é possível emitir comandos do Azure PowerShell para esse serviço de nuvem para investigar os problemas adicionais mencionados acima e fazer correções de configuração.

### Para corrigir manualmente os Serviços de Área de Trabalho Remota escutando a porta TCP

Se você não tiver conseguido executar o [pacote de diagnóstico de IaaS (Windows) do Azure](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema de **conectividade do RDP a uma VM do Azure (Reinicialização Necessária)**, no prompt da sessão remota do Azure PowerShell, execute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

A propriedade PortNumber mostra o número de porta atual. Se necessário, altere o número de porta da Área de Trabalho Remota de volta para seu valor padrão (3389) usando este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verifique se a porta foi alterada para 3389 usando este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Saia da sessão remota do Azure PowerShell usando este comando.

	Exit-PSSession

Verifique se o ponto de extremidade da Área de Trabalho Remota para a VM do Azure também está usando a porta TCP 3398 como sua porta interna. Reinicie a VM do Azure e tente novamente a conexão de Área de Trabalho Remota.


## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows](virtual-machines-windows-reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0420_2016-->
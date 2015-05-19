<properties 
	pageTitle="Solucionar problemas de conexões de Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows" 
	description="Saiba como restaurar a conectividade de RDP (Área de Trabalho Remota ) para sua máquina virtual do Azure com o diagnóstico e as etapas para isolar a origem do problema."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="josephd"/>

# Solucionar problemas de conexões de Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows

Este tópico descreve uma abordagem metódica para a correção e a determinação da causa raiz de conexões de Área de Trabalho Remota para Máquinas Virtuais do Azure baseadas no Windows.

## Etapa 1: executar o pacote de diagnóstico do Azure IaaS

Para resolver muitos dos problemas comuns referentes à criação de conexões de Área de Trabalho Remota, a Microsoft criou um [pacote de diagnóstico do Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Clique em **Pacote de diagnóstico do Microsoft Azure IaaS \(Windows\)** nesta página para criar uma nova sessão de diagnóstico.
2.	Na página **Quais dos seguintes problemas você está enfrentando com sua VM do Azure?**, selecione o problema **Conectividade RDP a uma VM do Azure \(necessário reinicializar\)**. 

Para obter mais informações, consulte o artigo da Base de Conhecimento do [pacote de diagnóstico do Microsoft Azure IaaS \(Windows\)](http://support.microsoft.com/kb/2976864).

Se a execução do pacote de diagnóstico do Azure IaaS não corrigir o problema ou se não for possível executar o pacote de diagnóstico, talvez seja necessária uma solução de problemas mais detalhada, descrita nas etapas a seguir.

> [AZURE.NOTE]O pacote de diagnóstico do Azure IaaS \(Windows\) deve ser executado em um computador que esteja executando o Windows 8, Windows 8.1, Windows Server 2012 ou Windows Server 2012 R2.

## Etapa 2: determinar a mensagem de erro do cliente de Área de Trabalho Remota

Use estas seções com base na mensagem de erro que você receber.

### Janela de mensagem de Conexão de Área de Trabalho Remota: a sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença.

Causa: o período de cortesia de licenciamento de 120 dias para a função de Servidor de Área de Trabalho Remota expirou e você precisa instalar licenças.
 
Como solução temporária, salve uma cópia local do arquivo RDP do Portal de Gerenciamento do Azure e, em seguida, execute este comando no prompt de comando do Windows PowerShell para conectar-se.

	mstsc <File name>.RDP /admin

Isso desabilitará o licenciamento apenas para essa conexão.

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à máquina virtual, você poderá usar o Gerenciador de Servidores para remover a função de Servidor de Área de Trabalho Remota.

Consulte também a postagem de blog [A M do Azure falha com "Sem Servidores de Licença da Área de Trabalho Remota disponíveis"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

### Janela de mensagem de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não consegue localizar o computador "nome".

Causa: o cliente da Área de Trabalho Remota em seu computador não pode resolver o nome do computador nas configurações do arquivo RDP.

Aqui está um exemplo do arquivo RDP gerado pelo Portal de Gerenciamento do Azure:

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

A parte do endereço consiste no nome de domínio totalmente qualificado do serviço de nuvem que contém a máquina virtual \(tailspin-azdatatier.cloudapp.net neste exemplo\) e a porta TCP externa do ponto de extremidade para o tráfego de Área de Trabalho Remota \(55919\).

Soluções possíveis para esse problema:

- Se você estiver na intranet de uma organização, verifique se o computador tem acesso ao servidor proxy e pode enviar o tráfego HTTPS para ele.
- Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo Portal de Gerenciamento do Azure para garantir que você tem o nome correto para o serviço de nuvem e a porta do ponto de extremidade da máquina virtual.

### Janela de mensagem de segurança do Windows: suas credenciais não funcionaram.

Causa: o nome da conta e a senha que você enviou não podem ser validados pela máquina virtual à qual você está se conectando.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou de uma conta baseada em domínio.

- Para contas locais, use a <computer name>sintaxe \<nome da conta\> \(exemplo: SQL1\\Admin4798\). 
- Para contas de domínio, use a <domain name>sintaxe \<nome\_da\_conta\> \(exemplo: CONTOSO\\eduardogomes\).

Para computadores promovidos a controladores de domínio em uma nova floresta do AD, a conta de administrador local à qual você está conectado ao realizar a promoção é convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta de administrador local anterior é excluída. Por exemplo, se você estiver conectado com a conta de administrador local DC1\\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\\DCAdmin será excluída, e uma nova conta de domínio CORP\\DCAdmin será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida. Verifique a senha para garantir que ela está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para Máquinas Virtuais do Windows](virtual-machines-windows-reset-password.md).

### Janela de mensagem de conexão de Área de Trabalho Remota: este computador não pode se conectar ao computador remoto.

Causa: a conta que você está usando para se conectar não tem direitos de logon na Área de Trabalho Remota.

Cada computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que têm o direito de fazer logon com uma conexão de Área de Trabalho Remota. Os membros do grupo local Administradores também têm acesso, mesmo que essas contas não sejam listadas como membros do grupo local Usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local Administradores também contém os Administradores de Domínio para o domínio.

Verifique se a conta que você está usando para iniciar a conexão tem direitos de logon de área de trabalho remota. Use uma conta de administrador local ou de administrador de domínio como uma solução temporária para criar uma conexão de Área de Trabalho Remota e adicione a conta desejada ao grupo local Usuários da Área de Trabalho Remota usando o snap-in Gerenciamento de Computador \(\*\*Ferramentas do Sistema \> Usuários e Grupos Locais \> Grupos \> Usuários da Área de Trabalho Remota\*\*\).

### Janela de mensagem de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não pode se conectar ao computador remoto por um destes motivos...

Causa: o cliente de Área de Trabalho Remota não pode acessar os Serviço de Área de Trabalho Remota na máquina virtual. Esse problema pode ocorrer devido a várias causas raiz.

Aqui está o conjunto de componentes envolvidos.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)
 
Antes de mergulhar em um processo passo a passo de solução de problemas, é útil examinar mentalmente o que foi alterado desde que você conseguiu criar com êxito conexões de Área de Trabalho Remota e usar essa alteração como base para corrigir o problema. Por exemplo:

- Se você conseguiu criar conexões de Área de Trabalho Remota e alterou o endereço IP público do serviço de nuvem que contém sua máquina virtual \(também conhecido como endereço IP virtual, ou [VIP]\), o cache do cliente DNS deve ter uma entrada para o nome DNS do serviço de nuvem e o *endereço IP antigo*. Libere o cache do cliente DNS e tente novamente. Como alternativa, tente estabelecer a conexão usando o novo VIP.
- Se você deixou de usar o Portal de Gerenciamento do Azure ou o Portal de Visualização do Azure e passou a usar um aplicativo para gerenciar suas conexões de Área de Trabalho Remota, verifique se a configuração do aplicativo inclui a porta TCP determinada de forma aleatória para o tráfego de Área de Trabalho Remota. 

As seções a seguir explicam como isolar e determinar as várias causas raiz para esse problema e fornecem soluções e soluções alternativas.

## Etapa 3: Etapas preliminares antes da solução de problemas detalhada

Execute estas etapas:

- Verificar o status da máquina virtual no Portal de Gerenciamento do Azure ou no Portal de Visualização do Azure
- [Reiniciar a máquina virtual](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx)

Depois dessas etapas, tente estabelecer novamente a conexão de Área de Trabalho Remota.

## Etapa 4: Solução de problemas detalhada 

A incapacidade do cliente de Área de Trabalho Remota de acessar os Serviços de Área de Trabalho Remota na máquina virtual do Azure pode ser causada pelas seguintes fontes de problemas ou configurações incorretas:

- Computador cliente de Área de Trabalho Remota
- Dispositivo de borda de intranet da organização
- Ponto de extremidade de serviço de nuvem e ACL \(lista de controle de acesso\)
- Grupos de segurança de rede
- Máquina virtual baseada no Windows Azure

### Fonte 1: computador cliente de Área de Trabalho Remota

Para eliminar seu computador como a fonte dos problemas ou erros de configuração, verifique se o computador pode estabelecer conexões de Área de Trabalho Remota com outro computador local baseado no Windows.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)
 
Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego de Área de Trabalho Remota
- Um software de proxy cliente instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Um software de monitoramento de rede instalado localmente que está impedindo conexões de Área de Trabalho Remota
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/não permitem tipos específicos de tráfego e que estão impedindo conexões de Área de Trabalho Remota

Em todos esses casos, tente desabilitar temporariamente o software e tente estabelecer uma conexão de Área de Trabalho Remota com um computador local para determinar a causa raiz. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software para permitir as conexões de Área de Trabalho Remota.

### Fonte 2: dispositivo de borda de intranet da organização

Para eliminar o dispositivo de borda de intranet de sua organização como a fonte dos problemas ou erros de configuração, verifique se um computador conectado diretamente à Internet pode estabelecer conexões de Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)
 
Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova máquina virtual do Azure em seu próprio serviço de nuvem e usá-la. Para obter mais informações, consulte [Criar uma máquina virtual que execute o Windows no Azure](virtual-machines-windows-tutorial.md). Exclua a máquina virtual e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão de Área de Trabalho Remota com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda de intranet da organização:

- Um firewall interno que está bloqueando conexões HTTPS com a Internet
- Seu servidor proxy que está impedindo conexões de Área de Trabalho Remota
- Software de detecção de intrusão ou monitoramento de rede executado em dispositivos em sua rede de borda que está impedindo conexões de Área de Trabalho Remota

Trabalhe com o administrador da rede para corrigir as configurações do dispositivo de borda de intranet da organização para permitir as conexões de Área de Trabalho Remota baseadas em HTTPS com a Internet.

### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

Para eliminar o ponto de extremidade de serviço de nuvem e a ACL como a fonte dos problemas ou erros de configuração, verifique se outra máquina virtual do Azure que está no mesmo serviço de nuvem pode estabelecer conexões de Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)
 
Se não tiver outra máquina virtual no mesmo serviço de nuvem, você poderá criar facilmente uma nova. Para obter mais informações, consulte [Criar uma máquina virtual que execute o Windows no Azure](virtual-machines-windows-tutorial.md). Exclua a máquina virtual extra ao concluir o teste.

Se você puder criar uma conexão de Área de Trabalho Remota com uma máquina virtual no mesmo serviço de nuvem, verifique o seguinte:

- A configuração de ponto de extremidade para o tráfego de Área de Trabalho Remota na máquina virtual de destino. A porta TCP privada do ponto de extremidade deve corresponder à porta TCP em que os Serviços de Área de Trabalho Remota na máquina virtual estão escutando, que, por padrão, é 3389. 
- A ACL para o ponto de extremidade de tráfego de Área de Trabalho Remota na máquina virtual de destino. As ACLs permitem que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de Área de Trabalho Remota para o ponto de extremidade. Examine suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [Sobre ACLs \(Listas de Controle de Acesso\) de rede](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Para eliminar o ponto de extremidade como a fonte do problema, remova o ponto de extremidade atual e crie um novo ponto de extremidade, escolhendo uma porta aleatória no intervalo 49152-65535 para o número da porta externa. Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-set-up-endpoints.md).

### Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede lhe proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego de Área de Trabalho Remota da Internet é permitido.

Para obter mais informações, consulte [Sobre grupos de segurança de rede](https://msdn.microsoft.com/library/azure/dn848316.aspx).

### Fonte 5: máquina virtual do Azure baseada no Windows

O último conjunto de problemas possíveis está na máquina virtual do Azure em si.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)
 
Primeiro, se você não conseguiu executar o [Pacote de diagnóstico do Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema **Conectividade RDP a uma VM do Azure \(necessário reinicializar\)**, siga as instruções em [Como redefinir uma senha ou o Serviço de Área de Trabalho Remota para máquinas virtuais do Windows](virtual-machines-windows-reset-password.md) para redefinir os Serviços de Área de Trabalho Remota na máquina virtual. Isso vai:

- Habilitar a regra de firewall padrão do Windows de "Área de Trabalho Remota" \(porta TCP 3389\).
- Habilitar conexões de Área de Trabalho Remota definindo o valor do Registro HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections como 0.

Tente estabelecer a conexão de seu computador novamente. Se você não tiver êxito, estes são alguns dos problemas possíveis:

- Os Serviços de Área de Trabalho Remota não estão em execução na máquina virtual de destino.
- Os Serviços de Área de Trabalho Remota não estão escutando na porta TCP 3389.
- O Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de Área de Trabalho Remota.
- Um software de detecção de intrusão ou monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de Área de Trabalho Remota.

Para corrigir esses possíveis problemas, você pode usar uma sessão remota do PowerShell para a máquina virtual do Azure. Primeiro, você deve instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Vá para [Configurar o acesso remoto seguro do PowerShell para máquinas virtuais do Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM.ps1** para uma pasta no computador local.

Em seguida, instale o PowerShell do Azure, se ainda não tiver feito isso. Consulte [Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md).

Em seguida, abra um prompt de comando do PowerShell do Azure e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM.ps1**. Para executar um script do PowerShell, você deve definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome de sua assinatura do Azure, o nome do serviço de nuvem e o nome da máquina virtual \(removendo os caracteres < and >\) e execute estes comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Você pode obter o nome de assinatura correto na propriedade SubscriptionName da exibição do comando **Get-AzureSubscription**. Você pode obter o nome do serviço de nuvem para a máquina virtual na coluna ServiceName da exibição do comando **Get-AzureVM**.

Para provar que você tem esse novo certificado, abra um snap-in Certificados voltado para o usuário atual e examine a pasta **Trusted Root Certification Authorities\\Certificates**. Você deve ver um certificado com o nome DNS de seu serviço de nuvem na coluna Emitido para \(exemplo: cloudservice4testing.cloudapp.net\).

Em seguida, inicie uma sessão remota do PowerShell com esses comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Depois de inserir credenciais de administrador válidas, você verá algo semelhante ao seguinte exemplo como o prompt do PowerShell do Azure:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

A primeira parte do prompt indica que agora você está emitindo comandos do PowerShell para o serviço de nuvem que contém a máquina virtual de destino. O nome do serviço de nuvem será diferente de "cloudservice4testing.cloudapp.net".

Agora você pode emitir comandos do PowerShell para investigar os problemas adicionais citados acima e fazer correções de configuração.

### Para corrigir manualmente os Serviços de Área de Trabalho Remota escutando na porta TCP

Se você não conseguiu executar o [Pacote de diagnóstico do Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o  problema **Conectividade RDP a uma VM do Azure \(necessário reinicializar\)**, no prompt de sessão remota do PowerShell, execute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

A propriedade PortNumber mostra o número de porta atual. Se necessário, altere o número de porta da Área de Trabalho Remota de volta para seu valor padrão \(3389\) com este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verifique se a porta foi alterada para 3389 com este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Saia da sessão remota do PowerShell com este comando.

	Exit-PSSession

Verifique se o ponto de extremidade da Área de Trabalho Remota para a máquina virtual do Azure também está usando a porta TCP 3398 como sua porta interna. Em seguida, reinicie a máquina virtual do Azure e tente estabelecer novamente a conexão de Área de Trabalho Remota.

## Etapa 5: enviar seu problema aos fóruns de suporte do Azure

Para obter ajuda de especialistas do Azure do mundo inteiro, você pode enviar seu problema aos fóruns do Azure do MSDN ou do Stack Overflow. Consulte [Fóruns do Microsoft Azure](http://azure.microsoft.com/support/forums/) para obter mais informações.

## Etapa 6: enviar um incidente de suporte do Azure

Se você tiver executado o [Pacote de diagnóstico do Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema **Conectividade RDP a uma VM do Azure \(necessário reinicializar\)** ou tiver concluído as etapas 2 a 5 neste artigo e tiver enviado seu problema aos fóruns de suporte do Azure, mas ainda não conseguir criar uma conexão de Área de Trabalho Remota, uma alternativa é verificar se você pode recriar a máquina virtual.

Se você não puder recriar a máquina virtual, talvez seja hora de enviar um incidente de suporte do Azure.

Para enviar um incidente, vá para o [Site de suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

Para obter informações sobre como usar o Suporte do Azure, consulte as [Perguntas frequentes de suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](virtual-machines-windows-reset-password.md)

[Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Perguntas frequentes sobre máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)


<!--HONumber=54-->
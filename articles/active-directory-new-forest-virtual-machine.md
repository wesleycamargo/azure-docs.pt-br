<properties urlDisplayName="Active Directory forest" pageTitle="Instalar uma floresta do Active Directory em uma rede virtual do Azure" metaKeywords="" description="Um tutorial que explica como criar uma nova floresta de Active Directory em uma máquina virtual (VM) na rede virtual do Azure." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="Justinha" />




#Instalar uma nova floresta do Active Directory em uma rede virtual do Azure

Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server em uma rede virtual do Azure que se encontra em uma máquina virtual (VM) em uma [rede virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx). Nesse caso, a rede virtual do Azure não está conectada a uma rede local. 

Você também pode estar interessado nestes tópicos relacionados:

- Você também pode [configurar uma VPN site a site usando o Assistente do Portal de Gerenciamento](http://msdn.microsoft.com/pt-br/library/windowsazure/dn133795.aspx) e instalar uma nova floresta ou estender uma floresta local para uma rede virtual do Azure. Para essas etapas, consulte [Instalar um controlador de domínio Active Directory de réplica em uma rede virtual do Azure](http://www.windowsazure.com/pt-br/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx).
-  Para obter orientações passo a passo para criar um ambiente de laboratório de teste no Azure que inclua o AD DS, consulte [Guia do laboratório de teste: Configuração base do Windows Server 2012 R2 no Azure](http://www.microsoft.com/pt-br/download/details.aspx?id=41684).



##Sumário##

* [Como isso difere do local?](#differ)
* [Etapa 1. Crie uma rede virtual do Azure](#createvnet)
* [Etapa 2: Criar uma VM para executar as funções de controlador de domínio e servidor DNS](#createvm)
* [Etapa 3: Instalar o Windows Server Active Directory](#installad)
* [Etapa 4: Configurar o servidor DNS da rede virtual do Azure](#dns)
* [Etapa 5: Criar VMs para membros de domínio e ingressar no domínio](#domainmembers)


<h2><a id="differ"></a>Como isso difere do local?</h2>
Não há muita diferença entre instalar um controlador de domínio no Azure em comparação com um local. As diferenças principais estão listadas na tabela a seguir. 

Para configurar...  | Local | Rede virtual do Azure	
------------- | -------------  | ------------
**Endereço IP do controlador de domínio** |Atribuir endereço IP estático nas propriedades do adaptador de rede | Executar o cmdlet Set-AzureStaticVNetIP para atribuir uma endereço IP estático
**Resolvedor do cliente DNS** | Definir endereço de servidor DNS preferencial e alternativo nas propriedades do adaptador de rede dos membros do domínio | Definir endereço de servidor DNS nas propriedades de rede virtual
**Armazenamento de banco de dados do Active Directory** Como opção, altere o local de armazenamento padrão de C:\   | Você precisa alterar o local de armazenamento padrão de C:\



<h2><a id="createvnet"></a>Etapa 1: Crie uma rede virtual do Azure</h2>
1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Crie uma rede virtual. Clique em <b>Redes</b> > <b>Criar uma rede virtual</b>. Use os valores da tabela a seguir para concluir o assistente. 

	Nessa página do assistente... | Especifique esses valores
	------------- | -------------
	**Detalhes da rede virtual** | <p>Nome: Digite um nome para a sua rede virtual</p><p>Região: Escolha a região mais próxima</p>
	**DNS e VPN** | <p>Deixe o servidor DNS em branco</p><p>Não selecione a opção de VPN</p>
	**Espaços de endereço de rede virtual** | <p>Nome da sub-rede: Digite um nome para a sua sub-rede</p><p>IP Inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Etapa 2: Crie uma VM para executar as funções de controlador de domínio e servidor DNS</h2>
 
1. Clique em <b>Novo</b> > <b>Computação</b> > <b>Máquina Virtual</b> > <b>Da Galeria</b>. 
2. Use os valores da tabela a seguir para concluir o assistente.

	Nessa página do assistente... | Especifique esses valores
	------------- | -------------
	**Sistema operacional** | Selecione **Windows Server 2012 R2 Datacenter**
	**Configuração da máquina virtual** | <p>Data do lançamento: Data de hoje</p><p>Nome da máquina: Especifique um único valor</p><p>Camada: Standard</p><p>Tamanho: Selecione um tamanho</p><p>Nome do usuário: Digite um nome. Essa conta de usuário será um membro do grupo interno Administradores. </p><p>Senha: Deve ter pelo menos 8 caracteres e incluir 3 dos seguintes tipos de caracteres:</p><ul><li>uma letra maiúscula</li><li>uma letra minúscula</li><li>um número</li><li>um caractere especial</li></ul>
	**Serviço de nuvem** | <p>Serviço de Nuvem: <b>Crie um novo serviço de nuvem</b></p><p>Nome do serviço de nuvem: Aceitar valor padrão</p><p>Region/AffinityGroup/VirtualNetwork: Selecione a rede virtual que você criou</p><p>Sub-rede da rede virtual: Selecione a sub-rede da rede virtual que você criou. </p><p>Conta de armazenamento: <b>Use uma conta de armazenamento gerada automaticamente</b></p><p>Conjunto de disponibilidade: <b>Nenhum</b></p><p>Pontos de extremidade: Aceitar valores padrão</p>
	**Agente de VM** | Selecione **Instalar o Agente VM**

1. O endereço IP dinâmico atribuído à VM por padrão é válido durante o serviço de nuvem. Mas ele mudará se a VM for desligada. Atribua um endereço IP estático ao [executar o cmdlet do PowerShell do Azure Set-AzureStaticVNetIP](http://msdn.microsoft.com/pt-br/library/windowsazure/dn630228.aspx) de forma que o endereço IP será mantido se você precisar desligar a VM alguma vez. 
2. Conecte um disco adicional à VM para armazenar o banco de dados, os logs e o SYSVOL do Active Directory. 
  3. Click the <b>VM</b> > <b>Anexar</b> > <b>Anexar disco vazio</b>. 
  4. Especifique um tamanho (por exemplo, 10 GB) e aceite todos os outros valores padrão.
3. Faça logon na VM e formate o disco adicional. 
  4. Clique em <b>Connect</b> para fazer logon na VM, clique em <b>Aberto</b> para criar uma sessão RDP e clique em <b>Connect</b> novamente.
  4. Altere as credenciais para o novo nome de usuário e a senha que você especificou.
  5. No Gerenciador do servidor, clique em <b>Ferramentas</b> > <b>Gerenciamento do computador</b>. 
  6. Clique em <b>Gerenciamento de disco</b> e clique em <b>OK</b> para inicializar o novo disco. 
  6. Clique com o botão direito do mouse no nome do disco e clique em <b>Novo Volume Simples</b>. Conclua o assistente para formatar a nova unidade. 

<h2><a id="installad"></a>Etapa 3: Instale o Windows Server Active Directory</h2>
[Instale o AD DS](http://technet.microsoft.com/library/jj574166.aspx) usando a mesma rotina que você usa no local (ou seja, você pode usar a interface do usuário, um arquivo de resposta ou o Windows PowerShell). Você precisa fornecer credenciais de administrador para instalar uma nova floresta. Para especificar o local do banco de dados, dos logs e do SYSVOL do Active Directory, altere o local de armazenamento padrão da unidade do sistema operacional pra o disco de dados adicional que você conectou à VM. 
<p>Após a instalação do DC, conecte-se novamente à VM e faça logon no DC. Não se esqueça de especificar credenciais de domínio.</p>

<h2><a id="dns"></a>Etapa 4: Configure o servidor DNS da rede virtual do Azure</h2>
1. Clique em <b>Redes Virtuais</b>, clique duas vezes na rede virtual que você criou e clique em <b>Configurar</b>. 
2. Em <b>Servidores DNS</b>, digite o nome e o DIP do DC e clique em <b>Salvar</B>. 
3. Selecione a máquina virtual e clique em <b>Reiniciar</b> para disparar a VM e definir as configurações de resolvedor DNS com o endereço IP do novo servidor DNS. 


<h2><a id="domainmembers"></a>Etapa 5: Crie VMs para membros de domínio e ingressar no domínio</h2>
<p>Crie VMs adicionais para provisionar computadores membro do domínio. Você pode usar a interface do usuário ou o PowerShell do Azure. Se você usar a interface do usuário, basta seguir as mesmas etapas que você usou para criar a primeira VM. Em seguida, una as VMs ao domínio como você faria no local. Se usar o PowerShell do Azure, você poderá provisionar VMs e ingressá-las no domínio quando forem iniciadas pela primeira vez. </p><p>Esse exemplo irá criar uma VM de domínio associado chamada de DC2 que executa o Windows Server 2012 R2 Datacenter. Após a DC2 ser provisionada, faça logon como Adm do Domínio e promova-a para ser uma réplica de DC. </p><p>Você pode executar o Get-AzureVMImage para obeter nomes de imagem. Por exemplo, para retornar uma lista de imagens para o Windows Server 2012 R2, execute o Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
	'

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
	$service = 'DC2'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Se reexecutar o script, você precisará fornecer um valor exclusivo para $service. Você pode executar Test-AzureName-Service <i>nome do serviço</i>, que retorna se o nome já existe. 	

## Consulte também

-  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configure uma rede virtual somente em nuvem no Portal de Gerenciamento](http://msdn.microsoft.com/pt-br/library/dn631643.aspx)

-  [Configure um VPN site a site no Portal de Gerenciamento](http://msdn.microsoft.com/pt-br/library/dn133795.aspx)

-  [Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure](http://azure.microsoft.com/pt-br/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Como instalar e configurar o PowerShell do Azure](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/)

-  [PowerShell do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156055.aspx)

-  [Cmdlets gerenciamento do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841)

-  [Definir o endereço IP estático da VM do Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Como atribuir um IP estático para uma VM do Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Instalar uma nova floresta do Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Introdução à virtualização de Serviços de Domínio Ative Directory (AD DS) (nível 100)](http://technet.microsoft.com/pt-br/library/hh831734.aspx)

-  [Guia de Laboratório de Teste: Configuração base do Windows Server 2012 R2 no Azure](http://www.microsoft.com/pt-br/download/details.aspx?id=41684)


<!--HONumber=35.2-->

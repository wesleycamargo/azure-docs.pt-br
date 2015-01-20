<properties urlDisplayName="Replica domain controller" pageTitle="Instalar um controlador de domínio de réplica no Azure" metaKeywords="" description="Um tutorial que explica como instalar um controlador de domínio de uma floresta do Active Directory no local em uma máquina virtual do Azure." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller on an Azure Virtual Network" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="Justinha" />


#Instalar um controlador de domínio de réplica do Active Directory em Redes Virtuais do Azure

Este tópico mostra como instalar controladores de domínio adicionais (também conhecidos como controladores de domínio de réplica) para um domínio Active Directory local em máquinas virtuais (VMs) do Azure numa rede virtual do Azure. 

Você também pode estar interessado nestes tópicos relacionados:

- Opcionalmente, você pode instalar uma nova floresta do Active Directory em uma rede virtual do Azure. Para essas etapas, consulte [instalar uma nova floresta do Active Directory em uma rede virtual do Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/).
-  Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Para obter orientações passo a passo para criar um ambiente de laboratório de teste no Azure que inclua o AD DS, consulte [Guia do laboratório de teste: Configuração base no Azure](http://www.microsoft.com/pt-br/download/details.aspx?id=41684).


##Sumário##

* [Diagrama do cenário](#diagram)
* [Etapa 1: Crie um site do Active Directory para a rede virtual do Azure](#createadsite)
* [Etapa 2: Crie uma rede virtual do Azure](#createvnet)
* [Etapa 3: Crie máquinas virtuais do Azure para as funções de controlador de domínio](#createdcvms)
* [Etapa 4: Instale o AD DS em máquinas virtuais do Azure](#installadds)
* [Etapa 5: Crie VMs para servidores de aplicativos](#createappvms)
* [Recursos adicionais](#resources)

<h2><a id="diagram"></a>Diagrama do cenário</h2>

Nesse cenário, os usuários externos precisam acessar os aplicativos que são executados ingressados no domínio. As VMs que executam os servidores de aplicativos e os controladores de domínio de réplica são instaladas em uma rede virtual do Azure. A rede virtual pode ser conectada à rede local por uma conexão [VPN site a site](http://msdn.microsoft.com/library/azure/dn133795.aspx), conforme mostrado no diagrama a seguir, ou você pode usar a [Rota Expressa](http://azure.microsoft.com/services/expressroute/) para uma conexão mais rápida. 

Os servidores de aplicativos e os controladores de domínio são implantados em [serviços de nuvem](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/) separados para distribuir o processamento de computação e em [conjuntos de disponibilidade](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) para melhorar a tolerância. 
Os DCs se replicam entre si e com os DCs locais por meio da replicação do Active Directory. Nenhuma ferramenta de sincronização é necessária.

![][1]

<h2><a id="createadsite"></a>Etapa 1: Crie um site do Active Directory para a rede virtual do Azure</h2>

É uma boa idéia criar um site no Active Directory que represente a região de rede correspondente à rede virtual. Que ajude a otimizar a autenticação, replicação e outras operações de localização do controlador de domínio. As etapas a seguir explicam como criar um site e para obter mais informações, consulte [Adicionando um novo Site](http://technet.microsoft.com/library/cc781496.aspx).

1. Abra os Sites e Serviços do Active Directory: **Gerenciador de Servidores** > **Ferramentas** > **Sites e Serviços do Active Directory**.
2. Crie um site para representar a região onde você criou uma rede virtual do Azure: clique em **Sites** > **Ação** > **Novo site** > digite o nome do novo site, como Azure US West > selecione um link de site > **OK**.
3. Crie uma sub-rede e associe-a ao novo site: clique duas vezes em **Sites** > clique com o botão direito em **Sub-redes** > **Nova sub-rede** > digite o intervalo de endereços IP da rede virtual (como 10.1.0.0/16 no diagrama do cenário) > selecione o novo site do Azure > **OK**.

<h2><a id="createvnet"></a>Etapa 2: Crie uma rede virtual do Azure</h2>

<ol><li><p>In the Azure Management Portal, click <b>New</b> > <b>Network Services</b> > <b>Virtual Network</b> > <b>Custom Create</b> and use the following values to complete the wizard.</p>

<table style="width:100%">
<tr>
<td>Nesta página do assistente</td>
<td>Especifique esses valores</td>
</tr>
<tr>
<td><b>Detalhes de rede virtual</b></td>
<td><ul><li>Nome: Digite um nome para a rede virtual, como WestUSVNet.</li><li>Região: Esse é o local do Azure onde a rede virtual estará localizada, como West US. Esse local não pode ser alterado depois da rede virtual ser criada.</li></ul>
</td>
</tr>
<tr>
<td><b>Conectividade entre Servidores DNS e VPN</b></td>
<td><ul><li>Servidores DNS: Especifique o nome e o endereço IP de um ou mais servidores DNS locais.</li><li>Conectividade: Selecione <b>Configurar uma VPN site a site</b>.</li><li>Rede local: Especifique uma nova rede local.</li></ul>Se você estiver usando a Rota Expressa em vez de uma VPN, consulte <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Configurar uma conexão de Rota Expressa por meio de um provedor do Exchange</a>.</td>
</tr>
<tr>
<td><b>Conectividade site a site</b></td>
<td><ul><li>Nome: Digite um nome para a rede local.</li><li>Endereço IP do dispositivo VPN: Especifique o endereço IP público do dispositivo que se conectará à rede virtual. O dispositivo VPN não pode ser localizado por trás de um NAT.</li><li>Endereço: Especifique os intervalos de endereços para sua rede local (como 192.168.0.0/16 no diagrama do cenário).</li></ul></td>
</tr>
<tr>
<td><b>Espaços de endereço da rede virtual</b></td>
<td><ul><li>Espaço de endereço: Especifique o intervalo de endereços IP para as máquinas virtuais que você deseja executar na rede virtual do Azure (como 10.1.0.0/16 no diagrama do cenário). Este intervalo de endereços não pode sobrepor os intervalos de endereços da rede local.</li><li>Sub-redes: Especifique um nome e endereço de sub-rede para servidores de aplicativos (como Front-end, 10.1.1.0/24) e para os controladores de domínio (como Back-end, 10.1.2.0/24).</li><li>Clique em <b>adicionar sub-rede de gateway</b>.</li></ul></td>
</tr>
</table>
</li>
<li><p>Em seguida, você configurará o gateway de rede virtual para criar uma conexão VPN site a site segura. Consulte <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurar um Gateway de Rede Virtual no Portal de gerenciamento</a> para obter instruções.</p>
</li>
<li><p>Crie a conexão VPN site a site entre a nova rede virtual e um dispositivo VPN local. Consulte <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurar um Gateway de Rede Virtual no Portal de gerenciamento</a> para obter instruções.</p>
</li>
</ol>

<h2><a id="createdcvms"></a>Etapa 3: Crie máquinas virtuais do Azure para as funções de controlador de domínio</h2>

<p>Repita as etapas a seguir para criar VMs para hospedar a função de controlador de domínio, conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância à falhas. </p>


<ol><li><p>In the Azure Management portal, click <b>New</b> > <b>Compute</b> > <b>Virtual Machine</b> > <b>From Gallery</b>. Use the following values to complete the wizard. Accept the default value for a setting unless another value is suggested or required.</p>
<table style="width:100%">
<tr>
<td><b>Nesta página do assistente</b></td>
<td><b>Especifique esses valores</b></td>
</tr>
<tr>
<td><b>Escolha uma imagem</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuração de Máquina Virtual</b></td>
<td><ul><li>Nome da Máquina Virtual: Digite um nome de rótulo único (como AzureDC2).</li><li>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</li><li>Nova senha/confirmar: Digite uma senha</li></ul></td>
</tr>
<tr>
<td><b>Configuração de máquina virtual</b></td>
<td><ul><li>Serviço de Nuvem: Escolha <b>Crie um novo serviço de nuvem</b> para a primeira máquina virtual e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão a função de controlador de domínio.</li><li>Nome DNS do Serviço de Nuvem Especifique um nome exclusivo</li><li>Região/grupo de afinidade/rede virtual: Especifique o nome da rede virtual (como WestUSVNet).</li><li>Conta de Armazenamento: Escolha <b>Use uma conta de armazenamento gerada automaticamente</b> para a primeira máquina virtual e selecione esse mesmo nome de conta de armazenamento ao criar mais VMs que hospedarão a função de controlador de domínio.</li><li>Conjunto de disponibilidade: Escolha <b>Criar um conjunto de disponibilidade</b>.</li><li>Nome do conjunto de disponibilidade: Digite um nome para o conjunto disponibilidade definido ao criar a primeira máquina virtual e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</li></ul></td>
</tr>
<tr>
<td><b>Configuração de máquina virtual</b></td>
<td>Selecione <b>Instalar o Agente VM</b> e todas as outras extensões necessárias.</td>
</tr>
</table>
</li>
<li><p>Anexe um disco a cada máquina virtual que executará a função de servidor de controlador de domínio. O disco adicional é necessário para armazenar o banco de dados, logs e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixe a <b>Preferência de Cache do host</b> definida como <b>Nenhum</b>. Após conectar-se à VM por primeira vez, abra o <b>Gerenciador de Servidores</b> > <b>Serviços de Arquivo e Armazenamento</b> para criar um volume nesse disco usando o NTFS.</p></li>
<li><p>Reserve um endereço IP estático para VMs que executarão a função de controlador de domínio. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer e <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">instale o Azure PowerShell</a> e execute o <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> cmdlet.</p></li>
<li><p>No Portal de Gerenciamento do Azure, clique no nome da rede virtual e, em seguida, clique na guia <b>Configurar</b> para <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">reconfigurar os endereços IP do servidor DNS para sua rede virtual</a> usar os endereços IP estáticos atribuídos aos controladores de domínio de réplica em vez dos endereços IP de servidores DNS locais. </p>
</li>
<li><p>Para garantir que todos as VMs do controlador de domínio de réplica na rede virtual sejam configuradas para usar servidores DNS na rede virtual, clique em <b>Máquinas Virtuais</b>, clique na coluna de status para cada VM e, em seguida, clique em <b>Reiniciar</b>. Aguarde até que a VM mostre o estado <b>Executando</b> antes de tentar conectar-se a ela. 
</p>
</li>
</ol>

<h2><a id="installadds"></a>Etapa 4: Instale o AD DS em máquinas virtuais do Azure</h2>

Conecte-se a uma VM e verifique se tem conectividade através da conexão VPN site a site ou Rota Expressa para recursos em sua rede local. Em seguida, instale o AD DS nas VMs do Azure. Você pode usar o mesmo processo que você usa para instalar um controlador de domínio adicional na sua rede local (interface do usuário, Windows PowerShell ou um arquivo de resposta). Enquanto instala o AD DS, certifique-se de especificar o novo volume para a localização do banco de dados, logs e SYSVOL do AD. Se você precisar de um atualizador na instalação do AD DS, consulte [Instalar os Serviços de Domínio Active Directory (nível 100)](http://technet.microsoft.com/library/hh472162.aspx) ou [Instalar um Controlador de Domínio de Réplica do Windows Server 2012 num Domínio Existente (nível 200)](http://technet.microsoft.com/library/jj574134.aspx).

<h2><a id="x=createappvms"></a>Etapa 5: Crie VMs para servidores de aplicativos</h2>

<ol><li><p>Repeat the following steps to create VMs to run as application servers. Accept the default value for a setting unless another value is suggested or required.</p>

<table style="width:100%">
<tr>
<td><b>Nesta página do assistente</b></td>
<td><b>Especifique esses valores</b></td>
</tr>
<tr>
<td><b>Escolha uma imagem</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuração de Máquina Virtual</b></td>
<td><ul><li>Nome da Máquina Virtual: Digite um nome de rótulo único (como TreyAppServer1).</li><li>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</li><li>Nova senha/confirmar:  Digite uma senha</li></ul></td>
</tr>
<tr>
<td><b>Configuração de máquina virtual</b></td>
<td><ul><li>Serviço de Nuvem: Escolha Criar um novo serviço de nuvem para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão o aplicativo.</li><li>Nome DNS do Serviço de Nuvem Especifique um nome exclusivo</li><li>Região/grupo de afinidade/rede virtual: Especifique o nome da rede virtual (como WestUSVNet).</li><li>Conta de Armazenamento: Escolha <b>Use uma conta de armazenamento gerada automaticamente</b> para a primeira máquina virtual e selecione esse mesmo nome de conta de armazenamento ao criar mais VMs que hospedarão a função de controlador de domínio.</li><li>Conjunto de disponibilidade: Escolha <b>Criar um conjunto de disponibilidade</b>.</li><li>Nome do conjunto de disponibilidade: Digite um nome para o conjunto disponibilidade definido ao criar a primeira máquina virtual e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</li></ul></td>
</tr>
<tr>
<td><b>Configuração de máquina virtual</b></td>
<td>Selecione <b>Instalar o Agente VM</b> e todas as outras extensões necessárias.</td>
</tr>
</table>


</li>
<li><p>Após cada VM ter sido provisionada, conecte-se e a associe ao domínio. No <b>Gerenciador de Servidores</b>, clique em <b>Servidor local</b> > <b>GRUPO DE TRABALHO</b> > <b>Alterar...</b> e, em seguida, selecione <b>Domínio</b> e digite o nome do seu domínio local. Forneça as credenciais de um usuário de domínio e, em seguida, reinicie a VM para concluir o ingresso no domínio.
</p>
</li>
</ol>
<p>
Como uma alternativa ao uso do portal de gerenciamento para provisionar VMs, você pode usar o Windows PowerShell para Microsoft Azure. Use <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> e <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> para provisionar uma VM como uma máquina ingressada no domínio durante a primeira inicialização e usar <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> para criar a VM em si. 
</p>

Para obter mais informações sobre como usar o Windows PowerShell, consulte [Introdução ao Azure PowerShell](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156055.aspx) e [Cmdlets de gerenciamento do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841).


<h2><a id="resources"></a>Recursos adicionais</h2>

-  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Como carregar controladores de domínio locais de Hyper-V existentes no Azure usando o PowerShell do Azure](http://support.microsoft.com/kb/2904015)

-  [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Rede Virtual do Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [PowerShell do Azure](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Cmdlets gerenciamento do Azure](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=35.2-->

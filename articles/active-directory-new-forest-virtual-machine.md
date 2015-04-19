<properties 
	pageTitle="Instalar uma floresta do Active Directory em uma rede virtual do Azure" 
	description="Um tutorial que explica como criar uma nova floresta do Active Directory em uma máquina virtual (VM) em uma Rede Virtual do Azure." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="03/04/2015" 
	ms.author="Justinha"/>


#Instalar uma nova floresta do Active Directory em uma rede virtual do Azure

Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server em uma rede virtual do Azure que se encontra em uma máquina virtual (VM) em uma [rede virtual do Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). Nesse caso, a rede virtual do Azure não está conectada a uma rede local. 

Você também pode estar interessado nestes tópicos relacionados:

- Você também pode [configurar uma VPN site a site usando o Assistente do Portal de Gerenciamento](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx) e instalar uma nova floresta ou estender uma floresta local para uma rede virtual do Azure. Para essas etapas, consulte [Instalar um controlador de domínio Active Directory de réplica em uma rede virtual do Azure](virtual-networks-install-replica-active-directory-domain-controller.md).
-  Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).


## Como isso difere do local?

Não há muita diferença entre instalar um controlador de domínio no Azure em comparação com um local. As diferenças principais estão listadas na tabela a seguir. 

Para configurar...   |No local | Rede virtual do Azure	
------------- | -------------  | ------------
**Endereço IP do controlador de domínio**  | Atribuir endereço IP estático nas propriedades do adaptador de rede   | Executar o cmdlet Set-AzureStaticVNetIP para atribuir uma endereço IP estático
**Resolvedor do cliente DNS**  | Definir endereço de servidor DNS preferencial e alternativo nas propriedades do adaptador de rede dos membros do domínio   | Definir endereço de servidor DNS nas propriedades de rede virtual
**Armazenamento de banco de dados do Active Directory**  | Como opção, altere o local de armazenamento padrão de C:\  | Você precisa alterar o local de armazenamento padrão de C:\



## Crie uma rede virtual do Azure

1. Entre no Portal de Gerenciamento do Azure.
2. Crie uma rede virtual. Clique em **Redes** > **Criar uma rede virtual**. Use os valores da tabela a seguir para concluir o assistente. 

	Nesta página do assistente...  | Especifique estes valores
	------------- | -------------
	**Detalhes de rede virtual**  | <p>Nome: Digite um nome para a sua rede virtual</p><p>Região: Escolha a região mais próxima</p>
	**DNS e VPN**  | <p>Deixe o servidor DNS em branco</p><p>Não selecione a opção de VPN</p>
	**Espaços de endereço da rede virtual**  | <p>Nome da sub-rede: Digite um nome para a sua sub-rede</p><p>IP Inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## Criar VMs para executar as funções de controlador de domínio e servidor DNS
 
Repita as etapas a seguir para criar VMs para hospedar a função de controlador de domínio, conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância à falhas. Se a rede virtual do Azure inclui pelo menos dois controladores de domínio configurados da mesma forma (ou seja, ambos são GCs, executam o servidor DNS e não contêm nenhuma função FSMO, etc.), coloque as VMs que executam tais controladores de domínio em um conjunto de disponibilidade para melhorar a tolerância.

1. No portal de gerenciamento do Azure, clique em **Novo** > **Computação** > **Máquina Virtual** > **Da galeria**. Use os valores a seguir para concluir o assistente. Aceite o valor padrão para uma configuração, a menos que outro valor seja sugerido ou necessário.

    Nesta página do assistente...  | Especifique estes valores
	------------- | -------------
	**Escolha uma imagem**  | Windows Server 2012 R2 Datacenter
	**Configuração de Máquina Virtual**  | <p>Nome da Máquina Virtual: Digite um nome de rótulo único (como AzureDC1).</p><p>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova senha/confirmar: Digite uma senha</p>
	**Configuração de Máquina Virtual**  | <p>Serviço de Nuvem: Escolha <b>Criar um novo serviço de nuvem</b> para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão a função de controlador de domínio.</p><p>Nome DNS do Serviço de Nuvem Especifique um nome exclusivo</p><p>Região/grupo de afinidade/rede virtual: Especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: Escolha <b>Usar uma conta de armazenamento gerada automaticamente</b> para a primeira máquina virtual e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedará a função de controlador de domínio.</p><p>Conjunto de disponibilidade: Escolha <b>Criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: Digite um nome para o conjunto disponibilidade definido ao criar a primeira máquina virtual e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p>
	**Configuração de Máquina Virtual**  | <p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p>
2. Anexe um disco a cada máquina virtual que executará a função de servidor de controlador de domínio. O disco adicional é necessário para armazenar o banco de dados, logs e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixe a **Preferência de Cache do Host** definida como **Nenhum**. Depois de entrar na VM pela primeira vez, abra o **Gerenciador do Servidor** > **Serviços de Arquivo e Armazenamento** para criar um volume nesse disco usando o NTFS.
3. Reserve um endereço IP estático para VMs que executarão a função de controlador de domínio. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer, [instale o PowerShell do Azure](powershell-install-configure.md) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Para obter mais informações sobre como definir um endereço IP estático, consulte [Configurar um endereço IP interno estático para uma VM](https://msdn.microsoft.com/library/azure/dn630228.aspx).

## Instale o Windows Server Active Directory

Use a mesma rotina para [instalar o AD DS](https://technet.microsoft.com/library/jj574166.aspx) que você usa no local (ou seja, você pode usar a interface do usuário, um arquivo de resposta ou o Windows PowerShell). Você precisa fornecer credenciais de administrador para instalar uma nova floresta. Para especificar o local do banco de dados, dos logs e do SYSVOL do Active Directory, altere o local de armazenamento padrão da unidade do sistema operacional pra o disco de dados adicional que você conectou à VM. 

Após a instalação do DC, conecte-se novamente à VM e faça logon no DC. Não se esqueça de especificar credenciais de domínio.

## Redefinir o servidor DNS para a rede virtual do Azure

1. Redefina a configuração do encaminhador DNS no novo servidor DNS/DC. 
  1. No Gerenciador do servidor, clique em **Ferramentas** > **DNS**. 
  2. Em **Gerenciador DNS**, clique com o botão direito no nome do servidor DNS e clique em **Propriedades**. 
  3. Na guia **Encaminhadores**, clique no endereço IP do encaminhador e clique em **Editar**.  Selecione o endereço IP e clique em **Excluir**. 
  4. Clique em **OK** para fechar o editor e em **Ok** novamente para fechar as propriedades do servidor DNS. 
2. Atualize a configuração do servidor DNS para a rede virtual. 
  1. Clique em **Redes virtuais** > clique duas vezes na rede virtual que você criou > **Configurar** > **Servidores DNS**, digite o nome e o DIP de uma das VMs que executa a função de servidor DNS/DC e clique em **Salvar**. 
  2. Selecione a VM e clique em **Reiniciar** para disparar a VM e definir as configurações de resolvedor DNS com o endereço IP do novo servidor DNS. 


## Criar VMs para membros do domínio

1. Repita as etapas a seguir para criar VMs para executar como servidores de aplicativos. Aceite o valor padrão para uma configuração, a menos que outro valor seja sugerido ou necessário.

	Nesta página do assistente...  | Especifique estes valores
	------------- | -------------
	**Escolha uma imagem**  | Windows Server 2012 R2 Datacenter
	**Configuração de Máquina Virtual**  | <p>Nome da Máquina Virtual: Digite um nome de rótulo único (como AppServer1).</p><p>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova senha/confirmar: Digite uma senha</p>
	**Configuração de Máquina Virtual**  | <p>Serviço de Nuvem: Escolha **Criar um novo serviço de nuvem** para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão o aplicativo.</p><p>Nome DNS do Serviço de Nuvem Especifique um nome exclusivo</p><p>Região/grupo de afinidade/rede virtual: Especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: Escolha **Usar uma conta de armazenamento gerada automaticamente** para a primeira máquina virtual e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedarão o aplicativo.</p><p>Conjunto de disponibilidade: Escolha **Criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: Digite um nome para o conjunto disponibilidade definido ao criar a primeira máquina virtual e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p>
	**Configuração de Máquina Virtual**  | <p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p>
2. Após cada VM ter sido provisionada, conecte-se e a associe ao domínio. Em **Gerenciador do Servidor**, clique em **Servidor Local** > **GRUPO DE TRABALHO** > **Alterar...** e, em seguida, selecione **Domínio** e digite o nome do domínio local. Forneça as credenciais de um usuário de domínio e, em seguida, reinicie a VM para concluir o ingresso no domínio.

Como uma alternativa ao uso do portal de gerenciamento para provisionar VMs, você pode usar o Windows PowerShell para Microsoft Azure. Use [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx) e [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) para provisionar uma máquina virtual como uma máquina ingressada no domínio durante a primeira inicialização e use [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) para criar a máquina virtual em si. 


Para obter mais informações sobre como usar o Windows PowerShell, consulte [Introdução aos Cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) e [Referência de Cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## Consulte também

-  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurar uma rede virtual somente em nuvem no Portal de Gerenciamento](https://msdn.microsoft.com/library/dn631643.aspx)

-  [Configurar uma VPN site a site no Portal de Gerenciamento](https://msdn.microsoft.com/library/dn133795.aspx)

-  [Instalar uma Réplica do Controlador de Domínio do Active Directory em uma rede virtual do Azure](virtual-networks-install-replica-active-directory-domain-controller.md)

-  [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx)

-  [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md)

-  [PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referência de Cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Definir o endereço IP estático da VM do Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Como atribuir um IP estático para uma VM do Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Instalar uma nova floresta do Active Directory](https://technet.microsoft.com/library/jj574166.aspx)

-  [Introdução à virtualização de Serviços do Domínio Active Directory (AD DS) (nível 100)](https://technet.microsoft.com/library/hh831734.aspx)

-  [Guia de Laboratório de Teste: Configuração base do Windows Server 2012 R2 no Azure](http://www.microsoft.com/download/details.aspx?id=41684)


<!--HONumber=47-->

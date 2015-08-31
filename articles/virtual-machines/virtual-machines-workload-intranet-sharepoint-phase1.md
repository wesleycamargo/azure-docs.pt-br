<properties
	pageTitle="Fase 1 da carga de trabalho do farm da intranet do SharePoint: Configurar o Azure"
	description="Na primeira fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você deve criar a rede virtual do Azure e outros elementos da infraestrutura do Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Fase 1 da carga de trabalho do farm da intranet do SharePoint: Configurar o Azure

Nesta fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, crie a infraestrutura de rede e de armazenamento do Azure no Gerenciamento de Serviços do Azure. Conclua esta fase antes de passar para a [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para conhecer todas as fases.

O Azure deve ser provisionado com os seguintes componentes básicos de rede:

- Uma rede virtual entre locais com uma sub-rede
- Três serviços de nuvem do Azure.
- Uma conta de armazenamento do Azure para armazenar imagens de disco VHD e discos de dados adicionais.

## Antes de começar

Antes de começar a configurar os componentes do Azure, preencha as tabelas a seguir. Para ajudá-lo nos procedimentos de configuração do Azure, imprima esta seção e anote as informações necessárias ou copie a seção para um documento e preencha os dados.

Para as configurações da rede virtual (VNet), preencha a Tabela V.

Item | Elemento de configuração | Descrição | Valor
--- | --- | --- | ---
1\. | Nome da VNet | Um nome a ser atribuído à Rede Virtual do Azure (exemplo: SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Local da VNet | O data center do Azure que conterá a rede virtual. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nome da rede local | Um nome a ser atribuído à rede da organização. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Endereço IP do dispositivo VPN | O endereço IPv4 público da interface do seu dispositivo VPN na Internet. Trabalhe com o departamento de TI para determinar esse endereço. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Espaço de endereço da VNet | O espaço de endereço (definido em um prefixo de endereço privado) da rede virtual. Trabalhe com o departamento de TI para determinar esse espaço de endereço. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primeiro servidor DNS final | O quarto endereço IP possível para o espaço de endereço da sub-rede da rede virtual (consulte a Tabela S). Trabalhe com o departamento de TI para determinar esses endereços. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Segundo servidor DNS final | O quinto endereço IP possível para o espaço de endereço da sub-rede da rede virtual (consulte a Tabela S). Trabalhe com o departamento de TI para determinar esses endereços. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela V: Configuração de rede virtual entre locais**

Preencha a tabela S para a sub-rede dessa solução. Dê à sub-rede um nome amigável, um único espaço de endereço IP com base no espaço de endereço da Rede Virtual e uma finalidade descritiva. O espaço de endereço deve estar no formato de Roteamento entre Domínios sem Classificação (CIDR), também conhecido como formato de prefixo de rede. Um exemplo é 10.24.64.0/20. Trabalhe com o departamento de TI para determinar esse espaço de endereço pelo espaço de endereço da rede virtual.

Item | Nome da sub-rede | Espaço de endereço da sub-rede | Finalidade
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela S: Sub-redes na rede virtual**

> [AZURE.NOTE]Essa arquitetura predefinida usa uma única sub-rede para manter a simplicidade. Se você quiser sobrepor um conjunto de filtros de tráfego para emular o isolamento da sub-rede, use os [Grupos de Segurança de Rede do Azure](https://msdn.microsoft.com/library/azure/dn848316.aspx).

Para os dois servidores DNS locais que você deseja usar ao configurar os controladores de domínio em sua rede virtual inicialmente, preencha a Tabela D. Dê a cada servidor DNS um nome amigável e um só endereço IP. Esse nome amigável não precisa coincidir com o nome do host ou o nome do computador do servidor DNS. Observe que duas entradas em branco são listadas, mas é possível adicionar outras. Trabalhe com o departamento de TI para determinar essa lista.

Item | Nome amigável do servidor DNS | Endereço IP do servidor DNS
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela D: Servidores DNS locais**

Para rotear pacotes da rede entre os locais de rede da sua organização na conexão VPN site a site, você deve configurar a rede virtual com uma rede local que contenha uma lista dos espaços de endereço (em notação CIDR) para todos os locais acessíveis na rede local de sua organização. A lista de espaços de endereço que definem sua rede local não deve incluir ou sobrepor o espaço de endereço usado para outras redes virtuais ou outras redes locais. Em outras palavras, os espaços de endereço para redes locais e redes virtuais configuradas devem ser exclusivos.

Para o conjunto de espaços de endereço da rede local, preencha a Tabela L. Observe que três entradas em branco são listadas, mas normalmente é necessário adicionar mais entradas. Trabalhe com o departamento de TI para determinar esse lista de espaços de endereço.

Item | Espaço de endereço da rede local
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela L: Prefixos de endereços para a rede local**

Para criar a rede virtual com as configurações das Tabelas V, S, D e L, use as instruções em [Criar uma rede virtual entre locais usando tabelas de configuração](virtual-machines-workload-deploy-vnet-config-tables.md).

> [AZURE.NOTE]Este procedimento orienta a criação de uma rede virtual que usa uma conexão VPN site a site. Para saber mais sobre como usar o ExpressRoute para sua conexão site a site, consulte [Visão geral técnica do ExpressRoute](http://msdn.microsoft.com/library/dn606309.aspx).

Depois de criar a rede virtual do Azure, o Portal de Gerenciamento do Azure determinará:

- O endereço IPv4 público do gateway da VPN do Azure para a sua rede virtual.
- A chave pré-compartilhada do protocolo IPsec da conexão VPN site a site.

Para ver esses itens no Portal de Gerenciamento do Azure depois de criar a rede virtual, clique em **Redes**, no nome da rede virtual e, em seguida, clique na opção de menu **Painel**.

Em seguida, você configurará o gateway de rede virtual para criar uma conexão VPN site a site segura. Consulte [Configurar um gateway de rede virtual no Portal de Gerenciamento](http://msdn.microsoft.com/library/jj156210.aspx) para obter instruções.

Em seguida, crie a conexão VPN site a site entre a nova rede virtual e um dispositivo VPN local. Para saber mais, consulte [Configurar um gateway de rede virtual no Portal de Gerenciamento](http://msdn.microsoft.com/library/jj156210.aspx).

Em seguida, certifique-se de que o espaço de endereço da rede virtual possa ser acessado pela sua rede local. Geralmente, isso é feito adicionando uma rota correspondente ao espaço de endereço da rede virtual ao seu dispositivo VPN e, em seguida, anunciando essa rota para o restante da infraestrutura de roteamento da rede da sua organização. Trabalhe com o departamento de TI para determinar como fazer isso.

Em seguida, use as instruções em [Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md) para instalar o Azure PowerShell no computador local. Abra um prompt de comando do Azure PowerShell.

Primeiro, selecione a assinatura correta do Azure com estes comandos. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Você pode obter o nome da assinatura na propriedade **SubscriptionName** na saída do comando **Get-AzureSubscription**.

Em seguida, crie os três serviços de nuvem necessários para esse farm do SharePoint. Preencha a Tabela C.

Item | Finalidade | Nome do serviço de nuvem
--- | --- | ---
1\. | Controladores de domínio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela C: Nomes de serviços de nuvem**

Você deve escolher um nome exclusivo para cada serviço de nuvem. *O nome do serviço de nuvem pode conter apenas letras, números e hifens. O primeiro e o último caractere no campo devem ser uma letra ou um número.*

Por exemplo, você poderia nomear o primeiro serviço de nuvem como DCs-*UniqueSequence*, onde *UniqueSequence* é uma abreviação da sua organização. Por exemplo, se sua organização se chamasse Tailspin Toys, você poderia chamar o serviço de nuvem de DCs-Tailspin.

Você pode testar a exclusividade do nome com o comando a seguir do Azure PowerShell em seu computador local.

	Test-AzureName -Service <Proposed cloud service name>

Se este comando retornar "False", o nome proposto é exclusivo. Em seguida, crie o serviço de nuvem com os procedimentos a seguir.

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

Registre o nome real de cada serviço de nuvem recém-criado na Tabela C.

Em seguida, crie uma conta de armazenamento para o farm do SharePoint. *Você deve escolher um nome exclusivo que contenha apenas letras minúsculas e números.* Você pode testar a exclusividade do nome da conta de armazenamento com os seguintes comandos do Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie a conta de armazenamento e defina a assinatura para usá-la com estes comandos.

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Em seguida, defina os nomes dos quatro conjuntos de disponibilidade. Preencha a Tabela A.

Item | Finalidade | Nome do conjunto de disponibilidade
--- | --- | ---
1\. | Controladores de domínio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores de aplicativos do SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Servidores Web front-end do SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela A: Nomes dos conjuntos de disponibilidade**

Você precisará desses nomes ao criar as máquinas virtuais nas fases 2, 3 e 4.

Esta é a configuração resultante da conclusão bem-sucedida desta fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## Próxima etapa

Para configurar a configuração dessa carga de trabalho, vá para [Fase 2: Configurar controladores de domínio](virtual-machines-workload-intranet-sharepoint-phase2.md).

## Recursos adicionais

[Implantação do SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabalho dos Serviços de Infraestrutura do Azure: aplicativo de linha de negócios de alta disponibilidade](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=August15_HO8-->
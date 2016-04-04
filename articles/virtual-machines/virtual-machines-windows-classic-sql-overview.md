<properties
	pageTitle="Visão geral do SQL Server em Máquinas Virtuais | Microsoft Azure"
	description="Este artigo fornece uma visão geral do SQL Server hospedado em Máquinas Virtuais do Azure. Isso inclui links para conteúdo mais aprofundado."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth"/>

# Visão geral do SQL Server em máquinas virtuais do Azure

## Introdução
Você pode hospedar o [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) em diversas configurações, que variam desde um único servidor de banco de dados até uma configuração de vários computadores usando Grupos de Disponibilidade do AlwaysOn e uma Rede Virtual do Azure.

>[AZURE.NOTE] A execução do SQL Server em uma VM do Azure é uma opção para armazenar dados relacionais no Azure. Você também pode usar o serviço de Banco de Dados SQL do Azure. Para obter mais informações, consulte [Noções básicas sobre o Banco de Dados SQL e o SQL Server em VMs do Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Para criar uma máquina virtual do SQL Server no Azure, você deve primeiro obter uma assinatura da Plataforma Azure. É possível adquirir uma assinatura do Azure em [Opções de Compra](https://azure.microsoft.com/pricing/purchase-options/). Para testá-la gratuitamente, visite [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### Implantar uma instância do SQL Server em uma única VM

Depois de se inscrever em uma assinatura, a maneira mais fácil de implantar uma máquina virtual do SQL Server no Azure será [provisionar uma imagem da galeria de computador do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md). Essas imagens incluem o licenciamento do SQL Server nos preços para a VM.

É importante observar que há dois modelos para criar e gerenciar máquinas virtuais do Azure: clássico e Gerenciador de Recursos. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Algumas documentações do SQL Server para VMs do Azure ainda se referem exclusivamente ao modelo clássico. Esses tópicos estão sendo atualizados ao longo do tempo para usar o novo portal do Azure e o modelo do Gerenciador de Recursos. Para saber mais, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md).

>[AZURE.NOTE] Quando possível, use o [portal do Azure](https://portal.azure.com/) mais recente para provisionar e gerenciar Máquinas Virtuais do SQL Server. Ele usa o armazenamento Premium como padrão e oferece configurações de Aplicação de Patch Automatizada, Backup Automatizado e do AlwaysOn.

A tabela a seguir fornece uma matriz de imagens do SQL Server disponíveis na galeria de máquinas virtuais.

|Versão do SQL Server|Sistema operacional|Edição do SQL Server|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|Avaliação|

Além dessas imagens pré-configuradas, você também pode [criar uma máquina virtual do Azure](virtual-machines-windows-hero-tutorial.md) sem o SQL Server pré-instalado. Você pode instalar qualquer instância do SQL Server para a qual tenha uma licença. Migre a licença do Azure para executar o SQL Server em uma Máquina Virtual do Azure usando a [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Neste cenário, você paga apenas pelos [custos](https://azure.microsoft.com/pricing/details/virtual-machines/) de computação e de armazenamento do Azure associados à máquina virtual.

Para determinar as melhores definições de configuração de máquina virtual para a sua imagem do SQL Server, examine as [Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-perf.md). Para cargas de trabalho de produção, o **DS3** é o tamanho mínimo recomendado de máquina virtual para o SQL Server Enterprise Edition, e o **DS2** é o tamanho mínimo recomendado de máquina virtual para a Standard Edition.

Além de examinar as práticas recomendadas de desempenho, outras tarefas iniciais incluem as seguintes:

- [Analisar práticas recomendadas de segurança do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-classic-sql-security.md)
- [Configurar conectividade](virtual-machines-windows-sql-connect.md)

### Migrar seus dados

Depois que sua máquina virtual do SQL Server estiver em execução, talvez você queira migrar bancos de dados existentes para a máquina. Há várias técnicas, mas o assistente de implantação no SQL Server Management Studio funciona bem para a maioria dos cenários. Para consultar uma discussão sobre os cenários e um tutorial do assistente, acesse [Migrando um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-classic-migrate-sql.md).

## Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar Grupos de Disponibilidade AlwaysOn do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O portal do Azure tem um modelo que define essa configuração para você. Para saber mais, confira [Oferta do AlwaysOn do SQL Server na Galeria do Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Para configurar manualmente o Grupo de Disponibilidade e o ouvinte associado, veja os artigos a seguir com base no modelo de implantação Clássica:

- [Configurar os Grupos de Disponibilidade AlwaysOn no Azure (GUI)](virtual-machines-windows-classic-portal-sql-availability.md)
- [Configurar um ouvinte de ILB para grupos de disponibilidade do AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Estender Grupos de Disponibilidade AlwaysOn locais para o Azure](virtual-machines-windows-classic-sql-onprem-availability.md)

Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-dr.md).

## Backup e restauração
Para bancos de dados locais, o Azure pode agir como um data center secundário para armazenar arquivos de backup do SQL Server. Para obter uma visão geral das opções de backup e restauração, veja [Backup e Restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-br.md).

O [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx) armazena arquivos de backup do Azure no armazenamento de blob do Azure. O [Backup Gerenciado do SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) permite agendar o backup e a retenção no Azure. Esses serviços podem ser usados com instâncias locais do SQL Server ou com o SQL Server em execução em VMs do Azure. As VMs do Azure também podem tirar proveito do [Backup Automatizado](virtual-machines-windows-classic-ps-sql-backup.md) e de [Patches Automatizados](virtual-machines-windows-classic-ps-sql-patch.md) para o SQL Server.

## Detalhes de configuração da imagens de VM do SQL Server

As tabelas a seguir descrevem a configuração de imagens de máquina virtual do SQL Server fornecidas pela plataforma.

### Windows Server

A instalação do Windows Server na imagem da plataforma contém as seguintes configurações e componentes:

|Recurso|Configuração
|---|---|
|Área de Trabalho Remota|Habilitado para a conta do administrador|
|Windows Update|Habilitado|
|Contas de Usuário|Por padrão, a conta de usuário especificada durante o provisionamento é membro do grupo local Administradores. Essa conta de administrador também é membro da função de servidor sysadmin do SQL Server|
|Grupos de trabalho|A máquina virtual é membro de um grupo de trabalho chamado GRUPO DE TRABALHO|
|Conta de Convidado|Desabilitado|
|Firewall do Windows com segurança avançada|Por|
|.NET Framework|Versão 4|
|Discos|O tamanho selecionado limita o número de discos de dados que você pode configurar. Confira [Tamanhos das máquinas virtuais para o Azure](virtual-machines-linux-sizes.md)|

### SQL Server

A instalação do SQL Server na imagem da plataforma contém as seguintes configurações e componentes:

|Recurso|Configuração|
|---|---|
|Mecanismo de banco de dados|Instalado|
|Serviços de análise|Instalado|
|Serviços de integração|Instalado|
|Serviços de Relatório|Configurado no modo Nativo|
|Grupos de disponibilidade AlwaysOn|Disponível no SQL Server 2012 ou posterior. Exige [configuração adicional](virtual-machines-windows-classic-sql-dr.md)
|Replicação|Instalado|
|Extrações Semânticas e de Texto Completo para Pesquisa|Instalado (Extrações Semânticas somente no SQL Server 2012 ou posterior)|
|Serviços de qualidade de dados|Instalado (somente SQL Server 2012 ou posterior)|
|Serviços de dados mestre|Instalado (somente SQL Server 2012 ou posterior). Exige [configuração e componentes adicionais](https://msdn.microsoft.com/library/ee633752.aspx)
|PowerPivot para SharePoint|Disponível (somente SQL Server 2012 ou posterior). Exige configuração e componentes adicionais (incluindo o SharePoint)|
|Distributed Replay Client|Disponível (somente SQL Server 2012 ou posterior), mas não instalado. Confira [Executando a instalação do SQL Server da imagem do SQL Server fornecida pela plataforma](#run-sql-server-setup-from-the-platform-provided-sql-server-image)|
|Ferramentas|Todas as ferramentas, incluindo o SQL Server Management Studio, o SQL Server Configuration Manager, o Business Intelligence Development Studio, a instalação do SQL Server, a Conectividade das Ferramentas de Cliente, o SDK de Ferramentas de Cliente, o SDK de Conectividade de Cliente SQL e as ferramentas de atualização e migração, como aplicativos da camada de dados (DAC), backup, restauração, anexar e desanexar|
|Manuais Online do SQL Server|Instalado, mas exige configuração usando o Visualizador da Ajuda|

### Configuração do mecanismo de banco de dados

As seguintes configurações do mecanismo de banco de dados são definidas. Para obter mais configurações, examine a instância do SQL Server.

|Recurso|Configuração|
|---|---|
|Instância|Contém uma instância padrão (sem nome) do Mecanismo de Banco de Dados SQL Server, escutando apenas no protocolo de memória compartilhada|
|Autenticação|Por padrão, o Azure seleciona a Autenticação do Windows durante a instalação da máquina virtual do SQL Server. Se quiser usar o logon ou criar uma nova conta do SQL Server, é preciso alterar o modo de autenticação. Para saber mais, confira [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-security.md).|
|sysadmin|O usuário do Azure que instalou a máquina virtual inicialmente é o único membro da função de servidor fixa sysadmin do SQL Server|
|Memória|A memória do Mecanismo de Banco de Dados é definida de acordo com a configuração da memória dinâmica|
|Autenticação de banco de dados independente|Desativar|
|Idioma padrão|Inglês|
|Encadeamento de propriedade de bancos de dados|Desativar|

### Programa de Aperfeiçoamento da Experiência do Usuário (CEIP)

O [Programa de Aperfeiçoamento da Experiência do Usuário (CEIP)](https://technet.microsoft.com/library/cc730757.aspx) está habilitado. Você pode desabilitar o CEIP por meio do utilitário de Relatório de Erro e Uso do SQL Server. Para iniciar o utilitário de Relatório de Erro e Uso do SQL Server, no menu Iniciar, clique em Todos os Programas, em Microsoft SQL Server versão, em Ferramentas de Configuração e, em seguida, clique em Relatório de Erro e Uso do SQL Server. Se não quiser usar uma instância do SQL Server com o CEIP habilitado, também é possível implantar sua própria imagem de máquina virtual no Azure. Para saber mais, confira [Criando e carregando um disco rígido virtual que contém o sistema operacional Windows Server](virtual-machines-windows-classic-createupload-vhd.md).

## Execute a instalação do SQL Server da imagem do SQL Server fornecida pela plataforma

Se criar uma máquina virtual usando uma imagem do SQL Server fornecida pela plataforma, você pode encontrar a mídia de instalação do SQL Server salva na máquina virtual, no diretório **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full**. Você pode executar a instalação desse diretório para realizar qualquer ação de instalação, inclusive adicionar ou remover recursos, adicionar uma nova instância ou reparar a instância se o espaço em disco permitir.

>[AZURE.NOTE] O Azure fornece várias versões das imagens do SQL Server. Se a data de lançamento da imagem do SQL Server fornecida pela plataforma for 15 de maio de 2014 ou posterior, ela contém a chave do produto por padrão. Se você provisionar uma máquina virtual usando uma imagem do SQL Server fornecida pela plataforma publicada antes dessa data, a VM não conterá a chave do produto. É recomendável sempre selecionar a versão mais recente da imagem ao provisionar uma nova VM.

## Recursos

- [Provisionar uma máquina virtual do SQL Server no Gerenciador de Recursos do Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-classic-migrate-sql.md)
- [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-dr.md)
- [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
- [Máquinas Virtuais do Azure](virtual-machines-linux-about.md)

<!---HONumber=AcomDC_0323_2016-->
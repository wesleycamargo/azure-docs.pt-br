<properties 
	pageTitle="Cargas de trabalho transacionais e data warehouse do SQL Server nas máquinas virtuais do Azure"
	description="Descreve as imagens de máquinas virtuais do SQL Server pré-configuradas e otimizadas no Azure para cargas de trabalho de data warehouse e OLTP."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# Cargas de trabalho transacionais e data warehouse do SQL Server nas máquinas virtuais do Azure

Para usar o SQL Server para cargas de trabalho transacionais ou de data warehouse em uma Máquina Virtual do Azure, recomendamos usar uma das imagens de máquina virtual pré-configuradas na Galeria de Máquinas Virtuais do Azure. Essas imagens foram otimizadas com base nas recomendações em [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx).

Este artigo se concentra na execução dessas cargas de trabalho nas Máquinas Virtuais do Azure (também é conhecido como infraestrutura como serviço ou IaaS). Você também pode executar cargas de trabalho transacionais e de data warehouse como um serviço no Azure. Para obter mais informações, consulte [Visualização do SQL Data Warehouse no Azure](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) e [Banco de Dados SQL Azure](http://azure.microsoft.com/documentation/services/sql-database/).

## Que imagens de VM pré-configuradas estão disponíveis?

As seguintes imagens de VM pré-configuradas estão disponíveis na Galeria de Máquinas Virtuais do Azure:

- [SQL Server 2014 Enterprise otimizado para cargas de trabalho transacionais no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [SQL Server 2014 Enterprise otimizado para DataWarehousings no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho transacionais no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho de DataWarehousing no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho transacionais no Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [SQL Server 2012 SP2 Enterprise otimizado para DataWarehousings no Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

Atualmente, há suporte para essas imagens em instâncias de VM que permitem até 16 discos de dados anexados para fornecer a taxa de transferência (ou largura de banda agregada) mais alta. Essas instâncias são camada Standard A4, A7, A8, A9, D4, D13, D14, F3, G4, G5 e camada Básica A4. Consulte [Tamanhos de máquinas virtuais no Azure](virtual-machines-size-specs.md) para obter mais detalhes sobre os tamanhos e as opções.

>[AZURE.NOTE]Antes de setembro de 2014, imagens anteriores da galeria de DW e transacionais estavam disponíveis. No entanto, essas imagens necessitavam que você anexasse discos de dados para serem usadas. É recomendável usar as imagens mais recentes acima, porque elas estão prontos para uso após o provisionamento.

## Provisionando uma VM SQL da Galeria usando as imagens transacionais/de DW

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

1. Clique em **MÁQUINA VIRTUAL** nos itens de menu do Azure no painel esquerdo.

1. Clique em **NOVO** no canto inferior esquerdo e clique em **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e **DA GALERIA**.

1. Na página seleção de imagem de máquina virtual, selecione um SQL Server para imagens transacionais ou de Data Warehouse.

	![Galeria de VM do Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. Na página **Configuração da máquina virtual**, na opção **TAMANHO**, escolha os tamanhos com suporte.

	![Configuração da Galeria de VM do Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]Somente a camada Standard A4, A7, A8, A9, D4, D13, D14, G3, G4 e G5 e a camada Básica A4 têm suporte no momento. Tentativas de provisionar VMs com tamanhos sem suporte falharão.

1. Aguarde o término do provisionamento. Enquanto aguarda, você pode ver o status do provisionamento na página de máquinas virtuais (como na imagem abaixo). Quando o provisionamento for concluído, o status será **Executando**, com uma marca de seleção.

	![Status da Galeria de VM do Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## Usando o PowerShell para criar as imagens transacionais/de DW

Você também pode usar o Commandlet **New-AzureQuickVM** do PowerShell para criar a máquina virtual. Você deve passar o nome do serviço de nuvem, nome da VM, nome da imagem, nome de usuário e senha do administrador e informações semelhantes como parâmetros. Uma maneira simples de obter o nome da imagem é usar **Get-AzureVMImage** para listar todas as imagens de VM disponíveis.

Por exemplo, o seguinte comando do PowerShell retorna a imagem mais recente que corresponde ao rótulo de imagem **SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho de DataWarehousing no Windows Server 2012 R2** da lista na seção anterior.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

Para saber mais sobre a criação de dados com o PowerShell, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Configurações específicas incluídas nas imagens transacionais/de DW

As otimizações incluídas nas imagens são baseadas nas [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx). Especificamente, a configuração dessas imagens inclui as seguintes otimizações.

>[AZURE.NOTE]Se estiver usando sua própria licença e criando uma máquina virtual de Data Warehouse ou transacional do zero, você pode basear suas otimizações no artigo de desempenho e no exemplo das otimizações nas imagens pré-configuradas da galeria abaixo.

### Configurações de disco


|---|---|
|Número de discos de dados anexados|15|
|Espaços de armazenamento|Dois pools de armazenamento:<br/>– 1 pool de dados com 12 discos de dados; tamanho fixo de 12 TB; coluna = 12<br/>– 1 pool de logs com 3 discos de dados; tamanho fixo de 3 TB; coluna = 3<br/><br/>Um disco de dados restante para o usuário anexar e determinar o uso.<br/><br/>**DW**: tamanho da faixa = 256 KB<br/>**Transacional**: tamanho da faixa = 64 KB|
|Tamanhos de disco, armazenamento em cache, tamanho da alocação|1 TB cada, HostCache = None, Tamanho da unidade de alocação do NTFS = 64 KB|

### Configurações do SQL Server

|---|---|
|Parâmetros de inicialização|-T1117 para ajudar a manter os arquivos de dados do mesmo tamanho caso o banco de dados precise de crescimento automático<br/><br/>-T1118 para auxiliar na escalabilidade de tempdb (para obter mais informações, consulte [Uso do sinalizador de rastreamento 1118 (T1118) do SQL Server (2005 e 2008)](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce).)|
|Modo de recuperação|**DW**: definido como SIMPLE para o banco de dados modelo usando ALTER DATABASE<br/>**Transacional**: nenhuma alteração|
|Locais padrão de configuração|Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados|
|Locais padrão para os bancos de dados|Bancos de dados do sistema são movidos para discos de dados.<br/><br/>O local para a criação de bancos de dados de usuário é alterado para os discos de dados.|
|Inicialização de arquivo instantânea|Habilitado|
|Bloquear páginas na memória|Habilitado (para obter mais informações, consulte [Habilitar a opção Bloquear páginas na memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)).|

## Perguntas frequentes

- Existe alguma diferença de preço entre as imagens otimizadas e as não otimizadas?

	Não. As imagens otimizadas seguem o mesmo modelo de preços (detalhes [aqui](http://azure.microsoft.com/pricing/details/virtual-machines/)) sem nenhum custo adicional. Observe que um custo mais alto está associado aos maiores tamanhos de instância VM.

- Há outras correções de desempenho que devo considerar?

	Sim, considere a possibilidade de aplicar correções de desempenho relevantes para o SQL Server:

	- [CORREÇÃO: Desempenho insatisfatório de E/S ao executar a seleção na operação de tabela temporária no SQL Server 2012](https://support.microsoft.com/kb/2958012)

	- [CORREÇÃO: "Os contadores de desempenho do SQL Server estão desabilitados" quando você move o recurso do SQL Server no SQL Server 2014](http://support.microsoft.com/kb/2973444)

- Como posso encontrar mais informações sobre espaços de armazenamento?

	Para obter mais detalhes sobre os espaços de armazenamento, consulte [Perguntas frequentes (FAQ) sobre os espaços de armazenamento](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)

- Qual é a diferença entre a nova imagem de Data Warehouse e a anterior?

	A imagem de DW anterior requer que os clientes executem etapas adicionais, como anexar os discos de dados depois de criar a máquina virtual, enquanto a nova imagem de DW está pronta para uso no momento da criação, de modo que está disponível mais rapidamente e tem menos chances de erros.

- E se eu precisar usar a imagem de DW anterior? Há alguma maneira de possa acessá-la?

	As imagens de VM anteriores ainda estão disponíveis, apenas não estão diretamente acessíveis da galeria. Em vez disso, você pode continuar usando cmdlets do Powershell. Por exemplo, você pode usar **Get-AzureVMImage** para listar todas as imagens e, quando localizar a imagem anterior do DW com base na descrição e data da publicação, pode usar **New-AzureVM** para provisionar.

## Próximas etapas

Depois de instalar qualquer máquina virtual com o SQL Server, você poderá querer:

- [Migrar seus dados](virtual-machines-migrate-onpremises-database.md)
- [Configurar a conectividade](virtual-machines-sql-server-connectivity.md)

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO8-->
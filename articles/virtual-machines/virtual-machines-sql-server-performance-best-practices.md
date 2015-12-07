<properties 
	pageTitle="Práticas recomendadas de desempenho do SQL Server | Microsoft Azure"
	description="Fornece as práticas recomendadas para aprimoramento do desempenho do SQL Server em VMs do Microsoft Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
	
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure

## Visão geral

Este tópico fornece as práticas recomendadas para aprimoramento do desempenho do SQL Server na máquina Virtual do Microsoft Azure. Durante a execução do SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho aplicáveis ao SQL Server no ambiente de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores como, por exemplo, o tamanho de uma máquina virtual e a configuração dos discos de dados.

Durante a criação de imagens do SQL Server, considere o uso do portal de visualização do Azure para tirar proveito dos recursos, como o uso padrão de armazenamento Premium e outras opções, como configurações de AlwaysOn, Backup automatizado e aplicação de patch automatizada.

Este documento concentra-se na obtenção do melhor desempenho para o SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todos os aprimoramentos relacionados abaixo. Considere suas necessidades de desempenho e padrões de carga de trabalho ao avaliar essas recomendações.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Lista de verificação rápida

Veja a seguir uma lista de verificação rápida para obter o melhor desempenho do SQL Server em máquinas virtuais do Azure:

|Área|Otimizações|
|---|---|
|**Tamanho da VM**|[DS3](virtual-machines-size-specs.md#standard-tier-ds-series) ou superior para a edição SQL Enterprise.<br/><br/>[DS2](virtual-machines-size-specs.md#standard-tier-ds-series) ou superior para as edições SQL Standard e Web.|
|**Armazenamento**|Use o [Armazenamento Premium](../storage/storage-premium-storage-preview-portal.md).<br/><br/>Mantenha a [conta de armazenamento](../storage/storage-create-storage-account.md) e a VM do SQL Server na mesma região.<br/><br/>Desabilite o [armazenamento com redundância geográfica](../storage/storage-redundancy.md) (replicação geográfica) do Azure na conta de armazenamento.|
|**Discos**|Use no mínimo 2 [discos P30](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whpt-BRing-premium-storage) (1 para arquivos de log; 1 para arquivos de dados e TempDB).<br/><br/>Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou log.<br/><br/>Habilite o cache de leitura no disco que hospeda os arquivos de dados e TempDB.<br/><br/>Não habilite o cache em discos que hospedam o arquivo de log.<br/><br/>Distribua vários discos de dados do Azure para obter maior rendimento de ES.<br/><br/>Formate com tamanhos de alocação documentados.|
|**E/S**|Habilite a compactação de página do banco de dados.<br/><br/>Habilite a inicialização instantânea de arquivo para arquivos de dados.<br/><br/>Limite ou desabilite o crescimento automático no banco de dados.<br/><br/>Desative a redução automática do banco de dados.<br/><br/>Mova todos os bancos de dados para os discos de dados, incluindo bancos de dados do sistema.<br/><br/>Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados.<br/><br/>Configure os locais padrão de arquivos de backup e de banco de dados.<br/><br/>Habilite as páginas bloqueadas.<br/><br/>Aplique correções de desempenho do SQL Server.|
|**Recursos específicos**|Faça backup diretamente no armazenamento de blob.|

Para saber mais, siga as diretrizes fornecidas nas subseções a seguir.

## Considerações sobre a conta de armazenamento e o tamanho da máquina virtual

Para aplicativos sensíveis ao desempenho, recomenda-se o uso dos seguintes tamanhos de máquinas virtuais:

- **SQL Server Enterprise Edition**: DS3 ou superior

- **SQL Server Standard e Web Editions**: DS2 ou superior

Para obter informações atualizadas sobre os tamanhos de máquina virtual com suporte, consulte [Tamanhos de máquinas virtuais](virtual-machines-size-specs.md).

Além disso, recomendamos a criação de sua conta de armazenamento do Azure no mesmo data center que suas máquinas virtuais do SQL Server a fim de reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois não há garantia para uma ordem de gravação consistente em vários discos. Em vez disso, considere a configuração de uma tecnologia de recuperação de desastres do SQL Server entre dois data centers do Azure. Para saber mais, consulte [Alta disponibilidade e recuperação de desastres para o SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Considerações sobre discos e desempenho

Quando você cria uma Máquina Virtual do Azure, a plataforma anexa pelo menos um disco à VM para o disco do sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento. Você também pode anexar outros discos à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como blobs de página. Há outro disco presente em máquinas virtuais do Azure chamado de disco temporário. É um disco localizado no nó que pode ser usado como espaço de rascunho.

### Disco do sistema operacional

Um disco de sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e está rotulado como unidade **C**.

A política padrão de caching em disco no sistema operacional é **Leitura/Gravação**. Para aplicativos sensíveis ao desempenho, recomendamos o uso de discos de dados em vez do disco do sistema operacional. Consulte a seção sobre Discos de Dados abaixo.

### Disco temporário

A unidade de armazenamento temporário, rotulada como unidade **D**:, não é mantida para o armazenamento de blob do Azure. Não armazene seus dados ou arquivos de log na unidade **D**:.

Armazene somente o TempDB e/ou Extensões do Pool de Buffer na unidade **D** ao usar VMs da série D ou G. Ao contrário de outras séries de VM, a unidade **D** das VMs das séries D e G é baseada em SSD. Isso pode melhorar o desempenho de cargas de trabalho que usam muitos objetos temporários ou que têm conjuntos de trabalho que não cabem na memória. Para saber mais, consulte [Usando SSDs em VMs do Azure para armazenar o TempDB do SQL Server e Extensões do Pool de Buffer](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx).

### Disco de dados

- **Número de discos de dados para arquivos de dados e de log**: no mínimo, use dois [discos P30](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whpt-BRing-premium-storage), sendo que um deles contém os arquivos de log e o outro contém os arquivos de dados e o TempDB. Para obter uma taxa de transferência superior, talvez seja necessário mais discos de dados. Para determinar o número de discos de dados, você precisa analisar o número de IOPS disponível para seus discos de dados e de log. Para obter essas informações, consulte as tabelas sobre IOPS por [tamanho da VM](virtual-machines-size-specs.md) e tamanho de disco no seguinte artigo: [Usando o Armazenamento Premium para discos](../storage/storage-premium-storage-preview-portal.md). Se você precisar de mais largura de banda, anexe outros discos usando o Particionamento de Disco. Se você não estiver usando o Armazenamento Premium, a recomendação será adicionar o número máximo de discos de dados suportados pelo [Tamanho da VM](virtual-machines-size-specs.md) e usar a Distribuição de disco. Para saber mais sobre Distribuição de disco, consulte a seção relacionada abaixo.

- **Política de caching**: habilite o caching nos discos de dados que hospedam seus arquivos de dados e TempDB. Se você não estiver usando o Armazenamento Premium, não habilite o caching em discos de dados. Para obter instruções sobre como configurar o caching de disco, consulte os tópicos a seguir: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

- **Tamanho de unidade de alocação de NTFS**: ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB.

- **Distribuição de disco**: recomendamos estas diretrizes:

	- Para o Windows 8/Windows Server 2012 ou posterior, use [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx). Defina o tamanho da distribuição como 64 KB para cargas de trabalho OLTP, e 256 KB para cargas de trabalho de data warehouse, a fim de evitar o impacto no desempenho devido ao desalinhamento da partição. Além disso, defina a contagem de colunas = número de discos físicos. Para configurar um Espaço de Armazenamento com mais de oito discos é necessário usar o PowerShell (não a UI do Gerenciador de Servidores) para definir explicitamente o número de colunas, a fim de corresponder ao número de discos. Para saber mais sobre como configurar os [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx), consulte [Cmdlets de espaços de armazenamento no Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)
	
	- Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Observe que essa opção tornou-se obsoleta no Windows 8/Windows Server 2012. Para saber mais, consulte a declaração de suporte em [O serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).
	
	- Se sua carga de trabalho não utilizar muito o log e não precisar de um IPS dedicado, você poderá configurar apenas um pool de armazenamento. Caso contrário, crie dois pools de armazenamento, um para os arquivos de log e outro para os arquivos de dados e o TempDB. Determine o número de discos associados a cada pool de armazenamento com base em suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para saber mais, consulte [Tamanhos de máquinas virtuais](virtual-machines-size-specs.md).

## Considerações sobre desempenho de E/S

- Os melhores resultados com o Armazenamento Premium são obtidos ao paralelizar seu aplicativo e as solicitações. O Armazenamento Premium foi projetado para cenários nos quais a profundidade da fila de E/S é maior do que um. Portanto, você verá pouco ou nenhum ganho de desempenho para solicitações em série de segmento único (mesmo se usarem muito armazenamento). Por exemplo, isso pode afetar os resultados do teste de segmento único de ferramentas de análise de desempenho, por exemplo, o SQLIO.

- Considere o uso da [compactação de página do banco de dados](https://msdn.microsoft.com/library/cc280449.aspx), pois isso pode ajudar a melhorar o desempenho de cargas de trabalho com uso intenso de E/S. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

- Considere a compactação de qualquer arquivo de dados durante a transferência de entrada/saída do Azure.

- Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, conceda à conta de serviço do SQL Server (MSSQLSERVER) SE\_MANAGE\_VOLUME\_NAME e adicione-a à política de segurança **Executar Tarefas de Manutenção de Volume**. Se você estiver usando uma imagem da plataforma do SQL Server para o Azure, a conta de serviço padrão (NT Service\\MSSQLSERVER) não será adicionada à política de segurança **Executar Tarefas de Manutenção de volume**. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume**, reinicie o serviço SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para saber mais, consulte [Inicialização de arquivo de banco de dados](https://msdn.microsoft.com/library/ms175935.aspx).

- O **crescimento automático** é considerado meramente uma contingência para o crescimento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando a opção Tamanho.

- Certifique-se de que a **redução automática** esteja desabilitada a fim de evitar uma sobrecarga desnecessária que possa afetar negativamente o desempenho.

- Se você estiver executando o SQL Server 2012, instale a Atualização Cumulativa 10 do Service Pack 1. Essa atualização contém a correção para o desempenho ruim de E/S ao executar a instrução seleção na tabela temporária no SQL Server 2012. Para saber mais, consulte este [artigo da Base de Dados de Conhecimento](http://support.microsoft.com/kb/2958012).

- Mova os bancos de dados do sistema (por exemplo, msdb e TempDB), backups e os diretórios de log e de dados padrão do SQL Server para os discos de dados não armazenados em cache a fim de melhorar o desempenho. Em seguida, execute as seguintes ações:

	- Ajuste os caminhos do arquivo XEvent e de Rastreamento.
	
	- Ajuste o caminho do Log de Erros do SQL.
	
	- Ajuste o caminho de backup padrão.
	
	- Ajuste o local do banco de dados padrão.

- Estabeleça páginas bloqueadas a fim de reduzir atividades de ES e de paginação.

## Considerações sobre desempenho específico ao recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a obter um desempenho maior:

- **Backup no armazenamento do Azure**: ao realizar backups do SQL Server em execução em máquinas virtuais do Azure, você poderá usar [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados. Quando fizer backup ou restaurar de ou para o armazenamento do Azure, siga as recomendações fornecidas em [Práticas recomendadas de backup do SQL Server para URL e Solução de problemas e restauração de backups armazenados no Armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando [Backup automatizado para SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-automated-backup.md).

	Antes do SQL Server 2012, use a [Ferramenta de backup do SQL Server para o Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência do backup utilizando vários alvos de distribuição de backup.

- **Arquivos de dados do SQL Server no Azure**: este recurso novo, [Arquivos de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível desde o SQL Server 2014. A execução do SQL Server com os arquivos de dados no Azure demonstra características de desempenho comparáveis as dos discos de dados do Azure.

## Próximas etapas

Se você tiver interesse em explorar com mais detalhes o SQL Server e o Armazenamento Premium, consulte o artigo [Usar o Armazenamento Premium do Azure com o SQL Server em máquinas virtuais](virtual-machines-sql-server-use-premium-storage.md).

Para conferir as práticas recomendadas de segurança, consulte [Considerações sobre segurança para o SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-security-considerations.md).

Revise outros tópicos sobre máquina virtual do SQL Server em [Visão geral do SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1125_2015-->
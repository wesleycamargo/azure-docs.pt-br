<properties
	pageTitle="Como usar o armazenamento do Azure para o backup e a restauração do SQL Server | Microsoft Azure"
	description="Backup do SQL Server e banco de dados SQL no armazenamento do Azure. Explica os benefícios do backup de bancos de dados SQL para o armazenamento do Azure, no qual os componentes do SQL Server e do armazenamento do Azure são necessários"
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="carlrab"/>



# Como usar o armazenamento do Azure para o backup e restauração SQL Server

## Visão geral

O recurso que fornece a capacidade de gravar backups do SQL Server no serviço de armazenamento de blobs do Azure foi lançado no SQL Server 2012 SP1 CU2. Você pode usar essa funcionalidade para fazer backup e restauração do serviço Blob do Azure com um banco de dados local do SQL Server ou um banco de dados do SQL Server em uma máquina virtual do Azure. Backup em nuvem oferece benefícios de disponibilidade, armazenamento externo replicados geo ilimitado e facilidade de migração de dados para e da nuvem. Você pode emitir declarações de BACKUP ou RESTAURAÇÃO usando Transact-SQL ou o SMO. Além disso, quando os arquivos de banco de dados são armazenados em um blob do Azure e você está usando o SQL Server 2016, você pode usar o [backup de arquivo de instantâneo](http://msdn.microsoft.com/library/mt169363.aspx) para fazer backups quase instantâneos e restaurações incrivelmente rápidas.

## Vantagens de usar o serviço Blob Azure para Backups do SQL Server

Gerenciamento de armazenamento, o risco de falha de armazenamento, acesso a armazenamento externo e configurar dispositivos são alguns dos desafios de backup gerais. Esses desafios existem para instâncias de banco de dados locais e instâncias de banco de dados de máquina virtual do Azure. A seguinte lista alguns dos principais benefícios de usar o armazenamento de serviço de armazenamento de BLOBs do Azure para backups do SQL Server:

* Ilimitado, confiável e flexível de armazenamento externo: pode ser um conveniente armazenar os backups nos blobs do Azure, flexíveis e fáceis de acessar esta opção de fora da empresa. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificar seus scripts/trabalhos existentes para usar a sintaxe **BACKUP TO URL**. Armazenamento externo deve ser tipicamente longe o suficiente da localização do banco de dados de produção para evitar um desastre único que pode afetar os dois locais de banco de dados externo e produção. Ao optar por fazer a [replicação geográfica de seus blobs do Azure](../storage/storage-redundancy.md), você tem uma camada extra de proteção em caso de um desastre que poderia afetar a região inteira. 
* Arquivo de backup: O serviço de armazenamento de BLOBs do Azure oferece uma alternativa melhor que a opção de fita usadas com frequência para arquivar backups. Armazenamento em fita pode exigir transporte físico para um recurso externo e medidas para proteger a mídia. Armazenar os backups em armazenamento de BLOBs do Azure fornece um instante e altamente disponível, e a opção de arquivamento de um duráveis.
* Sem sobrecarga de gerenciamento de hardware: não há nenhuma sobrecarga de gerenciamento de hardware com o Azure services. Serviços Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* No momento, para instâncias do SQL Server em execução em uma máquina virtual do Azure, o backup dos serviços de armazenamento de blobs do Azure pode ser feito criando discos anexados. No entanto, há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure para backups. Esse limite é de 16 discos para uma instância extra grande e menos para instâncias menores. Ao habilitar o backup direto para blobs do Azure, você terá acesso a um armazenamento praticamente ilimitado.
* Os backups armazenados nos blobs do Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser acessados facilmente para restaurações em um SQL Server local ou em outro SQL Server em execução em uma Máquina Virtual do Azure, sem a necessidade de desanexar/anexar o banco de dados ou baixar e anexar o VHD.
* Benefícios de custo: Paga somente pelo serviço que é usado. Pode ser econômico como uma opção de arquivamento externo e backup. Consulte a [calculadora de preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços") e o [artigo sobre preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Artigo de preços") para obter mais informações.
* Aproveite os snapshots de armazenamento: quando arquivos de banco de dados são armazenados em um blob do Azure e você está usando o SQL Server 2016, você pode usar o [backup de arquivo de instantâneo](http://msdn.microsoft.com/library/mt169363.aspx) para fazer backups quase instantâneos e restaurações incrivelmente rápidas.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

As seções a seguir apresentam o serviço de armazenamento de BLOBs do Azure e os componentes do SQL Server usados quando fazer backup ou a restauração do serviço de armazenamento de BLOBs do Azure. É importante compreender os componentes e a interação entre eles para fazer um backup ou restauração do serviço de armazenamento de BLOBs do Azure.

Criar uma conta do Azure é a primeira etapa para esse processo. Para obter instruções completas sobre a criação de uma conta de armazenamento e a execução de uma restauração simples usando o SQL Server 2014, consulte [Introdução ao serviço de armazenamento do Azure para Backup e Restauração do SQL Server](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx). Para obter instruções completas sobre a criação de uma conta de armazenamento e a execução de uma restauração simples usando o SQL Server 2014, consulte [Tutorial: Usando o serviço de armazenamento de Blob do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Componentes do serviço de armazenamento de blobs do Azure

* Conta de armazenamento: A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. Para obter mais informações sobre os serviços de armazenamento de blobs do Azure, consulte [Como usar o serviço de armazenamento de blobs do Azure](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Contêiner: Um contêiner fornece um agrupamento de um conjunto de Blobs e pode armazenar um número ilimitado de Blobs. Para escrever um SQL Server backup para um serviço Blob do Azure, você deve ter pelo menos o contêiner raiz criado.

* Blob: um arquivo de qualquer tipo e tamanho. Os blobs são endereçáveis usando o seguinte formato de URL: `https://<storage account>.blob.core.windows.net/<container>/<blob>` Para obter mais informações sobre blobs de páginas, consulte [Compreendendo blobs de bloco e blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx)

## Componentes do SQL Server

* URL: URL especifica um URI (Uniform Resource Identifier) para um único arquivo de backup. A URL é usada para fornecer o local e o nome do arquivo de backup do SQL Server. A URL deve apontar para um Blob real, não apenas um contêiner. Se o blob não existir, ele será criado. Se um blob existente for especificado, o BACKUP falhará, a menos que a > opção COM FORMATO seja especificada. A seguir está um exemplo do URL que você especifica no comando de BACKUP: ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Nota:</b> HTTPS não é obrigatório, mas é recomendável. <b>Importante</b> se você optar por copiar e carregar um arquivo de backup para o serviço de armazenamento de Blob do Azure, você deve usar um tipo de blob de páginas como sua opção de armazenamento se você planeja usar este arquivo para operações de restauração. RESTAURAÇÃO por meio de um tipo de blob de blocos falhará com um erro.

* Credencial: As informações necessárias para se conectar e autenticar ao serviço de armazenamento de BLOBs do Azure são armazenadas como uma credencial. Para o SQL Server gravar backups para uma restauração ou BLOBs do Azure dela, uma credencial do SQL Server deve ser criada. Para obter mais informações, veja [Credencial do SQL Server](https://msdn.microsoft.com/library/ms189522.aspx).

## Backups e restauração do banco de dados SQL Server com blobs do Azure - Conceitos e tarefas:

**Conceitos e considerações sobre exemplos de código:**

[Backup e restauração do SQL Server com o Serviço de armazenamento de blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Tutorial de Introdução:**

[Tutorial: Usando o serviço de armazenamento de Blob do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

**Práticas recomendadas, Solução de problemas:**

[Práticas recomendadas de backup e restauração (Serviço de armazenamento de blob do Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=Nov15_HO1-->
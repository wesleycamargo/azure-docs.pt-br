<properties 
	pageTitle="Atividades de movimentação de dados" 
	description="Saiba mais sobre as entidades do Data Factory que você pode usar em pipelines de um Data Factory para mover dados." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="spelluru"/>

# Atividades de movimentação de dados
A [Atividade de Cópia](#copyactivity) executa a movimentação de dados no Azure Data Factory e é alimentada por um [Serviço de Movimentação de Dados globalmente disponível](#global) que pode copiar dados entre vários armazenamentos de dados de forma segura, confiável e escalonável. O serviço escolhe automaticamente a região ideal para executar a operação de movimentação de dados baseada no local de origem e no armazenamento de dados do coletor. Atualmente, a região usada é a mais próxima ao armazenamento de dados do coletor.
 
Vamos entender como a movimentação de dados ocorre em cenários diferentes.

## Copiando dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e coletores (destino) residem na nuvem, a atividade de cópia passa pelas fases a seguir para copiar/mover os dados da origem para o coletor. O serviço de movimentação de dados

1. Lê dados do armazenamento de dados de origem
2.	Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo baseado nas configurações de conjuntos de dados de entrada e de saída e na atividade de cópia. 
3.	Grava dados no armazenamento de dados de destino

![cópia de nuvem para nuvem](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copiando dados entre um armazenamento de dados local e um armazenamento de dados na nuvem
Para [mover com segurança dados entre armazenamentos de dados locais dentro do seu firewall corporativo armazenamento de dados de nuvem](#moveonpremtocloud), você precisará instalar o gateway de gerenciamento de dados, que é um agente que permite a movimentação e o processamento de dados híbridos em seu computador local. O gateway de gerenciamento de dados pode ser instalado no mesmo computador do próprio armazenamento de dados ou em computador separado que tenha acesso ao armazenamento de dados.. Neste cenário, a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo são executadas pelo gateway de gerenciamento de dados. O serviço de movimentação de dados não está envolvido nesse cenário.

![cópia local para nuvem](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copiar dados de/para um armazenamento de dados em uma VM Iaas do Azure 
Você também pode mover dados de/para armazenamentos de dados com suporte hospedados nas VMs do Azure IaaS (máquinas virtuais de infraestrutura como serviço) usando o gateway de gerenciamento de dados. Nesse caso, o gateway de gerenciamento de dados pode ser instalado na mesma VM do Azure em que está o armazenamento de dados ou em uma VM separada que tenha acesso ao armazenamento de dados.

## Fontes de dados com suporte
A atividade de cópia copia os dados de um armazenamento de dados de **origem** para um armazenamento de dados **coletor**. O Data Factory oferece suporte aos seguintes armazenamentos de dados e os **dados de qualquer fonte podem ser gravados em qualquer coletor**. Clique em um armazenamento de dados para saber como copiar dados de/para ele.

| Fontes| Coletores |
|:------- | :---- |
| <ul><li>[Blob do Azure](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documentos do Azure](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de arquivos local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle Database local/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[MySQL Database local/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[DB2 Database local/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Teradata Database local/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Sybase Database local/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[PostgreSQL Database local/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documento do Azure](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de arquivos local /Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


## Tutorial
Para obter um tutorial rápido sobre como usar a Atividade de Cópia, confira [Tutorial: Usar a Atividade de Cópia em um pipeline do Azure Data Factory](data-factory-get-started.md). No tutorial, você usará a Atividade de Cópia para copiar dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure. A tabela a seguir lista as fontes e coletores com suporte pela Atividade de cópia.

## <a name="copyactivity"></a>Atividade de Cópia
A atividade de cópia usa um conjunto de dados de entrada (**origem**) e um conjunto de dados de saída (**coletor**). A cópia de dados é feita em um modo de lotes de acordo com a agenda especificada na atividade. Para saber mais sobre como definir atividades em geral em um alto nível como várias seções e propriedades JSON disponíveis para todas as atividades, confira o artigo [Entendendo pipelines e atividades](data-factory-create-pipelines.md).

A atividade de cópia fornece as seguintes funcionalidades:

### <a name="global"></a>Movimentação de dados globalmente disponível
Mesmo que a sua própria Azure Data Factory esteja disponível somente nas regiões oeste dos EUA e no norte da Europa, o serviço de movimentação de dados capacita a atividade de cópia disponível globalmente nas regiões e regiões geográficas a seguir. A topologia globalmente disponível garante a movimentação de dados eficiente, evitando saltos entre regiões na maioria dos casos.

| Região | Geografia |
| ------ | --------- | 
| Centro dos EUA | EUA |
| Leste dos EUA | EUA |
| Leste dos EUA 2 | EUA |
| Centro-Norte dos EUA | EUA |
| Centro-Sul dos Estados Unidos | EUA |
| Oeste dos EUA | EUA |
| Sul do Brasil | América Latina |
| Norte da Europa | EMEA |
| Europa Ocidental | EMEA |
| Sudeste Asiático | Ásia-Pacífico |
| Leste do Japão | Ásia-Pacífico |

Observe o seguinte:

- Se estiver copiando dados de uma **fonte de dados local** para a **nuvem** ou vice-versa (por exemplo: SQL Server local -> Blobs do Azure), a movimentação de dados, na verdade, é feita pelo **gateway de gerenciamento de dados** em seu ambiente local sem envolvimento do Serviço de Movimento de Dados.
- Se você estiver copiando de uma **fonte de nuvem** para um **destino de nuvem** (por exemplo: Blob do Azure -> SQL Azure), o **serviço de movimentação de dados** escolhe a implantação é **mais próxima do local de coletor na mesma região geográfica** para fazer a transferência. Por exemplo, se você está copiando do Sudeste da Ásia para o Oeste do Japão, a implantação do Serviço de Movimentação de Dados no Leste do Japão é usada para realizar a cópia. Quando a origem e o destino estiverem na mesma região geográfica e não houver nenhum Serviço de Movimentação de Dados disponível nela (por exemplo, Austrália, no momento), a atividade de cópia falhará em vez de percorrer uma região geográfica alternativa. Observação: o serviço de movimentação de dados seria estendido também à Austrália. 

### <a name="moveonpremtocloud"></a>Mover os dados com segurança entre o local e a nuvem
Um dos desafios da integração de dados moderna é mover dados continuamente para e do local para a nuvem. O gateway de gerenciamento de dados é um agente que você pode instalar localmente para habilitar pipelines de dados híbridos.

O gateway de dados fornece as seguintes funcionalidades:

1.	Gerenciar o acesso a armazenamentos de dados locais com segurança.
2.	Modelar armazenamentos de dados locais e armazenamentos de dados em nuvem na mesma data factory e mover dados.
3.	Ter um único painel de monitoramento e gerenciamento com visibilidade do status do gateway com o painel baseado em nuvem da data factory.

Você deve tratar a fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando usar a **Rota Expressa** e **usar o gateway** para estabelecer a conectividade entre o serviço e a fonte de dados.

Confira [Mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter mais detalhes.

### Movimentação de dados confiável e econômica
A atividade de cópia é projetada para mover grandes volumes de dados de forma confiável, resistente a erros transitórios em uma grande variedade de fontes de dados. Os dados podem ser copiados em uma maneira econômica com a opção de habilitar a compactação durante a transmissão.

### Conversões de tipo entre diferentes sistemas de tipo
Armazenamentos de dados diferentes têm sistemas de tipo nativo diferentes. A atividade de cópia executa conversões de tipo automáticas dos tipos de fonte para os tipos de coletor a abordagem de duas etapas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Você pode encontrar o mapeamento para um determinado sistema de tipo nativo para o .NET para o armazenamento de dados nos respectivos artigos de conector de armazenamento de dados. Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar tabelas para que as conversões certas sejam executadas durante a atividade de cópia.

### Trabalhando com diferentes formatos de arquivo
A atividade de cópia dá suporte a vários formatos de arquivo, incluindo os formatos binário, texto e Avro para repositórios baseados em arquivo. Você pode usar a atividade de cópia para converter dados de um formato para outro. Exemplo: texto (CSV) para Avro. Se os dados não estiverem estruturados, você pode omitir a propriedade **Structure** da definição de JSON do [conjunto de dados](data-factory-create-datasets.md).

### Propriedades da atividade de cópia
Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade.

No caso da atividade de cópia, a seção **typeProperties** varia de acordo com os tipos de fontes e coletores. Cada uma das páginas especificas do armazenamento de dados listadas acima documenta essas propriedades específicas para o tipo de armazenamento de dados.


### Desempenho e Ajuste da Atividade de Cópia 
Confira o artigo [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

<!---HONumber=AcomDC_1223_2015-->
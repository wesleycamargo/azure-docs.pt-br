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
	ms.date="02/03/2016" 
	ms.author="spelluru"/>

# Atividades de movimentação de dados
A [Atividade de Cópia](#copyactivity) executa a movimentação de dados no Azure Data Factory e é alimentada por um [serviço globalmente disponível](#global) que pode copiar dados entre vários armazenamentos de dados de forma segura, confiável e escalonável. O serviço escolhe automaticamente a região ideal para realizar a movimentação de dados. A região usada é a mais próxima ao armazenamento de dados do coletor.
 
Vamos entender como a movimentação de dados ocorre em cenários diferentes.

## Copiando dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e coletores (destino) residem na nuvem, a atividade de cópia passa pelas fases a seguir para copiar/mover os dados da origem para o coletor. O serviço que capacita a Atividade de Cópia executa o seguinte:

1. Lê dados do armazenamento de dados de origem
2.	Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo baseado nas configurações de conjuntos de dados de entrada e de saída e na atividade de cópia. 
3.	Grava dados no armazenamento de dados de destino

![cópia de nuvem para nuvem](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copiando dados entre um armazenamento de dados local e um armazenamento de dados na nuvem
Para [mover com segurança dados entre armazenamentos de dados locais dentro do seu firewall corporativo armazenamento de dados de nuvem](#moveonpremtocloud), você precisará instalar o gateway de gerenciamento de dados, que é um agente que permite a movimentação e o processamento de dados híbridos em seu computador local. O gateway de gerenciamento de dados pode ser instalado no mesmo computador do próprio armazenamento de dados ou em computador separado que tenha acesso ao armazenamento de dados.. Neste cenário, a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo são executadas pelo gateway de gerenciamento de dados. Os dados não fluem pelo serviço Azure Data Factory nesse caso. O gateway de gerenciamento de dados grava diretamente os dados no repositório de destino.

![cópia local para nuvem](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copiar dados de/para um armazenamento de dados em uma VM Iaas do Azure 
Você também pode mover dados de/para armazenamentos de dados com suporte hospedados nas VMs do Azure IaaS (máquinas virtuais de infraestrutura como serviço) usando o gateway de gerenciamento de dados. Nesse caso, o gateway de gerenciamento de dados pode ser instalado na mesma VM do Azure em que está o armazenamento de dados ou em uma VM separada que tenha acesso ao armazenamento de dados.

## Fontes de dados com suporte
A atividade de cópia copia os dados de um armazenamento de dados de **origem** para um armazenamento de dados **coletor**. O Data Factory oferece suporte aos seguintes armazenamentos de dados e os **dados de qualquer fonte podem ser gravados em qualquer coletor**. Clique em um armazenamento de dados para saber como copiar dados de/para ele.

| Fontes| Coletores |
|:------- | :---- |
| <ul><li>[Blob do Azure](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documentos do Azure (consulte a observação abaixo)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server Local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de Arquivos Local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle Database Local/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[Banco de Dados MySQL Local/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[Banco de Dados DB2 Local/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Banco de Dados Teradata Local/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Banco de Dados Sybase Local/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[Banco de Dados PostgreSQL Local/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[Fontes de dados ODBC local/Azure IaaS](data-factory-odbc-connector.md)</li><li>[Sistema de Arquivos Distribuído do Hadoop (HDFS) Local/Azure IaaS](data-factory-hdfs-connector.md)</li><li>[Fontes do OData](data-factory-odata-connector.md)</li><li>[Tabela Web](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Blob do Azure](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documentos do Azure (ver observação abaixo)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[SQL Server Local/IaaS do Azure](data-factory-sqlserver-connector.md)</li><li>[Sistema de Arquivos Local /IaaS do Azure](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Você só pode mover de/para um Banco de Dados de Documentos do Azure de/para outros serviços do Azure como Blobs do Azure, Tabela do Azure, Banco de Dados SQL, SQL Data Warehouse, Banco de Dados de Documentos do Azure e Repositório Azure Data Lake. Também haveria suporte em breve para a matriz completa para Banco de Dados de Documentos do Azure.

## Tutorial
Para obter um tutorial rápido sobre como usar a Atividade de Cópia, confira [Tutorial: Usar a Atividade de Cópia em um pipeline do Azure Data Factory](data-factory-get-started.md). No tutorial, você usará a Atividade de Cópia para copiar dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure.

## <a name="copyactivity"></a>Atividade de Cópia
A atividade de cópia usa um conjunto de dados de entrada (**origem**) e um conjunto de dados de saída (**coletor**). A cópia de dados é feita em um modo de lotes de acordo com a agenda especificada na atividade. Para saber mais sobre como definir atividades em geral, consulte o artigo [Compreendendo Pipelines e Atividades](data-factory-create-pipelines.md).

A atividade de cópia fornece as seguintes funcionalidades:

### <a name="global"></a>Movimentação de dados globalmente disponível
Mesmo que a sua própria Azure Data Factory esteja disponível somente nas regiões oeste dos EUA e na Europa Setentrional, o serviço capacita a atividade de cópia disponível globalmente nas regiões e regiões geográficas a seguir. A topologia globalmente disponível garante a movimentação de dados eficiente, evitando saltos entre regiões na maioria dos casos.

O **Gateway de Gerenciamento de Dados** ou o **Azure Data Factory** realiza a movimentação de dados com base na localização dos repositórios de dados de origem e de destino em uma operação de cópia. Confira a seguinte tabela para obter detalhes:

Local do repositório de dados de origem | Local do repositório de dados de destino | A movimentação de dados é executada por  
-------------------------- | ------------------------------- | ----------------------------- 
VM local/do Azure (IaaS) | nuvem | **Gateway de Gerenciamento de Dados** no computador local/VM do Azure. Os dados não fluem pelo serviço na nuvem. <br/> Observação: o Gateway de Gerenciamento de Dados pode estar no mesmo computador local/VM do Azure que o repositório de dados ou em outro computador local/VM do Azure, desde que possa se conectar a ambos os repositórios de dados.<br/>
nuvem | VM local/do Azure (IaaS) | Mesmo que acima. 
VM local/do Azure (IaaS) | VM local/do Azure | **O Gateway de Gerenciamento de Dados associado à origem**. Os dados não fluem pelo serviço na nuvem. Confira a observação acima.   
nuvem | nuvem | **O serviço de nuvem que capacita a Atividade de Cópia**. O Azure Data Factory usa a implantação do serviço na região mais próxima ao local do coletor na mesma região geográfica. Consulte a seguinte tabela para obter o mapeamento: <br/><br/><table><tr><th>Região do repositório de dados de destino</th><th>Região usada para movimentação de dados</th></tr><tr><td>Leste dos EUA</td><td>Leste dos EUA</td></tr><tr><td>Leste dos EUA 2</td><td>Leste dos EUA 2</td><tr/><tr><td>EUA Central</td><td>EUA Central</td><tr/><tr><td>Oeste dos EUA</td><td>Oeste dos EUA</td></tr><tr><td>Centro-Norte dos EUA</td><td>Centro-Norte dos EUA</td></tr><tr><td>Centro-Sul dos EUA</td><td>Centro-Sul dos EUA</td></tr><tr><td>Europa Setentrional</td><td>Europa Setentrional</td></tr><tr><td>Europa Ocidental</td><td>Europa Ocidental</td></tr><tr><td>Sudeste Asiático</td><td>Sudeste Asiático</td></tr><tr><td>Ásia Oriental</td><td>Sudeste Asiático</td></tr><tr><td>Leste do Japão</td><td>Leste do Japão</td></tr><tr><td>Oeste do Japão</td><td>Leste do Japão</td></tr><tr><td>Sul do Brasil</td><td>Sul do Brasil</td></tr><tr><td>Leste da Austrália</td><td>Leste da Austrália</td></tr><tr><td>Sudeste da Austrália</td><td>Sudeste da Austrália</td></tr></table>


> [AZURE.NOTE] Se a região do repositório de dados de destino não estiver na lista acima, a Atividade de Cópia falhará em vez de passar por uma região alternativa.



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

<!---HONumber=AcomDC_0309_2016-->
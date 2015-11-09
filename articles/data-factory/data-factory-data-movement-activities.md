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
	ms.date="10/27/2015" 
	ms.author="spelluru"/>

# Atividades de movimentação de dados
A data factory tem um [serviço disponível globalmente](#global) para dar suporte à movimentação de dados com [atividade de cópia](#copyactivity) em uma variedade de armazenamentos de dados listados abaixo. A data factory também tem suporte interno para [mover dados entre pontos locais e a nuvem com segurança](#moveonpremtocloud) usando o gateway de gerenciamento de dados.

Para obter um tutorial rápido sobre como usar a Atividade de Cópia, consulte [Tutorial: uso da Atividade de Cópia em um Pipeline do Azure Data Factory](data-factory-get-started.md). No tutorial, você usará a Atividade de Cópia para copiar dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure. A tabela a seguir lista as fontes e coletores com suporte pela Atividade de cópia.


## Repositórios de dados com suporte para a atividade de cópia
A atividade de cópia copia os dados de um armazenamento de dados de **origem** para um armazenamento de dados **coletor**. A data factory dá suporte aos seguintes combinações de armazenamentos de dados, fonte e coletor. Clique em um armazenamento de dados para saber como copiar dados de/para ele.

| **Fonte** | **Coletor** |
| ------ | ---- |
| [Blob do Azure](data-factory-azure-blob-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Banco de Dados de Documentos do Azure, Sistema de arquivos local, Repositório Azure Data Lake |
| [Tabela do Azure](data-factory-azure-table-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Banco de Dados de Documentos do Azure, Repositório Azure Data Lake |
| [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Banco de Dados de Documentos do Azure, Repositório Azure Data Lake |
| [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Banco de Dados de Documentos do Azure, Repositório Azure Data Lake |
| [Banco de Dados de Documentos do Azure](data-factory-azure-documentdb-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Repositório Azure Data Lake |
| [Repositório Azure Data Lake](data-factory-azure-datalake-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Banco de Dados de Documentos do Azure, Sistema de arquivos local, Repositório Azure Data Lake | 
| [SQL Server no IaaS](data-factory-sqlserver-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Sistema de arquivos local](data-factory-onprem-file-system-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Sistema de Arquivos local, Repositório Azure Data Lake |
| [SQL Server local](data-factory-sqlserver-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados Oracle local](data-factory-onprem-oracle-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados MySQL local](data-factory-onprem-mysql-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados DB2 local](data-factory-onprem-db2-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados Teradata local](data-factory-onprem-teradata-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados Sybase local](data-factory-onprem-sybase-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |
| [Banco de dados PostgreSQL local](data-factory-onprem-postgresql-connector.md) | Blob do Azure, Tabela do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server local, SQL Server na IaaS, Repositório Azure Data Lake |

## <a name="copyactivity"></a>Atividade de cópia
A atividade de cópia usa um conjunto de dados de entrada (**fonte**) e copia os dados por configuração de atividade para um conjunto de dados de saída (**coletor**). A cópia de dados é feita em um modo de lotes de acordo com a agenda especificada na atividade.

> [AZURE.NOTE]Para saber mais sobre como definir atividades em geral em um nível alto como várias seções e propriedades JSON disponíveis para todas as atividades, confira o artigo [Entendendo pipelines e atividades](data-factory-create-pipelines.md).

A atividade de cópia fornece as seguintes funcionalidades:

### <a name="global"></a>Movimentação de dados globalmente disponível
O serviço de movimentação de dados que possibilita a atividade de cópia está disponível globalmente nas seguintes regiões e regiões geográficas. A topologia globalmente disponível garante a movimentação de dados eficiente, evitando saltos entre regiões na maioria dos casos.

| Região | painel Geografia do app's selecionado |
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

### <a name="moveonpremtocloud"></a>Mover os dados com segurança entre o local e a nuvem
Um dos desafios da integração de dados moderna é mover dados continuamente para e do local para a nuvem. O gateway de gerenciamento de dados é um agente que você pode instalar localmente para habilitar pipelines de dados híbridos.

O gateway de dados fornece as seguintes funcionalidades:

1.	Gerenciar o acesso a armazenamentos de dados locais com segurança.
2.	Modelar armazenamentos de dados locais e armazenamentos de dados em nuvem na mesma data factory e mover dados.
3.	Ter um único painel de monitoramento e gerenciamento com visibilidade do status do gateway com o painel baseado em nuvem da data factory.


Veja [Mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter mais detalhes.

### Movimentação de dados confiável e econômica
A atividade de cópia é projetada para mover grandes volumes de dados de forma confiável, resistente a erros transitórios em uma grande variedade de fontes de dados. Os dados podem ser copiados em uma maneira econômica com a opção de habilitar a compactação durante a transmissão.

### Conversões de tipo entre diferentes sistemas de tipo
Armazenamentos de dados diferentes têm sistemas de tipo nativo diferentes. A atividade de cópia executa conversões de tipo automáticas dos tipos de fonte para os tipos de coletor a abordagem de duas etapas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Você pode encontrar o mapeamento para um determinado sistema de tipo nativo para o .NET para o armazenamento de dados nos respectivos artigos de conector de armazenamento de dados. Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar tabelas para que as conversões certas sejam executadas durante a atividade de cópia.

### Trabalhando com diferentes formatos de arquivo
A atividade de cópia dá suporte a vários formatos de arquivo, incluindo os formatos binário, texto e Avro para repositórios baseados em arquivo. É possível usar a atividade de cópia para a conversão de dados de um formato para outro. Exemplo: texto (CSV) para Avro.

### Propriedades da atividade de cópia
Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade.

No caso da atividade de cópia, a seção **typeProperties** varia de acordo com os tipos de fontes e coletores. Cada uma das páginas especificas do armazenamento de dados listadas acima documenta essas propriedades específicas para o tipo de armazenamento de dados.


## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-movement-activities.md).

<!---HONumber=Nov15_HO1-->
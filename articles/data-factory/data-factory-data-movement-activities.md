<properties 
	pageTitle="Atividades de movimentação de dados | Microsoft Azure" 
	description="Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre armazenamentos em nuvem, entre os locais e a nuvem. Usar a Atividade de Cópia." 
	keywords="movimentação de dados, migração de dados, copiar dados, transferir dados"
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
	ms.date="05/31/2016" 
	ms.author="spelluru"/>

# Movimentação de dados e a Atividade de Cópia: migrando dados para a nuvem e entre os armazenamentos de nuvem
A movimentação de dados de uma fonte para um coletor (destino) é executada pela [Atividade de Cópia](#copyactivity) no Azure Data Factory. A Atividade de Cópia é alimentada por um [serviço disponível globalmente](#global) seguro, confiável e escalonável. O serviço escolhe automaticamente a região ideal para executar a movimentação de dados, geralmente a região mais próxima do armazenamento de dados do coletor.

Aqui está como a migração de dados ocorre entre dois armazenamentos de dados de nuvem, entre um armazenamento de dados local e um armazenamento de dados em nuvem e de/para um armazenamento de dados em uma VM de IaaS do Azure.

## Copiando dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e coletores (destino) residem na nuvem, a atividade de cópia passa pelas fases a seguir para copiar/mover os dados da origem para o coletor. O serviço que capacita a Atividade de Cópia executa o seguinte:

1. Lê dados do armazenamento de dados de origem
2.	Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo baseado nas configurações de conjuntos de dados de entrada e de saída e na atividade de cópia.
3.	Grava dados no armazenamento de dados de destino

![cópia de nuvem para nuvem](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Para [mover com segurança dados entre armazenamentos de dados locais dentro do seu firewall corporativo armazenamento de dados de nuvem](#moveonpremtocloud), você precisará instalar o gateway de gerenciamento de dados, que é um agente que permite a movimentação e o processamento de dados híbridos em seu computador local. O gateway de gerenciamento de dados pode ser instalado no mesmo computador do próprio armazenamento de dados ou em computador separado que tenha acesso ao armazenamento de dados.. Neste cenário, a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo são executadas pelo gateway de gerenciamento de dados. Os dados não fluem pelo serviço Azure Data Factory nesse caso. O gateway de gerenciamento de dados grava diretamente os dados no repositório de destino.

![cópia local para nuvem](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copiar dados de/para um armazenamento de dados em uma VM Iaas do Azure 
Você também pode mover dados de/para armazenamentos de dados com suporte hospedados nas VMs do Azure IaaS (máquinas virtuais de infraestrutura como serviço) usando o gateway de gerenciamento de dados. Nesse caso, o gateway de gerenciamento de dados pode ser instalado na mesma VM do Azure em que está o armazenamento de dados ou em uma VM separada que tenha acesso ao armazenamento de dados.

## Fontes de dados com suporte
A atividade de cópia copia os dados de um armazenamento de dados de **origem** para um armazenamento de dados **coletor**. O Data Factory oferece suporte aos seguintes armazenamentos de dados e os **dados de qualquer fonte podem ser gravados em qualquer coletor**. Clique em um armazenamento de dados para saber como copiar dados de/para ele.

| Fontes| Coletores |
|:------- | :---- |
| <ul><li>[Blob do Azure](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documentos do Azure (confira a observação abaixo)](data-factory-azure-documentdb-connector.md)</li><li>[Armazenamento Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server Local/IaaS do Azure](data-factory-sqlserver-connector.md)</li><li>[Sistema de Arquivos Local/IaaS do Azure](data-factory-onprem-file-system-connector.md)</li><li>[Banco de Dados Oracle Local/IaaS do Azure](data-factory-onprem-oracle-connector.md)</li><li>[Banco de Dados MySQL Local/IaaS do Azure ](data-factory-onprem-mysql-connector.md)</li><li>[Banco de Dados DB2 Local/IaaS do Azure](data-factory-onprem-db2-connector.md)</li><li>[Banco de Dados Teradata Local/IaaS do Azure ](data-factory-onprem-teradata-connector.md)</li><li>[Banco de Dados Sybase Local/IaaS do Azure](data-factory-onprem-sybase-connector.md)</li><li>[Banco de Dados PostgreSQL Local/IaaS do Azure](data-factory-onprem-postgresql-connector.md)</li><li>[Fontes de dados ODBC Local/IaaS do Azure](data-factory-odbc-connector.md)</li><li>[Sistema de Arquivos Distribuído do Hadoop (HDFS) Local/IaaS do Azure](data-factory-hdfs-connector.md)</li><li>[Fontes do OData](data-factory-odata-connector.md)</li><li>[Tabela Web (tabela do HTML)](data-factory-web-table-connector.md)</li><li>[GE Historian Local/IaaS do Azure](data-factory-odbc-connector.md#ge-historian-store)</li></ul> | <ul><li>[Blob do Azure](data-factory-azure-blob-connector.md)</li><li>[Tabela do Azure](data-factory-azure-table-connector.md)</li><li>[Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)</li><li>[SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Banco de Dados de Documentos do Azure (confira a observação abaixo)](data-factory-azure-documentdb-connector.md)</li><li>[Armazenamento Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server Local/IaaS do Azure](data-factory-sqlserver-connector.md)</li><li>[Sistema de Arquivos Local /IaaS do Azure](data-factory-onprem-file-system-connector.md)</li><li>[Banco de Dados Oracle Local/IaaS do Azure](data-factory-onprem-oracle-connector.md)</li></ul> |


> [AZURE.NOTE] Não há suporte para a cópia de dados de/para um Banco de Dados de Documentos do Azure de/para armazenamentos de dados locais/de IaaS do Azure no momento. A matriz completa do Banco de Dados de Documentos do Azure estará habilitada em breve.

Se precisar mover dados de/para um armazenamento de dados que não seja compatível com a **Atividade de Cópia**, você poderá usar a **atividade personalizada** no Data Factory com sua própria lógica para copiar/mover os dados. Confira o artigo [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md) para obter detalhes sobre como criar e usar uma atividade personalizada.

## Tutorial
Para obter um tutorial rápido sobre como usar a Atividade de Cópia, confira [Tutorial: Usar a Atividade de Cópia em um pipeline do Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). No tutorial, você usará a Atividade de Cópia para copiar dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure.

## <a name="copyactivity"></a>Atividade de Cópia
A Atividade de Cópia copia dados de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**coletor**). A cópia de dados é feita em um modo de lotes de acordo com a agenda especificada na atividade. Para saber mais sobre como definir as atividades em geral, confira o artigo [Understanding Pipelines & Activities](data-factory-create-pipelines.md) (Noções básicas sobre pipelines e atividades).

A atividade de cópia fornece as seguintes funcionalidades:

### <a name="global"></a>Movimentação de dados globalmente disponível
Mesmo que a sua própria Azure Data Factory esteja disponível somente nas regiões oeste dos EUA, leste dos EUA e na Europa Setentrional, o serviço capacita a atividade de cópia disponível globalmente nas regiões e regiões geográficas a seguir. A topologia globalmente disponível garante a movimentação de dados eficiente, evitando saltos entre regiões na maioria dos casos.

O **Gateway de Gerenciamento de Dados** ou o **Azure Data Factory** realizam a movimentação de dados com base na localização dos repositórios de dados de origem e de destino em uma operação de cópia. Confira a seguinte tabela para obter detalhes:

Local do repositório de dados de origem | Local do repositório de dados de destino | A movimentação de dados é executada por  
-------------------------- | ------------------------------- | ----------------------------- 
VM local/do Azure (IaaS) | nuvem | **Gateway de Gerenciamento de Dados** em um computador local/VM do Azure. Os dados não fluem pelo serviço na nuvem. <br/><br/>Observação: o Gateway de Gerenciamento de Dados pode estar no mesmo computador local/VM do Azure que o armazenamento de dados ou em outro computador local/VM do Azure, desde que possa se conectar a ambos os repositórios de dados.
nuvem | VM local/do Azure (IaaS) | Mesmo que acima. 
VM local/do Azure (IaaS) | VM local/do Azure | **Gateway de Gerenciamento de Dados associado à origem**. Os dados não fluem pelo serviço na nuvem. Confira a observação acima.   
nuvem | nuvem | **O serviço de nuvem que capacita a Atividade de Cópia**. O Azure Data Factory usa a implantação do serviço na região mais próxima ao local do coletor na mesma região geográfica. Veja a seguinte tabela para obter o mapeamento: <br/><br/><table><tr><th>Região do armazenamento de dados de destino</th><th>Região usada para movimentação de dados</th></tr><tr><td>Leste dos EUA</td><td>Leste dos EUA</td></tr><tr><td>Leste dos EUA 2</td><td>Leste dos EUA 2</td><tr/><tr><td>EUA Central</td><td>EUA Central</td><tr/><tr><td>Oeste dos EUA</td><td>Oeste dos EUA</td></tr><tr><td>Centro-Norte dos EUA</td><td>Centro-Norte dos EUA</td></tr><tr><td>Centro-Sul dos EUA</td><td>Centro-Sul dos EUA</td></tr><tr><td>Europa Setentrional</td><td>Europa Setentrional</td></tr><tr><td>Europa Ocidental</td><td>Europa Ocidental</td></tr><tr><td>Sudeste Asiático</td><td>Sudeste Asiático</td></tr><tr><td>Ásia Oriental</td><td>Sudeste Asiático</td></tr><tr><td>Leste do Japão</td><td>Leste do Japão</td></tr><tr><td>Oeste do Japão</td><td>Leste do Japão</td></tr><tr><td>Sul do Brasil</td><td>Sul do Brasil</td></tr><tr><td>Leste da Austrália</td><td>Leste da Austrália</td></tr><tr><td>Sudeste da Austrália</td><td>Sudeste da Austrália</td></tr></table>


> [AZURE.NOTE] Se a região do repositório de dados de destino não estiver na lista acima, a Atividade de Cópia falhará em vez de passar por uma região alternativa.



### <a name="moveonpremtocloud"></a>Transfira os dados com segurança entre a localização local e a nuvem
Um dos desafios da integração de dados moderna é transferir dados continuamente para e do local para a nuvem. O gateway de gerenciamento de dados é um agente que você pode instalar localmente para habilitar pipelines de dados híbridos.

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
A Atividade de Cópia dá suporte a vários formatos de arquivo, incluindo os formatos binário, texto, Avro, ORC e JSON para repositórios baseados em arquivo. Você pode usar a atividade de cópia para converter dados de um formato para outro. Exemplo: texto (CSV) para Avro. Se os dados não estiverem estruturados, você pode omitir a propriedade **Structure** da definição de JSON do [conjunto de dados](data-factory-create-datasets.md).

### Propriedades da atividade de cópia
Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade.

No caso da Atividade de Cópia, a seção **typeProperties** varia de acordo com os tipos de fontes e coletores. Clique em uma fonte/um coletor na seção [Fontes/Coletores com Suporte](#supported-data-stores) para aprender sobre as propriedades de tipo compatíveis com a Atividade de Cópia para esse armazenamento de dados.

Cada uma das páginas especificas do armazenamento de dados listadas acima documenta essas propriedades específicas para o tipo de armazenamento de dados.

### Cópia solicitada
É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Digamos que você tenha duas atividades de cópia em um pipeline: CopyActivity1 e CopyActivity com os conjuntos de dados de saída dos dados de entrada a seguir.

CopyActivity1: Entrada: Dataset1 Saída Dataset2

CopyActivity2: Entradas: Dataset2 Saída Dataset4

CopyActivity2 seria executado somente se CopyActivity1 tivesse sido executado com êxito e Dataset2 estivesse disponível.

No exemplo acima, CopyActivity2 pode ter uma entrada diferente, digamos Dataset3, mas você precisará especificar Dataset2 também como uma entrada para CopyActivity2 para que a atividade não seja executada até que CopyActivity1 seja concluído. Por exemplo:

CopyActivity1: Entrada: Dataset1 Saída Dataset2

CopyActivity2: Entradas: Dataset3, Dataset2 Saída: Dataset4

Quando várias entradas forem especificadas, somente o primeiro conjunto de dados de entrada será usado para copiar dados, mas outros conjuntos de dados serão usados como dependências. CopyActivity2 começaria executando apenas quando as seguintes condições fossem atendidas:

- CopyActivity2 foi concluído com êxito e Dataset2 está disponível. Esse conjunto de dados não será usado ao copiar dados para Dataset4. Ele atua apenas como uma dependência de agendamento de CopyActivity2.
- Dataset3 está disponível. Esse conjunto de dados representa os dados que são copiados para o destino.


### Desempenho e Ajuste da Atividade de Cópia 
Confira o artigo [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.


## Assistente de Cópia do Data Factory
O **Assistente de Cópia do Data Factory** permite que você crie um pipeline para copiar dados de fontes compatíveis para destinos sem gravar as definições de JSON para serviços vinculados, conjuntos de dados e pipelines. Para inicializar o Assistente de Cópia, clique no bloco **Copiar dados** na página inicial do Data Factory.

![Assistente de cópia de dados](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### Recursos

#### Um assistente intuitivo e simples para copiar dados 
Este assistente permite mover dados facilmente de uma origem para um destino em minutos com estas etapas simples:

1.	Selecione a **origem**
2.	Selecione o **destino**
3.	Defina as **configurações**

![Selecione uma fonte de dados](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### Exploração de dados avançados e mapeamentos de esquema
Você pode procurar tabelas/pastas, visualizar dados, mapear esquema, validar expressões e executar transformações de dados simples no assistente.

**Procurar tabelas/pastas** ![Procurar tabelas e pastas](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### Experiência escalonável para tipos de dados e objetos diversificados
A experiência foi projetada tendo em mente Big Data desde o início. É simples e eficiente de criar pipelines de Data Factory que movem centenas de pastas, arquivos ou tabelas.

**Visualizar dados, mapear esquemas e executar transformações simples** ![Configurações de formato de arquivo](./media/data-factory-data-movement-activities/file-format-settings.png) ![Mapeamento de esquema](./media/data-factory-data-movement-activities/schema-mapping.png) ![Validar expressões](./media/data-factory-data-movement-activities/validate-expressions.png)

#### Experiência escalonável para tipos de dados e objetos diversificados
A experiência foi projetada tendo em mente Big Data desde o início. Mover centenas de pastas, arquivos ou tabelas é simples e eficiente usando o Assistente de Cópia.

![Selecionar tabelas para copiar dados](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### Opções de agendamento mais avançadas
Você pode executar a operação de cópia apenas uma vez ou segundo um agendamento (por hora, por dia etc...). Ambas as opções podem ser usadas para a variedade de conectores em cópias de área de trabalho local, da nuvem e locais. A cópia única permite a movimentação de dados de uma origem para um destino apenas uma vez e aplica-se a dados de qualquer tamanho e quaisquer formatos com suporte. A cópia programada permite copiar dados com uma recorrência definida. Você pode aproveitar as configurações avançadas (como repetição, tempo limite, alertas etc.) para configurar a cópia programada.

![Propriedades de agendamento](./media/data-factory-data-movement-activities/scheduling-properties.png)


### Experimentar 
Para ver uma explicação rápida sobre como usar o **Assistente de Cópia do Data Factory** para criar um pipeline com uma Atividade de Cópia, confira [Tutorial: Criar um pipeline com a Atividade de Cópia usando o Assistente de Cópia do Data Factory](data-factory-copy-data-wizard-tutorial.md).


### Variáveis no caminho da pasta de Blob do Azure
Você pode usar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base na [variável de sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). As variáveis com suporte são: **ano**, **mês**, **dia**, **hora**, **minuto** e **{personalizado}**. Exemplo: pastadeentrada/{ano}/{mês}/{dia}.

Suponha que você tenha pastas de entrada no seguinte formato:
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Clique no botão **Procurar** para **Arquivo ou pasta**, navegue até uma dessas pastas, digamos 2016 -> 03 -> 01 -> 02, e clique em **Escolher**. Você verá **2016/03/01/02** na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{dia}**, **02** por **{hora}** e pressione **TAB**. Você deve ver listas suspensas para escolher o **formato** dessas quatro variáveis, conforme mostrado abaixo:

![Usando variáveis de sistema](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

Você também pode usar uma variável **personalizada**, conforme mostrado abaixo, e usar qualquer [cadeia de caracteres com formato permitido](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Certifique-se de selecionar uma pasta com essa estrutura usando o botão Procurar primeiro, substitua um valor por **{personalizado}** e pressione **TAB** para ver a caixa de texto na qual você pode digitar a cadeia de caracteres de formato.

![Usando variáveis personalizadas](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

<!---HONumber=AcomDC_0629_2016-->
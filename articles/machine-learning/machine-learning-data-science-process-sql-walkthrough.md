<properties 
	pageTitle="Processo e Tecnologia de Análise Avançada em ação: usando o SQL Server | Microsoft Azure" 
	description="Processo e Tecnologia de Análise Avançada em ação"  
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="mohabib;fashah;bradsev"/>

                
# Processo e Tecnologia de Análise Avançada em ação: usando o SQL Server

Neste tutorial, você seguirá o guia do ADAPT (Processo e Tecnologia de Análise Avançada) do Azure de ponta a ponta para criar e implantar um modelo usando um conjunto de dados disponível ao público — o conjunto de dados [Corridas de Táxi em NYC](http://www.andresmh.com/nyctaxitrips/).


## <a name="dataset"></a>Descrição do conjunto de dados Corridas de Táxi em NYC

Os dados de Corridas de Táxi em NYC são cerca de 20 GB de arquivos CSV (aproximadamente 48 GB descompactados), que incluem mais de 173 milhões de corridas individuais e a tarifa paga por cada viagem. Cada registro de corrida inclui o local e horário de saída e chegada, número da carteira de motorista de taxista anônima e o número do medalhão (identificador exclusivo do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. O CSV contém ‘trip_data’ detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e quilometragem da corrida. Aqui estão alguns exemplos de registros:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. O CSV ‘trip_fare’ contém detalhes sobre as tarifas pagas em cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para unir trip_data e trip_fare é composta pelos campos: medallion, hack_license e pickup_datetime.

## <a name="mltasks"></a>Exemplos de tarefas de previsão

Reformularemos três problemas de previsão com base em *tip_amount*, sendo eles:

1. Classificação binária: prever ou não se uma gorjeta foi paga por uma corrida, ou seja, um *tip_amount* maior que US$ 0 é um exemplo de positivo, enquanto um *tip_amount* de US$ 0 é um exemplo de negativo.

2. Classificação multiclasse: prever o intervalo da gorjetas pagas pela corrida. Dividimos *tip_amount* em cinco compartimentos ou classes:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. Tarefa de regressão: prever o valor da gorjeta paga por uma corrida.


## <a name="setup"></a>Configurando o ambiente de ciência de dados do Azure para análise avançada

Como indicado no guia [Planejar seu ambiente](machine-learning-data-science-plan-your-environment.md), há várias opções para trabalhar com o conjunto de dados de Corridas de Táxi em NYC no Azure:

- Trabalhar com os dados em blobs do Azure e depois modelá-los no Aprendizado de Máquina do Azure
- Carregar os dados para um banco de dados SQL Server e depois modelá-los no Aprendizado de Máquina do Azure

Neste tutorial, demonstraremos a importação em massa paralela dos dados para um SQL Server, a exploração de dados, a engenharia de recursos e redução de amostragem usando o SQL Server Management Studio, bem como o IPython Notebook. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [IPython notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são compartilhados no GitHub. Um IPython Notebook de exemplo para trabalhar com dados em blobs do Azure também está disponível no mesmo local.

Para configurar seu ambiente de Ciência de Dados do Azure:

1. [Criar uma conta de armazenamento](../storage-create-storage-account.md)

2. [Criar um espaço de trabalho de AM do Azure](machine-learning-create-workspace.md)

3. [Provisionar uma Máquina Virtual de Ciência de Dados](machine-learning-data-science-setup-sql-server-virtual-machine.md), que servirá como um SQL Server, bem como um servidor do IPython Notebook.

	> [AZURE.NOTE]Os scripts de exemplo e IPython Notebook serão baixados para a máquina virtual de Ciência de Dados durante o processo de instalação. Quando o script de pós-instalação da VM estiver concluída, os exemplos estarão na sua biblioteca de Documentos da VM: — Scripts de exemplo: `C:\Users<user_name>\Documents\Data Science Scripts` — Exemplo de IPython Notebooks: `C:\Users<user_name>\Documents\IPython Notebooks\DataScienceSamples` onde `<user_name>` é o nome de logon do Windows da sua VM. Vamos nos referir às pastas de exemplo como **Scripts de Exemplo** e **IPython Notebook de Exemplo**.


Dependendo do tamanho do conjunto de dados, do local da fonte de dados e do ambiente de destino do Azure selecionado, esse cenário será semelhante ao [Cenário nº 5: Conjunto de dados grande em arquivos locais, SQL Server de destino na VM do Azure.](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb)

## <a name="getdata"></a>Obter os dados de fonte pública

Para obter o conjunto de dados [Corridas de Táxi em NYC](http://www.andresmh.com/nyctaxitrips/) do seu local público, você pode usar qualquer um dos métodos descritos em [Mover dados bidirecionalmente no Armazenamento de Blobs do Azure](machine-learning-data-science-move-azure-blob.md) para copiar os dados em sua nova máquina virtual.

Para copiar os dados usando AzCopy:

1. Faça logon na sua VM (máquina virtual)

2. Crie um novo diretório no disco de dados da VM (Observação: não use o Disco Temporário que acompanha a VM como um Disco de Dados).

3. Em uma janela de Prompt de Comando, execute a seguinte linha de comando Azcopy, substituindo <path_to_data_folder> pela pasta de dados criada em (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

	Quando o AzCopy for concluído, um total de 24 arquivos CSV zipados (12 de trip_data e 12 de trip_fare) devem estar na pasta de dados.

4. Descompacte os arquivos baixados. Observe a pasta em que os arquivos descompactados estão. Essa pasta será mencionada como <path_to_data_files>.

## <a name="dbload"></a>Importação de dados em massa para o Banco de Dados do SQL Server

O desempenho do carregamento/transferência de grandes volumes de dados para um banco de dados SQL e as consultas subsequentes pode ser melhorado usando _Tabelas Particionadas e Exibições_. Nesta seção, seguiremos as instruções descritas em [Importação de dados em massa paralela usando tabelas de partição do SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) para criar um novo banco de dados e carregar os dados em tabelas particionadas paralelas.

1. Após conectar à sua VM, inicie o **SQL Server Management Studio**.

2. Conecte-se usando a Autenticação do Windows.

	![Conexão SSMS][12]

3. Se você ainda não tiver alterado o modo de autenticação do SQL Server e criado um novo usuário de logon do SQL, abra o arquivo de script chamado **change_auth.sql** na pasta **Scripts de Exemplo**. Altere o nome de usuário padrão e a senha. Clique em **!Executar** na barra de ferramentas para executar o script.

	![Executar Script][13]

4. Verifique e/ou altere o banco de dados padrão do SQL Server e pastas de log padrão para garantir que bancos de dados recém-criados serão armazenadas em um Disco de Dados. A imagem da VM do SQL Server que é otimizada para cargas de datawarehousing é pré-configurada com discos de dados e de log. Se sua VM não incluía um Disco de Dados e você adicionou novos discos rígidos virtuais durante o processo de configuração da VM, altere as pastas padrão da seguinte maneira:

	- Clique com o botão direito do mouse no nome do SQL Server no painel esquerdo e clique em **Propriedades**.

		![Propriedades do SQL Server][14]

	- Selecione **Configurações de Banco de Dados** na lista **Selecionar uma página** à esquerda.

	- Verifique e/ou altere os **Locais padrão de banco de dados** para os locais de **Disco de Dados** de sua escolha. Este é o local onde residem os novos bancos de dados se criados com as configurações de local padrão.
	
		![Padrões de banco de dados SQL][15]

5. Para criar um novo banco de dados e um conjunto de grupos de arquivos para conter as tabelas particionadas, abra o script de exemplo **create_db_default.sql**. O script criará um novo banco de dados denominado **TaxiNYC** e 12 grupos de arquivos no local de dados padrão. Cada grupo de arquivos conterá um mês de dados trip_data e trip_fare. Modifique o nome do banco de dados, se desejado. Clique em **!Executar** para executar o script.

6. Em seguida, crie duas tabelas de partição, uma para trip_data e outra para trip_fare. Abra o script de exemplo **create_partitioned_table.sql**, que vai:

	- Criar uma função de partição para dividir os dados por mês.
	- Criar um esquema de partição para mapear dados de cada mês para outro grupo de arquivos.
	- Crie duas tabelas de partição mapeadas para o esquema de partição: **nyctaxi_trip** manterá os dados de trip_data e **nyctaxi_fare** os dados de trip_fare.

	Clique em **!Executar** para executar o script e criar as tabelas particionadas.

7. Na pasta **Scripts de Exemplo**, há dois exemplos de scripts de PowerShell fornecidos para demonstrar importações paralelas de dados em massa para tabelas do SQL Server.

	- **bcp_parallel_generic.ps1** é um script genérico para importação paralela de dados em massa em uma tabela. Modifique esse script para definir as variáveis de entrada e de destino, conforme indicado nas linhas de comentário no script.
	- **bcp_parallel_nyctaxi.ps1** é uma versão pré-configurada do script genérico e pode ser usada para carregar as duas tabelas dos dados de Corridas de Táxi em NYC.  

8. Clique com o botão direito no nome do script **bcp_parallel_nyctaxi.ps1** e clique em **Editar** para abri-lo no PowerShell. Examine as variáveis predefinidas e modifique-as de acordo com o nome do banco de dados selecionado, a pasta de dados de entrada, a pasta de log de destino e os caminhos para os arquivos de formato de exemplo **nyctaxi_trip.xml** e **nyctaxi_fare.xml** (fornecidos na pasta **Scripts de Exemplo**).

	![Importação em massa de dados][16]

	Você também pode selecionar o modo de autenticação, o padrão é a Autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará 24 operações de importação em massa paralelas, 12 para cada tabela particionada. Você pode monitorar o progresso da importação de dados abrindo a pasta de dados padrão do SQL Server conforme definido acima.

9. O script do PowerShell informa o início e término. Quando todas as importações em massa forem concluídas, a hora de término é relatada. Verifique a pasta de log de destino para ver se as importações em massa foram bem-sucedidas, ou seja, se nenhum erro foi relatado na pasta de log de destino.

10. O banco de dados agora está pronto para exploração, engenharia de recursos e outras operações conforme desejado. Uma vez que as tabelas são particionadas de acordo com o campo **pickup_datetime**, as consultas que incluem condições **pickup_datetime** na cláusula **WHERE** vão aproveitar o esquema de partição.

11. No **SQL Server Management Studio**, explore o script de exemplo fornecido **sample_queries.sql**. Para executar qualquer um dos exemplos de consulta, destaque as linhas de consulta e clique em **!Executar** na barra de ferramentas.

12. Os dados de Corridas de Táxi em NYC são carregados em duas tabelas separadas. Para melhorar as operações de associação, é altamente recomendável indexá-las. O exemplo de script **create_partitioned_index.sql** cria índices particionados na chave de associação composta **medallion, hack_license e pickup_datetime**.

## <a name="dbexplore"></a>Exploração de dados e engenharia de recursos no SQL Server

Nesta seção, realizaremos exploração de dados e geração de recursos executando consultas SQL diretamente no **SQL Server Management Studio** usando o banco de dados do SQL Server criado anteriormente. Um script de exemplo chamado **sample_queries.sql** é fornecido na pasta **Scripts de Exemplo**. Modifique o script para alterar o nome do banco de dados, se for diferente do padrão: **TaxiNYC**.

Neste exercício, você vai:

- Conectar-se ao **SQL Server Management Studio** usando a Autenticação do Windows ou Autenticação do SQL e o nome de logon e senha do SQL.
- Explorar as distribuições de dados de alguns campos em períodos diferentes.
- Investigar a qualidade dos dados dos campos de longitude e latitude.
- Gerar rótulos de classificação binária e multiclasse com base em **tip_amount**.
- Gerar recursos e computar/comparar as distâncias de viagem.
- Unir as duas tabelas e extrair uma amostra aleatória que será usada para compilar modelos.

Quando você estiver pronto para prosseguir para o Aprendizado de Máquina do Azure, você pode:

1. Salve a consulta SQL final para extrair os dados de exemplo e copiar e colar a consulta diretamente em um módulo de [Leitor][reader] no Aprendizado de Máquina do Azure ou 
2. Mantenha os dados de amostra e projetados que você planeja usar para criar modelos em uma nova tabela de banco de dados e use a nova tabela no módulo [Leitor][reader] no Aprendizado de Máquina do Azure.

Nesta seção, salvaremos a consulta final para extrair e coletar amostras dos dados. O segundo método é demonstrado na seção [Exploração de dados e engenharia de recursos no IPython Notebook](#ipnb).

Para uma verificação rápida do número de linhas e colunas nas tabelas preenchidas anteriormente usando a importação em massa paralela,

	-- Report number of rows in table nyctaxi_trip without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

	-- Report number of columns in table nyctaxi_trip
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip' 

#### Exploração: distribuição de corridas por licença

Este exemplo identifica o medalhão (número do táxi) com mais de 100 corridas dentro de um determinado período de tempo. A consulta aproveitaria o acesso à tabela particionada, já que é condicionada pelo esquema de partição de **pickup_datetime**. Consultar o conjunto de dados completo também usará a tabela particionada e/ou a verificação de índice.

	SELECT medallion, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

#### Exploração: distribuição de corridas por medallion e hack_license

	SELECT medallion, hack_license, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

#### Avaliação de qualidade de dados: verificar registros com longitude e/ou latitude incorretos

Este exemplo investiga se qualquer um dos campos longitude e/ou latitude contém um valor inválido (graus radianos devem estar entre -90 e 90), ou tiver coordenadas (0, 0).

	SELECT COUNT(*) FROM nyctaxi_trip
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### Exploração: distribuição de corridas com gorjeta versus sem gorjeta

Este exemplo localiza o número de corridas que receberam gorjetas em comparação com aquelas que não receberam em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Essa distribuição reflete a distribuição de rótulo binário a ser usado posteriormente para modelagem de classificação binária.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM nyctaxi_fare
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

#### Exploração: distribuição de classe/intervalo de gorjetas

Esse exemplo calcula a distribuição dos intervalos de gorjetas em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Essa é a distribuição das classes de rótulo que serão usados posteriormente para a modelagem de classificação multiclasse.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE 
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4 
		END AS tip_class
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

#### Exploração: calcular e comparar distância de viagem

Este exemplo converte a longitude e latitude de saída e chegada para pontos geográficos do SQL, calcula a distância de viagem usando a diferença de pontos geográficos do SQL e retorna uma amostra aleatória dos resultados de comparação. O exemplo limita os resultados às coordenadas válidas apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

	SELECT 
	pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
	,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
	,trip_distance
	,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
	FROM nyctaxi_trip
	tablesample(0.01 percent)
	WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### Engenharia de recursos em consultas SQL

As consultas de exploração de geração de rótulos e conversão de geografia também podem ser usadas para gerar rótulos/recursos removendo a parte da contagem. Exemplos de engenharia de recursos SQL adicionais são fornecidos na seção [Exploração de dados e engenharia de recursos no IPython Notebook](#ipnb). É mais eficiente executar consultas de geração do recurso no conjunto de dados completo ou em um grande subconjunto dele usando consultas SQL, as quais são executadas diretamente na instância de banco de dados do SQL Server. As consultas podem ser executadas no **SQL Server Management Studio**, IPython Notebook ou qualquer ferramenta/ambiente de desenvolvimento que possa acessar o banco de dados local ou remotamente.

#### Preparando dados para criação de modelo

A consulta a seguir une as tabelas **nyctaxi_trip** e **nyctaxi_fare**, gera um rótulo de classificação binária **tipped**, um rótulo de classificação de multiclasse **tip_class** e extrai uma amostra aleatória de % 1 do conjunto de dados totalmente unido. Essa consulta pode ser copiada e colada diretamente no módulo [Leitor][reader] do [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net) para ingestão de dados direta da instância de banco de dados do SQL Server no Azure. A consulta exclui registros com coordenadas incorretas (0, 0).

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM nyctaxi_trip t, nyctaxi_fare f
	TABLESAMPLE (1 percent)
	WHERE t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploração de dados e engenharia de recursos no IPython Notebook

Nesta seção, realizaremos a exploração de dados e a geração de recursos executando consultas SQL e Python no banco de dados do SQL Server criado anteriormente. Um IPython Notebook de exemplo chamado **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na pasta **IPython Notebook de Exemplo**. Este caderno também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência recomendada ao trabalhar com grandes volumes de dados é a seguinte:

- Leia em uma pequena amostra dos dados em um quadro de dados na memória.
- Execute algumas visualizações e explorações usando os dados de amostrados.
- Experimente a engenharia de recursos usando os dados amostrados. 
- Para exploração de dados maiores, manipulação de dados e engenharia de recursos, use o Python para emitir consultas SQL diretamente no banco de dados do SQL Server na VM do Azure.
- Decida o tamanho da amostra a ser usado para criação de modelo do Aprendizado de Máquina do Azure.

Quando estiver pronto para prosseguir para o Aprendizado de Máquina do Azure, você pode:

1. Salvar a consulta SQL final para extrair os dados de exemplo e copiar e colar a consulta diretamente em um módulo de [Leitor][reader] no Aprendizado de Máquina do Azure. Esse método é demonstrado na seção [Criando modelos no Aprendizado de Máquina do Azure](#mlmodel).    
2. Mantenha os dados de amostra e projetados que você planeja usar para criar modelos em uma nova tabela de banco de dados e use a nova tabela no módulo do [Leitor][reader].

A seguir estão alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. Para obter mais exemplos, consulte o IPython Notebook SQL de exemplo na pasta **IPython Notebook de Exemplo**.

#### Inicializar as credenciais de banco de dados

Inicialize as configurações de conexão de banco de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### Criar conexão de banco de dados

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### Relatar o número de linhas e colunas na tabela nyctaxi_trip

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions 
		WHERE object_id = OBJECT_ID('nyctaxi_trip')
	''', conn)
    
	print 'Total number of rows = %d' % nrows.iloc[0,0]
    
    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns 
		WHERE table_name = ('nyctaxi_trip')
	''', conn)
    
	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 14
    
#### Leitura de uma pequena amostra de dados do Banco de Dados do SQL Server

    t0 = time.time()
    
	query = '''
		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, 
			f.tolls_amount, f.total_amount, f.tip_amount 
		FROM nyctaxi_trip t, nyctaxi_fare f 
		TABLESAMPLE (0.05 PERCENT)
		WHERE t.medallion = f.medallion 
		AND   t.hack_license = f.hack_license 
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)
    
    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)
    
    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

O tempo para ler a tabela de exemplo é de 6.492000 segundos Número de linhas e colunas recuperadas = (84952, 21)
    
#### Estatísticas Descritivas

Agora está tudo pronto para explorar os dados amostrados. Começamos observando as estatísticas descritivas para o campo **trip_distance** (ou qualquer outro):

    df1['trip_distance'].describe()

#### Visualização: exemplo de plotagem da caixa

Em seguida, analisamos a caixa para a distância de viagem para visualizar os quantis

    df1.boxplot(column='trip_distance',return_type='dict')

![Plotar nº 1][1]

#### Visualização: exemplo de plotagem de distribuição

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plotar nº 2][2]

#### Visualização: plotagens de barra e linha

Neste exemplo, podemos compartimentalizar a distância da viagem em cinco compartimentos e visualizar os resultados de compartimentalização.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos plotar a distribuição de compartimentos acima em um gráfico de barras ou linhas abaixo

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plotar nº 3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plotar nº 4][4]

#### Visualização: exemplo de plotagem de dispersão

Mostramos o gráfico de dispersão entre **trip_time_in_secs** e **trip_distance** para ver se há alguma correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plotar nº 6][6]

Da mesma forma, é possível verificar a relação entre **rate_code** e **trip_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plotar nº 8][8]

### Redução de amostragem dos dados no SQL

Ao preparar dados para a criação de modelo no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net), você pode decidir se a **consulta SQL deve ser usada diretamente no módulo de Leitor** ou se mantém os dados de amostra e projetados em uma nova tabela, que você pode usar no módulo de [Leitor][reader] com um simples **SELECT * FROM <nome_da_sua_nova_tabela>**.

Nesta seção, criaremos uma nova tabela para armazenar os dados amostrados e engenhados. Um exemplo de uma consulta direta de SQL para criação de modelos é fornecido na seção [Exploração de dados e engenharia de recursos no SQL Server](#dbexplore).

#### Criar uma tabela de exemplo e preenchê-la com 1% das tabelas unidas. Descartar a tabela primeiro se ela existir.

Nesta seção, unimos as tabelas **nyctaxi_trip** e **nyctaxi_fare**, extraímos uma amostra aleatória de 1% e mantemos os dados de amostra em uma nova tabela chamada **nyctaxi_one_percent**:

    cursor = conn.cursor()
    
    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''
    
    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
		INTO nyctaxi_one_percent 
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (1 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
		AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''
    
    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()
    
### Exploração de dados usando consultas SQL em IPython Notebook

Nesta seção, exploraremos distribuições de dados usando os dados de amostra de 1% que são mantidos na nova tabela criada acima. Observe que explorações semelhantes podem ser executadas usando as tabelas originais, opcionalmente usando **TABLESAMPLE** para limitar a exploração de amostras ou limitando os resultados em um período de tempo determinado usando as partições **pickup_datetime**, conforme ilustrado na seção [Exploração de dados e engenharia de recursos no SQL Server](#dbexplore).

#### Exploração: distribuição diária de corridas

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Exploração: distribuição de corridas por licença

    query = '''
		SELECT medallion,count(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY medallion
	'''
    
	pd.read_sql(query,conn)

### Geração de recursos usando consultas SQL no IPython Notebook

Nesta seção, geraremos novos rótulos e recursos usando consultas SQL de forma direta, operando em uma tabela de amostra de 1% criada na seção anterior.

#### Geração de rótulo: gerar rótulos de classe

No exemplo a seguir, geramos dois conjuntos de rótulos a serem utilizados para modelagem:

1. Rótulos de classe binária **tipped** (prevendo se uma gorjeta será fornecida)
2. Rótulos multiclasse **tip_class** (prevendo o compartimento ou intervalo da gorjeta)

		nyctaxi_one_percent_add_col = '''
			ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
		'''
		
		cursor.execute(nyctaxi_one_percent_add_col)
		cursor.commit()
    
    	nyctaxi_one_percent_update_col = '''
        	UPDATE nyctaxi_one_percent 
            SET 
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

    	cursor.execute(nyctaxi_one_percent_update_col)
		cursor.commit()

#### Engenharia de recurso: recursos de contagem de colunas categóricas

Este exemplo transforma um campo de categoria em um campo numérico, substituindo cada categoria pela contagem de suas ocorrências nos dados.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B AS 
		(
			SELECT medallion, hack_license, 
				SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
				SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
			FROM nyctaxi_one_percent 
			GROUP BY medallion, hack_license
		) 
    
		UPDATE nyctaxi_one_percent 
		SET nyctaxi_one_percent.cmt_count = B.cmt_count,
			nyctaxi_one_percent.vts_count = B.vts_count
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion AND A.hack_license = B.hack_license
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Engenharia de recurso: recursos de compartimento para colunas numéricas

Este exemplo transforma um campo numérico contínuo em intervalos de categoria predefinidos, ou seja, transformação de campo numérico em um campo de categoria.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS 
		(
			SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs, 
			NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
		)
    
		UPDATE nyctaxi_one_percent 
		SET trip_time_bin = B.BinNumber
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion
		AND A.hack_license = B.hack_license
		AND A.pickup_datetime = B.pickup_datetime
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Recurso de engenharia: extrair recursos de local de latitude/longitude decimal

Este exemplo divide a representação decimal de um campo de latitude e/ou longitude em vários campos de região de granularidade diferente, como país, estado, cidade, bloco, etc. Observe que os novos campos geográficos não são mapeados para os locais reais. Para obter informações sobre o mapeamento de locais de codificação geográfica, consulte [Serviços REST do Bing Mapas](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent 
		ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
			l5 varchar(3), l6 varchar(3), l7 varchar(3)
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		UPDATE nyctaxi_one_percent
		SET l1=round(pickup_longitude,0) 
			, l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
			, l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
			, l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
			, l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
			, l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
			, l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END 
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Verificar a forma final da tabela apresentada

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Agora estamos prontos para prosseguir com a criação e implantação de modelo no [Aprendizado de Máquina do Azure](https://studio.azureml.net). Os dados estão prontos para qualquer problemas de previsão identificados anteriormente, ou seja:

1. Classificação binária: prever se uma gorjeta foi ou não paga em uma corrida.

2. Classificação multiclasse: prever o intervalo da gorjeta paga, de acordo com as classes definidas anteriormente.

3. Tarefa de regressão: prever o valor da gorjeta paga por uma corrida.


## <a name="mlmodel"></a>Criando modelos no Aprendizado de Máquina do Azure

Para iniciar o exercício de modelagem, faça logon no seu espaço de trabalho do Aprendizado de Máquina do Azure. Se você ainda não tiver criado uma espaço de trabalho de aprendizado de máquina, consulte [Criar um espaço de trabalho de AM do Azure](machine-learning-create-workspace.md).

1. Para ver os primeiros passos no Aprendizado de Máquina do Azure, consulte [O que é o Estúdio de Aprendizado de Máquina do Azure?](machine-learning-what-is-ml-studio.md)

2. Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net).

3. A página inicial do Estúdio fornece uma grande quantidade de informações, vídeos, tutoriais e links para a Referência de Módulos e outros recursos. Para saber mais sobre o Aprendizado de Máquina do Azure, consulte o [Centro de Documentação do Aprendizado de Máquina do Azure](http://azure.microsoft.com/documentation/services/machine-learning/).

Uma experiência de treinamento típico consiste no seguinte:

1. Criar uma experiência **+NEW**.
2. Levar os dados ao AM do Azure.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gerar recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou conjuntos de dados separados para cada um desses).
6. Selecionar um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido. Por exemplo, classificação binária, classificação multiclasse ou regressão.
7. Treinar um ou mais modelos usando o conjunto de dados de treinamento.
8. Pontuar o conjunto de dados de validação usando os modelos treinados.
9. Avaliar os modelos para computar a métrica relevante para o problema de aprendizado.
10. Ajustar os modelos e selecionar o melhor modelo a ser implantado.

Neste exercício, já exploramos e engenhamos os dados no SQL Server, e escolhemos o tamanho da amostra para ingestão no AM do Azure. Para compilar um ou mais dos modelos de previsão, decidimos:

1. Obter os dados do AM do Azure usando o módulo de [Leitor][reader] disponível na seção **Entrada e saída de dados**. Para saber mais, consulte a página de referência de módulo do [Leitor][reader].

	![Leitor do AM do Azure][17]

2. Selecione **Banco de Dados SQL do Azure** como a **Fonte de dados** no painel **Propriedades**.

3. Insira o nome de DNS do banco de dados no campo **Nome do servidor de banco de dados**. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Insira o **Nome do banco de dados** no campo correspondente.

5. Insira o **Nome de usuário do SQL** no **Nome de conta do usuário do servidor e a senha na **Senha da conta de usuário do servidor**.

6. Marque a opção **Aceitar qualquer certificado do servidor**.

7. Na área de edição de texto **Consulta de banco de dados**, cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos calculados, como rótulos) e reduza as amostras de dados para o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária lendo dados diretamente do banco de dados do SQL Server é mostrado na figura abaixo. Experimentos semelhantes podem ser construídos por meio de classificação multiclasse e problemas de regressão.

![Treino do AM do Azure][10]

> [AZURE.IMPORTANT]Nos exemplos de modelagem de extração de dados e consulta de amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**. Uma etapa importante (obrigatória) em cada um dos exercícios modelagem é **excluir** os rótulos desnecessários para os dois problemas e qualquer outro **vazamento de destino**. Por exemplo, ao usar classificação binária, use o rótulo **tipped** e exclua os campos **tip_class**, **tip_amount** e **total_amount**. Esses últimos são vazamentos de destino, já que eles indicam a gorjeta paga.
>
> Para excluir as colunas desnecessárias e/ou vazamentos de destino, você pode usar o módulo [Colunas do Projeto][project-columns] ou o [Editor de Metadados][metadata-editor]. Para saber mais, consulte as páginas de referência [Colunas do Projeto][project-columns] e [Editor de Metadados][metadata-editor].

## <a name="mldeploy"></a>Implantando modelos no Aprendizado de Máquina do Azure

Quando o modelo estiver pronto, você pode implantá-lo facilmente como um serviço Web diretamente do experimento. Para saber mais sobre a publicação de serviços Web de AM do Azure, consulte [Publicar um serviço Web do Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implantar um novo serviço Web, você precisa:

1. Criar um experimento de pontuação.
2. Publicar o serviço Web.

Para criar um experimento de pontuação por meio de um experimento de treinamento **Concluído**, clique em **CRIAR EXPERIMENTO DE PONTUAÇÃO** na barra de ação inferior.

![Pontuação do Azure][18]

O Aprendizado de Máquina do Azure tentará criar um experimento de pontuação com base nos componentes do experimento de treinamento. Em especial, ele vai:

1. Salvar o modelo treinado e remover os módulos de treinamento de modelo.
2. Identificar uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identificar uma **porta de saída** lógica para representar o esquema de saída do serviço Web.

Quando o experimento de pontuação for criado, revise-o e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta por uma exclua os campos de rótulo, pois eles não estarão disponíveis quando o serviço for chamado. Também é uma prática recomendada reduzir o tamanho do conjunto de dados de entrada e/ou da consulta a apenas alguns registros, suficientes para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas os **Rótulos Pontuados** e **Possibilidades Pontuadas** na saída usando o módulo [Colunas de Projeto][project-columns].

Um exemplo de experimento de pontuação é mostrado na figura abaixo. Quando estiver pronto para publicar, clique no botão **PUBLICAR SERVIÇO WEB** na barra de ação inferior.

![Publicação do AM do Azure][11]

Para recapitular, neste tutorial passo a passo você criou um ambiente de ciência de dados do Azure, trabalhou com um grande conjunto de dados públicos até a aquisição dos dados para treinamento de modelo e publicação de um serviço Web de Aprendizado de Máquina do Azure.

### Informações de Licença

Este passo a passo do exemplo, os scripts que o acompanham e os IPython Notebooks são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### Referências

• [Página de download das Corridas de Táxi em NYC de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/) • [FOILing em dados de Corrida de Táxi em NYC por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [Pesquisa e estatísticas de comissionamento de táxis e limusines de NYC](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=62-->
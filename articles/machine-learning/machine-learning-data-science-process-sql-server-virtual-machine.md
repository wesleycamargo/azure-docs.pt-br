<properties 
	pageTitle="Processar dados do SQL Azure | Microsoft Azure" 
	description="Processar dados do SQL Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="fashah;garye" />

#<a name="heading"></a>Processar dados na Máquina Virtual do SQL Server no Azure

Este documento aborda a exploração de dados e a geração de recursos dados armazenados em uma VM do SQL Server no Azure. Isso pode ser feito da seguinte maneira:

1. [Usando o SQL](#sql)
2. [Usando uma linguagem de programação como Python](#python) 


**Observação**
>As instruções SQL de exemplo neste documento pressupõem que os dados estão no SQL Server. Se não estiverem, consulte o mapa do processo de ciência de dados de nuvem para aprender a mover seus dados para o SQL Server.

###<a name="SQL"></a>Usando o SQL

Descrevemos as seguintes tarefas wrangling de dados nesta seção usando SQL:

1. [Exploração de Dados](#sql-dataexploration)
2. [Geração de Recursos](#sql-featuregen)

####<a name="sql-dataexploration"></a>Exploração de Dados
Aqui estão alguns scripts de SQL de exemplo que podem ser usados para explorar armazenamentos de dados no SQL Server.

**Observação**
> Para ver um exemplo prático, você pode usar o [conjunto de dados de Táxis de NYC](http://www.andresmh.com/nyctaxitrips/) e ver o IPNB intitulado [Realizar o wrangling de dados de NYC usando o IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para obter um treinamento de ponta a ponta.

1. Obter a contagem de observações por dia

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. Obter os níveis em uma coluna categórica

	`select  distinct <column_name> from <databasename>`

3. Obter o número de níveis na combinação de duas colunas categóricas

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obter a distribuição de colunas numéricas

	`select <column_name>, count(*) from <tablename> group by <column_name>`


####<a name="sql-featuregen"></a>Geração de Recursos

Nesta seção, descrevemos as maneiras de gerar recursos usando SQL:

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Agrupamento da Geração de Recursos](#sql-binningfeature)
3. [Propagar os recursos de uma única coluna](#sql-featurerollout)

**Observação**
>Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser unida com a tabela original.

####<a name="sql-countfeature"></a>Geração de recursos baseada em contagem

Este documento demonstra duas maneiras de gerar recursos de contagem. O primeiro método usa soma condicional e o segundo usa a cláusula 'where'. Eles podem então ser unidos à tabela original (usando colunas de chave primária) para que os recursos de contagem fiquem junto com os dados originais.

	select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

	select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
	where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

####<a name="sql-binningfeature"></a>Agrupamento da Geração de Recursos

O exemplo a seguir mostra como gerar recursos compartimentalizados guardando (usando 5 compartimentos) uma coluna numérica que poderá ser usada como um recurso:

	`SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


####<a name="sql-featurerollout"></a>Propagar os recursos de uma única coluna

Nesta seção, demonstraremos como propagar uma única coluna em uma tabela para gerar recursos adicionais. O exemplo presume que há uma coluna de latitude ou longitude na tabela da qual você está tentando gerar recursos.

Aqui está uma breve cartilha sobre os dados de localização de latitude/longitude (recursos de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). É útil entender isso antes de destacar o campo local:

- O sinal nos informa se estamos na parte norte ou sul, leste ou oeste do globo.
- Um dígito em centenas diferente de zero informa que estamos usando longitude, não latitude!
- O dígito de dezena indica uma posição de cerca de 1.000 quilômetros. Ele nos dá informações úteis sobre em qual continente ou oceano estamos.
- O dígito e unidades (um grau decimal) oferece uma posição até 111 quilômetros (60 milhas náuticas, cerca de 69 milhas). Ele pode indicar aproximadamente em qual grande estado ou país estamos.
- A primeira casa decimal representa até 11,1 km: ela pode distinguir a posição de uma cidade grande de uma cidade grande vizinha.
- A segunda casa decimal representa 1,1 km: ela pode separar um vila da próxima.
- A terceira casa decimal representa até 110 m: ela pode identificar um campo agrícola ou campus institucional grande.
- A quarta casa decimal representa até 11 m: ela pode identificar um lote de terreno. Ela é comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
- A quinta casa decimal representa até 1,1 m: ela distingue as árvores mas das outras. Uma precisão desse nível com unidades GPS comerciais só pode ser obtida com a correção diferencial.
- A sexta casa decimal representa até 0,11 m: você pode usá-la para dispor estruturas detalhadamente, projetar paisagens e criar estradas. Ela é mais do que suficiente para acompanhar os movimentos de geleiras e rios. Isso pode ser obtido coletando medidas arduamente com o GPS, tais como GPS com correção diferencial.

As informações de localização podem pode ser destacadas da maneira indicada a seguir, separando informações de região, local e cidade. Observe que também é possível chamar um ponto de extremidade REST tal como a API do Bing Mapas, disponível em `https://msdn.microsoft.com/library/ff701710.aspx` para obter as informações de região/distrito.

	select 
		<location_columnname>
		,round(<location_columnname>,0) as l1		
		,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end 	
		,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end 	
		,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end 	
		,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end 	
		,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end 	
		,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end 	
	from <tablename>

Os recursos de localização acima podem ser usados ainda para gerar recursos adicionais de contagem, como descrito anteriormente.


**DICA**

> 1. É possível inserir os registros com programação usando a linguagem de sua escolha. Talvez seja necessário inserir os dados em partes para melhorar a eficiência de gravação [Confira o exemplo de como fazer isso usando pyodbc aqui](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python). 
> 2. Outra alternativa é inserir dados no banco de dados usando o [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)

####<a name="sql-aml"></a>Conectar ao Aprendizado de Máquina do Azure

O recurso recém-gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e unido com a tabela original para o aprendizado de máquina. Recursos podem ser gerados ou acessados se já foram criados, usando o módulo [Leitor][reader] no AM do Azure conforme mostrado abaixo:

![leitores de azureml][1]

###<a name="python"></a>Usando uma linguagem de programação como Python

Usar o Python para explorar dados e gerar recursos quando os dados estão no SQL Server é semelhante ao processamento de dados no blob do Azure usando o Python conforme documentado [aqui](machine-learning-data-science-process-data-blob.md). Os dados precisam ser carregados do banco de dados para um quadro de dados pandas, quando então poderão ser processados. Documentamos o processo de conectar-se ao banco de dados e carregar os dados em um quadro de dados nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para se conectar a um banco de dados do SQL Server do Python usando pyodbc (substitua servername, dbname, nome de usuário e senha pelos seus valores específicos):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Biblioteca Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação Python. O código a seguir lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, você pode trabalhar com o quadro de dados Pandas conforme abordado nos tópicos [Processar dados de Blobs do Azure em seu ambiente de ciência de dados](machine-learning-data-science-process-data-blob.md).

### Exemplo da Ciência de Dados do Azure em Ação

Para obter um exemplo passo a passo e ponta a ponta do Processo de Ciência de Dados do Azure usando um conjunto de dados público, consulte [Processo de Ciência de Dados do Azure em Ação](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO3-->
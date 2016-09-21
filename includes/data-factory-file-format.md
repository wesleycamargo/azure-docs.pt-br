## Especificando formatos

### Especificando TextFormat

Se o formato é definido como **TextFormat**você pode especificar as seguintes propriedades **opcionais** na seção **Formato**.

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | O caractere usado para separar as colunas em um arquivo. | Somente um caractere é permitido. O valor padrão é vírgula (,). | Não |
| rowDelimiter | O caractere usado para separar as linhas em um arquivo. | Somente um caractere é permitido. O valor padrão é "\\r\\n" na gravação e qualquer um dos seguintes para leitura: ["\\r\\n", "\\r"," \\n"]. | Não |
| escapeChar | O caractere especial usado como escape do delimitador de coluna no conteúdo do arquivo de entrada. <br/><br/>Não é possível especificar ambos escapeChar e quoteChar para uma tabela. | Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se tiver a vírgula (,) como o delimitador de coluna, mas quiser ter o caractere de vírgula no texto (exemplo: "Hello, world"), você poderá definir '$' como o caractere de escape e usar a cadeia de caracteres "Hello$, world" na fonte. | Não | 
| quoteChar | O caractere usado para citar um valor de cadeia de caracteres. Os delimitadores de linha e coluna dentro dos caracteres de aspas seriam tratados como parte do valor de cadeia de caracteres. Essa propriedade é aplicável a ambos os conjuntos de dados de entrada e de saída.<br/><br/>Observe que não é possível especificar escapeChar e quoteChar para uma tabela. | Somente um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se tiver a vírgula (,) como o delimitador de coluna, mas quiser ter o caractere de vírgula no texto (exemplo: <Hello, world>), você poderá definir " (aspas duplas) como o caractere de citação e usar a cadeia de caracteres "Hello, world" na origem. | Não |
| nullValue | Um ou mais caracteres usados para representar um valor nulo. | Um ou mais caracteres. Os valores padrão são "\\N" e "NULL" na leitura e "\\N" na gravação. | Não |
| encodingName | Especifique o nome de codificação. | Um nomes de codificação válido. Consulte [Propriedade Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor padrão é UTF-8. | Não | 
| firstRowAsHeader | Especifica se a primeira linha será considerada como cabeçalho. Para um conjunto de dados de entrada, o Data Factory lê a primeira linha como cabeçalho. Para um conjunto de dados de saída, o Data Factory lê a primeira linha como cabeçalho. <br/><br/>Consulte [Cenários de uso de **firstRowAsHeader** e **skipLineCount**](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | True<br/>False (padrão) | Não |
| skipLineCount | Indica o número de linhas a serem ignoradas ao ler dados de arquivos de entrada. Se skipLineCount e firstRowAsHeader forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada. <br/><br/>Consulte [Cenários de uso de firstRowAsHeader e skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | Número inteiro | Não | 
| treatEmptyAsNull | Especifica se uma cadeia de caracteres nula ou vazia será ou não tratada como um valor nulo ao ler dados de um arquivo de entrada. | True (padrão)<br/>False | Não |  

#### Exemplo de TextFormat
O exemplo a seguir mostra algumas das propriedades de formato para TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
			"firstRowAsHeader": true,
			"skipLineCount": 0,
			"treatEmptyAsNull": true
	    }
	},

Para usar um escapeChar em vez de quoteChar, substitua a linha com quoteChar pelo seguinte:

	"escapeChar": "$",



### Cenários de uso de firstRowAsHeader e skipLineCount

- Você está copiando de uma fonte que não é de arquivo para um arquivo de texto e deseja adicionar uma linha de cabeçalho que contém os metadados de esquema (por exemplo: esquema SQL). Especifique **firstRowAsHeader** como true no conjunto de dados de saída para esse cenário.
- Você está copiando de um arquivo de texto contendo uma linha de cabeçalho para um coletor que não é em arquivo e gostaria de remover essa linha. Especifique **firstRowAsHeader** como true no conjunto de dados de entrada.
- Você está copiando de um arquivo de texto e deseja ignorar algumas linhas no início que não são de dados nem cabeçalho. Especifique **skipLineCount** para indicar o número de linhas a serem ignoradas. Se o restante do arquivo contiver uma linha de cabeçalho, você também poderá especificar **firstRowAsHeader**. Se **skipLineCount** e **firstRowAsHeader** forem especificados, as linhas serão ignoradas pela primeira vez e, em seguida, as informações de cabeçalho serão lidas do arquivo de entrada

### Especificando AvroFormat
Se o formato é definido como AvroFormat, não é necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar o formato Avro em uma tabela de Hive, confira [Tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Especificando JsonFormat

Se o formato for definido como **JsonFormat**, será possível especificar as propriedades **opcionais** a seguir na seção **Formato**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| filePattern | Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é: **setOfObjects**. Consulte as seções a seguir para obter detalhes sobre esses padrões.| Não |
| encodingName | Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, confira: propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor **padrão** é **UTF-8**. | Não | 
| nestingSeparator | Caractere que é usado para separar os níveis de aninhamento. O valor padrão é '.' (ponto). | Não | 


#### Padrão de arquivo setOfObjects

Cada arquivo contém um único objeto ou vários objetos concatenados/delimitados por linhas. Quando essa opção é escolhida em um conjunto de dados de saída, a atividade de cópia produz um único arquivo JSON com cada objeto por linha (delimitados por linha).

**objeto único**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**JSON delimitado por linha**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenado**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### Padrão de arquivo arrayOfObjects. 

Cada arquivo contém uma matriz de objetos.

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### Exemplo de JsonFormat

Se você tiver um arquivo JSON com o seguinte conteúdo:

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

e quiser copiá-lo para uma tabela SQL do Azure no seguinte formato:

ID | Name.First | Name.Middle | Name.Last | Marcas
--- | ---------- | ----------- | --------- | ----
1 | John | nulo | Doe | ["Data Factory”, "Azure"]

O conjunto de dados de entrada com o tipo JsonFormat é definido da seguinte maneira: (definição parcial com apenas as partes relevantes)

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

Se a estrutura não for definida, a atividade de cópia mescla a estrutura por padrão e copia tudo.

#### Estrutura JSON com suporte
Observe o seguinte:

- Cada objeto com uma coleção de pares nome/valor é mapeado para uma linha de dados em um formato tabular. Objetos podem ser aninhados e você pode definir como mesclar a estrutura em um conjunto de dados com o separador de aninhamento (.) por padrão. Consulte a seção [exemplo de JsonFormat](#jsonformat-example) acima para obter um exemplo.
- Se a estrutura não for definida no conjunto de dados do Data Factory, a atividade de cópia detectará o esquema do primeiro objeto e mesclará todo o objeto.
- Se a entrada JSON tiver uma matriz, a atividade de cópia converterá o valor da matriz inteira em uma cadeia de caracteres. É possível optar por ignorá-la usando [o mapeamento ou a filtragem de coluna](#column-mapping-with-translator-rules).
- Se houver nomes duplicados no mesmo nível, a atividade de cópia selecionará o último entre eles.
- Os nomes de propriedade diferenciam maiúsculas de minúsculas. Duas propriedades com o mesmo nome, mas com maiúsculas e minúsculas diferentes são tratadas como duas propriedades separadas.

### Especificando OrcFormat
Se o formato for definido como OrcFormat, não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

	"format":
	{
	    "type": "OrcFormat",
	}

> [AZURE.IMPORTANT] Se você não estiver copiando arquivos ORC **como são** entre repositórios de dados locais e na nuvem, você precisará instalar o JRE 8 (Java Runtime Environment) no computador do gateway. Um gateway de 64 bits exige JRE de 64 bits, enquanto um gateway de 32 bits exige JRE de 32 bits. Você pode encontrar as duas versões [aqui](http://go.microsoft.com/fwlink/?LinkId=808605). Escolha aquela que for apropriada.

Observe o seguinte:

-	Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST e UNION)
-	O arquivo ORC tem três [opções de compactação](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB e SNAPPY. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo ORC, o Data Factory escolhe ZLIB, que é o padrão para ORC. Não há nenhuma opção para substituir esse comportamento neste momento.

<!---HONumber=AcomDC_0907_2016-->
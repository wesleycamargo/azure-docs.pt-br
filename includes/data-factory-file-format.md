### Especificando TextFormat

Se o formato é definido como **TextFormat**você pode especificar as seguintes propriedades **opcionais** na seção **Formato**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| columnDelimiter | O caractere usado como um separador de coluna em um arquivo. Somente um caractere é permitido nesse momento. Essa marca é opcional. O valor padrão é vírgula (,). | Não |
| rowDelimiter | O caractere usado como um separador bruto no arquivo. Somente um caractere é permitido nesse momento. Essa marca é opcional. O valor padrão é qualquer um dos seguintes: ["\\r\\n", "\\r"," \\n"]. | Não |
| escapeChar | O caractere especial usado como escape do delimitador de coluna mostrado no conteúdo. Essa marca é opcional. Nenhum valor padrão. É necessário especificar, no máximo, um caractere para essa propriedade.<br/><br/>Por exemplo, se você tiver uma vírgula (,) como o delimitador de coluna, mas desejar ter o caractere de vírgula no texto (exemplo: “Hello, world”), poderá definir “$” como o caractere de escape e usar a cadeia de caracteres “Hello$, world” na origem.<br/><br/>Observe que não é possível especificar escapeChar e quoteChar para uma tabela. | Não | 
| quoteChar | O caractere especial é usado como o caractere no qual colocar o valor de cadeia de caracteres. Os delimitadores de linha e coluna dos caracteres de aspas seriam tratados como parte do valor de cadeia de caracteres. Essa marca é opcional. Nenhum valor padrão. Você deve especificar não mais de um caractere para essa propriedade.<br/><br/>Por exemplo, se você tiver a vírgula (,) como o delimitador de coluna, mas deseja ter caractere de vírgula no texto (exemplo: <Hello  world>), você pode definir ‘"’ como o caractere de citação e usar a cadeia de caracteres <"Hello, world"> na fonte. Essa propriedade é aplicável às tabelas de entrada e de saída.<br/><br/>Observe que não é possível especificar escapeChar e quoteChar para uma tabela. | Não |
| nullValue | Os caracteres usados para representar um valor nulo no conteúdo do arquivo de blob. Essa marca é opcional. O valor padrão é “\\N”.<br/><br/>Por exemplo, com base na amostra acima, “NaN” no blob será convertido em valor nulo quando copiado para o SQL Server, por exemplo. | Não |
| encodingName | Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, confira: [Propriedade Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor padrão é UTF-8. | Não | 

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
	    }
	},

Para usar um escapeChar em vez de quoteChar, substitua a linha com quoteChar pelo seguinte:

	"escapeChar": "$",

### Especificando AvroFormat
Se o formato é definido como AvroFormat, não é necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar o formato Avro em uma tabela de Hive, consulte [Tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Especificando JsonFormat

Se o formato é definido como **JsonFormat**, é possível especificar as seguintes propriedades **opcionais** na seção **Formato**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| filePattern | Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é: **setOfObjects**. Consulte as seções abaixo para obter detalhes sobre esses padrões.| Não |
| encodingName | Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, confira: [Propriedade Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor **padrão** é: **UTF-8**. | Não | 
| nestingSeparator | Caractere que é usado para separar os níveis de aninhamento. O valor **padrão** é **. (ponto)**. | Não | 


#### Padrão de arquivo setOfObjects

Cada arquivo contém um único objeto ou vários objetos concatenados/delimitados por linhas. Quando essa opção é escolhida em um conjunto de dados de saída, a cópia produzirá um único arquivo JSON com cada objeto por linha (delimitados por linha).

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

- Cada objeto com uma coleção de pares nome/valor será mapeado para uma linha de dados em um formato tabular. Objetos podem ser aninhados e você pode definir como mesclar a estrutura em um conjunto de dados com o separador de aninhamento (.) por padrão. Veja a seção [exemplo de JsonFormat exemplo](#jsonformat-example) acima para obter um exemplo.  
- Se a estrutura não for definida no conjunto de dados do Data Factory, a atividade de cópia detectará o esquema do primeiro objeto e mesclará todo o objeto. 
- Se a entrada JSON tiver uma matriz, a atividade de cópia converterá o valor da matriz inteira em uma cadeia de caracteres. É possível optar por ignorá-la usando [o mapeamento ou a filtragem de coluna](#column-mapping-with-translator-rules).
- Se houver um nome duplicado no mesmo nível, a atividade de cópia selecionará o último elemento.
- Os nomes de propriedade diferenciam maiúsculas de minúsculas. Duas propriedades com o mesmo nome, mas com maiúsculas e minúsculas diferentes serão tratadas como duas propriedades separadas. 

### Especificando OrcFormat
Se o formato for definido como OrcFormat, não será necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

	"format":
	{
	    "type": "OrcFormat",
	}

Observe o seguinte:
 
-	Se você estiver copiando dados entre repositórios de dados locais e na nuvem com o formato ORC e não copiar arquivos ORC no estado em que se encontram da origem para o coletor, será necessário instalar o JRE (Java Runtime Environment) no computador do gateway. 
-	Não há suporte para tipos de dados complexos (STRUCT, MAP, LIST e UNION)
-	O arquivo ORC tem três [opções de compactação](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB e SNAPPY. O Data Factory dá suporte à leitura de dados de arquivo ORC em qualquer um dos formatos compactados acima. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo ORC, o Data Factory escolhe o ZLIB padrão para ORC. Não há nenhuma opção para substituir esse comportamento neste momento. 

<!---HONumber=AcomDC_0525_2016-->
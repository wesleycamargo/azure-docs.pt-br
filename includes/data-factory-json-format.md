### Especificando JsonFormat

Se o formato é definido como **JsonFormat**, você pode especificar as seguintes propriedades **opcionais** na seção **Formato**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| filePattern | Indique o padrão de dados armazenados em cada arquivo JSON. Valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é: **setOfObjects**. Consulte as seções abaixo para obter detalhes sobre esses padrões.| Não |
| encodingName | Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, consulte: Propriedade [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor **padrão** é **UTF-8**. | Não | 
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

- Cada objeto com uma coleção de pares nome/valor será mapeado para uma linha de dados em um formato tabular. Objetos podem ser aninhados e você pode definir como mesclar a estrutura em um conjunto de dados com o separador de aninhamento (.) por padrão. Consulte a seção [exemplo JsonFormat exemplo](#jsonformat-example) acima para ver um exemplo.  
- Se a estrutura não for definida no conjunto de dados do Data Factory, a atividade de cópia detectará o esquema do primeiro objeto e mesclará todo o objeto. 
- Se a entrada JSON tiver uma matriz, a atividade de cópia converterá o valor da matriz inteira em uma cadeia de caracteres. Você pode optar por ignorá-la usando o [mapeamento ou filtragem de coluna](#column-mapping-with-translator-rules).
- Se houver um nome duplicado no mesmo nível, a atividade de cópia selecionará o último elemento.
- Os nomes de propriedade diferenciam maiúsculas de minúsculas. Duas propriedades com o mesmo nome, mas com maiúsculas e minúsculas diferentes serão tratadas como duas propriedades separadas. 

<!---HONumber=AcomDC_0316_2016-->
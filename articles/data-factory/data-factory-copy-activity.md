<properties 
	pageTitle="Copiar dados com o Data Factory do Azure" 
	description="Saiba como usar a atividade de cópia no Azure Data Factory para copiar dados de uma fonte de dados para outra." 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Copiar dados com o Azure Data Factory (atividade de cópia)
## Visão geral
Você pode usar a **Atividade de cópia** em uma pipeline para copiar dados de uma fonte para um coletor (destino) em um lote. A atividade de cópia pode ser utilizada nos seguintes cenários:

- **Ingresso para o Azure**. Nesse cenário, os dados são copiados de uma fonte de dados local (ex: SQL Server) para um armazenamento de dados do Azure (ex: blob, tabela ou Banco de Dados SQL do Azure) para os seguintes subcenários:
	- Coletar dados em um local centralizado no Azure para processamento adicional.
	- Migrar dados de plataformas de nuvem não pertencentes ao Azure ou locais para o Azure.
	- Arquivar ou fazer backup de dados do Azure para armazenamento econômico em camadas.
- **Saída do Azure**. Nesse cenário, os dados são copiados do Azure (ex: blob, tabela ou Banco de Dados SQL do Azure) para armazéns de dados locais e data warehouse (ex: SQL Server) para os seguintes subcenários:
	- Transferir dados para o local devido à falta de suporte para o data warehouse de nuvem.
	- Transferir dados para o local para aproveitar a solução local existente ou a infraestrutura de relatório.
	- Arquivar ou fazer backup de dados para os locais de armazenamento em camadas
- **Cópia do Azure para o Azure**. Nesse cenário, os dados distribuídos por meio de fontes de dados do Azure são agregados em um repositório centralizado de dados do Azure. Exemplos: tabela do Azure para blobs do Azure, blobs do Azure para tabela do Azure, tabela do Azure para SQL Azure, blob do Azure para SQL Azure.

Para saber mais, você pode:

- Consulte o vídeo [Apresentando a atividade de cópia do Azure Data Factory][copy-activity-video]
- Consulte, em [Introdução ao data factory do Azure][adfgetstarted], um tutorial que mostra como copiar dados de um armazenamento de blob do Azure para um Banco de Dados SQL do Azure usando a atividade de cópia. 
- Consulte [Habilitar pipelines para trabalhar com dados locais][use-onpremises-datasources] para uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blob do Azure usando a atividade de cópia.


## Com suporte de origens e coletores
A atividade de cópia dá suporte aos cenários da movimentação de dados a seguir:

- Copie os dados de um blob do Azure para um blob do Azure, tabela do Azure, Banco de Dados SQL do Azure, SQL Server local ou SQL Server no IaaS.
- Copie os dados de um Banco de Dados SQL do Azure para um blob do Azure, tabela do Azure, banco de dados do SQL do Azure, SQL Server local, SQL Server no IaaS
- Copie os dados de uma tabela do Azure para um blob do Azure, tabela do Azure ou Banco de Dados SQL do Azure.
- Copie os dados de um SQL Server local/SQL Server no IaaS para o blob do Azure ou o Banco de Dados SQL do Azure
- Copie os dados de um banco de dados Oracle local para um blob do Azure
- Copie os dados de um sistema de arquivos local para um blob do Azure
 

<table border="1">	
	<tr>
		<th><i>Fonte/coletor<i></th>
		<th>Blob do Azure</th>
		<th>Tabela do Azure</th>
		<th>Banco de Dados SQL Azure</th>
		<th>SQL Server local</th>
		<th>SQL Server na IaaS</th>
		<th>Banco de Dados de Documentos do Azure</th>
	</tr>	

	<tr>
		<td><b>Blob do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Tabela do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
		<td>X</td>
	</tr>	

	<tr>
		<td><b>Banco de Dados SQL do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>


	<tr>
		<td><b>SQL Server local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server no IaaS</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Sistema de arquivos local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados Oracle local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Sistema de arquivos local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados MySQL local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados DB2 local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados Teradata local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados Sybase local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de dados PostgreSQL local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Banco de Dados de Documentos do Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>

Consulte o tópico [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx) na biblioteca MSDN para obter mais detalhes.

### SQL na IaaS(Infraestrutura como um serviço)
Também há suporte para o SQL Server no IaaS como fonte e coletor. O Gateway de Gerenciamento de Dados é necessário na criação de um serviço vinculado ao SQL Server no IaaS. Você deve considerar a instalação do Gateway de Gerenciamento de Dados em uma máquina virtual diferente de um SQL Server de hospedagem para evitar a degradação do desempenho devido à concorrência entre o SQL Server e o gateway pelos recursos. Para obter detalhes sobre o gateway de gerenciamento de dados, consulte [Habilitar pipelines para acessar dados locais][use-onpremises-datasources].

1.	Máquina virtual com o nome DNS público e porta estática pública: mapeamento de porta privada
2.	Máquina virtual com o nome DNS público sem ponto de extremidade SQL exposto
3.	Rede virtual
	<ol type='a'>
<li>VPN de nuvem do Azure com topologia a seguir no final da lista. </li>	
<li>Máquina virtual com VPN de site a site local para nuvem utilizando a Rede virtual do Azure.</li>	
</ol>![Data Factory com a atividade de cópia][image-data-factory-copy-actvity]

## Atividade de cópia - componentes
A atividade de cópia contém os seguintes componentes:

- **Tabela de entrada**. Uma tabela é um conjunto de dados retangular que tem um esquema. O componente da tabela de entrada descreve os dados de entrada para a atividade que incluem o seguinte: nome da tabela, tipo de tabela e serviço vinculado que faz referência a uma fonte de dados que contém os dados de entrada.
- **Tabela de saída**. A tabela de saída descreve dados de saída para a atividade que incluem o seguinte: nome da tabela, tipo de tabela e o serviço vinculado que faz referência a uma fonte de dados que contém os dados de saída.
- **Regras de transformação**. As regras de transformação especificam como os dados de entrada são extraídos da fonte e como os dados de saída são carregados no coletor etc...
 
Uma atividade de cópia pode ter uma **tabela de entrada** e uma **tabela de saída**.

## <a name="CopyActivityJSONSchema"></a>JSON para atividade de cópia
Uma pipeline consiste em uma ou mais atividades. As atividades nos pipelines são definidas na seção de **atividades**. O JSON para uma pipeline é o seguinte:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Cada atividade na seção de **atividades** tem a seguinte estrutura de nível superior. A propriedade de **tipo** deve ser configurada como **CopyActivity**. A Atividade de Cópia pode ter apenas uma tabela de entrada e uma de saída.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

A tabela a seguir descreve as marcas utilizadas com uma seção de atividade.

<table border="1">	
	<tr>
		<th align="left">Marca</th>
		<th align="left">Descrição</th>
		<th align="left">Obrigatório</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Nome da atividade.</td>
		<td>S</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Texto que descreve qual a utilidade da atividade.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Especifica o tipo da atividade. <br/><br/>O <b>tipo</b> deve ser configurado como <b>CopyActivity</b>.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tabelas de entrada usadas pela atividade. Especifique uma única tabela de entrada para a Atividade de Cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tabelas de saída usadas pela atividade. Especifique uma única tabela de saída para a Atividade de Cópia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Propriedades da transformação depende do tipo. A <b>atividade cópia</b> exige que você especifique uma seção de <b>fonte</b> e <b>coletor</b> na seção de <b>transformação</b>. Mais detalhes serão fornecidos posteriormente neste artigo. </td>
		<td>S</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Diretivas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, os valores padrão serão utilizados.</td>
		<td>N</td>
	</tr>


</table>

Consulte [Referência de Script JSON][json-script-reference] para obter informações detalhadas sobre propriedades/marcas JSON.

### tipos de fonte e coletor
Para obter uma lista de tipos de origem e coletor e as propriedades desses tipos com suporte, consulte o tópico [Suporte para origens e coletores][msdn-supported-sources-sinks] na biblioteca MSDN.

## Atividade de cópia - exemplo
Neste exemplo, uma tabela de entrada e uma tabela de saída são definidas e as tabelas são utilizadas em uma Atividade de cópia em uma pipeline que copia dados de um banco de dados SQL Server local para um blob do Azure.

**Suposições** Os seguintes artefatos do Azure Data Factory são referenciados em exemplos de scripts JSON que se segue:

* Grupo de recursos denominado **ADF**.
* Um data factory do Azure denominado **CopyFactory**.
* Um serviço vinculado denominado **MyOnPremisesSQLDB** que aponta para um banco de dados de SQL Server local.
* Um serviço vinculado denominado **MyAzureStorage** que aponta para um armazenamento de blob do Azure.

### Tabela de entrada JSON
O script JSON a seguir define uma tabela de entrada que se refere a uma tabela SQL: **MyTable** em um banco de dados de SQL Server local que o serviço vinculado **MyOnPremisesSQLDB** define. Observe que **name** é o nome da tabela do Azure Data Factory e **tableName** é o nome da tabela SQL em um banco de dados de SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

O seguinte exemplo de comando do PowerShell do Azure usa a **New-AzureDataFactoryTable** que usa um arquivo JSON que contém o script acima para criar uma tabela (**MyOnPremTable**) em uma data factory do Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Consulte [Referência do cmdlet][cmdlet-reference] para obter detalhes sobre os cmdlets do Data Factory.

### Tabela de saída JSON
O script JSON a seguir define uma tabela de saída: **MyDemoBlob**, que se refere a um blob do Azure: **MyBlob** na pasta blob: **MySubFolder** no contêiner de blob: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

O seguinte exemplo de comando do PowerShell do Azure usa a **New-AzureDataFactoryTable** que usa um arquivo JSON que contém o script acima para criar uma tabela (**MyDemoBlob**) em uma data factory do Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### Pipeline (com a Atividade de cópia) JSON
Neste exemplo, um pipeline: **CopyActivityPipeline** está definido com as seguintes propriedades:

- A propriedade **tipo** é definida como **CopyActivity**.
- **MyOnPremTable** é especificado como a entrada (marca de **entradas**).
- **MyAzureBlob** é especificado como a saída (marca de **saídas**)
- A seção **Transformação** contém duas subseções: **fonte** e **coletor**. O tipo de fonte é configurado como **SqlSource** e o tipo de coletor como **BlobSink**. O **sqlReaderQuery** define a transformação (projeção) a ser realizada na fonte. Para obter detalhes sobre todas as propriedades, consulte a [Referência de script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 O seguinte exemplo de comando do PowerShell do Azure usa a **New-AzureDataFactoryPipeline** que usa um arquivo JSON que contém o script acima para criar um pipeline (**CopyActivityPipeline**) em um data factory do Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]Consulte [Exemplos de uso de atividade de cópia no Azure Data Factory][copy-activity-examples] para obter mais exemplos de uso da atividade de cópia.

## Segurança
Esta seção inclui diretrizes gerais de segurança e práticas recomendadas que ajudam a estabelecer o acesso seguro aos armazenamentos de dados para a atividade de cópia.

Para armazenamentos de dados oferecer conexão HTTPS, escolha a conexão HTTPS para a atividade de cópia estabelecer uma comunicação segura na rede. Por exemplo, para **o armazenamento do Azure**, use **DefaultEndpointsProtocol = https** que está na cadeia de conexão.

Para o **Banco de Dados SQL do Azure**, solicite explicitamente uma conexão criptografada e não confie em certificados de servidor para evitar o ataque de "homem no meio". Para fazer isso, use **Encrypt = True** e **TrustServerCertificate = False** na cadeia de conexão. Para obter detalhes, consulte [Diretrizes e limitações do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394108.aspx).

Para os bancos de dados tradicionais como **SQL Server**, especialmente quando as instâncias estiverem em uma máquina virtual do Azure e habilite a opção de conexão criptografada ao configurar um certificado assinado, com **Encrypt = True** e **TrustServerCertificate = False** na cadeia de conexão. Para saber mais , consulte [Habilitar conexões criptografadas para o mecanismo de banco de dados](https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx) e [Sintaxe de cadeia de conexão](https://msdn.microsoft.com/library/ms254500.aspx).

## Cenários avançados
- **Filtragem de coluna usando a definição de estrutura**. Dependendo do tipo de tabela, é possível especificar um subconjunto das colunas da fonte especificando menos colunas na definição de **estrutura** da definição de tabela que existe na fonte de dados subjacente.
- **Regras de transformação - mapeamento de coluna**. O mapeamento de coluna pode ser utilizado para especificar como colunas na tabela de fonte são mapeados para colunas na tabela de coletor.
- **Manipulação de tipo de dados pela atividade de cópia**. Explica em qual caso os tipos de dados especificados na seção de estrutura da definição de tabela são respeitados/ignorados.
- **Invocar o procedimento armazenado para o coletor SQL**. Ao copiar dados no SQL Server ou no Banco de Dados SQL do Azure, um procedimento armazenado do usuário especificado poderá ser configurado e invocado.

Consulte o artigo [Cenários avançados para usar a atividade de cópia com o Azure Data Factory][copy-activity-advanced] para obter detalhes sobre esses cenários.

## Instruções passo a passo
Consulte, em [Introdução ao data factory do Azure][adfgetstarted], um tutorial que mostra como copiar dados de um armazenamento de blob do Azure para um Banco de Dados SQL do Azure usando a atividade de cópia.
 
Consulte [Habilitar pipelines para trabalhar com dados locais][use-onpremises-datasources] para uma explicação passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um armazenamento de blob do Azure usando a atividade de cópia

## Consulte também
- [Atividade de cópia - exemplos][copy-activity-examples]
- [Vídeo: apresentando a atividade de cópia do Data Factory do Azure][copy-activity-video]
- [Tópico atividade de cópia na biblioteca MSDN][msdn-copy-activity]
- [Cenários avançados para usar a atividade de cópia com o Data Factory do Azure][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=62-->
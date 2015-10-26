<properties 
	pageTitle="Documentação da API de Recomendações do Aprendizado de Máquina | Microsoft Azure" 
	description="Documentação da API de Recomendações de Aprendizado de Máquina do Azure para um mecanismo de recomendações disponível no Microsoft Azure Marketplace." 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2015" 
	ms.author="LuisCa"/>

#Documentação da API de Recomendações do Aprendizado de Máquina do Azure

Este documento descreve as APIs de Recomendação do Aprendizado de Máquina do Microsoft Azure.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Visão geral
Este documento é uma referência para a API. Você deve começar pelo documento “Recomendação de Aprendizagem de Máquina do Azure – Início Rápido".

A API de Recomendações do Aprendizado de Máquina do Azure pode ser dividida em 10 grupos lógicos:

1.	<ins>Modelo Básico</ins> – APIs que permitem realizar operações básicas no modelo (por exemplo, criar, atualizar e excluir um modelo).
2.	<ins>Modelo Avançado</ins> – APIs que permitem aprofundar-se nos dados avançados no modelo
3.	<ins>Modelo de Regras de Negócio</ins> – APIs que permitem gerenciar regras comerciais dos resultados de recomendação do modelo.
4.	<ins>Catálogo</ins> – APIs que permitem executar operações básicas em um catálogo de modelo. Um catálogo contém informações de metadados sobre os itens dos dados de uso.
5.	<ins>Recurso</ins> - APIs que permitem obter informações no item presente no catálogo e como usar essas informações para criar recomendações melhores.
6.	<ins>Dados de uso</ins> – APIs que permitem executar operações básicas com os dados de uso do modelo. Os dados de uso no formulário básico são formados por linhas que incluem pares de &#60;usuárioId&#62;,&#60;itemId&#62;.
7.	<ins>Compilação</ins> – APIs que permitem disparar uma compilação de modelo e realizam operações básicas relacionadas a essa compilação. Você pode iniciar uma compilação de modelo uma vez que você tenha dados de uso valiosos.
8.	<ins>Recomendação</ins> – após a compilação de um modelo terminar, você pode consumir recomendações usando essas APIs.
9.	<ins>Dados de usuário</ins> - APIs que permitem que você busque informações sobre os dados de uso do usuário.
10.	<ins>Notificações</ins> – APIs que permitem receber notificações sobre problemas relacionados às suas operações de API. (Por exemplo, você está reportando dados de uso por meio da aquisição de dados e a maioria dos eventos de processamento está com falha. Uma notificação de erro será gerada.)

##2\. Tópicos avançados

###2\.1. Qualidade da recomendação

Criar um modelo de recomendação geralmente é suficiente para permitir que o sistema forneça recomendações. No entanto, a qualidade da recomendação varia de acordo com o uso processado e a abrangência do catálogo. Por exemplo se você tiver muitos itens sem interesse (sem uso significativo), o sistema terá dificuldade para fornecer uma recomendação para um item ou para usar um item como aquele recomendado. Para solucionar o problema de item sem interesse, o sistema permite o uso de metadados dos itens para aprimorar as recomendações. Esses metadados são conhecidos como recursos. Os recursos mais comuns são o autor de um livro ou um ator de um filme. Recursos são fornecidos pelo catálogo na forma de cadeias de caracteres de chave/valor. Para o formato completo do arquivo de catálogo, consulte a [seção de importação de catálogo](#81-import-catalog-data). A seção a seguir explica o uso de recursos para aperfeiçoar o modelo de recomendação.

###2\.2. Compilação de classificação

Recursos podem aperfeiçoar o modelo de recomendação, mas isso requer o uso de recursos significativos. Uma nova compilação foi apresentada para essa finalidade: uma compilação de classificação. Esta compilação classifica a utilidade dos recursos. Um recurso significativo é um recurso com uma pontuação de classificação 2 ou maior. Depois de se entender quais recursos são significativos, dispare uma compilação de recomendação com a lista (ou sublista) de recursos significativos. É possível usar esses recursos para o aprimoramento de itens com e sem interesse. Para usá-los em itens com interesse, o parâmetro de compilação `UseFeatureInModel` deve ser configurado. Para usá-los em itens sem interesse, o parâmetro de compilação `AllowColdItemPlacement` deve ser habilitado. Observação: não é possível habilitar `AllowColdItemPlacement` sem habilitar `UseFeatureInModel`.

###2\.3. Raciocínio de recomendação

O raciocínio de recomendação é outro aspecto do uso de recursos. De fato, o mecanismo de Recomendações do Aprendizado de Máquina do Azure pode usar recursos para fornecer explicações de recomendação (também conhecido como raciocínio), resultando em maior confiança no item recomendado por parte do consumidor da recomendação. Para habilitar o raciocínio, os parâmetros `AllowFeatureCorrelation` e `ReasoningFeatureList` devem ser configurado antes de solicitar uma compilação de recomendação.

##3\. Limitações

- O número máximo de modelos por assinatura é 10.
- O número máximo de itens que um catálogo pode conter é 100.000.
- O número máximo de pontos de uso mantidos é cerca de 5.000.000. Os mais antigos serão excluídos se novos forem carregados ou relatados.
- O volume máximo dos dados que podem ser enviado no POST (por exemplo, importar dados de catálogo e importar dados de uso) é de 200 MB.
- O número de transações por segundo para uma compilação de modelo de recomendação que não está ativa é cerca de 2 TPS. Uma compilação de modelo de recomendação ativo pode conter até 20 TPS.

##4\. APIs – Informações Gerais

###4\.1. Autenticação
Siga as diretrizes do Microsoft Azure Marketplace referentes à autenticação. O Marketplace dá suporte aos métodos de autenticação Básico e OAuth.

###4\.2. URI de serviço
O URI da raiz de serviço para as APIs de Recomendações do Aprendizado de Máquina do Azure está [aqui.](https://api.datamarket.azure.com/amla/recommendations/v3/)

O URI do serviço completo é expresso usando elementos da especificação de OData.

###4\.3. Versão da API
Cada chamada à API terá, por fim, um parâmetro de consulta chamado apiVersion que deve ser definido como 1.0.

###4\.4. IDs diferenciam minúsculas e maiúsculas
IDs, retornados por qualquer uma das APIS, diferenciam minúsculas de maiúsculas e devem ser usados desta maneira quando passados como parâmetros nas chamadas de API subsequentes. Por exemplo, IDS d modelo e de catálogo diferenciam maiúsculas de minúsculas.

##5\. Modelo Básico

###5\.1. Criar modelo
Cria uma solicitação "criar modelo".

| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelName |	Somente letras (A-Z, a-z), números (0-9), hifens (-) e o sublinhado (\_) são permitidos.<br>Comprimento máximo: 20 | 
| apiVersion | 1.0 | 
||| 
| Corpo da Solicitação | NENHUM |


**Resposta**:

Código de status HTTP: 200

- `feed/entry/content/properties/id` – Contém a ID do modelo. **Observação**: a ID do modelo diferencia maiúsculas de minúsculas.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.2. Obter modelo
Criar uma solicitação “obter modelo".

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	ID |	O identificador exclusivo do modelo (diferencia maiúsculas e minúsculas) |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

Os dados do modelo podem ser encontrados sob os seguintes elementos:

- `feed/entry/content/properties/Id` – ID exclusivo do modelo.
- `feed/entry/content/properties/Name` – Nome do modelo.
- `feed/entry/content/properties/Date` – Data de criação do modelo.
- `feed/entry/content/properties/Status` – Status do modelo. Um dos seguintes:
    - Criado - O modelo é criado e não contém Catálogo e Uso.
	- ReadyForBuild – O modelo é criado e contém Catálogo e Uso.
- `feed/entry/content/properties/HasActiveBuild` – Indica se o modelo foi criado com êxito.
- `feed/entry/content/properties/BuildId` – ID da compilação ativa do modelo.
- `feed/entry/content/properties/Mpr` – Classificação porcentual média do modelo (MPR - consulte ModelInsight para obter mais informações).
- `feed/entry/content/properties/UserName` – Nome do usuário interno do modelo.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.3. Obter todos os modelos
Recupera todos os modelos do usuário atual.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

- `feed/entry/content/properties/Id` – ID exclusivo do modelo.
- `feed/entry/content/properties/Name` – Nome do modelo.
- `feed/entry/content/properties/Date` – Data de criação do modelo.
- `feed/entry/content/properties/Status` – Status do modelo. Um dos seguintes:
  - Criado - O modelo é criado e não contém Catálogo e Uso.
  - ReadyForBuild – O modelo é criado e contém Catálogo e Uso.
- `feed/entry/content/properties/HasActiveBuild` – Indica se o modelo foi criado com êxito.
- `feed/entry/content/properties/BuildId` – ID da compilação ativa do modelo.
- `feed/entry/content/properties/Mpr` – MPR do modelo MPR (consulte o ModelInsight para obter mais informações).
- `feed/entry/content/properties/UserName` – Nome do usuário interno do modelo.
- `feed/entry/content/properties/UsageFileNames` – Lista de arquivos de uso do modelo separados por vírgula.
- `feed/entry/content/properties/CatalogId` – ID do catálogo do modelo.
- `feed/entry/content/properties/Description` – Descrição do modelo.
- `feed/entry/content/properties/CatalogFileName` – Nome do arquivo do catálogo do modelo.

XML de OData


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5\.4. Atualizar modelo

Você pode atualizar a descrição do modelo ou a ID de compilação ativa.<br> <ins>ID de compilação ativa</ins> – Cada compilação de cada modelo possui uma ID da compilação. A ID de compilação ativa é a primeira compilação executada com êxito de cada novo modelo. Depois que tiver uma ID de compilação ativa e criar compilações adicionais para o mesmo modelo, você precisará defini-lo explicitamente como a ID de compilação padrão, se desejar. Ao consumir recomendações, se você não especificar a ID de compilação que deseja usar, o padrão será usado automaticamente.<br> Esse mecanismo permite, depois de ter um modelo de recomendação em produção, compilar e testar novos modelos antes de promovê-los para produção.


| Método HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	ID | O identificador exclusivo do modelo (diferencia maiúsculas e minúsculas) |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Observe que as marcas XML ActiveBuildId e Descrição são opcionais. Se você não quiser definir a Descrição ou ActiveBuildId, remova a marca inteira.|

**Resposta**:

Código de status HTTP: 200

###5\.5. Excluir modelo
Exclui um modelo existente por ID.

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	ID |	O identificador exclusivo do modelo (diferencia maiúsculas e minúsculas) |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. Modelo Avançado

###6\.1. Visão de modelo de dados
Retorna dados estatísticos sobre os dados com os quais este modelo foi criado.

Disponível somente para compilação de Recomendação.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

Os dados são retornados como uma coleção de propriedades.

- `feed/entry/id/content/properties/key` - Contém o nome da propriedade.
- `feed/entry/id/content/properties/value` - Contém o valor da propriedade.

A tabela a seguir descreve o valor que cada chave representa.

|Chave|Descrição|
|:-----|:----|
| AvgItemLength | Média de usuários distintos por item. |
| AvgUserLength | Média de itens distintos por usuários. |
| DensificationNumberOfItems | Número de itens após a remoção dos itens que não podem ser modelados. |
| DensificationNumberOfUsers | Número de pontos de uso após a remoção de usuários e itens que não podem ser modelados. |
| DensificationNumberOfRecords | Número de pontos de uso após a remoção de usuários e itens que não podem ser modelados. |
| MaxItemLength | Número de usuários distintos para o item mais popular. |
| MaxUserLength | Número máximo de itens distintos para um usuário. |
| MinItemLength | Número máximo de usuários distintos para um item. |
| MinUserLength | Número mínimo de itens distintos para um usuário. |
| RawNumberOfItems | Número de itens nos arquivos de uso. |
| RawNumberOfUsers | Número de pontos de uso antes de qualquer remoção. |
| RawNumberOfRecords | Número de pontos de uso antes de qualquer remoção. |
| SamplingNumberOfItems | N/D |
| SamplingNumberOfRecords | N/D |
| SamplingNumberOfUsers | N/D |

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. Percepção de modelo
Retorna informações de modelo na compilação ativa ou (se fornecido) em uma compilação específica.

Disponível somente para compilação de Recomendação.

| Método HTTP | URI |
|:--------|:--------|
|GET |Com a ID de compilação ativa:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Com a ID de compilação específica:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	buildId |	Opcional – número que identifica uma compilação bem-sucedida. |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

Os dados são retornados como uma coleção de propriedades.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


A tabela a seguir descreve o valor que cada chave representa.

| Chave | Descrição |
|:---- |:----|
| CatalogCoverage | Qual parte do catálogo pode ser modelado com padrões de uso. O restante dos itens precisará de recursos com base no conteúdo. |
| Mpr | Classificação percentual média do modelo. Menor é melhor. |
| NumberOfDimensions | Número de dimensões usado pelo algoritmo de fatoração de matriz. |


XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. Obter um exemplo de modelo
Obter um exemplo de modelo de recomendação.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Com a ID de compilação específica:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

XML de OData

A resposta é retornada no formato de texto sem formatação:

<pre>
Nível 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Classificação: 0,5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Classificação: 0,5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Classificação: 0,5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Classificação: 0,5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Classificação: 0,514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Classificação: 0,5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Classificação: 0,5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Classificação: 0,5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Classificação: 0,5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Classificação: 0,5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Classificação: 0,5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Classificação: 0,5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Classificação: 0,5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Classificação: 0,5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Classificação: 0,5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Classificação: 0,537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Classificação: 0,5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Classificação: 0,5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Classificação: 0,5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Classificação: 0,5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Classificação: 0,5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Classificação: 0,5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Classificação: 0,5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Classificação: 0,5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Classificação: 0,5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Classificação: 0,5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Classificação: 0,5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Classificação: 0,5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Classificação: 0,5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Classificação: 0,5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Classificação: 0,5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Classificação: 0,5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Classificação: 0,5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Classificação: 0,5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Classificação: 0,5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Classificação: 0,5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Classificação: 0,5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Classificação: 0,5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Classificação: 0,5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Classificação: 0,5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Classificação: 0,5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Classificação: 0,5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Classificação: 0,5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Classificação: 0,5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Classificação: 0,5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Classificação: 0,5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Classificação: 0,5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Classificação: 0,5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Classificação: 0,5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Classificação: 0,5367

Nível 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Classificação: 0,5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Classificação: 0,5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Classificação: 0,5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Classificação: 0,5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Classificação: 0,5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Classificação: 0,5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Classificação: 0,543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Classificação: 0,5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Classificação: 0,5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Classificação: 0,5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Classificação: 0,5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Classificação: 0,5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Classificação: 0,5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Classificação: 0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Classificação: 0,5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Classificação: 0,5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Classificação: 0,5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Classificação: 0,5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Classificação: 0,5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Classificação: 0,5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Classificação: 0,5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Classificação: 0,5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Classificação: 0,5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Classificação: 0,5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Classificação: 0,5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Classificação: 0,5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Classificação: 0,5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Classificação: 0,5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Classificação: 0,5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Classificação: 0,5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Classificação: 0,5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Classificação: 0,5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Classificação: 0,5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Classificação: 0,5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Classificação: 0,5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Classificação: 0,5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Classificação: 0,5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Classificação: 0,5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Classificação: 0,5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Classificação: 0,54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Classificação: 0,5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Classificação: 0,5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Classificação: 0,5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Classificação: 0,5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Classificação: 0,5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Classificação: 0,5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Classificação: 0,5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Classificação: 0,5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Classificação: 0,5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Classificação: 0,5367

</pre>

##7\. Regras de negócio do modelo
Há quatro tipos de regras: <strong>BlockList</strong> - BlockList permite fornecer uma lista de itens cujos resultados não devem ser retornados nos resultados de recomendação. <strong>Upsale</strong> - Upsale permite reforçar os itens a serem retornados nos resultados de recomendação. <strong>WhiteList</strong> - White List permite fornecer uma lista de itens na qual apenas os itens presentes nesta lista podem ser retornados como resultados de recomendação (o oposto de BlockList). <strong>PerSeedBlockList</strong> - Per Seed Block List permite fornecer por itens uma lista dos itens que não podem ser retornados como resultados de recomendação.


###7\.1. Obter regras de modelo

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Exemplo:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

- `feed/entry/content/properties/Id` – Identificador exclusivo desta regra.
- `feed/entry/content/properties/Type` – O tipo da regra.
- `feed/entry/content/properties/Parameter` – O parâmetro da regra.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. Adicionar regra

| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | <ins>Para adicionar a regra BlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>Para adicionar a regra Upsale:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>Para adicionar a regra WhiteList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>Para adicionar a regra PerSeedBlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**Resposta**:

Código de status HTTP: 200

A API retorna a regra recém-criada com seus detalhes. A propriedade de regras pode ser recuperada dos caminhos a seguir:

- `feed/entry/content/properties/Id` – Identificador exclusivo desta regra.
- `feed/entry/content/properties/Type` – O tipo de regra: BlockList ou Upsale.
- `feed/entry/content/properties/Parameter` – O parâmetro da regra.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. Excluir regra

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	filterId |	Identificador exclusivo do filtro |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

###7\.4. Excluir todas as regras

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

##8\. Catálogo

###8\.1. Importar Dados de Catálogo

Se você carregar vários arquivos de catálogo para o mesmo modelo com várias chamadas, inseriremos apenas os novos itens de catálogo. Os itens existentes permanecerão com os valores originais. Você não pode atualizar os dados do catálogo usando este método.

Os dados do catálogo devem seguir o seguinte formato:

- Sem recursos – `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Com recursos – `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Observação: o tamanho máximo do arquivo é de 200 MB.

** Detalhes de formato **

| Nome | Obrigatório | Tipo | Descrição |
|:---|:---|:---|:---|
| Id do item |Sim | [A-z], [a-z], [0-9] [\_]& #40; Sublinhado&#41;, [-] &#40;Traço&#41;<br>Comprimento máximo: 50 | Identificador exclusivo de um item. | 
| Nome do Item | Sim | Todos os caracteres alfanuméricos<br> Comprimento máximo: 255 | Nome do item. | 
| Categoria do Item | Sim | Todos os caracteres alfanuméricos <br> Comprimento máximo: 255 | Categoria à qual este item pertence (por exemplo, Livros de culinária, Drama...); pode estar vazio. | 
| Descrição | Não, a menos que existam recursos (mas pode estar vazio) | Todos os caracteres alfanuméricos <br> Comprimento máximo: 4000 | Descrição deste item. | 
| Lista de recursos | Não | Todos os caracteres alfanuméricos <br> Comprimento máximo: 4000 | Lista separada por vírgulas de nome do recurso=valor do recurso que pode ser usado para aprimorar a recomendação de modelo; consulte a seção [Tópicos avançados](#2-advanced-topics). |


| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
| nome do arquivo | Identificador textual do catálogo.<br>Somente letras (A-Z, a-z), números (0-9), hifens (-) e o sublinhado (\_) são permitidos.<br>Comprimento máximo: 50 | 
| apiVersion | 1.0 | 
||| 
| Corpo da Solicitação | Exemplo (com recursos):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Livro,descrição do livro,autor=Richard Wright,editora=Harper Flamingo Canada,ano=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Livro,,autor=Nick Bantock,editora=Harpercollins,ano=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Livro,,autor=Timothy Findley, editora=HarperFlamingo Canada, ano=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Bestas encurraladas,Livro,descrição do livro,autor=Magnus Mills, editora=Arcade Publishing, ano=1998</pre> |


**Resposta**:

Código de status HTTP: 200

A API retorna um relatório da importação. - `feed\entry\content\properties\LineCount` – Número de linhas aceitas. - `feed\entry\content\properties\ErrorCount` – Número de linhas que não foram inseridas devido a um erro.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. Obter catálogo
Recupera todos os itens de catálogo.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

A resposta inclui uma entrada por item de catálogo. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/ExternalId` – ID externa do item do catálogo, fornecida pelo cliente.
- `feed/entry/content/properties/InternalId` – ID interna do item do catálogo, gerada pelas Recomendações do Aprendizado de Máquina do Azure.
- `feed/entry/content/properties/Name` – Nome do item do catálogo.
- `feed/entry/content/properties/Category` – Categoria do item do catálogo.
- `feed/entry/content/properties/Description` – Descrição do item do catálogo.
- `feed/entry/content/properties/Metadata` – Metadados do item do catálogo.


XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.3. Obter itens de catálogo por Token

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	token |	Token do nome do item do catálogo. Deve conter pelo menos três caracteres. |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

A resposta inclui uma entrada por item de catálogo. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/ExternalId` – ID externa do item do catálogo, fornecida pelo cliente.
- `feed/entry/content/properties/InternalId` – ID interna do item do catálogo, gerada pelas Recomendações do Aprendizado de Máquina do Azure.
- `feed/entry/content/properties/Name` – Nome do item do catálogo.
- `feed/entry/content/properties/Category` – Categoria do item do catálogo.
- `feed/entry/content/properties/Description` – Descrição do item do catálogo.
- `feed/entry/content/properties/Metadata` – Metadados do item do catálogo.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9\. Dados de uso
###9\.1. Importar Dados de Uso
####9\.1.1. Carregamento de Arquivo
Esta seção mostra como carregar dados de uso usando um arquivo. Você pode chamar essa API várias vezes com dados de uso. Todos os dados de uso serão salvos para todas as chamadas.

| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
| nome do arquivo | Identificador textual do catálogo.<br>Há permissão apenas para letras (A-Z, a-z), números (0-9), hifens (-) e sublinhados (_).<br>Comprimento máximo: 50 | 
| apiVersion | 1.0 | 
||| 
| Corpo da solicitação | Dados de uso. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nome</th><th>Obrigatório</th><th>Tipo</th><th>Descrição</th></tr><tr><td>Id do usuário</td><td>Sim</td><td>[A-z], [a-z], [0-9], [_] &#40;Sublinhado&#41;, [-] &#40;Traço&#41;<br> Comprimento máximo: 255 </td><td>Identificador exclusivo de um usuário.</td></tr><tr><td>Id do Item</td><td>Sim</td><td>[A-z], [a-z], [0-9], [&#95;] &#40;Sublinhado&#41;, [-] &#40;Traço&#41;<br> Comprimento máximo: 50</td><td>Identificador exclusivo de um item.</td></tr><tr><td>Hora</td><td>Não</td><td>Data no formato: AAAA/MM/DDTHH:MM:SS (por exemplo 2013/06/20T10:00:00)</td><td>Hora dos dados.</td></tr><tr><td>Evento</td><td>Não; caso seja fornecido também precisará da data</td><td>Um dos seguintes:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Tamanho máximo do arquivo: 200MB<br><br>Exemplo:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Resposta**:

Código de status HTTP: 200

- `Feed\entry\content\properties\LineCount` – O número de linhas aceitas.
- `Feed\entry\content\properties\ErrorCount` – O número de linhas que não foram inseridas devido a um erro.
- `Feed\entry\content\properties\FileId` – Identificador do arquivo.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. Usar a Aquisição de Dados
Esta seção mostra como enviar eventos em tempo real para as Recomendações do Aprendizado de Máquina do Azure, geralmente do seu site.

| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|Corpo da solicitação| Entrada de dados de evento para cada evento que você deseja enviar. Você deve enviar a mesma ID no campo SessionId para a mesma sessão de usuário ou navegador. (Consulte o exemplo de corpo de evento abaixo.)|


- Exemplo de evento “Click”:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- Exemplo de evento “RecommendationClick”:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemplo de evento “AddShopCart”:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemplo de evento “RemoveShopCart”:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RemoveShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Exemplo de evento “Purchase”: <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId> <SessionId>11112222</SessionId> <EventData> <EventData> <Name>Purchase</Name> <PurchaseItems> <PurchaseItems> <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId> <Count>3</Count> </PurchaseItems> </PurchaseItems> </EventData> </EventData> </Event>

- Exemplo de envio de dois eventos “Click” e “AddShopCart”:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**Response**: código de status HTTP: 200

###9\.2. Lista dos arquivos de modelo de uso
Recupera os metadados de todos os arquivos de uso do modelo.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	forModelId |	Identificador exclusivo do modelo |
|	apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

A resposta inclui uma entrada por arquivo de uso. Cada entrada tem os seguintes dados:

- `feed\entry\content\properties\Id` – ID do arquivo de uso.
- `feed\entry\content\properties\Length` – Comprimento de arquivo de uso em MB.
- `feed\entry\content\properties\DateModified` – Data da criação do arquivo de uso.
- `feed\entry\content\properties\UseInModel` – Se o arquivo de uso foi usado no modelo ou não.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9\.3. Obter estatísticas de uso
Obter estatísticas de uso

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| startDate |	Data de início. Formato: aaaa/MM/ddTHH:mm:ss |
| endDate |	Data de término. Formato: aaaa/MM/ddTHH:mm:ss |
| eventTypes |	Cadeia de caracteres separada por vírgulas de tipos de eventos ou nulo para obter todos os eventos. |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

Uma coleção de elementos de chave/valor. Cada um deles contém a soma dos eventos para um tipo de evento específico agrupados por hora.

- `feed\entry[i]\content\properties\Key` – Contém a hora (agrupados por hora) e o tipo de evento.
- `feed\entry[i]\content\properties\Value` – Contagem de eventos total.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. Obter um exemplo de arquivo de uso
Recupera os primeiros 2 KB de conteúdo de arquivos de uso.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| fileId |	Identificador exclusivo do arquivo de uso do modelo. |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

A resposta é retornada em formato de texto simples: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 </pre>


###9\.5. Obtenha o modelo do arquivo de uso
Recupera o conteúdo completo do arquivo de uso.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| mid |	Identificador exclusivo do modelo |
| fid |	Identificador exclusivo do arquivo de uso do modelo. |
| baixar | 1 |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

A resposta é retornada em formato de texto sem formatação: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 </pre>

###9\.6. Excluir o arquivo de uso
Exclui o arquivo de uso do modelo especificado.

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| fileId | Identificador exclusivo do arquivo a ser excluído |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200


###9\.7. Excluir todos os arquivos de uso
Exclui todos os arquivos de uso do modelo.

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

##10\. Recursos
Esta seção mostra como recuperar informações de recurso, como os recursos importados e seus valores, sua classificação e quando essa classificação foi alocada. Os recursos são importados como parte dos dados do catálogo e, em seguida, sua posição é associada quando uma compilação de classificação é criada. A classificação de recursos pode mudar de acordo com o padrão dos dados de uso e tipo de itens. Mas, para uso/itens consistentes, a classificação deve ter apenas pequenas flutuações. A classificação de recursos é um número não negativo. O número 0 significa que o recurso não foi classificado (acontece se você invocar essa API antes da conclusão da primeira compilação de classificação). A data em que a classificação foi atribuída é chamada de atualização da pontuação.

###10\.1. Obter informações de recursos (para a última compilação de classificação)
Recupera as informações de recurso, incluindo classificação, para a última compilação de classificação bem-sucedida.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
|samplingSize| Número de valores a serem incluídos para cada recurso de acordo com os dados presentes no catálogo. <br/>Os valores possíveis são:<br> - 1 - Todas as amostras. <br>0 - Sem amostragem. <br>N - Retornar N amostras para cada nome de recurso.|
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |


**Resposta**:

Código de status HTTP: 200

A resposta contém uma lista de entradas de informações de recurso. Cada entrada contém:

- `feed/entry/content/m:properties/d:Name` - Nome do recurso.
- `feed/entry/content/m:properties/d:RankUpdateDate` - Data em que a classificação foi alocada para esse recurso, conhecido como recurso de atualização de pontuação. Uma data do histórico (“0001-01-01T00:00:00”) significa que nenhuma compilação de classificação foi executada.
- `feed/entry/content/m:properties/d:Rank` - Classificação de recurso (float). Uma classificação de 2.0 e superior é considerada um bom recurso.
- `feed/entry/content/m:properties/d:SampleValues` - Lista separada por vírgulas dos valores até o tamanho de amostragem solicitado.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. Obter informações de recursos (para uma compilação de classificação específica)

Recupera as informações de recurso, incluindo a classificação, para uma compilação de classificação específica.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------			|
| modelId |	Identificador exclusivo do modelo |
|samplingSize| Número de valores a serem incluídos para cada recurso de acordo com os dados presentes no catálogo.<br/> Os valores possíveis são:<br> - 1 - Todas as amostras. <br>0 - Sem amostragem. <br>N - Retornar N amostras para cada nome de recurso.|
|rankBuildId| Identificador exclusivo da compilação de classificação ou -1 para a última compilação de classificação|
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |


**Resposta**:

Código de status HTTP: 200

A resposta contém uma lista de entradas de informações de recurso. Cada entrada contém:

- `feed/entry/content/m:properties/d:Name` - Nome do recurso.
- `feed/entry/content/m:properties/d:RankUpdateDate` - Data em que a classificação foi alocada para esse recurso, conhecido como recurso de atualização de pontuação. Uma data do histórico (“0001-01-01T00:00:00”) significa que nenhuma compilação de classificação foi executada.
- `feed/entry/content/m:properties/d:Rank` - Classificação de recurso (float). Uma classificação de 2.0 e superior é considerada um bom recurso.
- `feed/entry/content/m:properties/d:SampleValues` - Lista separada por vírgulas dos valores até o tamanho de amostragem solicitado.

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. Compilação

  Esta seção explica as diferentes APIs relacionadas a compilações. Existem 3 tipos de compilações: uma compilação de recomendação, uma compilação de classificação e uma compilação FBT (frequentemente comprada junto).

A compilação de recomendação tem o objetivo de gerar um modelo de recomendação usado para previsões. As previsões (para esse tipo de compilação) vêm em duas versões: também conhecido como * I2I Recomendações de Item a Item - ao receber um item ou uma lista de itens, essa opção poderá prever uma lista de itens que provavelmente serão de grande interesse. Também conhecido como * U2I Recomendações do Usuário ao Item - ao receber uma ID de usuário (e, opcionalmente, uma lista de itens), essa opção poderá prever uma lista de itens que provavelmente serão de grande interesse para o usuário especificado (e suas opções adicionais de itens). As recomendações de U2I são baseadas no histórico de itens que foram de interesse do usuário até o momento em que o modelo foi criado.

Uma compilação de classificação é uma compilação técnica que permite que você saiba mais sobre a utilidade dos seus recursos. Geralmente, para obter o melhor resultado para um modelo de recomendação envolvendo recursos, siga as seguintes etapas: - Dispare uma compilação de classificação (a menos que a pontuação dos seus recursos seja estável) e espere até obter a pontuação de recurso. - Recupere a posição de seus recursos chamando a API [Obter informações de recursos](#101-get-features-info-for-last-rank-build). - Configure uma compilação de recomendação com os seguintes parâmetros: - `useFeatureInModel` - Definido como Verdadeiro. - `ModelingFeatureList` - Defina uma lista separada por vírgulas dos recursos com pontuação de 2.0 ou mais (de acordo com as classificações obtidas na etapa anterior). - `AllowColdItemPlacement` - Definido como Verdadeiro. - Opcionalmente, você pode definir `EnableFeatureCorrelation` como Verdadeiro e `ReasoningFeatureList` à lista de recursos que você deseja usar para obter explicações (normalmente a mesma lista de recursos usados na modelagem ou um sublista). - Dispare a compilação de recomendação com os parâmetros configurados.

Observação: se você não definir nenhum parâmetro (por exemplo, invocar a compilação de recomendação sem parâmetros) ou se você não desabilitar explicitamente o uso de recursos (por exemplo, `UseFeatureInModel` definido como Falso), o sistema vai configurar os parâmetros relacionados a recursos aos valores explicados acima caso exista uma compilação de classificação.

Não há nenhuma restrição sobre como executar uma compilação de classificação e uma compilação de recomendação simultaneamente para o mesmo modelo. No entanto, não é possível executar duas compilações do mesmo tipo no mesmo modelo em paralelo.

Uma compilação FBT (Frequentemente Comprado Juntos) é outro algoritmo de recomendações chamado por vezes de recomendador "conservador", o que é bom para os catálogos não homogêneos por natureza (homogêneas: livros, filmes, alguns alimentos, moda; não homogêneo: computadores e dispositivos, entre domínios, altamente diversificados).

Observação: se os arquivos de uso que você carregou contiverem o campo opcional "tipo de evento", somente os eventos “Compra” serão usados para modelagem FBT. Se nenhum tipo de evento for fornecido, todos os eventos serão considerados como compra.


####11\.1 Parâmetros de compilação

Cada tipo de compilação pode ser configurado por meio de um conjunto de parâmetros (descritos abaixo). Se você não configurar os parâmetros, o sistema atribuirá automaticamente valores aos parâmetros de acordo com as informações presentes ao disparar uma compilação.

#####11\.1.1. Condensador de uso
Os usuários ou itens com poucos pontos de uso podem conter mais ruído que informações. O sistema tenta prever o número mínimo de pontos de uso por usuário/item a ser usado em um modelo. Esse número estará dentro do intervalo definido pelos parâmetros ItemCutoffLowerBound e ItemCutoffUpperBound para itens, e o intervalo definido pelos parâmetros UserCutOffLowerBound e UserCutoffUpperBound para os usuários. O efeito condensador sobre os itens ou usuários pode ser minimizado ao configurar pelo menos um dos limites correspondentes a zero.

#####11\.1.2. Parâmetros de compilação de classificação

A tabela a seguir descreve os parâmetros de compilação para uma compilação de classificação.

|Chave|Descrição|Tipo|Valor Válido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | O número de iterações que o modelo executa é refletido pelo tempo de computação geral e a precisão do modelo. Quanto maior o número, maior será a precisão obtida, mas o tempo de computação será mais longo.| Número inteiro | 10-50 |
| NumberOfModelDimensions | O número de dimensões está relacionado ao número de “recursos” que o modelo tentará localizar em seus dados. Aumentar o número de dimensões possibilitará um melhor ajuste dos resultados em clusters menores. No entanto, ter muitas dimensões impedirá que o modelo localize correlações entre itens. | Número inteiro | 10-40 |
|ItemCutOffLowerBound| Define o limite inferior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|ItemCutOffUpperBound| Define o limite superior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffLowerBound| Define o limite inferior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffUpperBound| Define o limite superior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |

#####11\.1.3. Parâmetros de compilação de recomendação
A tabela a seguir descreve os parâmetros de compilação para uma compilação de recomendação.

|Chave|Descrição|Tipo|Valor Válido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | O número de iterações que o modelo executa é refletido pelo tempo de computação geral e a precisão do modelo. Quanto maior o número, maior será a precisão obtida, mas o tempo de computação será mais longo.| Número inteiro | 10-50 |
| NumberOfModelDimensions | O número de dimensões está relacionado ao número de “recursos” que o modelo tentará localizar em seus dados. Aumentar o número de dimensões possibilitará um melhor ajuste dos resultados em clusters menores. No entanto, ter muitas dimensões impedirá que o modelo localize correlações entre itens. | Número inteiro | 10-40 |
|ItemCutOffLowerBound| Define o limite inferior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|ItemCutOffUpperBound| Define o limite superior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffLowerBound| Define o limite inferior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffUpperBound| Define o limite superior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
| Descrição | Descrição da compilação. | Cadeia de caracteres | Qualquer texto, máximo de 512 caracteres |
| EnableModelingInsights | Permite computar métricas sobre o modelo de recomendação. | Booliano | Verdadeiro/Falso |
| UseFeaturesInModel | Indica se os recursos podem ser usados para aperfeiçoar o modelo de recomendação. | Booliano | Verdadeiro/Falso |
| ModelingFeatureList | Lista separada por vírgulas de nomes de recursos a serem usados na compilação de recomendação a fim de melhorar as recomendações. | Cadeia de caracteres | Nomes de recursos, até 512 caracteres |
| AllowColdItemPlacement | Indica se a recomendação também enviar itens sem interesse através da similaridade de recursos. | Booliano | Verdadeiro/Falso |
| EnableFeatureCorrelation | Indica se os recursos podem ser usados no raciocínio. | Booliano | Verdadeiro/Falso |
| ReasoningFeatureList | Lista separada por vírgulas de nomes de recursos a serem usado em frases de raciocínio (isto é, explicações de recomendação). | Cadeia de caracteres | Nomes de recursos, até 512 caracteres |
| EnableU2I | Permitir a recomendação personalizada também conhecido como U2I (usuário às recomendações de item). | Booliano | True/False (verdadeiro por padrão) |

#####11\.1.4. Parâmetros de compilação FBT
A tabela a seguir descreve os parâmetros de compilação para uma compilação de recomendação.

|Chave|Descrição|Tipo|Valor Válido (Padrão)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | O quanto o modelo é conservador. O número de co-ocorrências de itens a ser considerado para modelagem.| Número inteiro | 3-50 (6) |
|FbtMaxItemSetSize | Limita o número de itens em um conjunto frequente.| Número inteiro | 2-3 (2) |
|FbtMinimalScore | Pontuação mínima que um conjunto frequente deve ter para ser incluído nos resultados retornados. Quanto maior, melhor.| Duplo | 0 e acima (0) |

###11\.2. Disparar uma Compilação de Recomendação

  Por padrão, essa API vai disparar uma compilação de modelo de recomendação. Para disparar uma compilação de classificação (para classificar recursos), a variante da API de compilação com o parâmetro de tipo de compilação deve ser usada.


| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"` (Se estiver enviando Corpo da Solicitação)|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| userDescription | Identificador textual do catálogo. Observe que se você usar espaços você deve codificá-los com 20%. Consulte o exemplo acima.<br>Comprimento máximo: 50 |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | Se for deixado em branco, a compilação será executada com os parâmetros de compilação padrão.<br><br>Se você quiser definir os parâmetros de compilação, envie-os como XML no corpo como mostrado no exemplo a seguir. (Consulte a seção "Parâmetros de compilação" para obter uma explicação dos parâmetros.)`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Resposta**:

Código de status HTTP: 200

Esta é uma API assíncrona. Você receberá uma ID de compilação como uma resposta. Para saber quando a compilação foi concluída, você deve chamar a API "Obter Status de Compilações de um Modelo" e localizar a ID desta compilação na resposta. Observe que uma compilação pode levar de minutos a horas dependendo do tamanho dos dados.

Você não pode consumir recomendações até a compilação terminar.

Status de compilação válidos:

- Criar – A solicitação de compilação foi criada.
- Em fila – A solicitação de compilação foi enviada e será enfileirada.
- Compilando – A compilação está em andamento.
- Sucesso – A compilação concluída com êxito.
- Erro – A compilação foi concluída com uma falha.
- Cancelado – A compilação foi cancelada.
- Cancelando – Uma solicitação de cancelamento para a compilação foi enviada.


Observe que a ID de compilação pode ser encontrada no seguinte caminho: `Feed\entry\content\properties\Id`

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###11\.3. Disparar Compilação (Recomendação, Classificação ou FBT)

| Método HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"` (Se estiver enviando Corpo da Solicitação)|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId |	Identificador exclusivo do modelo |
| userDescription | Identificador textual do catálogo. Observe que se você usar espaços você deve codificá-los com 20%. Consulte o exemplo acima.<br>Comprimento máximo: 50 |
| buildType | Tipo de compilação para invocar: <br/> - “Recomendação” para compilação de recomendação <br> - “Classificação” para compilação de classificação <br/> - “Fbt” para compilação FBT
| apiVersion | 1\.0 |
||| 
| Corpo da solicitação | Se mantido vazio, a compilação será executada com os parâmetros padrão de compilação.<br><br>Se você deseja definir parâmetros de compilação, envie-os como XML no corpo como no exemplo a seguir. (Consulte a seção "Parâmetros de compilação" para obter uma explicação e a lista completa dos parâmetros.)`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**Resposta**:

Código de status HTTP: 200

Esta é uma API assíncrona. Você receberá uma ID de compilação como uma resposta. Para saber quando a compilação foi concluída, você deve chamar a API "Obter Status de Compilações de um Modelo" e localizar a ID desta compilação na resposta. Observe que uma compilação pode levar de minutos a horas dependendo do tamanho dos dados.

Você não pode consumir recomendações até a compilação terminar.

Status de compilação válidos:

- Criado – O modelo foi criado.
- Na fila – A compilação do modelo foi disparada e está na fila.
- Criando – O modelo está sendo criado.
- Sucesso – A compilação concluída com êxito.
- Erro – A compilação foi concluída com uma falha.
- Cancelado – A compilação foi cancelada.
- Cancelando – A compilação está sendo cancelada.

Observe que a ID de compilação pode ser encontrada no seguinte caminho: `Feed\entry\content\properties\Id`

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>




###11\.4. Obter status da compilação de um modelo
Recupera compilações e seus status para um modelo específico.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	modelId |	Identificador exclusivo do modelo |
|	onlyLastBuild |	Indica se é necessário retornar todo o histórico de compilação do modelo ou apenas o status da compilação mais recente |
|	apiVersion |	1\.0 |


**Resposta**:

Código de status HTTP: 200

A resposta inclui uma entrada por compilação. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/UserName` – O nome do usuário.
- `feed/entry/content/properties/ModelName` – O nome do modelo.
- `feed/entry/content/properties/ModelId` – Identificador exclusivo do modelo.
- `feed/entry/content/properties/IsDeployed` – Se a compilação é implantada (ou seja, uma compilação ativa).
- `feed/entry/content/properties/BuildId` – Identificador exclusivo da compilação.
- `feed/entry/content/properties/BuildType` - Tipo de compilação.
- `feed/entry/content/properties/Status` – Status da compilação. Este pode ser uma das seguintes opções: Erro, Criando, Na fila, Cancelando, Cancelado e Êxito.
- `feed/entry/content/properties/StatusMessage` – Mensagem de status detalhada (aplica-se somente a estados específicos).
- `feed/entry/content/properties/Progress` – Andamento da compilação (%).
- `feed/entry/content/properties/StartTime` – Hora de início da compilação.
- `feed/entry/content/properties/EndTime` – Hora de término da compilação.
- `feed/entry/content/properties/ExecutionTime` – Duração da compilação.
- `feed/entry/content/properties/ProgressStep` – Detalhes sobre o estágio atual de uma compilação em andamento.

Status válidos da compilação: - Criada - A entrada da solicitação de compilação foi criada. - Na fila – A solicitação de compilação foi disparada e está na fila. -Criando – A compilação está em andamento. - Êxito – Compilação concluída com êxito. - Erro – Compilação concluída com falha. - Cancelada – A compilação foi cancelada. - Cancelando – A compilação está sendo cancelada.

Valores válidos para o tipo de compilação: - Classificação - Compilação de classificação. - Recomendação - Compilação de recomendação.


XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11\.5. Obter Status da Compilação
Recupera os status da compilação de todos os modelos de um usuário

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
|	onlyLastBuild |	Indica se é necessário retornar todo o histórico de compilação do modelo ou apenas o status da compilação mais recente. |
|	apiVersion |	1\.0 |


**Resposta**:

Código de status HTTP: 200

A resposta inclui uma entrada por compilação. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/UserName` – O nome do usuário.
- `feed/entry/content/properties/ModelName` – O nome do modelo.
- `feed/entry/content/properties/ModelId` – Identificador exclusivo do modelo.
- `feed/entry/content/properties/IsDeployed` – Se a compilação é implantada.
- `feed/entry/content/properties/BuildId` – Identificador exclusivo da compilação.
- `feed/entry/content/properties/BuildType` - Tipo de compilação.
- `feed/entry/content/properties/Status` – Status da compilação. Este pode ser uma das seguintes opções: Erro, Criando, Na fila, Cancelado, Cancelando e Êxito.
- `feed/entry/content/properties/StatusMessage` – Mensagem de status detalhada (aplica-se somente a estados específicos).
- `feed/entry/content/properties/Progress` – Andamento da compilação (%).
- `feed/entry/content/properties/StartTime` – Hora de início da compilação.
- `feed/entry/content/properties/EndTime` – Hora de término da compilação.
- `feed/entry/content/properties/ExecutionTime` – Duração da compilação.
- `feed/entry/content/properties/ProgressStep` – Detalhes sobre o estágio atual de uma compilação em andamento.

Status válidos da compilação: - Criada - A entrada da solicitação de compilação foi criada. - Na fila – A solicitação de compilação foi disparada e está na fila. -Criando – A compilação está em andamento. - Êxito – Compilação concluída com êxito. - Erro – Compilação concluída com falha. - Cancelada – A compilação foi cancelada. - Cancelando – A compilação está sendo cancelada.


Valores válidos para o tipo de compilação: - Classificação - Compilação de classificação. - Recomendação - Compilação de recomendação.


XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11\.6. Excluir compilação
Exclui uma compilação.

OBSERVAÇÃO: <br>Não é possível excluir uma compilação ativa. O modelo deve ser atualizado para uma compilação ativa diferente antes de excluí-la.<br>Não é possível excluir uma compilação em andamento. Você deve cancelar a compilação antes chamando <strong>Cancelar Compilação</strong>.

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| buildId | Identificador exclusivo da compilação. |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200

###11\.7. Cancelar compilação
Cancela uma compilação que está no status de compilação.

| Método HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| buildId | Identificador exclusivo da compilação. |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200

###11\.8. Obter parâmetros de compilação
Recupera parâmetros de compilação.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| buildId | Identificador exclusivo da compilação. |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200

Essa API retorna uma coleção de elementos de chave/valor. Cada elemento representa um parâmetro e seu valor:- `feed/entry/content/properties/Key` – Nome do parâmetro de compilação. `feed/entry/content/properties/Value` – Valor do parâmetro de compilação.

A tabela a seguir descreve o valor que cada chave representa.

|Chave|Descrição|Tipo|Valor Válido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | O número de iterações que o modelo executa é refletido pelo tempo de computação geral e a precisão do modelo. Quanto maior o número, maior será a precisão obtida, mas o tempo de computação será mais longo.| Número inteiro | 10-50 |
| NumberOfModelDimensions | O número de dimensões está relacionado ao número de “recursos” que o modelo tentará localizar em seus dados. Aumentar o número de dimensões possibilitará um melhor ajuste dos resultados em clusters menores. No entanto, ter muitas dimensões impedirá que o modelo localize correlações entre itens. | Número inteiro | 10-40 |
|ItemCutOffLowerBound| Define o limite inferior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|ItemCutOffUpperBound| Define o limite superior de item para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffLowerBound| Define o limite inferior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
|UserCutOffUpperBound| Define o limite superior de usuário para o condensador. Consulte o condensador de uso acima. | Número inteiro | 2 ou mais (0 desabilita o condensador) |
| Descrição | Descrição da compilação. | Cadeia de caracteres | Qualquer texto, máximo de 512 caracteres |
| EnableModelingInsights | Permite computar métricas sobre o modelo de recomendação. | Booliano | Verdadeiro/Falso |
| UseFeaturesInModel | Indica se os recursos podem ser usados para aperfeiçoar o modelo de recomendação. | Booliano | Verdadeiro/Falso |
| ModelingFeatureList | Lista separada por vírgulas de nomes de recursos a serem usados na compilação de recomendação a fim de melhorar as recomendações. | Cadeia de caracteres | Nomes de recursos, até 512 caracteres |
| AllowColdItemPlacement | Indica se a recomendação também enviar itens sem interesse através da similaridade de recursos. | Booliano | Verdadeiro/Falso |
| EnableFeatureCorrelation | Indica se os recursos podem ser usados no raciocínio. | Booliano | Verdadeiro/Falso |
| ReasoningFeatureList | Lista separada por vírgulas de nomes de recursos a serem usado em frases de raciocínio (isto é, explicações de recomendação). | Cadeia de caracteres | Nomes de recursos, até 512 caracteres |


XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. Recomendações
###12\.1. Obter Recomendações de Item (para compilação ativa)

Obtenha recomendações da compilação ativa do tipo "Recomendação" ou "Fbt" com base em uma lista de itens de propagação (entrada).

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| itemIds | Lista separada por vírgulas dos itens para recomendar. <br>Se a compilação ativa for do tipo FBT, você poderá enviar somente um item. <br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

A resposta de exemplo a seguir inclui 10 itens recomendados.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.2. Obter Recomendações de Item (de uma compilação específica)

Obtenha recomendações de uma compilação específica do tipo “Recomendação” ou “Fbt”.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| itemIds | Lista separada por vírgulas dos itens para recomendar. <br>Se a compilação ativa for do tipo FBT, você poderá enviar somente um item. <br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso
| buildId | a id de compilação a ser usada para esta solicitação de recomendação |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

Veja um exemplo de resposta no 12.1

###12\.3. Obter Recomendações de FBT (para compilação ativa)

Obtenha recomendações da compilação ativa do tipo "Fbt" com base em um item de semente (entrada).

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| itemId | Item a ser recomendado. <br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| minimalScore | Pontuação mínima que um conjunto frequente deve ter para ser incluído nos resultados retornados |
| includeMetatadata | Uso futuro, sempre falso |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por conjunto de item recomendado (um conjunto de itens que são geralmente comprados junto com o item de semente/entrada). Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id1` – ID do item recomendado - `Feed\entry\content\properties\Name1` – Nome do item. - `Feed\entry\content\properties\Id2` – 2ª ID do item recomendado (opcional). - `Feed\entry\content\properties\Name2` – Nome do segundo item (opcional). - `Feed\entry\content\properties\Rating` – Classificação da recomendação; número mais alto significa maior confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio de recomendação (por exemplo, explicações de recomendação).

A resposta de exemplo a seguir inclui 3 conjuntos de itens recomendados.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. Obter Recomendações FBT (de uma compilação específica)

Obtenha recomendações de uma compilação específica do tipo "Fbt".

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| itemId | Item a ser recomendado. <br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| minimalScore | Pontuação mínima que um conjunto frequente deve ter para ser incluído nos resultados retornados |
| includeMetatadata | Uso futuro, sempre falso |
| buildId | a id de compilação a ser usada para esta solicitação de recomendação |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por conjunto de item recomendado (um conjunto de itens que são geralmente comprados junto com o item de semente/entrada). Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id1` – ID do item recomendado - `Feed\entry\content\properties\Name1` – Nome do item. - `Feed\entry\content\properties\Id2` – 2ª ID do item recomendado (opcional). - `Feed\entry\content\properties\Name2` – Nome do segundo item (opcional). - `Feed\entry\content\properties\Rating` – Classificação da recomendação; número mais alto significa maior confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio de recomendação (por exemplo, explicações de recomendação).

Veja um exemplo de resposta no 12.3

###12\.5. Obter Recomendações do Usuário (para compilação ativa)

Obtenha recomendações de usuário de uma compilação do tipo "Recomendação" marcada como compilação ativa.

A API retornará uma lista de itens prevista de acordo com o histórico de uso do usuário.

Observações: 1. Não há nenhuma recomendação de usuário para a compilação FBT. 2. Se a compilação ativa for FBT, esse método retornará um erro.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| coluna | Identificador exclusivo do usuário |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

Veja um exemplo de resposta no 12.1

###12\.6. Obter recomendações de usuário com a lista de itens (para compilação ativa)

Obtenha recomendações de usuário de uma compilação do tipo "Recomendação" marcada como compilação ativa com uma lista de itens adicionais

A API retornará uma lista de itens previstos de acordo com o histórico de uso do usuário e os outros itens fornecidos.

Observações: 1. Não há nenhuma recomendação de usuário para a compilação FBT. 2. Se a compilação ativa for FBT, esse método retornará um erro.


| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemIds=%27<itemIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| coluna | Identificador exclusivo do usuário |
| itemIds | Lista separada por vírgulas dos itens para recomendar. Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

Veja um exemplo de resposta no 12.1

###12\.7. Obter Recomendações de Usuário (de uma compilação específica)

Obtenha recomendações de usuário de uma compilação específica do tipo "Recomendação".

A API retornará uma lista de itens previstos de acordo com o histórico de uso do usuário (usado na compilação específica).

Observação: Não há nenhuma recomendação de usuário para a compilação FBT.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| coluna | Identificador exclusivo do usuário |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso |
| buildId | a id de compilação a ser usada para esta solicitação de recomendação |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

Veja um exemplo de resposta no 12.1


###12\.8. Obtenha Recomendações de Usuário com a lista de itens (de uma compilação específica)

Obtenha recomendações de usuário de uma compilação específica do tipo "Recomendação" e a lista de itens adicionais.

A API retornará uma lista de itens previstos de acordo com o histórico de uso do usuário e os outros itens da lista.

Observação: Não há nenhuma recomendação de usuário para a compilação FBT.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| coluna | Identificador exclusivo do usuário |
| itemIds | Lista separada por vírgulas dos itens para recomendar. Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Uso futuro, sempre falso |
| buildId | a id de compilação a ser usada para esta solicitação de recomendação |
| apiVersion | 1\.0 |

**Resposta:**

Código de status HTTP: 200


A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – Classificação da recomendação; quanto mais alto o número, maior a confiança. - `Feed\entry\content\properties\Reasoning` – Raciocínio da recomendação (ou seja, explicações de recomendação).

Veja um exemplo de resposta no 12.1

##13\. Histórico de uso do usuário
Após a compilação de um modelo de recomendação, o sistema permitirá recuperar o histórico do usuário (os itens associados a um usuário específico) usado para a compilação. Essa API permite recuperar o histórico do usuário

Observação: o histórico do usuário está disponível atualmente apenas para compilações de recomendação.

###13\.1 Recuperar o histórico do usuário
Recupere a lista de itens usada na compilação ativa ou na compilação especificada para a ID de usuário fornecida.

| Método HTTP | URI |
|:--------|:--------|
|GET | Obter o histórico do usuário para a compilação ativa.<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Obtenha o histórico do usuário para determinada compilação `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Exemplo:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | o identificador exclusivo do modelo.|
| coluna | o identificador exclusivo do usuário.
| buildId | parâmetro opcional, permitir indicar a partir de qual versão do histórico do usuário deve fazer a busca
| apiVersion | 1\.0 |


**Resposta:**

Código de status HTTP: 200

A resposta inclui uma entrada por item recomendado. Cada entrada tem os seguintes dados:- `Feed\entry\content\properties\Id` – ID do item recomendado - `Feed\entry\content\properties\Name` – Nome do item. - `Feed\entry\content\properties\Rating` – N/A. - `Feed\entry\content\properties\Reasoning` – N/A.

XML de OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. Notificações
As Recomendações do Aprendizado de Máquina do Azure criam notificações quando erros persistentes ocorrem no sistema. Há três tipos de notificações: 1. Falha na compilação – Essa notificação é disparada para cada falha de compilação. 2. Falha no processo de aquisição de dados - Essa notificação é disparada quando há mais de 100 erros nos últimos 5 minutos no processamento de eventos de uso por modelo. 3. Falha de consumo de recomendação - Essa notificação é disparada quando há mais de 100 erros nos últimos 5 minutos no processamento de solicitações de recomendação por modelo.


###14\.1. Obter notificações
Recupera todas as notificação para todos os modelos ou para um único modelo.

| Método HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Obter todas as notificações para todos os modelos:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Exemplo para obter notificações para um modelo específico:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Parâmetro opcional. Quando omitido, você receberá todas as notificações para todos os modelos. <br>Valor válido: o identificador exclusivo do modelo.|
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta:**

Código de status HTTP: 200

XML de OData

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14\.2. Excluir as notificações de modelo
Exclui todas notificações de leitura de um modelo.

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| modelId | Identificador exclusivo do modelo |
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200

###14\.3. Excluir as notificações do usuário
Exclui todas as notificações de todos os modelos

| Método HTTP | URI |
|:--------|:--------|
|EXCLUIR |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Nome do Parâmetro |	Valores Válidos |
|:--------			|:--------								|
| apiVersion | 1\.0 |
||| 
| Corpo da Solicitação | NENHUM |

**Resposta**:

Código de status HTTP: 200




##15\. Legal
Este documento é fornecido "no estado em que se encontra". Informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio.<br><br> Alguns exemplos aqui representados são fornecidos somente para fins de ilustração e são fictícios. Nenhuma associação ou conexão real é intencional ou deve ser inferida.<br><br> Este documento não fornece a você nenhum direito legal a qualquer propriedade intelectual de qualquer produto da Microsoft. Você pode copiar e usar este documento para fins de consulta interna.<br><br> © 2015 Microsoft. Todos os direitos reservados.
 

<!---HONumber=Oct15_HO3-->
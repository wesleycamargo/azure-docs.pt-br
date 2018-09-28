---
title: Carregar dados com o SDK de preparação de dados de aprendizado de máquina do Azure - Python
description: Aprenda sobre o carregamento de dados com o SDK de preparação de dados de aprendizado de máquina do Azure. Você pode carregar diferentes tipos de dados de entrada, especificar tipos e parâmetros de arquivos de dados ou usar a funcionalidade de leitura inteligente do SDK para detectar automaticamente o tipo de arquivo.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 000870e3273799930f519ff32d6b072d8c2d1f10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989678"
---
#<a name="load-data-with-the-azure-machine-learning-data-prep-sdk"></a>Carregar dados com o SDK de preparação de dados de aprendizado de máquina do Azure

O [SDK de preparação de dados de aprendizado de máquina do Azure](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) permite que você carregue diferentes tipos de dados de entrada. Você pode especificar o tipo de arquivo de dados e seus parâmetros ou usar a funcionalidade de leitura inteligente do SDK para detectar automaticamente o tipo de um arquivo.

## <a name="read-lines"></a>Linhas de leitura
Uma das maneiras mais simples para carregar dados é lê-lo como linhas de texto.

```
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Linha|
|----|-----|
|0|Data \|\| temperatura mínima \|\| temperatura máxima|
|1|1-07-2015 \|\|  -4.1 \|\|  10.0|
|2|2-07-2015 \|\|  -0.8 \|\|  10.8|
|3|3-07-2015 \|\|  -7.0 \|\|  10.5|
|4|4-07-2015 \|\|  -5.5 \|\|  9.3|

Depois que os dados são processados, você pode recuperar um DataFrame do pandas para o conjunto de dados completo.

```
df = dataflow.to_pandas_dataframe()
df
```

||Linha|
|----|-----|
|0|Data \|\| Temperatura mínima \|\|Temperatura máxima|
|1|1-07-2015\|\| 4.1\|\| 10.0|
|2|2-07-2015\|\| 0.8\|\| 10.8|
|3|3-07-2015\|\| 7.0\|\| 10.5|
|4|4-07-2015\|\| 5.5\|\| 9.3|

## <a name="read-csv"></a>Ler CSV
Ao ler arquivos delimitados, você pode deixar o tempo de execução subjacente inferir os parâmetros de análise (como um separador, codificação, se usar cabeçalhos, etc.) em vez de fornecê-los. Para este exemplo, tente ler um arquivo especificando apenas sua localização. 

```
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Condado de Hale|10171002158| |
|2|ALABAMA|1|101710|Condado de Hale|10171002162| |
|3|ALABAMA|1|101710|Condado de Hale|10171002158| |
|4|ALABAMA|1|101710|Condado de Hale|10171002158|2|

Um dos parâmetros que você pode especificar é um número de linhas para pular nos arquivos que você está lendo. Use o código a seguir para filtrar a linha duplicada.
```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Condado de Hale|10171002158|29|
|1|ALABAMA|1|101710|Condado de Hale|10171002162|40 |
|2|ALABAMA|1|101710|Condado de Hale|10171002158| 43|
|3|ALABAMA|1|101710|Condado de Hale|10171002158|2|
|4|ALABAMA|1|101710|Condado de Hale|10171000589|23 |

Em seguida, você pode examinar os tipos de dados das colunas.
```
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```
Infelizmente, todas as nossas colunas voltaram como strings. Isso ocorre porque, por padrão, o SDK de preparação de dados de aprendizado de máquina do Azure não altera seu tipo de dados. A fonte de dados que estamos lendo é um arquivo de texto, portanto, o SDK lê todos os valores como strings. Para este exemplo, no entanto, queremos analisar colunas numéricas como números. Para fazer isso, você pode definir o parâmetro inference_arguments como current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```
Várias das colunas foram corretamente detectadas como números e seu tipo é definido como float64. Com o processamento concluído, você pode recuperar um DataFrame do pandas para o conjunto de dados completo.

```
df = dataflow.to_pandas_dataframe()
df
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|1.017100e+10|49.0|
|1|ALABAMA|Condado de Hale|1.017100e+10|49.0|
|2|ALABAMA|Condado de Hale|1.017100e+10|43.0|
|3|ALABAMA|Condado de Hale|1.017100e+10|2,0|
|4|ALABAMA|Condado de Hale|1.017100e+10|23.0|

## <a name="read-excel"></a>Leia o Excel
O SDK de preparação de dados de aprendizado de máquina do Azure inclui uma função `read_excel` para carrega arquivos do Excel.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```
||Coluna1|Coluna2|Coluna3|Coluna4|Coluna5|Coluna6|Coluna7|Coluna8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Ferro, IVB|60000000.0|Encontrado|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19.58333|17.91667|
|1|Cabo York|Iron, IIIAB|58200000.0|Encontrado|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76.13333|-64.93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000.0|Encontrado|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27.46667|-60.58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000.0|Encontrado|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35.05000|-111.03333|
|4|Armanty|Iron, IIIE|28000000.0|Encontrado|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47.00000|88.00000|

Você carregou a primeira folha do arquivo do Excel. Você pode obter o mesmo resultado especificando explicitamente o nome da planilha que deseja carregar. Se você quiser carregar a segunda planilha, pode fornecer seu nome como um argumento.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Coluna1|Coluna2|Coluna3|Coluna4|Coluna5|Coluna6|Coluna7|Coluna8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|2|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|3|RANK|Title|Estúdio|Worldwide|Domestic / %|Coluna1|Overseas / %|Coluna2|Year^|
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

Como você pode ver, a tabela na segunda folha tinha cabeçalhos e três linhas vazias. Você precisa modificar os argumentos da função de acordo.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

||RANK|Title|Estúdio|Worldwide|Domestic / %|Coluna1|Overseas / %|Coluna2|Year^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186,8|658. 7|0,301|1528,1|0. 699|1997^|
|2|3|Os Avengers da Marvel|BV|1518,6|623,4|0,41|895. 2|0,59|2012|
|3|4|Harryry Potter e o Deathly Hallows parte 2|WB|1341. 5|381|0.284|960.5|0.716|2011|
|4|5|Frozen|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="read-fixed-width-files"></a>Ler arquivos de largura fixa
Para arquivos com largura fixa, você pode especificar uma lista de deslocamentos. A primeira coluna é sempre assumida para iniciar no deslocamento 0.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```
||010000|99999|BOGUS NORUEGA|NÃO|NO_1|ENRS|Coluna7|Coluna8|Coluna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORUEGA|NÃO|NÃO|ENSO||||
|1|010010|99999|JAN MAYEN|NÃO|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NÃO|NÃO|||||
|3|010014|99999|SOERSTOKKEN|NÃO|NÃO|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NÃO|NÃO|ENBL|+61383|+005867|+03270|


Se não houver cabeçalhos nos arquivos, você deverá tratar a primeira linha como dados. Passando `PromoteHeadersMode.NONE` ao argumento de palavra-chave de cabeçalho, você pode evitar a detecção de cabeçalho e obter os dados corretos.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```
||Coluna1|Coluna2|Coluna3|Coluna4|Coluna5|Coluna6|Coluna7|Coluna8|Coluna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORUEGA|NÃO|NO_1|ENRS|Coluna7|Coluna8|Coluna9|
|1|010003|99999|BOGUS NORUEGA|NÃO|NÃO|ENSO||||
|2|010010|99999|JAN MAYEN|NÃO|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NÃO|NÃO|||||
|4|010014|99999|SOERSTOKKEN|NÃO|NÃO|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NÃO|NÃO|ENBL|+61383|+005867|+03270|

## <a name="read-sql"></a>Leia SQL
O SDK de preparação de dados de aprendizado de máquina do Azure também pode carregar dados de servidores SQL. Atualmente, somente o Microsoft SQL Server é suportado.
Para ler dados de um servidor SQL, crie um objeto de fonte de dados que contenha as informações de conexão.

```
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Como você pode ver, o parâmetro de senha `MSSQLDataSource` aceita um objeto secreto. Você pode obter um objeto secreto de duas maneiras:
-   Registre o segredo e seu valor no mecanismo de execução. 
-   Crie o segredo com apenas um ID (útil se o valor secreto já estiver registrado no ambiente de execução).

Depois de criar um objeto de fonte de dados, você pode continuar a ler os dados.
```
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|NOME|ProductNumber|Cor|StandardCost|ListPrice|Tamanho|Peso|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Preto|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Vermelho|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|Vermelho|13.0863|34.99|Nenhum|Nenhum|35|33|2005-07-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet, Black|HL-U509|Preto|13.0863|34.99|Nenhum|Nenhum|35|33|2005-07-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|Meias para Motocicleta, M|SO-B909-M|Branco|3,3963|9.50|M|Nenhum|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```
df = dataflow.to_pandas_dataframe()
df.dtypes
```
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="read-from-azure-data-lake-storage"></a>Ler do armazenamento do Azure Data Lake
Existem duas maneiras de o SDK adquirir o token OAuth necessário para acessar o Armazenamento do Data Lake do Azure:
-   Recuperar o token de acesso de uma sessão de login recente do login do CLI do Azure do usuário
-   Use um serviço principal (SP) e um certificado como secreto

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Use um token de acesso de uma sessão recente do CLI do Azure
Na sua máquina local, execute o seguinte comando:

> [!NOTE] 
> Se a sua conta de usuário for um membro de mais de um locatário do Azure, será necessário especificar o locatário no formulário de nome de host do URL do AAD.


```
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Crie uma entidade de serviço com a CLI do Azure
Você pode usar a CLI do Azure para criar uma entidade de serviço e o certificado correspondente. Essa entidade de serviço específica é configurada como Reader, com seu escopo reduzido para apenas a conta de armazenamento de dados do Azure Data Lake 'dpreptestfiles'
```
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Este comando emite o `appId` e o caminho para o arquivo de certificado (geralmente na pasta pessoal). O arquivo .crt contém o certificado público e a chave privada no formato PEM.

Extrai a impressão digital:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

##### <a name="configure-an-azure-data-lake-storage-account-for-the-service-principal"></a>Configurar uma conta do Armazenamento do Azure Data Lake para o responsável pelo serviço
Para configurar a ACL para o sistema de arquivos do Azure Data Lake Storage, use o objectId do usuário ou, para este exemplo, o principal do serviço:
```
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```
##### <a name="configure-read-and-execute-access-for-the-azure-data-lake-storage-file-system"></a>Configure o acesso de Leitura e Execução para o sistema de arquivos do Azure Data Lake Storage.
Como o modelo de ACL HDFS subjacente não suporta herança, você precisa configurar a ACL para pastas e arquivos individualmente.
```
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
#### <a name="acquire-an-oauth-access-token"></a>Adquirir um token de acesso do OAuth
Use o pacote `adal` (via: `pip install adal`) para criar um contexto de autenticação no locatário da MSFT e adquirir um token de acesso do OAuth. Para ADLS, o recurso na solicitação de token deve ser para 'https://datalake.azure.net', que é diferente da maioria dos outros recursos do Azure.

```
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Site|street|city|Município|
|----|------|-----|----|----|----|----|
|0|1012063|Associação do mercado dos fazendeiros de Caledonia - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Fazendeiros ' mercado|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 Mercado de Agricultores da Rua Principal|http://thetownofsixmile.wordpress.com/ |106 S. Rua principal|Seis milhas|||
|4|1010691|10º Comunidade dos Mercados dos fazendeiros da rua|http://agrimissouri.com/mo-grown/grodetail.php... |10ª Rua e Popular|Lamar|Barton|


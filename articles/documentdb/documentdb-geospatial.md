---
title: Trabalhando com dados geoespaciais no DocumentDB do Azure | Microsoft Docs
description: Compreenda como criar, indexar e consultar objetos espaciais com o Banco de Dados de Documentos do Azure.
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: documentdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/16/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 2d833a559b72569983340972ba3b905b9e42e61d
ms.openlocfilehash: f78c0fc1959f72164508af9d8945744b67fac68b


---
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Trabalhando com dados geoespaciais no Banco de Dados de Documentos do Azure
Este artigo é uma introdução à funcionalidade geoespacial do [Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/). Depois de ler este artigo, você poderá responder as seguintes perguntas:

* Como armazeno dados espaciais no Banco de Dados de Documentos do Azure?
* Como faço para consultar dados geoespaciais no Banco de Dados de Documentos do Azure no SQL e no LINQ?
* Como eu habilito ou desabilito a indexação espacial no Banco de Dados de Documentos?

Consulte este [projeto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para obter exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais
Os dados espaciais descrevem a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, eles correspondem aos objetos na Terra, ou seja, dados geoespaciais. Os dados espaciais podem ser usados para representar a localização de uma pessoa, um lugar de interesse ou a divisa de uma cidade ou de um lago. Com frequência, os casos de uso comum envolvem consultas de proximidade, por exemplo, “encontre todas as cafeterias próximas ao local atual”. 

### <a name="geojson"></a>GeoJSON
O Banco de Dados de Documentos dá suporte à indexação e à consulta de dados de ponto geoespaciais representados usando a [especificação GeoJSON](http://geojson.org/geojson-spec.html). As estruturas de dados GeoJSON são sempre  objetos JSON válidos e, portanto, podem ser armazenadas e consultadas usando o Banco de Dados de Documentos sem qualquer ferramenta ou biblioteca especializada. Os SDKs do Banco de Dados de Documentos fornecem classes auxiliares e métodos que facilitam o trabalho com dados espaciais. 

### <a name="points-linestrings-and-polygons"></a>Pontos, LineStrings e Polígonos
Um **Ponto** denota uma única posição no espaço. Em dados geoespaciais, um Ponto representa o local exato, que poderia ser um endereço de um supermercado, de um quiosque, de um automóvel ou de uma cidade.  Um ponto é representado no GeoJSON (e no Banco de Dados de Documentos) usando seu par de coordenadas ou a longitude e a latitude. Veja um exemplo JSON para um ponto.

**Pontos no Banco de Dados de Documentos**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

> [!NOTE]
> A especificação de GeoJSON mostra a longitude primeiro e a latitude depois. Assim como acontece em outros aplicativos de mapeamento, a longitude e a latitude são ângulos e são representados em graus. Os valores de longitude são medidos a partir do Meridiano Principal e estão entre -180,0 e 180,0 graus e os valores de latitude são medidos a partir do Equador e estão entre -90,0 e 90,0 graus. 
> 
> O Banco de Dados de Documentos interpreta coordenadas como representadas de acordo com o sistema de referência WGS-84. Consulte abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.
> 
> 

Isso pode ser inserido em um documento do Banco de Dados de Documentos como mostrado neste exemplo de um perfil de usuário com dados de local:

**Usar o perfil com local armazenado no Banco de Dados de Documentos**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Além dos pontos, o GeoJSON também dá suporte a LineStrings e a polígonos. **LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os conectam. Em dados geoespaciais, as LineStrings são comumente usadas para representar vias expressas ou rios. Um **Polígono** é um limite de pontos conectados que formam uma LineString fechada. Os polígonos são comumente usados para representar formações naturais, como lagos ou jurisdições políticas, como cidades e estados. Veja um exemplo de um Polígono no DocumentDB. 

**Polígonos no Banco de Dados de Documentos**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

> [!NOTE]
> A especificação GeoJSON exige que, para Polígonos válidos, o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada.
> 
> Os pontos em um Polígono devem ser especificados no sentido anti-horário. Um Polígono especificado no sentido horário representa o inverso da região dentro dele.
> 
> 

Além de Ponto, LineString e Polígono, o GeoJSON também especifica a representação de como agrupar vários locais geoespaciais, além de como associar propriedades arbitrárias a geolocalização como um **Recurso**. Como esses objetos são JSON válido, todos eles podem ser armazenados e processados no Banco de Dados de Documentos. No entanto, o Banco de Dados de Documentos dá suporte apenas à indexação automática de pontos.

### <a name="coordinate-reference-systems"></a>Sistemas de referência de coordenadas
Como a forma da Terra é irregular, as coordenadas de dados geoespaciais são representadas em muitos sistemas de coordenadas de referência (CRS), cada um com seus próprios quadros de referência e unidades de medida. Por exemplo, o "National Grid of Britain" é um sistema de referência muito preciso para o Reino Unido, mas não para fora dele. 

O CRS mais popular em uso hoje é o Sistema Geodésico Mundial [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Os dispositivos GPS e vários serviços de mapeamento, incluindo as APIs do Google Maps e do Bing Mapas usam WGS-84. O Banco de Dados de Documentos dá suporte à indexação e à consulta de dados geoespaciais usando apenas o CRS WGS-84. 

## <a name="creating-documents-with-spatial-data"></a>Criando documentos com dados espaciais
Quando você criar documentos que contenham valores GeoJSON, eles serão automaticamente indexados com um índice espacial de acordo com a política de indexação da coleção. Se você estiver trabalhando com um SDK do Banco de Dados de Documentos em uma linguagem dinamicamente tipada, como Python ou Node.js, deverá criar um GeoJSON válido.

**Criar documentos com dados geoespaciais no Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Se você estiver trabalhando com os SDKs do .NET (ou do Java), poderá usar as novas classes Point e Polygon no namespace Microsoft.Azure.Documents.Spatial para inserir informações de localização em seus objetos de aplicativo. Essas classes ajudam a simplificar a serialização e a desserialização de dados espaciais no GeoJSON.

**Criar documentos com dados geoespaciais no .NET**

    using Microsoft.Azure.Documents.Spatial;

    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }

        // More properties
    }

    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Se você não tiver as informações de latitude e de longitude, mas se tiver os endereços físicos ou o nome do local, como a cidade ou o país, poderá procurar as coordenadas reais usando um serviço de geocodificação, como os Serviços REST do Bing Mapas. Saiba mais sobre a geocodificação do Bing Mapas [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultando tipos espaciais
Agora que já vimos como inserir dados geoespaciais, vamos dar uma olhada em como consultar esses dados usando o Banco de Dados de Documentos com SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Funções internas espaciais do SQL
O Banco de Dados de Documentos dá suporte às seguintes funções internas do Open Geospatial Consortium (OGC) para consultas geoespaciais. Para obter mais detalhes sobre o conjunto completo de funções internas na linguagem SQL, consulte [Consultar o Banco de Dados de Documentos](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Retorna uma expressão booliana que indica se o primeiro objeto GeoJSON (Ponto, Polígono ou LineString) está em um segundo objeto GeoJSON (Ponto, Polígono ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Retorna uma expressão booliana que indica se os dois objetos GeoJSON especificados (Ponto, Polígono ou LineString) se cruzam.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retorna um valor Booliano que indica se a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.</td>
</tr>
</table>

As funções espaciais podem ser usadas para executar consultas de proximidade em consultas espaciais. Por exemplo, veja uma consulta que retorna todos os documentos de família que estejam em um raio de 30 km do local especificado usando a função interna ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se você incluir a indexação espacial em sua política de indexação, as "consultas de distância" serão servidas com eficiência por meio do índice. Para obter mais detalhes sobre a indexação espacial, consulte a seção abaixo. Se você não tiver um índice espacial para os caminhos especificados, ainda poderá executar consultas espaciais especificando o cabeçalho da solicitação `x-ms-documentdb-query-enable-scan` com o valor definido como "true". No .NET, isso pode ser feito passando o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como true. 

ST_WITHIN pode ser usado para verificar se um ponto está dentro de um Polígono. Normalmente, os Polígonos são usados para representar limites como códigos postais, fronteiras de estado ou formações naturais. Novamente, se você incluir a indexação espacial em sua política de indexação, as consultas "internas" serão servidas com eficiência por meio do índice. 

Os argumentos do polígono no ST_WITHIN podem conter apenas um único toque, ou seja, os Polígonos não devem conter orifícios neles. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Da mesma forma como os tipos incompatíveis funcionam na consulta do Banco de Dados de Documentos, se o valor de local especificado em um dos argumentos for malformado ou inválido, então ele será avaliado como **indefinido** e o documento avaliado será ignorado nos resultados da consulta. Se sua consulta não retornar resultados, execute ST_ISVALIDDETAILED para depurar o motivo pelo qual o tipo spatail é inválido.     
> 
> 

O DocumentDB também dá suporte à execução de consultas inversas, ou seja, você pode indexar Polígonos ou linhas no DocumentDB e consultar as áreas que contêm um ponto especificado. Esse padrão é normalmente usado em logística para identificar, por exemplo, quando um caminhão entra ou sai de uma determinada área. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser usados para verificar se um objeto espacial é válido. Por exemplo, a consulta a seguir verifica a validade de um ponto com um valor de latitude fora do intervalo (-132,8). ST_ISVALID retorna um valor Booliano e ST_ISVALIDDETAILED retorna o Booliano e uma cadeia de caracteres com o motivo pelo qual ele é considerado inválido.

** Consulta **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Essas funções também podem ser usadas para validar Polígonos. Por exemplo, ST_ISVALIDDETAILED é usado aqui para validar um Polígono que não está fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>Consultas LINQ no SDK do .NET
O SDK do .NET do Banco de Dados de Documentos também fornece métodos stub `Distance()` e `Within()` para uso em expressões LINQ. O provedor LINQ do Banco de Dados de Documentos traduz essas chamadas do método nas chamadas de função internas do SQL equivalentes (ST_DISTANCE e ST_WITHIN, respectivamente). 

Veja um exemplo de uma consulta LINQ que localiza todos os documentos da coleção do Banco de Dados de Documentos cujo valor de "local" está em um raio de 30 km do ponto especificado usando LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, veja uma consulta para localizar todos os documentos cuja "localização" seja o interior da caixa/Polígono especificada. 

**Consulta LINQ para dentro**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que já vimos como consultar documentos usando LINQ e SQL, vamos dar uma olhada em como configurar o Banco de Dados de Documentos para indexação espacial.

## <a name="indexing"></a>Indexação
Como descrito no documento [Indexação independente de esquema com o Banco de Dados de Documentos do Azure](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , projetamos o mecanismo de banco de dados do Banco de Dados de Documentos para ser verdadeiramente independente de esquema e para fornecer suporte de primeira classe para JSON. O mecanismo de banco de dados otimizado para gravação do DocumentDB também compreende originalmente os dados espaciais (pontos, Polígonos e linhas) representados no padrão GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas em um plano 2D e então dividida progressivamente em células usando um **quadtree**. Essas células são mapeadas para 1D com base na localização da célula em uma **curva de preenchimento de espaço de Hilbert**, que preserva a localidade de pontos. Além disso, quando dados de localização são indexados, eles passam por um processo conhecido como **mosaico**, ou seja, todas as células que interceptam uma localização são identificadas e armazenadas como chaves no índice do Banco de Dados de Documentos. No momento da consulta, argumentos como pontos e Polígonos também são incluídos no mosaico para extrair os intervalos de IDs de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclua o índice espacial para /* (todos os caminhos), todos os pontos encontrados na coleção serão indexados para consultas espaciais eficientes (ST_WITHIN e ST_DISTANCE). Os índices espaciais não têm um valor de precisão e sempre usam um valor de precisão padrão.

> [!NOTE]
> O DocumentDB oferece suporte à indexação automática de Points, Polygons e LineStrings
> 
> 

O trecho JSON a seguir mostra uma política de indexação com indexação espacial habilitada, ou seja, qualquer ponto GeoJSON encontrado em documentos para consultas espaciais do índice. Se você estiver modificando a política de indexação usando o Portal do Azure, poderá especificar o JSON a seguir para a política de indexação para habilitar a indexação espacial em sua coleção.

**JSON da política de indexação da coleção com espacial habilitado para pontos e Polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Veja um trecho de código no .NET que mostra como criar uma coleção com indexação espacial ativado para todos os caminhos que contenham pontos. 

**Criar uma coleção com indexação espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E veja como você pode modificar uma coleção existente para aproveitar a indexação espacial em quaisquer pontos armazenados em documentos.

**Modificar uma coleção existente com indexação espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Se o valor GeoJSON de localização no documento estiver malformado ou inválido, então ele não será indexado para consultas espaciais. Você pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.
> 
> Caso sua definição de coleção inclua uma chave de partição, o andamento da transformação de indexação não será relatado. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você já aprendeu como começar a usar o suporte geoespacial no Banco de Dados de Documentos, poderá:

* Começar a codificar com os [exemplos de código geoespacial .NET no Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Pratique com as consultas geoespaciais no [Query Playground do Banco de Dados de Documentos](http://www.documentdb.com/sql/demo#geospatial)
* Saiba mais sobre [consultas do Banco de Dados de Documentos](documentdb-sql-query.md)
* Saiba mais sobre [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md)




<!--HONumber=Nov16_HO3-->



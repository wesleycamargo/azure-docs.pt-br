---
title: "Unidades de solicitação e estimativa de taxa de transferência - Azure DocumentDB | Microsoft Docs"
description: "Saiba mais sobre como entender, especificar e estimar os requisitos de unidades de solicitação no Banco de Dados de Documentos."
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6185c703e9148c71d9995b92540b8ea72fba5cc0
ms.lasthandoff: 04/18/2017


---
# <a name="request-units-in-documentdb"></a>Unidades de Solicitação no Banco de Dados de Documentos
Agora disponível: [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner)do Banco de Dados de Documentos. Saiba mais em [Estimativa das necessidades de produção](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de produtividade][5]

## <a name="introduction"></a>Introdução
O [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados NoSQL para documentos JSON totalmente escalável e gerenciável. Com o Banco de Dados de Documentos, você não precisa alugar máquinas virtuais, implantar software ou monitorar banco de dados. O Banco de Dados de Documentos é operado e continuamente monitorado por engenheiros da Microsoft para a melhor fornecer disponibilidade, desempenho e proteção aos dados do cliente. Os dados no DocumentDB são armazenados dentro de coleções, que são contêineres elásticos e altamente disponíveis. Em vez de gerenciar e pensar sobre os recursos de hardware, como CPU, memória e IOPs de uma coleção, você pode reservar a taxa de transferência em termos de solicitações por segundo. O DocumentDB gerencia automaticamente o provisionamento, o particionamento transparente e o dimensionamento da sua coleção para fornecer o número provisionado de solicitações. 

O DocumentDB oferece suporte a uma série de APIs para leituras, gravações, consultas e execuções de procedimentos armazenados. Como nem todas as solicitações são iguais, eles recebem um valor normalizado de **unidades de solicitação** com base na quantidade de computação necessária para atender à solicitação. O número de unidades de solicitação de uma operação é determinístico, e você pode acompanhar o número de unidades de solicitação consumidas por qualquer operação no DocumentDB por meio de um cabeçalho de resposta.

Cada coleção no DocumentDB pode ser reservada com taxa de transferência e expressada em termos de unidades de solicitação. Isso é manifestado em blocos de 100 unidades de solicitação por segundo, que variam de centenas até milhões de unidades de solicitação por segundo. A produtividade provisionada pode ser ajustada durante toda a vida útil de uma coleção para se adaptar às necessidades de processamento em constante mudança e aos padrões de acesso do aplicativo. 

Após ler este artigo, você poderá responder as perguntas a seguir:  

* O que são unidades de solicitação e solicitações de encargos?
* Como especificar a capacidade da unidade de solicitação para uma coleção?
* Como estimar as necessidades de unidades de solicitação de meu aplicativo?
* O que acontecerá se eu exceder a capacidade da unidade de solicitação de uma coleção?

## <a name="request-units-and-request-charges"></a>Unidades de solicitação e solicitações de encargos
O DocumentDB e a API para MongoDB fornecem desempenho rápido e previsível *reservando* recursos para atender às necessidades de produtividade do aplicativo.  Como os padrões de carga e acesso do aplicativo mudam com o tempo, o Banco de Dados de Documentos permite que você aumente ou diminua facilmente a quantidade da taxa de transferência reservada disponível para o aplicativo.

Com o Banco de Dados de Documentos, a taxa de transferência reservada é especificada em termos de unidades de solicitação processadas por segundo.  Você pode considerar as unidades de solicitação como a moeda de produtividade, com as quais você *reserva* uma quantidade de unidades de solicitação garantidas disponíveis para o aplicativo por segundo.  Cada operação no Banco de Dados de Documentos (gravar um documento, fazer uma consulta, atualizar um documento) consome CPU, memória e IOPS.  Ou seja, cada operação resulta em um *encargo de solicitação*, que é expressa em *unidades de solicitação*.  Ao entender os fatores que afetam os encargos de unidade de solicitação e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo da maneira mais econômica possível. 

É recomendável começar assistindo ao vídeo a seguir, no qual Aravind Ramachandran explica as unidades de solicitação e o desempenho previsível com o DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-documentdb"></a>Especificação da capacidade da unidade de solicitação no DocumentDB
Ao criar uma coleção no DocumentDB, você pode especificar o número de unidades de solicitação por segundo (RU por segundo) que deseja reservar para a coleção. Com base na taxa de transferência provisionada, o DocumentDB aloca partições físicas para hospedar sua coleção e, em seguida, divide e redistribui dados em partições conforme a demanda.

O DocumentDB requer uma chave de partição a ser especificada quando uma coleção é provisionada com 10.000 unidades de solicitação ou superior. Uma chave de partição também é necessária para dimensionar a taxa de transferência da coleção superior a 10.000 unidades de solicitação no futuro. Portanto, é altamente recomendável configurar uma [chave de partição](documentdb-partition-data.md) durante a criação de uma coleção, independentemente da taxa de transferência inicial. Como os dados podem estar divididos em várias partições, é necessário escolher uma chave de partição que tem uma cardinalidade alta (100s para milhões de valores distintos) para que a coleção e solicitações podem ser dimensionadas uniformemente com DocumentDB. 

> [!NOTE]
> Uma chave de partição é um limite lógico e não físico. Portanto, não é necessário limitar o número de valores de chave de partição distinta. Na verdade, já que o DocumentDB tem mais opções de balanceamento de carga, é melhor ter mais valores de chave de partição distintos do que menos.

Segue um trecho de código para criar uma coleção com 3.000 unidades de solicitação por segundo usando o SDK do .NET:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

O DocumentDB opera em um modelo de reserva na taxa de transferência. Ou seja, você será cobrado pela quantidade de taxa de transferência *reservada* da coleção, independentemente do quanto da taxa de transferência estiver em *uso*. No entanto, à medida que a carga, os dados e os padrões de uso do aplicativo forem alterados, você poderá escalar para mais e menos a quantidade de RUs reservadas por meio de SDKs do DocumentDB ou usando o [Portal do Azure](https://portal.azure.com).

Cada coleção é mapeada para um recurso `Offer` no DocumentDB, que tem metadados sobre a taxa de transferência provisionada da coleção. Para alterar a taxa de transferência alocada, procure o recurso de oferta correspondente para uma coleção e, em seguida, atualize-o com o novo valor de taxa de transferência. Aqui está um trecho de código para alterar a taxa de transferência de uma coleção para 5.000 unidades de solicitação por segundo usando o SDK do .NET:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Não há nenhum impacto sobre a disponibilidade de sua coleção quando você altera a taxa de transferência. Normalmente, a nova taxa de transferência reservada se torna eficaz em segundos no aplicativo da nova taxa de transferência.

## <a name="specifying-request-unit-capacity-in-api-for-mongodb"></a>Especificação da capacidade da unidade de solicitação na API para MongoDB
A API para MongoDB permite que você especifique o número de unidades de solicitação por segundo (RU por segundo) que deseja reservar para a coleção.

A API para o MongoDB opera no mesmo modelo de reserva com base em taxa de transferência que o DocumentDB. Ou seja, você será cobrado pela quantidade de taxa de transferência *reservada* da coleção, independentemente do quanto da taxa de transferência estiver em *uso*. No entanto, à medida que a carga, os dados e os padrões de uso do aplicativo forem alterados, você poderá escalar para mais e menos a quantidade de RUs reservadas por meio do [Portal do Azure](https://portal.azure.com).

Não há nenhum impacto sobre a disponibilidade de sua coleção quando você altera a taxa de transferência. Normalmente, a nova taxa de transferência reservada se torna eficaz em segundos no aplicativo da nova taxa de transferência.

## <a name="request-unit-considerations"></a>Considerações sobre unidades de solicitação
Ao estimar o número de unidades de solicitação a serem reservadas para a coleção do Banco de Dados de Documentos, é importante considerar as seguintes variáveis:

* **Tamanho do documento**. À medida que os tamanhos de documento aumentam, as unidades consumidas para ler ou gravar os dados também aumentam.
* **Contagem de propriedades do documento**. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento aumentarão conforme a contagem de propriedades aumentar.
* **Consistência de dados**. Ao serem usados níveis de consistência de dados Strong ou Bounded Staleness, unidades adicionais serão consumidas para ler documentos.
* **Propriedades indexadas**. Uma política de indexação em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas ou habilitando a indexação lenta.
* **Indexação de documentos**. Por padrão, cada documento é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus documentos.
* **Padrões de consulta**. A complexidade de uma consulta afeta a quantidade de Unidades de Solicitação que são consumidas para uma operação. O número de predicados, a natureza dos predicados, as projeções, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.
* **Uso de scripts**.  Assim como ocorre com as consultas, os procedimentos armazenados e os gatilhos consomem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

## <a name="estimating-throughput-needs"></a>Estimativa das necessidades de produção
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler (por meio de self link ou id) um único documento JSON de 1 KB que consiste em 10 valores de propriedade exclusivos (excluindo as propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo documento consumirá mais processamento do serviço e, assim, mais unidades de solicitação.   

> [!NOTE]
> A linha de base de uma unidade de solicitação para um documento de 1 KB corresponde a um GET simples por self link ou id do documento.
> 
> 

Por exemplo, veja uma tabela que mostra quantas unidades de solicitação provisionar em três tamanhos de documento diferentes (1 KB, 4 KB e 64 KB) e em dois níveis de desempenho diferentes (500 leituras/segundo + 100 gravações/segundo e 500 leituras/segundo + 500 gravações/segundo). A consistência dos dados foi configurada em Sessão e a política de indexação foi definida como Nenhuma.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do documento</strong></p></td>
            <td valign="top"><p><strong>Leituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Gravações/segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitação</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1.350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4.150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9.800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29.000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Usar a calculadora de unidade de solicitação
Para ajudar os clientes a ajustar as estimativas de produtividade, há uma [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) baseada na Web que ajuda a fazer uma estimativa dos requisitos de unidade de solicitação para operações comuns, incluindo:

* Criações de documento (gravações)
* Leituras de documento
* Exclusões de documento
* Atualizações do documento

A ferramenta também inclui suporte para estimar as necessidades de armazenamento de dados com base em documentos de exemplo que você fornecer.

Usar a ferramenta é simples:

1. Carregue um ou mais documentos JSON representativos.
   
    ![Carregar documentos na calculadora de unidade de solicitação][2]
2. Para estimar os requisitos de armazenamento de dados, insira o número total de documentos que você espera armazenar.
3. Insira o número necessário de operações criar, ler e excluir operações que você requeira (por segundo). Para calcular as alterações da unidade de solicitação relacionadas às operações de atualização do documento, carregue uma cópia do documento de exemplo da etapa 1 acima que inclui atualizações típicas de campo.  Por exemplo, se as atualizações de documento normalmente modificam duas propriedades chamadas lastLogin e userVisits, em seguida, simplesmente copie o documento de exemplo, atualize os valores para essas duas propriedades e carregue o documento copiado.
   
    ![Inserir requisitos de produtividade na calculadora de unidade de solicitação][3]
4. Clique em Calcular e examinar os resultados.
   
    ![Resultados da calculadora de unidade de solicitação][4]

> [!NOTE]
> Se você tiver tipos de documento que sejam muito diferentes em termos de tamanho e número de propriedades indexadas, carregue uma amostra da cada *tipo* do documento típico na ferramenta e calcule os resultados.
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>Usar o cabeçalho de resposta do encargo de solicitação do Banco de Dados de Documentos
Todas as respostas do serviço do DocumentDB incluem um cabeçalho personalizado (`x-ms-request-charge`) que contém as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos SDKs do Banco de Dados de Documentos. No SDK .NET, RequestCharge é uma propriedade do objeto ResourceResponse.  Para consultas, o Gerenciador de Consultas do Banco de Dados de Documentos no portal do Azure fornece informações sobre solicitações de encargo para consultas executadas.

![Análise de encargos de RU no Gerenciador de Consultas][1]

Tendo isso em mente, um método para estimar a quantidade de taxa de transferência reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um documento representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.  Também meça e inclua consultas típicas e o uso de scripts do Banco de Dados de Documentos.

> [!NOTE]
> Se você tiver tipos de documento que sejam muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidade de solicitação de operação aplicável associado a cada *tipo* de documento típico.
> 
> 

Por exemplo:

1. Registre o encargo de unidade de solicitação para a criação (inserção) de um documento típico. 
2. Registre o encargo de unidade de solicitação para a leitura de um documento típico.
3. Registre o encargo de unidade de solicitação para a atualização de um documento típico.
4. Registre o encargo de unidade de solicitação de consultas de documento comuns e típicos.
5. Registre o encargo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, gatilhos, funções definidas pelo usuário) utilizados pelo aplicativo
6. Calcule as unidades de solicitação necessárias, dado o número estimado de operações que você prevê que executará por segundo.

### <a id="GetLastRequestStatistics"></a>Usar o comando GetLastRequestStatistics da API para MongoDB
A API para MongoDB dá suporte a um comando personalizado, *getLastRequestStatistics*, para recuperar a carga de solicitação de operações especificadas.

Por exemplo, no Shell do Mongo, execute a operação para a qual você deseja verificar a carga de solicitação.
```
> db.sample.find()
```

Em seguida, execute o comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Tendo isso em mente, um método para estimar a quantidade de taxa de transferência reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um documento representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.

> [!NOTE]
> Se você tiver tipos de documento que sejam muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidade de solicitação de operação aplicável associado a cada *tipo* de documento típico.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Usar as métricas de portal da API para MongoDB
A maneira mais simples de obter uma boa estimativa dos encargos da unidade de solicitação para seu banco de dados da API para MongoDB é usar as métricas do [Portal do Azure](https://portal.azure.com). Com os gráficos *Número de solicitações* e *Custo da Solicitação*, você pode obter uma estimativa de quantas unidades de solicitação cada operação está consumindo, e quantas unidades de solicitação elas consomem com relação às outras operações.

![Métricas do portal da API para MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Um exemplo de estimativa de unidade de solicitação
Considere o seguinte documento de aproximadamente 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Os documentos são reduzidos no Banco de Dados de Documentos, assim, o tamanho do documento acima calculado pelo sistema é um pouco menor que 1 KB.
> 
> 

A tabela a seguir mostra os encargos de unidade de solicitação aproximados para operações típicas nesse documento (o encargo de unidade de solicitação aproximado pressupõe que o nível de consistência de conta seja definido como "Sessão" e que todos os documentos estejam indexados automaticamente):

| Operação | Encargo de Unidade de Solicitação |
| --- | --- |
| Criar documento |Cerca de 15 RUs |
| Ler documento |Cerca de 1 RU |
| Consultar documento por id |Cerca de 2,5 RUs |

Adicionalmente, a tabela mostra os encargos de unidade de solicitação aproximados para consultas típicas usadas no aplicativo:

| Consultar | Encargo de Unidade de Solicitação | # Número de Documentos Retornados |
| --- | --- | --- |
| Selecionar alimentos por id |Cerca de 2,5 RUs |1 |
| Selecionar alimentos por fabricante |Cerca de 7 RUs |7 |
| Selecionar por grupo de alimentos e solicitar por peso |Cerca de 70 RUs |100 |
| Selecionar os 10 alimentos principais em um grupo de alimentos |Cerca de 10 RUs |10 |

> [!NOTE]
> Os encargos de RU variam com base no número de documentos retornados.
> 
> 

Com essas informações, podemos estimar os requisitos de RU para o aplicativo, dado o número de operações e consultas que esperamos por segundo:

| Operação/consulta | Número estimado por segundo | RUs necessárias |
| --- | --- | --- |
| Criar documento |10 |150 |
| Ler documento |100 |100 |
| Selecionar alimentos por fabricante |25 |175 |
| Selecionar por grupo de alimentos |10 |700 |
| Selecionar os 10 principais |15 |Total de 150 |

Nesse caso, esperamos um requisito de taxa de transferência médio de 1.275 RUs/s.  Arredondando para a centena mais próxima, vamos provisionar 1.300 RUs/s para a coleção desse aplicativo.

## <a id="RequestRateTooLarge"></a> Exceder os limites de taxa de transferência reservada no DocumentDB
Lembre-se de que o consumo de unidades de solicitação é avaliado como uma taxa por segundo. Para aplicativos que ultrapassam a taxa de unidades solicitação provisionada para uma coleção, as solicitações a essa coleção são limitadas até que a taxa caia para baixo do nível reservado. Quando ocorre uma restrição, o servidor encerra preventivamente a solicitação com RequestRateTooLargeException (código de status HTTP 429) e retorna o cabeçalho x-ms-retry-after-ms, indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se estiver usando as consultas do SDK do cliente .NET e LINQ, em seguida, na maioria das vezes, você nunca precisará lidar com essa exceção, pois a versão atual do SDK do Cliente .NET captura implicitamente essa resposta, respeita o cabeçalho retry-after especificado pelo servidor e tenta novamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente acima da taxa de solicitação, o comportamento de repetição padrão poderá não ser suficiente e o cliente lançará uma DocumentClientException com o código de status 429 para o aplicativo. Em casos como esse, você pode considerar a manipulação do comportamento de repetição e a lógica nas rotinas de tratamento de erro do aplicativo ou o aumento da taxa de transferência reservada para a coleção.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Exceder os limites de taxa de transferência reservada na API para MongoDB
Os aplicativos que ultrapassarem as unidades de solicitação provisionadas para uma coleção serão limitados até que a taxa caia para baixo do nível reservado. Quando ocorrer uma limitação, o back-end terminará preventivamente a solicitação com um código de erro *16500* - *Muitas Solicitações*. Por padrão, a API para MongoDB repetirá automaticamente até 10 vezes antes de retornar um código de erro *Muitas Solicitações*. Se você estiver recebendo códigos de erro *Muitas Solicitações*, considere a repetição nas rotinas de manipulação de erro de seu aplicativo ou [aumentar a taxa de transferência reservada para a coleção](documentdb-set-throughput.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a produtividade reservada com os bancos de dados do Banco de Dados de Documentos do Azure, explore estes recursos:

* [Definição de preços no Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modelando dados no Banco de Dados de Documentos](documentdb-modeling-data.md)
* [Níveis de desempenho do Banco de Dados de Documentos](documentdb-partition-data.md)

Para saber mais sobre o Banco de Dados de Documentos, veja a [documentação](https://azure.microsoft.com/documentation/services/documentdb/)do Banco de Dados de Documentos do Azure. 

Para começar com os testes de desempenho e escala com o DocumentDB, confira [Teste de desempenho e escalabilidade com o Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png


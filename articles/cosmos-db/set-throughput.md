---
title: Provisionar a produtividade do Azure Cosmos DB | Microsoft Docs
description: Saiba como definir a produtividade provisionada para contêineres, coleções, grafos e tabelas do Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 99cd7fe6f9f46ff4d6dbbf6a6e024b3b32679724
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444252"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Definir e obter a taxa de transferência de contêineres e banco de dados do Microsoft Azure Cosmos DB

Você pode definir a produtividade de um contêiner do Azure Cosmos DB ou de um conjunto de contêineres usando o portal do Azure ou usando os SDKs do cliente. 

**Taxa de transferência da provisão para um contêiner individual:** Quando você provisiona a produtividade para um conjunto de contêineres, todos esses contêineres compartilham a produtividade provisionada. O provisionamento da produtividade para contêineres individuais garantirá a reserva de produtividade para esse contêiner específico. Ao atribuir RU/s no nível do contêiner individual, os contêineres podem ser criados como *fixos* ou *ilimitados*. Contêineres de tamanho fixo têm um limite máximo de 10 GB e taxa de transferência de 10.000 RU/s. Para criar um contêiner ilimitado, você deve especificar uma taxa de transferência mínima de 1.000 RU/s e uma [chave de partição](partition-data.md). Uma vez que seus dados talvez precisem ser divididos em várias partições, é necessário selecionar uma chave de partição que tenha alta cardinalidade (100 milhões de valores distintos). Ao selecionar uma chave de partição com muitos valores distintos, você garante que seu contêiner/tabela/gráfico e solicitações possam ser colocados em escala de maneira uniforme pelo Azure Cosmos DB. 

**Taxa de transferência de provisionamento para uma definição de contêineres ou um banco de dados:** o provisionamento da produtividade para um banco de dados permite compartilhar a produtividade entre todos os contêineres que pertencem a esse banco de dados. Dentro de um banco de dados do Azure Cosmos DB, você pode ter um conjunto de contêineres que compartilham a produtividade, bem como contêineres, que têm produtividade dedicada. Ao atribuir RU/s em um conjunto de contêineres, os contêineres pertencentes a esse conjunto são tratados como contêineres *ilimitados* e devem especificar uma chave de partição.

Com base na produtividade provisionada, o Azure Cosmos DB alocará partições físicas para hospedar seu(s) contêiner(es) e divide/redistribui os dados entre partições conforme eles aumentam. O provisionamento de produtividade no nível do contêiner e no nível do banco de dados são ofertas separadas e mudar entre elas requer a migração de dados de origem para destino. Isso significa que você precisa criar um novo banco de dados ou uma nova coleção e, em seguida, migrar os dados usando a [biblioteca do executor em massa](bulk-executor-overview.md) ou o [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). A imagem a seguir ilustra o provisionamento de taxa de transferência em níveis diferentes:

![Unidades de solicitação de provisionamento para contêineres individuais e conjunto de contêineres](./media/request-units/provisioning_set_containers.png)

Nas próximas seções, você apenderá as etapas necessárias para configurar a produtividade em diferentes níveis de uma conta do Microsoft Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Provisionar a produtividade usando o portal do Azure

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Provisionar a produtividade para um contêiner (coleção/gráfico/tabela)

1. Entre no [Portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo, selecione **Todos os recursos** e localizar sua conta do Azure Cosmos DB.  
3. Você pode configurar a produtividade durante a criação de um contêiner (coleção, gráfico, tabela) ou a produtividade de atualização para um contêiner existente.  
4. Para atribuir a produtividade durante a criação de um contêiner, abra a folha **Data Explorer** e selecione **Nova coleção** (novo gráfico, nova tabela para outras APIs)  
5. Preencha o formulário na folha **Adicionar coleção**. Os campos nessa folha estão descritos na tabela a seguir:  

   |**Configuração**  |**Descrição**  |
   |---------|---------|
   |ID do banco de dados  |  Forneça um nome exclusivo para identificar o banco de dados. O banco de dados é um contêiner lógico de uma ou mais coleções. Os nomes de banco de dados devem conter de 1 a 255 caracteres e não podem conter /, \\, #, ?, ou um espaço à direita. |
   |ID da coleção  | Forneça um nome exclusivo para identificar a coleção. As IDs de coleção têm os mesmos requisitos de caracteres que os nomes de banco de dados. |
   |Capacidade de armazenamento   | Esse valor representa a capacidade de armazenamento do banco de dados. Ao provisionar a produtividade para uma coleção individual, a capacidade de armazenamento pode ser **fixa (10 GB)** ou **ilimitada**. A capacidade de armazenamento ilimitada exige que você defina uma chave de partição para seus dados.  |
   |Throughput   | Cada coleção e banco de dados podem ter a produtividade em unidades de solicitação por segundo.  Para capacidade de armazenamento fixa, a produtividade mínima é 400 unidades de solicitação por segundo (RU/s); para capacidade de armazenamento ilimitado, a produtividade mínima é definida como 1000 RU/s.|

6. Depois de inserir valores para esses campos, selecione **OK** para salvar as configurações.  

   ![Definir a produtividade para uma coleção](./media/set-throughput/set-throughput-for-container.png)

7. Para atualizar a produtividade para um contêiner existente, expanda o banco de dados e o contêiner e, em seguida, clique em **Configurações**. Na nova janela, digite o novo valor de produtividade e, em seguida, selecione **Salvar**.  

   ![Atualizar a produtividade para uma coleção](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Provisionar a produtividade para um conjunto de contêineres ou no nível do banco de dados

1. Entre no [Portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo, selecione **Todos os recursos** e localizar sua conta do Azure Cosmos DB.  
3. Você pode configurar a produtividade durante a criação de um banco de dados ou atualizar a produtividade para um banco de dados existente.  
4. Para atribuir a produtividade durante a criação de um banco de dados, abra a folha do **Data Explorer** e selecione **Novo banco de dados**  
5. Preencher o valor **id de banco de dados**, marque a opção **Provisionar produtividade** e configure o valor da produtividade. Um banco de dados pode ser provisionado com o valor de produtividade mínima 50.000 RU/s.  

   ![Definir a produtividade com a opção novo banco de dados](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Para atualizar a produtividade para um banco de dados existente, expanda o banco de dados e o contêiner e, em seguida, clique em **Dimensionar**. Na nova janela, digite o novo valor de produtividade e, em seguida, selecione **Salvar**.  

   ![Atualizar a produtividade para um banco de dados](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Provisionar a produtividade para um conjunto de contêineres, bem como para um contêiner individual em um banco de dados

1. Entre no [Portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo, selecione **Todos os recursos** e localizar sua conta do Azure Cosmos DB.  
3. Crie um banco de dados e atribua a produtividade para ele. Abra a folha **Data Explorer** e selecione **Novo banco de dados**  
4. Preencher o valor **id de banco de dados**, marque a opção **Provisionar produtividade** e configure o valor da produtividade. Um banco de dados pode ser provisionado com o valor de produtividade mínima 50.000 RU/s.  

   ![Definir a produtividade com a opção novo banco de dados](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Em seguida, crie uma coleção no banco de dados criado na etapa acima. Para criar uma coleção, clique com botão direito no banco de dados e selecione **Nova coleção**.  

6. Na folha **Adicionar coleção**, insira um nome para a coleção e chave de partição. Opcionalmente, você pode provisionar a produtividade para o contêiner específico se você optar por não atribuir um valor de produtividade, a produtividade atribuída ao banco de dados é compartilhada na coleção.  

   ![Opcionalmente, defina a produtividade para o contêiner](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Considerações ao provisionamento de produtividade

Abaixo estão algumas considerações para ajudá-lo a decidir sobre a estratégia de reserva de produtividade.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Considerações ao provisionamento de produtividade no nível de banco de dados

Considere provisionar a produtividade no nível de banco de dados (ou seja, para o conjunto de contêineres) nos seguintes casos:

* Se você tiver uma dúzia ou mais de contêineres que podem compartilhar a produtividade em alguns ou todos eles.  

* Quando você estiver migrando de um banco de dados de único locatário que foi projetado para ser executado em VMs hospedadas IaaS ou locais (por exemplo, bancos de dados relacionais ou NoSQL) para o Azure Cosmos DB e tem vários contêineres.  

* Se você quiser considerar picos não planejados de cargas de trabalho usando a produtividade em pool no nível do banco de dados.  

* Em vez configurar a produtividade em um contêiner individual, você está interessado em obter a produtividade agregada em um conjunto de contêineres no banco de dados.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Considerações ao provisionamento de produtividade no nível de contêiner

Considere provisionar a produtividade em um contêiner individual nos seguintes casos:

* Se você tiver um número menor de contêineres do Azure Cosmos DB.  

* Se você deseja obter a produtividade garantida em um determinado contêiner apoiado por SLA.

## <a name="throughput-ranges"></a>Intervalos de produtividade

A tabela a seguir lista a produtividade disponível para cada contêiner:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contêiner de partição única</strong></p></td>
            <td valign="top"><p><strong>Contêiner particionado</strong></p></td>
            <td valign="top"><p><strong>Conjunto de contêineres</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>1.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>50.000 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Definir a produtividade usando a API do SQL do .NET

### <a name="set-throughput-at-the-container-level"></a>Definir a taxa de transferência no nível do contêiner
Segue um trecho de código para criar um contêiner com 3.000 unidades de solicitação por segundo para um contêiner individual usando SDK do .NET da API do SQL:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-at-the-for-a-set-of-containers-or-at-the-database-level"></a>Definir a taxa de transferência para um conjunto de contêineres ou no nível do banco de dados

Segue um trecho de código para provisionar 100.000 unidades de solicitação por segundo em um conjunto de contêineres usando SDK do .NET da API do SQL:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

O Microsoft Azure Cosmos DB opera em um modelo de reserva na produtividade. Ou seja, você será cobrado pela quantidade de produtividade *reservada*, independentemente do quanto da produtividade estiver em *uso*. À medida que a carga, os dados e os padrões de uso do aplicativo mudarem, você poderá aumentar ou reduzir verticalmente o número de RUs reservadas por meio de SDKs ou usando o [Portal do Azure](https://portal.azure.com).

Cada contêiner, ou conjunto de contêineres, é mapeado para um recurso `Offer` no Azure Cosmos DB, que tem metadados sobre a produtividade provisionada. Altere a produtividade alocada procurando o recurso de oferta correspondente de um contêiner e, em seguida, atualizando-o com o novo valor de produtividade. Aqui está um trecho de código para alterar a taxa de transferência de um contêiner para 5.000 unidades de solicitação por segundo usando o SDK do .NET. Após alterar a taxa de transferência, atualize todas as janelas existentes do portal do Azure para a taxa de transferência alterada a ser exibida. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Não há nenhum impacto sobre a disponibilidade do contêiner, ou conjunto de contêineres, quando a produtividade é alterada. Normalmente, a nova taxa de transferência reservada se torna eficaz em segundos no aplicativo da nova taxa de transferência.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Para definir a taxa de transferência usando a API do SQL do Java

O trecho de código a seguir recupera a taxa de transferência atual e o altera para 500 RU/s. Para obter um exemplo de código completo, consulte o arquivo [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) no GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Obter a taxa de transferência com o uso comando GetLastRequestStatistics da API do MongoDB

A API MongoDB dá suporte a um comando personalizado *getLastRequestStatistics*, para recuperar o encargo de solicitação para operações determinadas.

Por exemplo, no shell do Mongo, execute a operação para a qual você deseja verificar a carga de solicitação.
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

Um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um item representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidades de solicitação da operação aplicável associado a cada *tipo* de item típico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obter a taxa de transferência usando métricas de do portal API do MongoDB

A maneira mais simples de obter uma boa estimativa de encargos da unidade de solicitação para o banco de dados de API MongoDB é usar as métricas do [Portal do Azure](https://portal.azure.com). Com os gráficos *Número de solicitações* e *Custo da Solicitação*, você pode obter uma estimativa de quantas unidades de solicitação cada operação está consumindo, e quantas unidades de solicitação elas consomem com relação às outras operações.

![Métricas do portal da API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Exceder os limites de taxa de transferência reservados na API MongoDB
Aplicativos que excedem a taxa de transferência para um contêiner ou um conjunto de contêineres serão limitados até que a taxa fique abaixo do nível da taxa de transferência provisionado. Quando ocorrer uma limitação, o back-end terminará a solicitação com um `16500`código de erro - `Too Many Requests`. Por padrão, a API MongoDB tentará novamente até 10 vezes antes de retornar um `Too Many Requests` código de erro. Se você estiver recebendo muitos códigos de erro `Too Many Requests` códigos de erro, considere adicionar uma repetição de lógico em suas rotinas de manuseio de erro do aplicativo ou [ aumentar a taxa e transferência provisionada para o contêiner](set-throughput.md).

## <a name="throughput-faq"></a>Perguntas frequentes sobre taxa de transferência

**Posso definir minha taxa de transferência com valor menor que 400 RU/s?**

400 RU/s é a taxa de transferência mínima disponível nos contêineres de partição única do Azure Cosmos DB (1000 RU/s é a mínima para contêineres particionados). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando um método econômico para desenvolver e testar o Cosmos DB, poderá usar o [Emulador do Azure Cosmos DB](local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

**Como defino a produtividade usando a API do MongoDB?**

Não há nenhuma extensão de API do MongoDB para definir a produtividade. A recomendação é usar a API do SQL, conforme mostrado em [Para definir a taxa de transferência usando a API do SQL do .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como estimar as unidades de solicitação e produtividade, consulte [Solicitar unidades e estimar produtividade no Azure Cosmos DB](request-units.md)

* Para saber mais sobre o provisionamento e atingir uma escala mundial com o Cosmos DB, consulte [Particionamento e escala com o Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png

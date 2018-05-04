---
title: Provisionar a produtividade do Azure Cosmos DB | Microsoft Docs
description: Saiba como definir a produtividade provisionada para contêineres, coleções, grafos e tabelas do Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Definir e obter a taxa de transferência de contêineres do Microsoft Azure Cosmos DB

Você pode definir a produtividade dos contêineres do Azure Cosmos DB no portal do Azure ou usando os SDKs do cliente. 

A tabela a seguir lista a produtividade disponível para cada contêiner:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contêiner de partição única</strong></p></td>
            <td valign="top"><p><strong>Contêiner particionado</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>1000 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir a taxa de transferência usando o portal do Azure

1. Em uma nova janela, abra o [portal do Azure](https://portal.azure.com).
2. Na barra esquerda, clique em **Azure Cosmos DB** ou em **Todos os serviços** na parte inferior, role até **Bancos de Dados** e, em seguida, clique em **Azure Cosmos DB**.
3. Selecione sua conta do Cosmos DB.
4. Na nova janela, clique em **Data Explorer** no menu de navegação.
5. Na nova janela, expanda o banco de dados e o contêiner e, em seguida, clique em **Dimensionamento e Configurações**.
6. Na nova janela, digite o novo valor de produtividade na caixa **Produtividade** e, em seguida, clique em **Salvar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Para definir a taxa de transferência usando a API do SQL do .NET

O trecho de código a seguir recupera a taxa de transferência atual e o altera para 500 RU/s. Para o exemplo de código completo, consulte o projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) no GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Para definir a taxa de transferência usando a API do SQL do Java

O trecho de código a seguir recupera a taxa de transferência atual e o altera para 500 RU/s. Para obter um exemplo de código completo, consulte o arquivo [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) no GitHub. 

```Java
// find offer associated with this collection
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

Tendo isso em mente, um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um item representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidades de solicitação da operação aplicável associado a cada *tipo* de item típico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Obter a taxa de transferência usando métricas de do portal API do MongoDB

A maneira mais simples de obter uma boa estimativa de encargos da unidade de solicitação para o banco de dados de API MongoDB é usar as métricas do [Portal do Azure](https://portal.azure.com). Com os gráficos *Número de solicitações* e *Custo da Solicitação*, você pode obter uma estimativa de quantas unidades de solicitação cada operação está consumindo, e quantas unidades de solicitação elas consomem com relação às outras operações.

![Métricas do portal da API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Exceder os limites de taxa de transferência reservados na API MongoDB
Aplicativos que excedem a taxa de transferência para um contêiner serão limitados até que a taxa fique abaixo do nível da taxa de transferência provisionado. Quando ocorrer uma limitação, o back-end terminará preventivamente a solicitação com um código de erro `16500`- `Too Many Requests`. Por padrão, a API MongoDB tentará novamente até 10 vezes antes de retornar um `Too Many Requests` código de erro. Se você estiver recebendo muitos códigos de erro `Too Many Requests` códigos de erro, considere adicionar uma repetição de lógico em suas rotinas de manuseio de erro do aplicativo ou [ aumentar a taxa e transferência provisionada para o contêiner](set-throughput.md).

## <a name="throughput-faq"></a>Perguntas frequentes sobre taxa de transferência

**Posso definir minha taxa de transferência com valor menor que 400 RU/s?**

400 RU/s é a taxa de transferência mínima disponível nos contêineres de partição única do Azure Cosmos DB (1000 RU/s é a mínima para contêineres particionados). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando um método econômico para desenvolver e testar o Cosmos DB, poderá usar o [Emulador do Azure Cosmos DB](local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

**Como defino a produtividade usando a API do MongoDB?**

Não há nenhuma extensão de API do MongoDB para definir a produtividade. A recomendação é usar a API do SQL, conforme mostrado em [Para definir a taxa de transferência usando a API do SQL do .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e atingir uma escala mundial com o Cosmos DB, consulte [Particionamento e escala com o Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
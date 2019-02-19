---
title: Como implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho no Azure Cosmos DB
description: Saiba como implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 9033a7502919c8dc05053048272f3fa62f81b31d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118790"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Como implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho

O Azure Cosmos DB oferece cinco níveis de consistência bem definidos para sua escolha visando equilibrar a compensação entre consistência, desempenho e disponibilidade. A coerência forte garante que os dados sejam replicados de forma síncrona e a durabilidade mantida em todas as regiões com as quais a conta do Cosmos foi configurada. Embora essa configuração forneça o mais alto nível de durabilidade, ela ocorre em detrimento do desempenho e da disponibilidade. Se um aplicativo desejar controlar/diminuir o controle da durabilidade dos dados de acordo com a necessidade do aplicativo sem comprometer a disponibilidade, ele poderá empregar a sincronização personalizada na camada de aplicativo para atingir o nível desejado de durabilidade.

O diagrama abaixo ilustra o modelo de sincronização personalizada.

![Sincronização personalizada](./media/how-to-custom-synchronization/custom-synchronization.png)

Neste cenário, um contêiner do Cosmos é replicado globalmente em várias regiões e vários continentes. O uso da coerência forte em todas as regiões neste cenário afetará o desempenho. Para garantir um nível mais alto de durabilidade dos dados sem comprometer a latência de gravação, o aplicativo poderá usar dois clientes que compartilham o mesmo token de sessão.

O primeiro cliente pode gravar os dados na região local (por exemplo, Oeste dos EUA). O segundo cliente (por exemplo, no Leste dos EUA) é um cliente de leitura usado para garantir a sincronização. Com o fluxo do token de sessão da resposta de gravação para a leitura a seguir, a leitura garantirá a sincronização de gravações para o Leste dos EUA. O Azure Cosmos DB garantirá que as gravações sejam vistas por, pelo menos, uma região e tenham a garantia de sobreviver a uma interrupção regional, caso a região de gravação original fique inativa. Nesse cenário, todas as gravações são sincronizadas para o Leste dos EUA, reduzindo a latência de empregar a coerência forte em todas as regiões. Em um cenário de vários mestres, em que as gravações ocorrem em todas as regiões, esse modelo pode ser estendido para sincronização em várias regiões em paralelo.

## <a name="configure-the-clients"></a>Configurar os clientes

A amostra abaixo descreve a camada de acesso a dados, criando uma instância de dois clientes para essa finalidade.

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementar sincronização personalizada

Depois que os clientes forem inicializados, o aplicativo poderá executar gravações na região local (Oeste dos EUA) e poderá forçar a sincronização das gravações para o Leste dos EUA, conforme mostrado a seguir.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Esse modelo pode ser estendido para sincronização em várias regiões em paralelo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a distribuição global e a consistência no Azure Cosmos DB, leia os seguintes artigos:

* [Escolhendo o nível de consistência correto no Azure Cosmos DB](consistency-levels-choosing.md)

* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)

* [Como gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md)

* [Particionamento e distribuição de dados no Azure Cosmos DB](partition-data.md)

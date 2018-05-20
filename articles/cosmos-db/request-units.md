---
title: Unidades de solicitação e estimativa de produtividade – Azure Cosmos DB | Microsoft Docs
description: Saiba mais sobre como entender, especificar e estimar os requisitos de unidades de solicitação no Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 0aa87aeaf852d7309c29c1298e326c101a944904
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é o multimodelo de banco de dados distribuído globalmente da Microsoft. Com o Azure Cosmos DB, você não precisa alugar máquinas virtuais, implantar softwares ou monitorar bancos de dados. O Azure Cosmos DB é operado e continuamente monitorado pelos melhores engenheiros da Microsoft para fornecer disponibilidade, desempenho e proteção de dados da mais alta qualidade. Você pode acessar seus dados usando as APIs de sua escolha, como [SQL API](documentdb-introduction.md), [API do MongoDB](mongodb-introduction.md), [API de Tabela](table-introduction.md) e gráfico por meio da [API do Graph](graph-introduction.md) – todos têm suporte nativo. 

A moeda do Azure Cosmos DB é a RU **(Unidade de Solicitação)**. Com RUs, você não precisa reservar capacidades de leitura/gravação nem provisionar CPU, memória e IOPS. O Azure Cosmos DB dá suporte a uma série de APIs com operações diferentes que variam de leituras e gravações simples a consultas de grafo complexas. Como nem todas as solicitações são iguais, elas são atribuídas a uma quantidade normalizada de **unidades de solicitação** com base na quantidade de computação necessária para atender à solicitação. O número de unidades de solicitação de uma operação é determinístico e você pode acompanhar o número de unidades de solicitação consumidas por uma operação no Azure Cosmos DB por meio de um cabeçalho de resposta. 

Para fornecer um desempenho previsível, você precisa reservar a produtividade em unidades de 100 RUs/segundo. É possível [estimar suas necessidades de taxa de transferência](request-units.md#estimating-throughput-needs) usando a calculadora da unidade de solicitação do [Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

![Calculadora de produtividade][5]

Após ler este artigo, você poderá responder as perguntas a seguir:  

* O que são unidades de solicitação e solicitações de encargos no Microsoft Azure Cosmos DB?
* Como especificar a capacidade da unidade de solicitação para um contêiner ou conjunto de contêineres do Microsoft Azure Cosmos DB?
* Como estimar as necessidades de unidades de solicitação de meu aplicativo?
* O que acontece se eu exceder a capacidade da unidade de solicitação para um contêiner ou conjunto de contêiners do Microsoft Azure Cosmos DB?

Como o Microsoft Azure Cosmos DB é um banco de dados de vários modelos, é importante observar que este artigo é aplicável a todos os modelos de dados e APIs no Microsoft Azure Cosmos DB. Este artigo usa termos genéricos como *contêiner* e um *item* para referenciar genericamente a uma coleção, gráfico, ou uma tabela e um documento, um nó ou uma entidade, respectivamente.

## <a name="request-units-and-request-charges"></a>Unidades de solicitação e solicitações de encargos
O Azure Cosmos DB fornece desempenho rápido e previsível *reservando* recursos para atender às necessidades de produtividade do aplicativo.  Como os padrões de carga e acesso do aplicativo mudam com o tempo, o Azure Cosmos DB permite que você aumente ou diminua facilmente a quantidade de produtividade reservada disponível para o aplicativo.

Com o Azure Cosmos DB, a produtividade reservada é especificada em termos de processamento de unidades de solicitação por segundo. Você pode considerar as unidades de solicitação como a moeda de produtividade, com as quais você *reserva* uma quantidade de unidades de solicitação garantidas disponíveis para o aplicativo por segundo.  Cada operação do Azure Cosmos DB – gravar um documento, realizar uma consulta, atualizar um documento – consome CPU, memória e IOPS.  Ou seja, cada operação resulta em um *encargo de solicitação*, que é expressa em *unidades de solicitação*.  Ao entender os fatores que afetam os encargos de unidade de solicitação e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo da maneira mais econômica possível. O Data Explorer no portal do Azure também é uma ferramenta excelente para testar o núcleo de uma consulta.

É recomendável que você assista ao vídeo a seguir, no qual o Gerente de Programa do Azure Cosmos DB, Andrew Liu, discute as unidades de solicitação.

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolamento de taxa de transferência em bancos de dados distribuído globalmente

Quando o banco de dados é replicado em mais de uma região, o Azure Cosmos DB fornece isolamento de taxa de transferência para garantir que o uso de RU em uma região não afete o uso de RU em outra região. Por exemplo, se você gravar dados em uma região e ler dados de outra região, as RUs usadas para executar a operação de gravação na região *A* não removem as RUs usadas para a operação de leitura na região *B*. As RUs não são divididas entre as regiões implantadas. Cada região onde o banco de dados é replicado tem o número total de RUs provisionada. Para obter mais informações sobre replicação global, consulte [Como distribuir os dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considerações sobre unidades de solicitação
Ao estimar o número de unidades de solicitação para provisão, é importante considerar as seguintes variáveis:

* **Tamanho do item**. Conforme o número de unidades de solicitação aumenta, as unidades consumidas para ler ou gravar os dados também aumentam.
* **Contagem de propriedades do item**. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento/nó/entidade aumentam conforme a contagem de propriedades aumenta.
* **Consistência de dados**. Ao usar os níveis de consistência de dados como Forte ou Desatualização Limitada, unidades adicionais são consumidas para ler os itens.
* **Propriedades indexadas**. Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação para operações de gravação, limitando o número de propriedades indexadas ou habilitando a indexação lenta.
* **Indexação de documentos**. Por padrão, cada item é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus itens.
* **Padrões de consulta**. A complexidade de uma consulta afeta a quantidade de unidades de solicitação consumida para uma operação. O número de resultados de consulta, o número de predicados, a natureza dos predicados, as projeções, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.
* **Uso de scripts**.  Assim como ocorre com as consultas, os procedimentos armazenados e os gatilhos consomem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

## <a name="estimating-throughput-needs"></a>Estimativa das necessidades de produção
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler (por meio de autolink ou ID) um único item de 1 KB que consiste em 10 valores de propriedade exclusivos (excluindo as propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo item consumirá mais processamento do serviço e, assim, mais unidades de solicitação.   

> [!NOTE]
> A linha de base de uma unidade de solicitação para um item de 1 KB corresponde a um GET simples por autolink ou ID do item.
> 
> 

Por exemplo, aqui está uma tabela que mostra quantas unidades de solicitação devem ser provisionadas para itens com três tamanhos de item diferentes (1 KB, 4 KB e 64 KB) e em dois níveis de desempenho diferentes (500 leituras/segundo + 100 gravações/segundo e 500 leituras/segundo + 500 gravações/segundo). A consistência dos dados foi configurada em *Sessão*, e a política de indexação foi definida como *Nenhuma*.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do item</strong></p></td>
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
Para ajudar os clientes a ajustar as estimativas de produtividade, há uma [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) baseada na Web que ajuda estimar os requisitos de unidade de solicitação para operações comuns, incluindo:

* Criações de itens (gravações)
* Leituras de itens
* Exclusões de itens
* Atualizações de itens

A ferramenta também inclui suporte para estimar as necessidades de armazenamento de dados com base nos itens de exemplo fornecidos.

Usar a ferramenta é simples:

1. Carregue um ou mais itens representativos (por exemplo, um documento JSON).
   
    ![Carregar itens na calculadora de unidades de solicitação][2]
2. Para estimar os requisitos de armazenamento de dados, insira o número total de itens (por exemplo, documentos, linhas ou vértices) que você espera armazenar.
3. Insira o número de operações de criação, leitura e atualização e exclusão necessário (por segundo). Para estimar as cobranças da unidade de solicitação das operações de atualização de itens, carregue uma cópia do item de exemplo da etapa 1 acima que inclui atualizações típicas de campo.  Por exemplo, se as atualizações de itens normalmente modificarem duas propriedades chamadas l*lastLogin* e *userVisits*, copie o item de exemplo, atualizar os valores dessas duas propriedades e carregar o item copiado.
   
    ![Inserir requisitos de produtividade na calculadora de unidade de solicitação][3]
4. Clique em Calcular e examinar os resultados.
   
    ![Resultados da calculadora de unidade de solicitação][4]

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, carregue uma amostra da cada *tipo* do item típico na ferramenta e, depois, calcule os resultados.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usar o cabeçalho de resposta de encargos da solicitação do Azure Cosmos DB
Todas as respostas do serviço Azure Cosmos DB incluem um cabeçalho personalizado (`x-ms-request-charge`) que contém as unidades de solicitação consumidas para uma determinada solicitação. Esse cabeçalho também está acessível por meio dos SDKs do Azure Cosmos DB. No SDK do .NET, `RequestCharge` é uma propriedade do `ResourceResponse` objeto.  Para consultas, o Data Explorer do Azure Cosmos DB no portal do Azure fornece informações de encargos de solicitação para as consultas executadas.

Tendo isso em mente, um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um item representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.  Também meça e inclua consultas típicas e o uso de scripts do Azure Cosmos DB.

> [!NOTE]
> Se você tiver tipos de itens muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidade de solicitação da operação aplicável associado a cada *tipo* de item típico.
> 
> 

Por exemplo: 

1. Registre o encargo de unidades de solicitação para a criação (inserção) de um item típico. 
2. Registre o encargo de unidades de solicitação para a leitura de um item típico.
3. Registre o encargo de unidades de solicitação para a atualização de um item típico.
4. Registre o encargo de unidades de solicitação para consultas de itens comuns e típicos.
5. Registre o encargo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, gatilhos, funções definidas pelo usuário) utilizados pelo aplicativo
6. Calcule as unidades de solicitação necessárias, dado o número estimado de operações que você prevê que executará por segundo.

## <a name="a-request-unit-estimate-example"></a>Um exemplo de estimativa de unidade de solicitação
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
> Os documentos são reduzidos no Azure Cosmos DB, portanto, o tamanho calculado pelo sistema do documento acima é um pouco menor que 1 KB.
> 
> 

A seguinte tabela mostra os encargos de unidades de solicitação aproximados para operações típicas nesse item (o encargo de unidades de solicitação aproximado pressupõe que o nível de consistência de conta seja definido como *Sessão* e que todos os itens sejam indexados automaticamente):

| Operação | Encargo de Unidade de Solicitação |
| --- | --- |
| Criar item |Cerca de 15 RUs |
| Ler item |Cerca de 1 RU |
| Consultar item por ID |Cerca de 2,5 RUs |

Adicionalmente, a tabela mostra os encargos de unidade de solicitação aproximados para consultas típicas usadas no aplicativo:

| Consultar | Encargo de Unidade de Solicitação | Nº de itens retornados |
| --- | --- | --- |
| Selecionar alimentos por id |Cerca de 2,5 RUs |1 |
| Selecionar alimentos por fabricante |Cerca de 7 RUs |7 |
| Selecionar por grupo de alimentos e solicitar por peso |Cerca de 70 RUs |100 |
| Selecionar os 10 alimentos principais em um grupo de alimentos |Cerca de 10 RUs |10 |

> [!NOTE]
> Os encargos de RU irão variar com base no número de itens retornados.
> 
> 

Com essas informações, é possível estimar os requisitos de RU para esse aplicativo, dado o número de operações e consultas que você espera por segundo:

| Operação/consulta | Número estimado por segundo | RUs necessárias |
| --- | --- | --- |
| Criar item |10 |150 |
| Ler item |100 |100 |
| Selecionar alimentos por fabricante |25 |175 |
| Selecionar por grupo de alimentos |10 |700 |
| Selecionar os 10 principais |15 |Total de 150 |

Nesse caso, esperamos um requisito de taxa de transferência média de 1.275 RUs/s.  Arredondando para a centena mais próxima, vamos provisionar 1.300 RUs/s para o contêiner (ou conjunto de contêineres) desse aplicativo.

## <a id="RequestRateTooLarge"></a> Excedendo os limites de produtividade reservada no Azure Cosmos DB
Lembre-se de que o consumo de unidades de solicitação é avaliado como uma taxa por segundo. Para aplicativos que excedem a taxa das unidades de solicitação provisionada, serão limitados até que a taxa fique abaixo do nível de rendimento provisionado. Quando ocorre uma restrição com taxa limitada, o servidor encerra por preempção a solicitação com `RequestRateTooLargeException` (código de status HTTP 429) e retorna o cabeçalho `x-ms-retry-after-ms`, indicando o tempo, em milissegundos, que o usuário deve aguardar antes de repetir a solicitação.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se estiver usando as consultas do SDK do cliente .NET e LINQ, em seguida, na maioria das vezes, você nunca precisará lidar com essa exceção, pois a versão atual do SDK do Cliente .NET captura implicitamente essa resposta, respeita o cabeçalho retry-after especificado pelo servidor e tenta novamente a solicitação automaticamente. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente acima da taxa de solicitação, o comportamento de repetição padrão poderá não ser suficiente e o cliente lançará um `DocumentClientException`com o código de status 429 para o aplicativo. Em casos como esse, considere a manipulação do comportamento de repetição e da lógica nas rotinas de tratamento de erro do aplicativo ou o aumento da taxa de transferência provisionada para o contêiner (ou conjunto de contêineres).

## <a name="next-steps"></a>Próximas etapas
 
Para saber mais sobre como definir e obter a taxa de transferência usando o Portal e o SDK do Azure, consulte:

* [Definir e obter a taxa de transferência no Azure Cosmos DB](set-throughput.md)

Para saber mais sobre a produtividade reservada com bancos de dados do Azure Cosmos DB, conheça estes recursos:

* [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Particionando dados no Azure Cosmos DB](partition-data.md)

Para saber mais sobre o Azure Cosmos DB, consulte a [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do Azure Cosmos DB. 

Para obter uma introdução aos testes de escala e desempenho com o Azure Cosmos DB, consulte [Testes de desempenho e escala com o Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png


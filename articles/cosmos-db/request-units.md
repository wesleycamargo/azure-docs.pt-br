---
title: Unidades de solicitação e estimando a taxa de transferência - banco de dados do Azure Cosmos | Microsoft Docs
description: Saiba mais sobre como entender, especificar e estimar os requisitos de unidades de solicitação no Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rimman
ms.openlocfilehash: 66beeb2cc724f75d17a4c155f1cdb888153e8fbf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286758"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades no banco de dados do Azure Cosmos de solicitação

[Banco de dados do Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) é o Microsoft Global multimodel banco de dados distribuído. Com o Azure Cosmos DB, você não precisa alugar máquinas virtuais, implantar softwares ou monitorar bancos de dados. Banco de dados do Azure Cosmos é operado e continuamente monitorado pelos engenheiros da Microsoft superiores para fornecer proteção de dados, desempenho e disponibilidade de classe internacional. Você pode acessar seus dados por meio de APIs de sua escolha, como o [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), e [tabela](table-introduction.md) APIs e gráfico por meio de [Gremlin API](graph-introduction.md). Todas as APIs de todos os nativamente têm suporte. 

A moeda do Azure Cosmos DB é a RU *(Unidade de Solicitação)*. Com unidades de solicitação, você não precisa reservar capacidade de leitura/gravação ou provisão da CPU, memória e IOPS. Banco de dados do Azure Cosmos dá suporte a várias APIs que têm diferentes operações, desde simples lê e grava a consultas complexas de gráfico. Porque nem todas as solicitações forem iguais, as solicitações são atribuídas a uma quantidade normalizada de unidades de solicitação com base na quantidade de computação necessária para atender à solicitação. O número de unidades de solicitação para uma operação é determinístico. Você pode controlar o número de unidades de solicitação que são consumidos por qualquer operação no banco de dados do Azure Cosmos por meio de um cabeçalho de resposta. 

Para fornecer um desempenho previsível, reserve a taxa de transferência em unidades de 100 RU/segundo. É possível [estimar suas necessidades de taxa de transferência](request-units.md#estimating-throughput-needs) usando a calculadora da unidade de solicitação do [Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

![Calculadora de produtividade][5]

Após ler este artigo, você poderá responder as perguntas a seguir:

* O que são unidades de solicitação e solicitações de encargos no Microsoft Azure Cosmos DB?
* Como especificar a capacidade da unidade de solicitação para um contêiner ou conjunto de contêineres do Microsoft Azure Cosmos DB?
* Como estimar as necessidades de unidades de solicitação de meu aplicativo?
* O que acontece se eu exceder a capacidade da unidade de solicitação para um contêiner ou conjunto de contêiners do Microsoft Azure Cosmos DB?

Como o banco de dados do Azure Cosmos é um banco de dados multimodel, é importante observar que este artigo é aplicável a todos os modelos de dados e APIs no banco de dados do Azure Cosmos. Este artigo usa termos genéricos como *contêiner* para referenciar genericamente uma coleção ou um gráfico e *item* para referenciar uma tabela, o documento, o nó ou a entidade genericamente.

## <a name="request-units-and-request-charges"></a>Unidades de solicitação e solicitações de encargos

Banco de dados do Azure Cosmos rápida, oferece um desempenho previsível ao reservar recursos para atender às necessidades de produção do seu aplicativo. Padrões de carga e acesso de aplicativo alterado ao longo do tempo. Banco de dados do Azure Cosmos pode ajudá-lo a facilmente aumentar ou diminuir a quantidade de produtividade reservados disponível para seu aplicativo.

Com o banco de dados do Azure Cosmos, taxa de transferência reservada é especificada em termos de unidade de solicitação de processamento por segundo. Você pode pensar em solicitação como taxa de transferência. Você reserva um número de unidades de solicitação garantidas para estar disponível para sua inscrição em uma base por segundo. Cada operação no Azure Cosmos DB, incluindo escrevendo um documento, executar uma consulta e atualização de um documento, consome CPU, memória e IOPS. Ou seja, cada operação incorre em uma taxa de solicitação, que é expressa em unidades de solicitação. Quando você entende os fatores que afetam as cobranças da unidade de solicitação e os requisitos de taxa de transferência do seu aplicativo, você pode executar o aplicativo da maneira mais econômica possível. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolamento de taxa de transferência em bancos de dados distribuído globalmente

Se você replicar o banco de dados para mais de uma região, o banco de dados do Azure Cosmos fornece isolamento de taxa de transferência para garantir o uso de unidade de solicitação em uma região não afeta o uso de unidade de solicitação em outra região. Por exemplo, se você gravar dados em uma região e ler dados de outra região, as unidades de solicitação usadas para executar a operação de gravação na região A não tiram as unidades de solicitação usadas para a operação de leitura na região B. As unidades de solicitação não estão divididas nas regiões nas quais você implantou seu banco de dados. Cada região na qual o banco de dados é replicado tem o número total de unidades de solicitação provisionado. Para obter mais informações sobre replicação global, consulte [Como distribuir os dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considerações sobre unidades de solicitação
Quando você calcular o número de unidades de solicitação para provisionar, é importante considerar as seguintes variáveis:

* **Tamanho do item**. Conforme o número de unidades de solicitação aumenta, as unidades consumidas para ler ou gravar os dados também aumentam.
* **Contagem de propriedades do item**. Supondo que a indexação de padrão de todas as propriedades, as unidades consumidas para gravar um aumento de documento, nó ou entidade como o aumento do número de propriedade.
* **Consistência de dados**. Quando você usa modelos de consistência de dados como forte ou desatualização limitada, unidades de solicitação adicionais são consumidas para ler itens.
* **Propriedades indexadas**. Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação para operações de gravação, limitando o número de propriedades indexadas ou habilitando a indexação lenta.
* **Indexação de documentos**. Por padrão, cada item é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus itens.
* **Padrões de consulta**. A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de consultas de resultados, o número de predicados, a natureza dos predicados, o número de funções definidas pelo usuário, o tamanho da fonte de dados, e projeções todos afetam o custo das operações de consulta.
* **Uso de scripts**. Assim como ocorre com as consultas, os procedimentos armazenados e os gatilhos consomem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolve seu aplicativo, verifique se o cabeçalho de carga de solicitação para entender melhor como cada operação consome a capacidade da unidade de solicitação.

## <a name="estimating-throughput-needs"></a>Estimativa das necessidades de produção
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única solicitação de unidade representa a capacidade de processamento necessária para ler (via self link ou ID) de um único item 1 KB que consiste em 10 valores de propriedade unique (excluindo as propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo item consome mais processamento do serviço e, portanto, requer mais unidades de solicitação. 

> [!NOTE]
> A linha de base de 1 unidade de solicitação para um item de 1 KB corresponde a um GET simples por link próprio ou ID do item.
> 
> 

Por exemplo, aqui está uma tabela que mostra quantas unidades de solicitação devem ser provisionadas para itens com três tamanhos de item diferentes (1 KB, 4 KB e 64 KB) e em dois níveis de desempenho diferentes (500 leituras/segundo + 100 gravações/segundo e 500 leituras/segundo + 500 gravações/segundo). Neste exemplo, a consistência de dados é definida como **sessão**, e a política de indexação é definida como **nenhum**.

| Tamanho do item | Leituras/segundo | Gravações/segundo | Unidades de solicitação
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1.000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3.000 RU/s
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1.350 RU/s
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4.150 RU/s
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9.800 RU/s
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29.000 RU/s


### <a name="use-the-request-unit-calculator"></a>Usar a calculadora de unidade de solicitação
Para ajudá-lo a ajustar suas estimativas de taxa de transferência, você pode usar um baseado na web [Calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner). A Calculadora pode ajudar sua estimativa os requisitos de unidade de solicitação para operações comuns, incluindo:

* Criações de itens (gravações)
* Leituras de itens
* Exclusões de itens
* Atualizações de itens

A ferramenta também inclui suporte para estimar as necessidades de armazenamento de dados com base nos itens de exemplo que você fornecer.

Para usar a ferramenta:

1. Carregue um ou mais itens representativos (por exemplo, um documento JSON).
   
    ![Carregar itens na calculadora de unidades de solicitação][2]
2. Para estimar os requisitos de armazenamento de dados, insira o número total de itens (por exemplo, documentos, linhas ou vértices) que você espera armazenar.
3. Insira o número de criação, leitura, atualização e operações de exclusão que você precisa (em uma base por segundo). Para estimar os encargos de unidade de solicitação de operações de atualização de item, carregue uma cópia do item de exemplo da etapa 1, o que inclui atualizações de campo típico. Por exemplo, se o item atualizações normalmente modificam duas propriedades chamadas *lastLogin* e *userVisits*, copiar um item de exemplo, atualize os valores para essas duas propriedades e, em seguida, carregar o item copiado.
   
    ![Inserir requisitos de produtividade na calculadora de unidade de solicitação][3]
4. Selecione **Calculate**e, em seguida, examine os resultados.
   
    ![Resultados da calculadora de unidade de solicitação][4]

> [!NOTE]
> Se você tiver tipos de item que diferem significativamente em termos de tamanho e o número de propriedades indexadas, carregue um exemplo de cada *tipo* de típico para a ferramenta do item e, em seguida, calcular os resultados.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usar o cabeçalho de resposta de encargos da solicitação do Azure Cosmos DB
Todas as respostas do serviço Azure Cosmos DB incluem um cabeçalho personalizado (`x-ms-request-charge`) que contém as unidades de solicitação consumidas para uma determinada solicitação. Você também pode acessar esse cabeçalho por meio de SDKs do Azure Cosmos banco de dados. No SDK .NET, **RequestCharge** é uma propriedade do objeto **ResourceResponse**. Para consultas, o Data Explorer do Azure Cosmos DB no portal do Azure fornece informações de encargos de solicitação para as consultas executadas. Para saber mais sobre como obter e taxa de transferência de conjunto usando APIs de vários modelos diferentes consulte [definir e obter a taxa de transferência de banco de dados do Azure Cosmos](set-throughput.md) artigo.

É um método para calcular a quantidade de produtividade reservados exigida por seu aplicativo registrar o custo de unidade de solicitação associado à execução de operações típicas em relação a um item de representante que é usado pelo seu aplicativo. Em seguida, calcule o número de operações que você antecipar para executar a cada segundo. Certifique-se de também medir e incluem consultas típicas e uso de script de banco de dados do Azure Cosmos.

> [!NOTE]
> Se você tiver tipos de item que diferem significativamente em termos de tamanho e o número de propriedades indexadas, registrar o encargo de unidade de solicitação de operação aplicável associado a cada *tipo* de item típico.
> 
> 

Por exemplo, estas são as etapas que você pode executar:

1. Registre o encargo de unidades de solicitação para a criação (inserção) de um item típico. 
2. Registre o encargo de unidades de solicitação para a leitura de um item típico.
3. Registre o encargo de unidades de solicitação para a atualização de um item típico.
4. Registre o encargo de unidades de solicitação para consultas de itens comuns e típicos.
5. Registre o custo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, disparadores, funções definidas pelo usuário) que o aplicativo usa.
6. Calcule as unidades de solicitação necessárias, dado o número estimado de operações que você prevê que executará por segundo.

## <a name="a-request-unit-estimate-example"></a>Um exemplo de estimativa de unidade de solicitação
Considere o seguinte documento, que é de aproximadamente 1 KB de tamanho:

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
> Documentos são minimizados no banco de dados do Azure Cosmos, portanto, o tamanho calculado pelo sistema do documento acima é um pouco menos de 1 KB.
> 
> 

A tabela a seguir mostra os encargos de unidade de solicitação aproximado para operações típicas neste item. (O custo de unidade de solicitação aproximado pressupõe que o nível de consistência de conta é definido como **sessão** e que todos os itens são indexados automaticamente.)

| Operação | Custos de unidade de solicitação |
| --- | --- |
| Criar item |Cerca de 15 RUs |
| Ler item |Cerca de 1 RU |
| Item de consulta por ID |Cerca de 2,5 RUs |

A tabela a seguir mostra a solicitação aproximada encargos de unidade para consultas típicas usadas no aplicativo:

| Consultar | Custos de unidade de solicitação | n º de itens retornados |
| --- | --- | --- |
| Selecione os alimentos por ID |Cerca de 2,5 RUs |1 |
| Selecionar alimentos por fabricante |Cerca de 7 RUs |7 |
| Selecionar por grupo de alimentos e solicitar por peso |Cerca de 70 RUs |100 |
| Selecionar os 10 alimentos principais em um grupo de alimentos |Cerca de 10 RUs |10 |

> [!NOTE]
> Encargos de unidade de solicitação variam com base no número de itens retornados.
> 
> 

Com essas informações, você pode estimar os requisitos de unidade de solicitação para esse aplicativo, dado o número de operações e consultas que você espera por segundo:

| Operação/consulta | Número estimado por segundo | Unidades de solicitação necessários |
| --- | --- | --- |
| Criar item |10 |150 |
| Ler item |100 |100 |
| Selecionar alimentos por fabricante |25 |175 |
| Selecionar por grupo de alimentos |10 |700 |
| Selecionar os 10 principais |15 |Total de 150 |

Nesse caso, você espera um requisito de taxa de transferência média do 1,275 RU/segundo. Arredondamento até 100 o mais próximo, você deve provisionar 1.300 RU/segundo para este aplicativo contêiner (ou conjunto de contêineres).

## <a id="RequestRateTooLarge"></a> Excedendo os limites de produtividade reservada no Azure Cosmos DB
Consumo de unidade de solicitação é avaliado em uma taxa por segundo. Para aplicativos que excederem a taxa de unidade de solicitação de provisionamento, solicitações são limitadas por taxa até que a taxa caia abaixo do nível de taxa de transferência fornecida. Quando uma solicitação é o limite de taxa, o servidor preventivamente termina a solicitação com `RequestRateTooLargeException` (código de status HTTP 429) e retorna o `x-ms-retry-after-ms` cabeçalho. O cabeçalho indica a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se você usar as consultas de SDK de cliente .NET e LINQ, na maioria das vezes, você nunca precisa lidar com essa exceção. A versão atual do SDK do cliente .NET implicitamente captura essa resposta, respeita o servidor especificado cabeçalho retry-after e repete automaticamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente cumulativamente operando acima a taxa de solicitação, o comportamento de repetição padrão poderá ser insuficiente e o cliente gera um `DocumentClientException` com código de status 429 ao aplicativo. Nesses casos, convém considerar lidar com o comportamento de repetição e lógica em rotinas de tratamento de erros do aplicativo ou aumentar a taxa de transferência provisionada para o contêiner (ou conjunto de contêineres).

## <a name="next-steps"></a>Próximas etapas
 
- Saiba como [definir e obter a taxa de transferência de banco de dados do Azure Cosmos](set-throughput.md) usando o portal do Azure e SDKs.
- Saiba mais sobre [desempenho e dimensionamento de teste com o banco de dados do Azure Cosmos](performance-testing.md).
- Para saber mais sobre a taxa de transferência reservada com bancos de dados do banco de dados do Azure Cosmos, consulte [preços do banco de dados do Azure Cosmos](https://azure.microsoft.com/pricing/details/cosmos-db/) e [o particionamento de dados no banco de dados do Azure Cosmos](partition-data.md).
- Para saber mais sobre o Azure Cosmos DB, consulte a documentação [ do Azure Cosmos DB ](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png


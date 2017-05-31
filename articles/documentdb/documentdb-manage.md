---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b9c8ef0343259d5a0adb2cefa9063f9d86edbc3c
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="storage-and-predictable-performance-provisioning-in-azure-cosmos-db"></a>Armazenamento e provisionamento de desempenho previsível no Azure Cosmos DB
O Azure Cosmos DB é um serviço de banco de dados NoSQL orientado a documentos, escalonável e totalmente gerenciado para documentos JSON. Com o Azure Cosmos DB, você não precisa alugar máquinas virtuais, implantar softwares ou monitorar bancos de dados. O Azure Cosmos DB é operado e continuamente monitorado pelos engenheiros da Microsoft para fornecer disponibilidade, desempenho e proteção de dados da mais alta qualidade.  

Comece a usar o Azure Cosmos DB [criando uma conta de banco de dados](documentdb-create-account.md) e, em seguida, criando [um banco de dados e uma coleção do Azure Cosmos DB](documentdb-create-collection.md) por meio do [portal do Azure](https://portal.azure.com/). Os bancos de dados do Azure Cosmos DB são oferecidos em unidades de armazenamento com suporte de SSD (unidade de estado sólido) e produtividade. Essas unidades de armazenamento são provisionadas com a criação de coleções de bancos de dados, e cada coleção tem uma taxa de transferência reservada que pode ser escalada verticalmente para cima ou para baixo, a qualquer momento, para atender às demandas do seu aplicativo.

Se seu aplicativo exceder a produtividade reservada para uma ou várias coleções, as solicitações serão limitadas por coleção. Isso significa que algumas solicitações do aplicativo podem ser bem-sucedidas enquanto outras são limitadas.

Este artigo fornece uma visão geral dos recursos e métricas disponíveis para gerenciar a capacidade e planejar o armazenamento de dados.

## <a name="database-account"></a>Conta de banco de dados
Como assinante do Azure, você pode provisionar uma ou mais contas de bancos de dados do Azure Cosmos DB para gerenciar recursos de banco de dados. Cada assinatura é associada uma única assinatura do Azure.

As contas do Azure Cosmos DB podem ser criadas por meio do [portal do Azure](documentdb-create-account.md) ou usando [um modelo do ARM ou a CLI do Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bancos de dados
Um banco de dados individual do Azure Cosmos DB pode conter praticamente uma quantidade ilimitada de armazenamento de documentos agrupada em coleções. As coleções fornecem isolamento de desempenho — cada coleção pode ser provisionada com produtividade que não é compartilhada com outras coleções no mesmo banco de dados ou conta. Um banco de dados do Azure Cosmos DB é elástico em termos de tamanho, variando de GBs a TBs de armazenamento de documentos com suporte de SSD e produtividade provisionada. Ao contrário de um banco de dados RDBMS tradicional, um banco de dados do Azure Cosmos DB não é vinculado a um único computador e pode abranger vários computadores ou clusters.  

Com o Azure Cosmos DB, conforme você precisar dimensionar seus aplicativos, poderá criar mais coleções ou bancos de dados ou ambos. Os bancos de dados podem ser criados por meio do [portal do Azure](documentdb-create-database.md) ou de um dos [SDKs do Azure Cosmos DB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Coleções de banco de dados
Cada banco de dados do Azure Cosmos DB pode conter uma ou mais coleções. As coleções atuam como partições de dados altamente disponíveis para processamento e armazenamento de documentos. Cada coleção pode armazenar documentos com esquema heterogêneo. As funcionalidades de consulta e indexação automática do Azure Cosmos DB permitem que você filtre e recupere documentos com facilidade. Uma coleção fornece o escopo para armazenamento de documentos e execução de consultas. Uma coleção também é um domínio de transação para todos os documentos contidos nele. As coleções são a produtividade alocada com base no valor definido no Portal do Azure ou pelos SDKs.

As coleções são particionadas automaticamente em um ou mais servidores físicos pelo Azure Cosmos DB. Ao criar uma coleção, você pode especificar a produtividade provisionada em termos de unidades de solicitação por segundo e uma propriedade de chave de partição. O valor dessa propriedade é usado pelo Azure Cosmos DB para distribuir documentos entre partições e encaminhar solicitações como consultas. O valor de chave de partição também atua como o limite de transação para procedimentos armazenados e gatilhos. Cada coleção tem uma quantidade reservada de produtividade específica, que não é compartilhada com outras coleções na mesma conta. Portanto, você pode escalar horizontalmente o aplicativo em termos de armazenamento e taxa de transferência.

As coleções podem ser criadas no [Portal do Azure](documentdb-create-collection.md) ou por meio de qualquer um dos [SDKs do DocumentDB](documentdb-sdk-dotnet.md).   

## <a name="request-units-and-database-operations"></a>Unidades de solicitação e operações de banco de dados
Ao criar uma coleção, você reserva a produtividade em termos de [RU (unidades de solicitação)](documentdb-request-units.md) por segundo. Em vez de gerenciar e pensar em recursos de hardware, você pode pensar em uma **unidade de solicitação** como uma única medida de recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo. Uma leitura de um documento de 1 KB consome a mesma 1 RU, independentemente do número de itens armazenados ou do número de solicitações simultâneas em execução ao mesmo tempo. Todas as solicitações no Banco de Dados de Documentos, incluindo operações complexas, como consultas SQL, têm um valor de RU previsível que pode ser determinado no momento do desenvolvimento. Se você souber o tamanho dos documentos e a frequência de cada operação (leituras, gravações e consultas) para oferecer suporte ao seu aplicativo, será possível provisionar a quantidade exata de produtividade para atender às necessidades do aplicativo e ajustar a escala do banco de dados para cima e para baixo à medida que o desempenho precisa mudar.

Cada coleção pode ser reservada com produtividade em blocos de 100 unidades de solicitação por segundo, de centenas a milhões de unidades de solicitação por segundo. A produtividade provisionada pode ser ajustada durante toda a vida útil de uma coleção para se adaptar às necessidades de processamento em constante mudança e aos padrões de acesso do aplicativo. Para obter mais informações, consulte [Níveis de desempenho do Azure Cosmos DB](documentdb-performance-levels.md).

> [!IMPORTANT]
> Coleções são entidades faturáveis. O custo é determinado pela produtividade provisionada da coleção medida nas unidades de solicitação por segundo com o armazenamento total consumido em gigabytes.
>
>

Quantas unidades de solicitação uma operação específica, como inserir, excluir, consultar ou uma execução de procedimento armazenado consome? Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. A leitura de um documento de 1 KB é de 1 RU, mas uma solicitação para inserir, substituir ou excluir o mesmo documento consumirá mais processamento do serviço e, portanto, mais unidades de solicitação. Cada resposta do serviço inclui um cabeçalho personalizado (`x-ms-request-charge`) que relata as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos [SDKs](documentdb-sdk-dotnet.md). No SDK .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) é uma propriedade do objeto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx). Se quiser estimar suas necessidades de produtividade antes de fazer uma chamada única, você pode usar o [planejador de capacidade](documentdb-request-units.md#estimating-throughput-needs) para ajudar com essa estimativa.

> [!NOTE]
> A linha de base de uma unidade de solicitação para um documento de 1 KB corresponde a um GET simples do documento com [Consistência de Sessão](documentdb-consistency-levels.md).
>
>

Há vários fatores que afetam as unidades de solicitação consumidas em uma operação em relação a uma conta de banco de dados do Azure Cosmos DB. Esses fatores incluem:

* Tamanho do documento. À medida que os tamanhos de documento aumentam, as unidades consumidas para ler ou gravar os dados também aumentam.
* Contagem de propriedades. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento aumentarão conforme a contagem de propriedades aumentar.
* Consistência de dados. Ao serem usados níveis de consistência de dados Strong ou Bounded Staleness, unidades adicionais serão consumidas para ler documentos.
* Propriedades indexadas. Uma política de indexação em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas.
* Indexação de documentos. Por padrão, cada documento é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus documentos.

Para obter mais informações, consulte [Unidades de solicitação do Azure Cosmos DB](documentdb-request-units.md).

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
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3.000 RU/s</p></td>
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

Consultas, procedimentos armazenados e gatilhos consumem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.  

## <a name="choice-of-consistency-level-and-throughput"></a>Escolha do nível de consistência e da taxa de transferência
A escolha do nível de consistência padrão tem impacto na taxa de transferência e na latência. Você pode definir o nível de consistência padrão programaticamente e através do portal do Azure. Você também pode substituir o nível de consistência com base em cada solicitação. Por padrão, o nível de consistência é definido como **Sessão**, que fornece leitura/gravação monotônica e lê suas garantias de gravação. A consistência Session é excelente para aplicativos centrados no usuário e fornece um equilíbrio ideal de compensações de consistência e desempenho.    

Para obter instruções sobre como alterar o nível de consistência no portal do Azure, consulte [Como gerenciar uma conta do Azure Cosmos DB](documentdb-manage-account.md#consistency). Ou, para obter mais informações sobre os níveis de consistência, consulte [Usando níveis de consistência](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Armazenamento de documentos provisionado e sobrecarga de índice
O Azure Cosmos DB dá suporte à criação de coleções de partição única e particionadas. Cada partição no Azure Cosmos DB dá suporte a até 10 GB de armazenamento com suporte de SSD. O armazenamento de documentos de 10 GB inclui os documentos e o armazenamento para o índice. Por padrão, uma coleção do Azure Cosmos DB está configurada para indexar automaticamente todos os documentos sem exigir explicitamente nenhum índice ou esquema secundário. Com base nos aplicativos que usam o Azure Cosmos DB, a sobrecarga de índice típica está entre 2 e 20%. A tecnologia de indexação usada pelo Azure Cosmos DB garante que, independentemente dos valores das propriedades, a sobrecarga de índice não excede mais de 80% do tamanho dos documentos com configurações padrão.

Por padrão, todos os documentos são indexados pelo Azure Cosmos DB automaticamente. No entanto, caso você deseje ajustar a sobrecarga de índice, poderá optar por remover determinados documentos da indexação no momento da inserção ou substituição de um documento, conforme descrito em [Políticas de indexação do Azure Cosmos DB](documentdb-indexing-policies.md). Você pode configurar uma coleção do Azure Cosmos DB para impedir que todos os documentos na coleção sejam indexados. Você também pode configurar uma coleção do Azure Cosmos DB para indexar seletivamente apenas determinadas propriedades ou caminhos com caracteres curinga de seus documentos JSON, conforme descrito em [Configurando a política de indexação de uma coleção](documentdb-indexing-policies.md#CustomizingIndexingPolicy). A exclusão de propriedades ou documentos também melhora a taxa de transferência de gravação – o que significa que você consumirá menos unidades de solicitação.   

## <a name="next-steps"></a>Próximas etapas
Para continuar aprendendo como o Azure Cosmos DB funciona, consulte [Particionamento e escala no Azure Cosmos DB](documentdb-partition-data.md).

Para obter instruções sobre como monitorar os níveis de desempenho no portal do Azure, consulte [Monitorar uma conta do Azure Cosmos DB](documentdb-monitor-accounts.md). Para obter mais informações sobre como escolher os níveis de desempenho para as coleções, consulte [Níveis de desempenho no Azure Cosmos DB](documentdb-performance-levels.md).


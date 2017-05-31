---
title: "Perguntas sobre o banco de dados do Azure Cosmos DB – Perguntas frequentes | Microsoft Docs"
description: "Obtenha respostas para perguntas frequentes sobre o Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente. Responda perguntas de banco de dados sobre capacidade, níveis de desempenho e dimensionamento."
keywords: Perguntas de banco de dados, perguntas frequentes, banco de dados de documentos, azure, Microsoft azure
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>Perguntas frequentes sobre o Azure Cosmos DB
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>Perguntas sobre banco de dados referentes aos conceitos básicos do Microsoft Azure Cosmos DB
### <a name="what-is-microsoft-azure-cosmos-db"></a>O que é o Microsoft Azure Cosmos DB?
O Microsoft Azure Cosmos DB é um serviço de multimodelo de banco de dados replicado globalmente que oferece consultas avançadas de dados sem esquemas, ajuda a fornecer um desempenho confiável e configurável e possibilita um desenvolvimento rápido – tudo isso por meio de uma plataforma gerenciada com suporte e o alcance do Microsoft Azure. O Azure Cosmos DB é a solução ideal para aplicativos Web, móveis, de jogos e IoT quando a produtividade previsível, alta disponibilidade, baixa latência e o modelo de dados sem esquemas são requisitos fundamentais. O Azure Cosmos DB fornece flexibilidade de esquemas e indexação avançada e inclui suporte a transações de vários documentos com JavaScript integrado.  

O DB Cosmos do Azure foi iniciado no final de 2010 para resolver pontos problemáticos de desenvolvedores, que eram enfrentados por aplicativos de grande escala na Microsoft. Como a criação de aplicativos distribuídos globalmente não é um problema exclusivo da Microsoft, tornamos o serviço disponível externamente para todos os desenvolvedores do Azure no formato do Azure DocumentDB. O BD Cosmos do Azure é a próxima grande melhoria na evolução do DocumentDB e agora o estamos disponibilizando para você usar. Como parte desta versão do BD Cosmos do Azure, os clientes do DocumentDB (com seus dados) se tornam automaticamente clientes do BD Cosmos do Azure. A transição é contínua e agora eles têm acesso a uma variedade maior de novas funcionalidades oferecidas pelo DB Cosmos do Azure. 

Para ver mais perguntas e respostas sobre banco de dados, além de instruções sobre como implantar e usar esse serviço, consulte a [página de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-happened-to-documentdb"></a>O que aconteceu com o DocumentDB?
A API do DocumentDB é uma das APIs e dos modelos de dados com suporte no Azure Cosmos DB. Além disso, o Azure Cosmos DB traz como suporte a API do Graph (versão prévia), a API de Tabela (versão prévia) e a API do MongoDB. 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Como fazer para acessar minha conta do DocumentDB no portal do Azure?
Basta clicar no ícone do Azure Cosmos DB no menu do lado esquerdo do portal do Azure. Se você já tinha uma conta do DocumentDB, agora você tem uma conta do Azure Cosmos DB, sem alterações na cobrança.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típicos do Azure Cosmos DB?
O Azure Cosmos DB é uma ótima escolha para novos aplicativos Web, móveis, de jogos e IoT, em que a escala automática, o desempenho previsível, tempos de resposta rápidos na ordem de milissegundos e a capacidade de consultar dados sem esquemas são importantes. O Azure Cosmos DB funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos.  Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Azure Cosmos DB](documentdb-use-cases.md).  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como o Azure Cosmos DB oferece um desempenho previsível?
Uma [unidade de solicitação](documentdb-request-units.md) é a medida de produtividade no Azure Cosmos DB. 1 RU corresponde à produtividade do GET de um documento de 1 KB. Toda operação no Azure Cosmos DB, incluindo leituras, gravações, consultas SQL e execuções de procedimentos armazenados, tem um valor determinístico de RU baseado na produtividade necessária para concluir a operação. Em vez de pensar em CPU, E/S, memória e como cada uma dessas medidas afeta a produtividade do seu aplicativo, você pode pensar em uma medida de RU única.

Cada contêiner do Azure Cosmos DB pode ser reservado com produtividade provisionada em termos de RUs da produtividade por segundo. Em aplicativos de qualquer escala, você pode usar um parâmetro de comparação em solicitações individuais para medir seus valores de RU e provisionar contêineres para manipular a soma total de unidades de solicitação em todas as solicitações. Também é possível aumentar ou reduzir verticalmente a produtividade de seu contêiner, conforme as necessidades do aplicativo mudam. Para obter mais informações sobre unidades de solicitação, bem como ajuda para determinar as necessidades do contêiner, leia [Estimativa das necessidades de produtividade](documentdb-request-units.md#estimating-throughput-needs) e experimente a [calculadora de produtividade](https://www.documentdb.com/capacityplanner). Contêineres aqui se referem à coleção da API do DocumentDB, aos gráficos da API do Graph, à coleção da API do MongoDB e às tabelas da API de Tabela.  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O Azure Cosmos DB está em conformidade com a HIPAA?
Sim, o Azure Cosmos DB está em conformidade com a HIPAA. A HIPAA estabelece os requisitos para o uso, a divulgação e a proteção de informações de integridade individualmente identificáveis. Para saber mais, confira o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento do Azure Cosmos DB?
Não há limite para a quantidade total de dados que um contêiner pode armazenar no Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de produtividade do Azure Cosmos DB?
Não há limite para a quantidade total de produtividade à qual um contêiner pode dar suporte no Azure Cosmos DB. O importante aqui é que a carga de trabalho precisa ser distribuída quase que de maneira uniforme entre um número suficientemente grande de chaves de partição.

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>Quanto custa o Microsoft Azure Cosmos DB?
Consulte a página de [detalhes de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/documentdb/) para obter detalhes. Os encargos pelo uso do Azure Cosmos DB são determinados pelo número de contêineres provisionados, pelo número de horas em que os contêineres estavam online e pela produtividade provisionada de cada contêiner. Contêineres aqui se referem à coleção da API do DocumentDB, aos gráficos da API do Graph, à coleção da API do MongoDB e às tabelas da API de Tabela. 

### <a name="is-there-a-free-account-available"></a>Existe uma conta gratuita disponível?
Se for novo no Azure, você poderá se inscrever para uma [conta gratuita do Azure](https://azure.microsoft.com/free/), que dá 30 dias e US$ 200 para experimentar todos os serviços do Azure. Ou então, se tiver uma assinatura do Visual Studio, você estará qualificado para receber [US$ 150 em créditos Azure gratuitos por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) a serem usados em qualquer serviço do Azure.  

Também use o [Emulador do Azure Cosmos DB](documentdb-nosql-local-emulator.md) para desenvolver e testar seu aplicativo no local, gratuitamente, sem criar uma assinatura do Azure. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador do Azure Cosmos DB, você poderá passar a usar uma conta do Azure Cosmos DB na nuvem.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como fazer para obter mais ajuda com o Azure Cosmos DB?
Caso precise de ajuda, contate-nos no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) ou no [fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) ou agende um chat individual com a equipe de engenharia do Azure Cosmos DB enviando um email para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com). 

## <a name="set-up-microsoft-azure-cosmos-db"></a>Configurar o Microsoft Azure Cosmos DB
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>Como fazer para se inscrever no Microsoft Azure Cosmos DB?
O Microsoft Azure Cosmos DB está disponível no Portal do Azure. Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure. Depois de se inscrever em uma assinatura do Microsoft Azure, você poderá adicionar uma conta da API do DocumentDB, API do Graph (versão prévia), API de Tabela (versão prévia) ou API do MongoDB à sua assinatura do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?
Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que possuem a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestra. A chave mestra primária e a secundária estão disponíveis na folha **Chaves** do [Portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](documentdb-manage-account.md#keys).

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>Quais são as regiões para as quais PreferredLocations pode ser definido? 
As regiões atuais válidas para PrefferredLocations são – “Oeste dos EUA”, “Centro-Oeste dos EUA”, “Oeste dos EUA 2”, “Leste dos EUA”, “Leste dos EUA 2”, “EUA Central”, “Centro-Sul dos EUA”, “Centro-Norte dos EUA”, “Europa Ocidental”, “Europa Setentrional”, “Ásia Oriental”, “Sudeste Asiático”, “Oeste do Japão”, “Leste do Japão”, “Sudeste da Austrália”, “Leste da Austrália”, “Índia Central”, “Sul da Índia”, “Índia Ocidental”, “Leste do Canadá”, “Canadá Central”, “Alemanha Central”, “Nordeste da Alemanha”, “Norte da China”, “Leste da China”, “Coreia do Sul”, “Coreia Central”, “Oeste do Reino Unido”, “Sul do Reino Unido”, “Sul do Brasil”, “USGov Arizona” e “USGov Texas”.

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>Há algo que devo saber ao distribuir dados pelo mundo todo por meio dos data centers do Azure? 
O Cosmos DB está presente em todas as regiões. Como ele é o serviço principal, cada novo Data center terá uma presença do Cosmos DB. Essa lista é documentada acima a partir de agora. Ao defini-las, você precisa se lembrar de que o Cosmos DB respeita nuvens soberanas e governamentais. Isso indica que, se você criar uma conta nessa região e desejar replicá-la, você não terá permissão para fazer isso. Da mesma forma, você não poderá acessar essas localizações para habilitar a replicação nessa região por meio de uma conta externa. 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>Vocês planejam fornecer mais opções de preços no futuro?
Sim. Hoje, o Cosmos DB fornece um modelo com otimização da produtividade. Planejamos fornecer mais modelos de preços ideais em um futuro próximo. 

 
# <a name="documentdb-api"></a>API do DocumentDB 
## <a name="database-questions-about-developing-against-documentdb-api"></a>Perguntas sobre banco de dados referentes ao desenvolvimento na API do DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>Como fazer para começar a desenvolver na API do DocumentDB?
A API do Microsoft DocumentDB está disponível no [Portal do Azure][azure-portal].  Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure.  Depois de se inscrever em uma assinatura do Microsoft Azure, você poderá adicionar o contêiner da API do DocumentDB à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Azure Cosmos DB, consulte [Criar uma conta de banco de dados do Azure Cosmos DB](documentdb-create-account.md). Se você já tinha uma conta do DocumentDB, agora terá uma conta do Azure Cosmos DB.  

[SDKs](documentdb-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java.  Os desenvolvedores também podem usar as [APIs HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com os recursos do Azure Cosmos DB em uma série de plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>Posso acessar algumas amostras prontas para obter uma vantagem?
Amostras dos SDKs do [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) e [Python](documentdb-python-samples.md) da API do DocumentDB estão disponíveis no GitHub.


### <a name="does-documentdb-api--database-support-schema-free-data"></a>O banco de dados da API do DocumentDB dá suporte a dados sem esquemas?
Sim, a API do DocumentDB permite que os aplicativos armazenem documentos JSON arbitrários sem dicas nem definições de esquema. Os dados ficam disponíveis imediatamente para consulta por meio da interface de consulta SQL do Azure Cosmos DB.   

### <a name="does-documentdb-api-support-acid-transactions"></a>A API do DocumentDB dá suporte a transações ACID?
Sim, a API do DocumentDB dá suporte a transações entre documentos expressas como procedimentos armazenados e gatilhos do JavaScript. As transações têm como escopo uma única partição em cada coleção e são executadas com semântica ACID como tudo ou nada, isoladas de outras solicitações de códigos e de usuários executadas simultaneamente.  Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida. Para saber mais sobre transações, confira [Transações de programa de banco de dados](documentdb-programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>O que é uma coleção?
Uma coleção é um grupo de documentos e sua lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](documentdb-performance-levels.md) é determinado pela produtividade e pelo armazenamento usados. As coleções podem abranger uma ou mais partições/servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados.

As coleções também são as entidades de cobrança do Azure Cosmos DB. Cada coleção é cobrada por hora com base na produtividade provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte [Preços da API do DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-create-a-database"></a>Como crio um banco de dados?
Crie bancos de dados usando o [Portal do Azure](), conforme descrito em [Criar uma coleção e um banco de dados do Azure Cosmos DB](documentdb-create-collection.md), um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Como configuro usuários e permissões?
Crie usuários e permissões usando um dos [SDKs da API do DocumentDB](documentdb-sdk-dotnet.md) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

### <a name="does-documentdb-api-support-sql"></a>A API do DocumentDB dá suporte ao SQL?
A linguagem de consulta SQL é um subconjunto avançado da funcionalidade de consulta com suporte no SQL. A linguagem de consulta SQL do Azure Cosmos DB fornece operadores hierárquicos e relacionais avançados e extensibilidade por meio de UDFs (funções definidas pelo usuário) baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore, o que é usado pelas técnicas de indexação automática e pelo dialeto de consulta SQL do Azure Cosmos DB.  Para obter detalhes sobre como usar a gramática SQL, consulte o artigo [QueryDocumentDB][query].

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>A API do DocumentDB dá suporte a funções de agregação SQL?
A API do DocumentDB dá suporte à agregação de baixa latência em qualquer escala por meio das funções de agregação `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` pela gramática SQL. Para saber mais, consulte [Funções de agregação](documentdb-sql-query.md#Aggregates).

### <a name="how-does-documentdb-api--provide-concurrency"></a>Como a API do DocumentDB fornece simultaneidade?
A API do DocumentDB dá suporte ao OCC (controle de simultaneidade otimista) por meio de marcações da entidade HTTP ou Etags. Cada recurso da API do DocumentDB tem uma Etag e a Etag é definida no servidor sempre que um documento é atualizado. O cabeçalho etag e o valor atual são incluídos em todas as mensagens de resposta. As etags podem ser usadas com o cabeçalho If-Match a fim de permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-Match é o valor de etag a ser comparado. Se o valor de etag corresponder ao valor de etag do servidor, o recurso será atualizado. Se a etag não for mais atual, o servidor rejeitará a operação com um código de resposta "HTTP 412 Falha de pré-condição". O cliente precisará refazer a buscar do recurso a fim de obter o valor de etag atual do recurso. Além disso, as etags podem ser usadas com o cabeçalho If-None-Match para determinar se é necessário realizar uma nova busca de um recurso.

Para usar a simultaneidade otimista em .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para obter um exemplo de .NET, confira [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no github.

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>Como fazer para executar transações na API do DocumentDB?
A API do DocumentDB dá suporte a transações integradas à linguagem por meio de procedimentos armazenados e gatilhos do JavaScript. Todas as operações de banco de dados dentro de scripts são executadas com escopo no isolamento de instantâneo para a coleção, se for uma coleção de única partição, ou documentos com o mesmo valor de chave de partição em uma coleção, se a coleção for particionada. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Consulte [Programação do lado do servidor da API do DocumentDB](documentdb-programming.md) para obter mais detalhes.

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>Como fazer para inserir documentos em massa na API do DocumentDB?
Há três maneiras de inserir documentos em massa no Azure Cosmos DB:

* A ferramenta de migração de dados, conforme descrito em [Importar dados para a API do DocumentDB](documentdb-import-data.md).
* O Gerenciador de Documentos no Portal do Azure, como descrito em [Adicionar documentos em massa com o Gerenciador de Documentos](documentdb-view-json-document-explorer.md#bulk-add-documents).
* Procedimentos armazenados, conforme descrito em [Programação do lado do servidor da API do DocumentDB](documentdb-programming.md).

### <a name="does-documentdb-api-support-resource-link-caching"></a>A API do DocumentDB dá suporte ao cache de link de recursos?
Sim. Como o Cosmos DB é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Os clientes da API do DocumentDB podem especificar um cabeçalho “If-None-Match” para leituras em qualquer recurso, como documento ou coleção e atualizar suas cópias locais somente quando a versão do servidor é alterada.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Uma instância local da API do DocumentDB está disponível?
Sim. O [Emulador do Azure Cosmos DB](documentdb-nosql-local-emulator.md) fornece uma emulação de alta fidelidade do serviço API do DocumentDB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Desenvolva e teste aplicativos usando o Emulador do Azure Cosmos DB e implante-os no Azure em escala global, apenas fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>Perguntas de banco de dados sobre como desenvolver na API para MongoDB
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>O que é a API para MongoDB do Azure Cosmos DB?
A API para MongoDB do Microsoft Azure Cosmos DB é uma camada de compatibilidade que permite aos aplicativos se comunicar com facilidade e transparência com o mecanismo de banco de dados nativo do Azure Cosmos DB usando as APIs e os drivers do Apache MongoDB existentes e com suporte na comunidade. Agora, os desenvolvedores podem usar as cadeias de ferramentas e competências existentes do MongoDB para criar aplicativos que utilizam o Azure Cosmos DB, beneficiando-se das funcionalidades exclusivas do Azure Cosmos DB, que incluem indexação automática, manutenção de backup, SLAs (contratos de nível de serviço) com suporte financeiro, etc.

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>Como posso me conectar ao meu banco de dados da API para MongoDB?
A maneira mais rápida de se conectar à API para MongoDB do Azure Cosmos DB é acessar o [portal do Azure](https://portal.azure.com). Navegue até sua conta. Na *navegação esquerda* da conta, clique em *Início Rápido*. *Início Rápido* é a melhor maneira de obter trechos de código para se conectar ao banco de dados. 

O Azure Cosmos DB impõe padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por *SSL*; portanto, use o TLSv1.2.

Para obter mais detalhes, veja [Conectar-se ao banco de dados da API para MongoDB](documentdb-connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Há códigos de erro adicionais para um banco de dados da API para MongoDB?
A API para o MongoDB tem seus próprios códigos de erro específicos, além dos códigos de erro comuns do MongoDB.


| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas excedeu a taxa de unidades de solicitação provisionada para a coleção e foi limitado. | Considere expandir a taxa de transferência da coleção no Portal do Azure ou tente novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação excedeu a alocação de memória do cliente. | Reduza o escopo da operação por meio de um critério de consulta mais restritivo ou contate o suporte no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>*Ex:  &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Davi"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>Perguntas sobre banco de dados referentes ao desenvolvimento com o Azure Cosmos DB: API de Tabela (versão prévia)

### <a name="terms"></a>Termos 
O Azure Cosmos DB: API de Tabela (versão prévia) refere-se à oferta premium do Azure Cosmos DB para o suporte a tabelas divulgado no Build 2017. 

O SDK de Tabela padrão é o SDK de Tabela do armazenamento do Azure pré-existente. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Como posso usar a nova oferta de API de Tabela (versão prévia)? 
A API de Tabela da Microsoft está disponível no [Portal do Azure][azure-portal].  Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure.  Depois de se inscrever em uma assinatura do Microsoft Azure, você poderá adicionar o contêiner da API de Tabela à sua assinatura do Azure.  
No período de versão prévia, os [SDKs](../cosmos-db/table-sdk-dotnet.md) estão disponíveis para o .NET. Conclua o início rápido da [API de Tabela](../cosmos-db/create-table-dotnet.md) para começar.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>É necessário ter um novo SDK para usar a API de Tabela (versão prévia)? 
Sim, um novo SDK na forma de um pacote NuGet está disponível aqui: [API de Tabela](../cosmos-db/table-sdk-dotnet.md). Ele é chamado SDK para o Armazenamento do Microsoft Azure 8.1.2 com a API de Tabela Premium (versão prévia).  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>Como fazer para fornecer comentários sobre o SDK e bugs?
Compartilhe seus comentários usando um destes métodos:

* [No UserVoice](https://feedback.azure.com/forums/263030-documentdb)
* [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Qual é a cadeia de conexão que preciso usar para me conectar à API de Tabela (versão prévia)?
A cadeia de conexão é `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`. Obtenha a cadeia de conexão na página Chaves do portal do Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>Como fazer para substituir as definições de configuração das opções de solicitação na nova API de Tabela (versão prévia)?
Essas configurações são documentadas em [Funcionalidades do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Altere as configurações adicionando-as ao app.config na seção appsettings do aplicativo cliente.
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>Há alguma alteração para os clientes que usam o SDK de Tabela Standard existente?
Nenhuma. Não há nenhuma alteração para clientes novos ou existentes que usam o SDK de Tabela Standard existente. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Como fazer para exibir dados de tabela que são armazenados no Azure Cosmos DB para uso com a API de Tabela (versão prévia)? 
Use o portal do Azure para procurar os dados. Use também o código da API de Tabela (versão prévia) ou as ferramentas mencionadas abaixo. 

### <a name="which-tools-will-work-with-table-api-preview"></a>Quais ferramentas funcionarão com a API de Tabela (versão prévia)? 
Versão mais antiga do Azure Explorer (0.8.9).

Ferramentas que têm a flexibilidade para aceitar uma cadeia de conexão no formato especificado anteriormente podem dar suporte a nova API de Tabela (versão prévia). Uma lista das ferramentas de tabela é fornecida na página [Ferramentas de cliente do Armazenamento do Azure](../storage/storage-explorers.md). 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>A CLI e o PowerShell funcionam com a nova API de Tabela (versão prévia)?
Ainda estamos planejando adicionar suporte à CLI e ao PowerShell na API de Tabela (versão prévia). 

### <a name="is-the-concurrency-on-operations-controlled"></a>A simultaneidade nas operações é controlada?
Sim, a simultaneidade otimista é fornecida com o uso do mecanismo de Etag, conforme esperado na API de Tabela Standard. 

### <a name="is-odata-query-model-supported-for-entities"></a>Há suporte para o modelo de consulta OData em entidades? 
Sim, a API de Tabela (versão prévia) dá suporte às consultas OData e Linq. 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>Posso me conectar à Tabela do Azure standard e à nova API de Tabela premium (versão prévia) lado a lado no mesmo aplicativo? 
Sim, isso pode ser feito com a criação de duas instâncias diferentes do CloudTableClient apontando para seus próprios URIs por meio da connectionstring.

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>Como fazer para migrar o aplicativo do Armazenamento de Tabelas existente para essa nova oferta?
Contate askdocdb@microsoft.com se desejar aproveitar a nova oferta da API de Tabela em seus dados existentes do Armazenamento de Tabelas. 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>Qual é o roteiro desse serviço e quando outras funcionalidades da API de Tabela Standard serão oferecidas?
Estamos planejando adicionar suporte a Tokens de SAS, ServiceContext, Estatísticas, Criptografia, Análise e outros recursos conforme avançamos para a GA.  Envie-nos seus comentários no [UserVoice](https://feedback.azure.com/forums/263030-documentdb). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>Como a expansão do tamanho do armazenamento é feita nesse serviço, digamos, se eu começar com N GBs de dados e meus dados aumentarem para 1 TB ao longo do tempo?  
O Cosmos DB foi projetado para fornecer armazenamento ilimitado com o uso da escala horizontal. Nosso serviço monitorará e efetivamente aumentará seu armazenamento. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Como fazer para monitorar a oferta da API de Tabela (versão prévia)?
Use o painel Métricas da API de Tabela (versão prévia) para monitorar as solicitações e o uso do armazenamento. 

### <a name="how-do-i-calculate-throughput-i-require"></a>Como fazer para calcular a produtividade de que preciso?
Use o Avaliador de capacidade para calcular a TableThroughput necessária para as operações, conforme documentado em [Estimar unidades de solicitação e o armazenamento de dados](https://www.documentdb.com/capacityplanner). Em geral, você pode representar a entidade como um JSON e fornecer os números para as operações. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK da API de Tabela (versão prévia) localmente com o emulador?
Sim, você pode usar a API de Tabela (versão prévia) no emulador local ao usar o novo SDK. Baixe o novo emulador [aqui](documentdb-nosql-local-emulator.md). O valor de StorageConnectionString no app.config deve ser “DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081”. 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>Um aplicativo que já tenho pode trabalhar com a API de Tabela (versão prévia)? 
A área de superfície da nova API de Tabela (versão prévia) é compatível com o SDK de Tabela Standard do Azure existente nas operações de criação, exclusão, atualização e consulta com a API do .NET. Você precisa garantir que tem a chave de linha, pois a API de Tabela (versão prévia) exige a chave de partição e a chave de linha. Também estamos planejando adicionar outro suporte de SDK conforme avançamos para a GA dessa oferta de serviço.

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>Preciso migrar o aplicativo existente baseado na Tabela do Azure para o novo SDK caso eu não deseje usar os recursos da API de Tabela (versão prévia)?
Não. Os clientes existentes podem criar e usar os ativos presentes da Tabela Standard sem nenhum tipo de interrupção. No entanto, se você não usar a nova API de Tabela (versão prévia), não poderá se beneficiar do índice automático, da opção de consistência adicional ou da distribuição global. 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>Como fazer para adicionar a replicação dos dados na API de Tabela Premium (versão prévia) a várias regiões do Azure?
Use as [configurações de replicação global](documentdb-portal-global-replication.md) do portal do Cosmos DB para adicionar regiões que são adequadas para seu aplicativo. Para desenvolver um aplicativo distribuído globalmente, você deve também adicionar seu aplicativo com as informações de PreferredLocation definidas como a região local, a fim de fornecer uma baixa latência de leitura. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>Como fazer para alterar a região de gravação primária da conta na API de Tabela Premium (versão prévia)?
Use o painel do portal de replicação global do Cosmos DB para adicionar uma região e, em seguida, fazer failover para a região desejada. Para obter instruções, consulte [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](documentdb-regional-failovers.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como fazer para configurar minhas regiões de leitura preferenciais com baixa latência ao distribuir meus dados? 
Você precisa utilizar a chave PreferredLocation no app.config para ajudá-lo a fazer as leituras da localização local. No aplicativo existente, a API de Tabela (versão prévia) gerará um erro se LocationMode for definido. Remova esse código, pois a API de Tabela Premium (versão prévia) coletará essas informações no arquivo app.config.  Essas configurações são documentadas em [Funcionalidades do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>Como considerar a consistência na API de Tabela (versão prévia)? 
O Cosmos DB fornece compensações bem fundamentadas entre consistência, disponibilidade e latência. A partir desta versão, ele fornece 5 níveis de consistência para sua escolha. Esses níveis de consistência estão disponíveis para o desenvolvedor da API de Tabela (versão prévia). Isso permite aos desenvolvedores escolher o modelo de consistência certo no nível da Tabela e a solicitação individual ao consultar os dados.  Quando um cliente se conecta, ele pode especificar um nível de consistência – isso pode ser alterado por meio da configuração do valor da chave TableConsistencyLevel no app.config. A API de Tabela (versão prévia) fornece leituras de baixa latência com a Leitura de suas próprias gravações com a consistência da sessão como padrão. Para obter mais informações, consulte [Níveis de consistência](documentdb-consistency-levels.md). Por padrão, hoje, a Tabela Standard oferece uma coerência forte em uma região e a eventual nas localizações secundárias.  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>Com a comparação da coerência eventual e forte que são possíveis com a Tabela Standard, isso significa que agora temos mais opções?
Sim. Essas opções são documentadas no artigo [Níveis de consistência](documentdb-consistency-levels.md) para ajudar os desenvolvedores de aplicativos a aproveitar a característica distribuída do Cosmos DB. Como também são fornecidas garantias para a consistência, você pode aproveitar esses recursos com confiança hoje mesmo. Essas configurações são documentadas em [Funcionalidades do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global é habilitada, quanto tempo é necessário para replicar os dados?
Confirmamos os dados de forma permanente na região local e enviamos os dados por push para outras regiões imediatamente, em questão de milissegundos e essa replicação só depende do RTT do data center. Leia mais sobre as capacidades de distribuição global do Cosmos DB em [Azure Cosmos DB: um serviço de banco de dados distribuído globalmente no Azure](documentdb-distribute-data-globally.md).

### <a name="can-the-read-request-consistency-be-changed"></a>A consistência de solicitação de leitura pode ser alterada?
O Cosmos DB permite definir a consistência no nível do contêiner que é uma Tabela. O SDK também permite alterar o nível fornecendo o valor para a chave TableConsistencyLevel no arquivo app.config. Estes são os valores possíveis – Strong|Bounded Staleness|Session|ConsistentPrefix|Eventual. Isso é documentado no artigo [Níveis de consistência](documentdb-consistency-levels.md). O importante aqui é se lembrar de que a consistência de solicitação não pode ser superior ao que está definido para a Tabela. Por exemplo, se você tiver uma consistência de configuração para a Tabela igual a eventual e definir o nível de consistência como forte na solicitação, isso não funcionará. 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>Como uma conta da API de Tabela Premium (versão prévia) lida com o failover caso uma região fique inativa? 
A API de Tabela Premium (versão prévia) utiliza a plataforma distribuída globalmente do Cosmos DB. Para garantir que seu aplicativo pode tolerar o tempo de inatividade do data center – é necessário habilitar, pelo menos, mais uma região para a conta no portal do Cosmos DB [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](documentdb-regional-failovers.md). Defina a prioridade da região usando o portal [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](documentdb-regional-failovers.md). Adicione quantas regiões desejar à conta e controle onde ela pode fazer failover fornecendo a prioridade. Obviamente, nem é preciso dizer que você precisa fornecer um aplicativo nessa região para utilizar o banco de dados. Dessa forma, os clientes não verão nenhum tempo de inatividade. O SDK do cliente tem hospedagem automática – ele pode detectar a região inativa e fazer failover automaticamente para a nova região.

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>A API de Tabela Premium (versão prévia) está habilitada para backups?
Sim. A API de Tabela Premium (versão prévia) utiliza a plataforma do Cosmos DB para backups. Os backups são feitos automaticamente. O backup do Cosmos DB é documentado em [Backup e restauração online com o Azure Cosmos DB](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md)

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>A API de Tabela (versão prévia) indexa todos os atributos de entidades por padrão?
Sim, por padrão, todos os atributos da entidade são indexados. Os detalhes de indexação são documentados no artigo [Azure Cosmos DB: Políticas de indexação](documentdb-indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>Isso significa que não é necessário criar índices diferentes para atender às consultas? 
Sim. O Cosmos DB fornece a indexação automática de todos os atributos sem nenhuma definição de esquema. Isso libera o desenvolvedor para se concentrar no aplicativo, em vez de se preocupar com o gerenciamento e a criação de índice. Os detalhes de indexação são documentados no artigo [Azure Cosmos DB: Políticas de indexação](documentdb-indexing-policies.md).

### <a name="can-the-indexing-policy-be-changed"></a>A política de indexação pode ser alterada?
Sim. Você pode alterar o índice fornecendo a definição de índice. O significado dessas configurações é documentado no artigo [Funcionalidades do Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Você precisa codificar essas configurações corretamente e fornecê-las com escape.  

No formato JSON de cadeia de caracteres do arquivo app.config.
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>O Cosmos DB como plataforma parece ter muitas funcionalidades, como classificação, agregações, hierarquia e outras. Há previsão para adição delas à API de Tabela? 
Na versão prévia, o Azure Cosmos DB dá suporte à mesma funcionalidade de consulta que o armazenamento de tabela do Azure para a API de Tabela. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Os recursos adicionais serão fornecidos na API de Tabela em uma atualização futura do serviço. Consulte [Consulta do Azure Cosmos DB](../documentdb/documentdb-sql-query.md) para uma visão geral desses recursos.
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quando devo alterar a TableThroughput da API de Tabela (versão prévia)?
Você deve alterar a TableThroughput quando realizar o ETL dos dados ou desejar carregar uma grande quantidade de dados em um curto período. 

OU

Você precisa de mais produtividade do contêiner no back-end, porque vê que a Produtividade Utilizada é maior que a Produtividade Provisionada nas métricas e está quase atingindo a limitação. Isso é documentado no artigo [Definir a produtividade](documentdb-set-throughput.md).

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>Posso aumentar ou diminuir verticalmente a produtividade de minha tabela da API de Tabela (versão prévia)? 
Sim, você pode usar o painel de escala do portal do Cosmos DB para fazer isso. Isso é documentado no tópico [Definir a produtividade](documentdb-set-throughput.md).

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>A API de Tabela Premium (versão prévia) pode utilizar a oferta de RU por minuto? 
Sim, a Tabela Premium (versão prévia) aproveita as funcionalidades do Cosmos DB para fornecer SLAs de Desempenho, Latência, Disponibilidade e Consistência. Isso garante que ela pode utilizar a oferta de RU por minuto, conforme documentado em [Unidades de Solicitação](documentdb-request-units.md). Essa funcionalidade permite que o cliente não provisione para o pico e atenue os picos na carga de trabalho.

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>Há uma TableThroughput Padrão que é definida para Tabelas recém-provisionadas?
Sim. Se você não substituir a TableThroughput por meio do app.config e não usar um contêiner pré-criado no Cosmos DB, o serviço criará uma Tabela com uma produtividade de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>Há alguma alteração de preço para aqueles que já são clientes da API de Tabela Standard?
Nenhuma. Não há nenhuma alteração de preço para aqueles que já são clientes da API de Tabela Standard. 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>Como o preço é calculado para a API de Tabela (versão prévia)? 
Isso depende da TableThroughput alocada. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Como fazer para lidar com a limitação nas Tabelas na oferta da API de Tabela (versão prévia)? 
Se a taxa de solicitação exceder a capacidade da produtividade provisionada para o contêiner subjacente, você receberá um erro e o SDK tentará a chamada novamente usando a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>Por que é necessário escolher uma produtividade, além de PartitionKey e RowKey, para utilizar a oferta da Tabela Premium (versão prévia) do Cosmos DB?
O Cosmos DB definirá uma produtividade padrão para o contêiner caso você não forneça uma no app.config. 

O Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Isso é possível quando o mecanismo pode impor a governança nas operações do locatário. Basicamente, a definição de TableThroughput significa garantir que você obtém a produtividade e a latência garantidas, pois agora a plataforma reservará essa capacidade e garantirá o sucesso da operação.  
A especificação da produtividade também permite que você altere-a de modo elástico para aproveitar a sazonalidade de seu aplicativo e atender às necessidades de produtividade, além de reduzir os custos.

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>O SDK do Armazenamento do Azure foi muito barato para mim, pois pago apenas para armazenar os dados e raramente realizo consultas. A nova oferta do Cosmos DB parece estar me cobrando mesmo sem ter realizado nenhuma transação ou armazenado nada. O que está acontecendo?

O Cosmos DB foi projetado para ser um sistema baseado em SLA distribuído globalmente com garantias de disponibilidade, latência e produtividade. Quando você reserva a produtividade no Cosmos DB, ela fica garantida, ao contrário de outros sistemas. O Cosmos DB também oferece outras funcionalidades que os clientes estão solicitando há muito tempo – como índices secundários, distribuição global, etc. Durante o período de versão prévia, fornecemos o modelo com otimização da produtividade e, a longo prazo, planejamos fornecer um modelo com otimização do armazenamento para atender às necessidades de nossos clientes.  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>Nunca atinjo a cota completa (indicando que uma partição está cheia) quando continuo ingerindo dados no Armazenamento de tabelas do Azure. Com a API de Tabela (versão prévia), posso receber esse erro. Essa oferta está me limitando e forçando a alterar meu aplicativo atual?

O Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Para garantir que você obtém a garantia de desempenho premium, você precisa garantir que o tamanho dos dados e o índice são gerenciáveis e escalonáveis. O limite de 10 GB no número de entidades/itens por chave de partição serve para garantir que fornecemos um excelente desempenho de pesquisa e consulta. Para garantir que seu aplicativo tem uma escala ideal, até mesmo no Armazenamento do Azure, solicitamos que você não crie uma partição ativa armazenando todas as informações de uma partição e consultando-a.  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>PartitionKey e RowKey ainda são necessárias na nova API de Tabela (versão prévia)? 
Sim. Como a área de superfície da API de Tabela (versão prévia) é semelhante ao SDK do Armazenamento de tabelas do Azure, a chave de partição fornece uma ótima maneira de distribuir os dados. A chave de linha é exclusiva nessa partição. Sim, a Chave de linha deve estar presente e não pode ser nula como no SDK Standard. A partir da versão prévia do Build, o comprimento da RowKey é de 255 bytes e da PartitionKey é de 100 bytes (a ser aumentado em breve para 1 KB). 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>Quais serão as mensagens de erro da API de Tabela (versão prévia)?
Como essa versão prévia é compatível com a Tabela Standard, a maioria dos erros será mapeada para os erros da Tabela Standard. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>Por que atinjo a limitação quando tento criar várias tabelas uma após a outra na API de Tabela (versão prévia)?
O Cosmos DB é um sistema baseado em SLA que fornece garantia de latência, produtividade, disponibilidade e consistência. Como ele é um sistema provisionado, ele reserva recursos para garantir esses requisitos. A taxa de criação de Tabelas de modo rápido é detectada e limitada. Recomendamos examinar a taxa de criação de tabelas e moderá-la para abaixo de 5 por minuto. Lembre-se de que a API de Tabela (versão prévia) é um sistema provisionado e, portanto, no momento em que você provisioná-la, precisará pagar por isso.  

# <a name="graph-apipreview"></a>API do Graph (versão prévia)
## <a name="database-questions-about-developing-against-graph-apipreview"></a>Perguntas sobre banco de dados referentes ao desenvolvimento na API do Graph (versão prévia)
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>Como posso utilizar a API do Graph (versão prévia) com o Cosmos DB?
A funcionalidade da API do Graph (versão prévia) pode ser utilizada em uma biblioteca de extensão. Ela é chamada Gráficos do Microsoft Azure e está disponível no NuGet. 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>Parece que vocês dão suporte ao Gremlin como uma linguagem de percurso. Vocês pretendem adicionar mais alguns formulários de consulta?
Sim, no futuro, temos planos de adicionar outros mecanismos de consulta. 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>Como posso usar a nova oferta da API do Graph (versão prévia)? 
Conclua o início rápido da [API do Graph](../cosmos-db/create-graph-dotnet.md) para começar.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md


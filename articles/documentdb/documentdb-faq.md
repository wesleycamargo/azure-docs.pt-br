<properties 
	pageTitle="Perguntas de banco de dados sobre o Banco de Dados de Documentos — perguntas frequentes | Microsoft Azure" 
	description="Obtenha respostas para perguntas frequentes sobre o Banco de Dados de Documentos, um serviço de banco de dados de documentos NoSql para o JSON. Responda perguntas de banco de dados sobre capacidade, níveis de desempenho e dimensionamento." 
	keywords="Perguntas de banco de dados, perguntas frequentes, banco de dados de documentos, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2016" 
	ms.author="mimig"/>


#Perguntas frequentes sobre o Banco de Dados de Documentos

## Perguntas de banco de dados sobre as noções básicas do Banco de Dados de Documentos do Microsoft Azure

### O que é o Banco de Dados de Documentos do Microsoft Azure? 
O Banco de Dados de Documentos do Microsoft Azure é um serviço de banco de dados de documentos NoSQL grande e rápido que oferece consulta sofisticada de dados sem esquemas, ajuda a oferecer um desempenho confiável e configurável e habilita um desenvolvimento rápido – tudo isso por meio de uma plataforma gerenciada com o suporte e o alcance do Microsoft Azure. O Banco de Dados de Documentos é a solução certa para aplicativos Web, móveis, de jogos e IoT quando a produtividade previsível, alta disponibilidade, baixa latência e o modelo de dados sem esquema são requisitos essenciais. O Banco de Dados de Documentos fornece flexibilidade de esquemas e indexação sofisticada por meio de um modelo de dados JSON nativo e inclui suporte de transações com diversos documentos com JavaScript integrado.
  
Para obter mais perguntas, respostas e instruções de banco de dados sobre como implantar e usar esse serviço, consulte a [página de documentação do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/services/documentdb/).

### Que tipo de banco de dados é o Banco de Dados de Documentos?
O Banco de Dados de Documentos é um banco de dados voltado a documentos NoSQL que armazena dados no formato JSON. O Banco de Dados de Documentos dá suporte a estruturas de dados autocontidos aninhados, que podem ser consultados com uma sofisticada [gramática de consulta SQL](documentdb-sql-query.md) do Banco de Dados de Documentos. O Banco de Dados de Documentos fornece processamento de transações de alto desempenho de JavaScript por parte do servidor com [procedimentos armazenados, gatilhos e funções definidas pelo usuário](documentdb-programming.md). O banco de dados dá suporte também a níveis de consistência ajustáveis pelo desenvolvedor, com [níveis de desempenho](documentdb-performance-levels.md) associados.
 
### Os bancos de dados do Banco de Dados de Documentos têm tabelas como um banco de dados relacional (RDBMS)?
Não, o Banco de Dados de Documentos armazena dados em coleções de documentos JSON. Para saber mais sobre os recursos do Banco de Dados de Documentos, consulte o [Modelo e conceitos de recursos do Banco de Dados de Documentos](documentdb-resources.md). Para obter mais informações sobre como as soluções de NoSQL, por exemplo, o Banco de Dados de Documentos, diferem das soluções relacionais, confira [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### Os bancos de dados do Banco de Dados de Documentos têm suporte para dados sem esquema?
Sim, o Banco de Dados de Documentos permite que os aplicativos armazenem documentos JSON arbitrários sem definições ou dicas de esquema. Os dados ficam disponíveis imediatamente para consulta com a interface de consulta SQL do Banco de Dados de Documentos.

### O Banco de Dados de Documentos tem suporte para transações ACID?
Sim, o Banco de Dados de Documentos suporta transações entre documentos expressas como procedimentos e gatilhos armazenados de JavaScript. As transações têm como escopo uma única partição em cada coleção e são executadas com semântica ACID como tudo ou nada, isoladas de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida. Para saber mais sobre transações, confira [Transações de programa de banco de dados](documentdb-programming.md#database-program-transactions).

### Quais são os casos de uso típicos do Banco de Dados de Documentos?  
O Banco de Dados de Documentos é uma boa escolha para novos aplicativos Web, móveis, de jogos e IoT, em que a escala automática, o desempenho previsível, a ordem rápida dos tempos de resposta de milissegundos e a capacidade de consultar dados sem esquema são importantes. O Banco de Dados de Documentos funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns do Banco de Dados de Documentos](documentdb-use-cases.md).

### Como o Banco de Dados de Documentos oferece desempenho previsível?
Uma [unidade de solicitação](documentdb-request-units.md) é a medida de produtividade no Banco de Dados de Documentos. 1 RU corresponde à produtividade do GET de um documento de 1 KB. Toda operação no Banco de Dados de Documentos, incluindo leituras, gravações, consultas SQL e execuções de procedimentos armazenados, tem um valor determinístico de RU baseado na produtividade necessária para concluir a operação. Em vez de pensar em CPU, E/S, memória e como cada uma dessas medidas afeta a produtividade do seu aplicativo, você pode pensar em uma medida de RU única.

Cada coleção do Banco de Dados de Documentos pode ser reservada com produtividade provisionada em termos de RU da produtividade por segundo. Em aplicativos de qualquer escala, você pode avaliar o desempenho das solicitações individuais para medir seus valores de RU e provisionar coleções para lidar com a soma total de unidades de solicitação entre todas as solicitações. Também é possível escalar ou reduzir verticalmente a produtividade de sua coleção conforme as necessidades do aplicativo aumentam. Para saber mais sobre unidades de solicitação e ajuda para determinar as necessidades de sua coleção, leia sobre o [Gerenciamento de desempenho e capacidade](documentdb-manage.md) e experimente a [calculadora de produtividade](https://www.documentdb.com/capacityplanner).

### O Banco de Dados de Documentos é compatível com HIPAA?
Sim, o Banco de Dados de Documentos é compatível com HIPAA. A HIPAA estabelece os requisitos para o uso, a divulgação e a proteção de informações de integridade individualmente identificáveis. Para saber mais, confira o [Centro de Confiabilidade da Microsoft](https://www.microsoft.com/pt-BR/TrustCenter/Compliance/HIPAA).

### Quais são os limites de armazenamento do Banco de Dados de Documentos? 
Não há limite teórico para a quantidade total de dados que uma coleção pode armazenar no Banco de Dados de Documentos. Se você deseja armazenar mais de 250 GB de dados em uma única coleção, [entre em contato com o suporte](documentdb-increase-limits.md) para que a cota da sua conta seja aumentada.

### Quais são os limites de produtividade do Banco de Dados de Documentos? 
Não há limite teórico para a quantidade total de produtividade à qual uma coleção pode dar suporte no Banco de Dados de Documentos, se sua carga de trabalho puder ser distribuída quase que de maneira uniforme entre um número suficientemente grande de chaves de partição. Se você quiser exceder 250.000 unidades de solicitação/segundo por coleção ou conta, [entre em contato com o suporte](documentdb-increase-limits.md) para que a cota da sua conta seja aumentada.

### Quanto custa o Banco de Dados de Documentos do Microsoft Azure?
Consulte a página [Detalhes de preços do Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/) para obter mais informações. Os encargos pelo do uso do Banco de Dados de Documentos são determinados pelo número de coleções em uso, pelo número de horas que as coleções estiveram online e pelo armazenamento consumido e produtividade provisionada para cada coleção.

### Existe uma conta gratuita disponível?
Se for novo no Azure, você poderá se inscrever para uma [conta gratuita do Azure](https://azure.microsoft.com/free/), que dá 30 dias e US$ 200 para experimentar todos os serviços do Azure. Ou então, se tiver uma assinatura do Visual Studio, você estará qualificado para receber [US$ 150 em créditos Azure gratuitos por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) a serem usados em qualquer serviço do Azure.

### Como obter ajuda adicional com o Banco de Dados de Documentos?
Caso você precise de ajuda, entre em contato conosco no [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), nos [Fóruns de desenvolvedores do MSDN do Banco de Dados de Documentos do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) ou agende um [chat individual com a equipe de engenharia do Banco de Dados de Documentos](http://www.askdocdb.com/). Para se manter atualizado quanto às últimas notícias e recursos do Banco de Dados de Documentos, siga-nos no [Twitter](https://twitter.com/DocumentDB).

## Configurar o Banco de Dados de Documentos do Microsoft Azure

### Como me inscrevo no Banco de Dados de Documentos do Microsoft Azure?
O Banco de Dados de Documentos do Microsoft Azure está disponível no [Portal do Azure][azure-portal]. Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure. Após se inscrever para uma assinatura do Microsoft Azure, e possível adicionar uma conta do Banco de Dados de Documentos à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta do Banco de Dados de Documentos, consulte [Criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

### O que é uma chave mestra?
Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que possuem a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestra. A chave mestra primária e a secundária estão disponíveis na folha **Chaves** do [Portal do Azure][azure-portal]. Para saber mais sobre as chaves, consulte [Exibir, copiar e regenerar chaves de acesso](documentdb-manage-account.md#keys).

### Como crio um banco de dados?
Você pode criar bancos de dados usando o [Portal do Azure](), como descrito em [Criar um banco de dados do Banco de Dados de Documentos](documentdb-create-database.md), um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### O que é uma coleção?
Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](documentdb-performance-levels.md) é determinado pela produtividade e pelo armazenamento usados. As coleções podem abranger uma ou mais partições/servidores e podem ser dimensionadas para lidar com volumes de armazenamento ou taxa de transferência praticamente ilimitados.

As coleções também são as entidades de cobrança para o Banco de Dados de Documentos. Cada coleção é cobrada por hora com base na produtividade provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/).

### Como configuro usuários e permissões?
Você pode criar usuários e permissões usando um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md) ou por meio das [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Perguntas de banco de dados sobre como desenvolver no Banco de Dados de Documentos do Microsoft Azure

### Como começo a desenvolver no Banco de Dados de Documentos?
Os [SDKs](documentdb-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem aproveitar as [APIs HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com os recursos do Banco de Dados de Documentos em uma série de plataformas e linguagens.

Exemplos dos SDKs [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) e [Python](documentdb-python-samples.md) do Banco de Dados de Documentos estão disponíveis no GitHub.

### O Banco de Dados de Documentos dá suporte ao SQL?
A linguagem de consulta SQL do Banco de Dados de Documentos é um subconjunto aprimorado da funcionalidade de consulta com suporte do SQL. A linguagem de consulta SQL do Banco de Dados de Documentos fornece extensibilidade e operadores hierárquicos e relacionais por meio de JavaScript com base em UDFs (funções definidas por usuário). A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore, o que é usado pelas técnicas de indexação automática do Banco de Dados de Documentos, bem como pelo dialeto de consulta SQL do Banco de Dados de Documentos. Para obter detalhes sobre como usar a gramática SQL, veja o artigo [Consultar o Banco de Dados de Documentos][query].

### Quais são os tipos de dados com suporte pelo Banco de Dados de Documentos?
Os tipos de dados primitivos suportados no Banco de Dados de Documentos são os mesmos que os do JSON. O JSON tem um sistema de tipo simples que consiste em cadeias de caracteres, números (precisão dupla IEEE754) e boolianos – verdadeiro e falso e nulos. Os tipos de dados mais complexos como DateTime, Guid, Int64 e Geometria podem ser representados tanto no JSON quanto no Banco de Dados de Documentos com a criação de objetos aninhados usando o operador { } e matrizes usando o operador [ ].

### Como o Banco de Dados de Documentos fornece simultaneidade?
O Banco de Dados de Documentos suporta o controle de simultaneidade otimista (OCC) por meio de marcas de entidade HTTP ou etags. Cada recurso do Banco de Dados de Documentos tem uma etag, e a etag é definida no servidor sempre que um documento é atualizado. O cabeçalho etag e o valor atual são incluídos em todas as mensagens de resposta. As etags podem ser usadas com o cabeçalho If-Match a fim de permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-Match é o valor de etag a ser comparado. Se o valor de etag corresponder ao valor de etag do servidor, o recurso será atualizado. Se a etag não for mais atual, o servidor rejeitará a operação com um código de resposta "HTTP 412 Falha de pré-condição". O cliente precisará refazer a buscar do recurso a fim de obter o valor de etag atual do recurso. Além disso, as etags podem ser usadas com o cabeçalho If-None-Match para determinar se é necessário realizar uma nova busca de um recurso.

Para usar a simultaneidade otimista em .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx). Para obter um exemplo de .NET, confira [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no github.

### Como executo transações no Banco de Dados de Documentos?
O Banco de Dados de Documentos suporta transações integradas de linguagens com procedimentos e gatilhos armazenados de JavaScript. Todas as operações de banco de dados dentro de scripts são executadas com escopo no isolamento de instantâneo para a coleção, se for uma coleção de única partição, ou documentos com o mesmo valor de chave de partição em uma coleção, se a coleção for particionada. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Consulte [Programação do Banco de Dados de Documentos no servidor](documentdb-programming.md).

### Como posso inserir documentos em massa no Banco de Dados de Documentos? 
Há três maneiras de inserir documentos em massa no Banco de Dados de Documentos:

- A ferramenta de migração de dados, como descrito em [Importar dados para o Banco de Dados de Documentos](documentdb-import-data.md).
- O Gerenciador de Documentos no Portal do Azure, como descrito em [Adicionar documentos em massa com o Gerenciador de Documentos](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimentos armazenados, como descrito em [Programação de servidor do Banco de Dados de Documentos](documentdb-programming.md).

### O Banco de Dados de Documentos tem suporte para cache de link?
Sim, como o Banco de Dados de Documentos é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Clientes do Banco de Dados de Documentos podem especificar um cabeçalho "If-None-Match" para leituras em qualquer recurso, como documento e coleção, e atualizar suas cópias locais somente quando a versão do servidor for alterada.

### Uma instância local do Banco de Dados de Documentos está disponível?
No momento, não há uma instância local do Banco de Dados de Documentos disponível. Você pode acompanhar o status de um emulador local e votar no [Fórum de comentários](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0706_2016-->
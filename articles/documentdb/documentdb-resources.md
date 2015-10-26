<properties 
	pageTitle="Conceitos e modelo de recursos hierárquico do Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba mais sobre modelo hierárquico de bancos de dados, coleções, UDF (função definida pelo usuário), documentos e permissões do Banco de Dados de Documentos para gerenciar recursos e muito mais."
	keywords="Hierarchical Model, documentdb, azure, Microsoft azure"	
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="anhoh"/>

# Conceitos e modelo de recursos hierárquico do Banco de Dados de Documentos

As entidades de banco de dados gerenciadas pelo Banco de Dados de Documentos são chamadas de **recursos**. Cada recurso é identificado de maneira exclusiva por um URI lógico. Você pode interagir com os recursos usando verbos de HTTP padrão, cabeçalhos de solicitação/resposta e códigos de status.

Após ler este artigo, você poderá responder as perguntas a seguir:

- Quais são os recursos do Banco de Dados de Documentos?
- O que é modelo hierárquico de recursos do Banco de Dados de Documentos?
- O que são recursos definidos pelo sistema versus recursos definidos pelo usuário?
- Como eu acesso um recurso?
- Como eu trabalho com coleções?
- Como trabalhar com procedimentos armazenados, disparadores e UDFs (Funções Definidas pelo Usuário)?

##Modelo de recursos hierárquico
Como o diagrama a seguir ilustra, o **modelo de recursos** hierárquico do Banco de Dados de Documentos é formado por conjuntos de recursos em uma conta de banco de dados, cada um podendo ser acessado por meio de um URI lógico e estável. Neste artigo, um conjunto de recursos será chamado de **feed**.

>[AZURE.NOTE]Ele oferece um protocolo TCP altamente eficiente que também possui o modelo de comunicação RESTful, disponível por meio do [SDK do cliente .NET.](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modelo de recursos hierárquico do Banco de Dados de Documentos][1] **Modelo de recurso hierárquico em uma conta de banco de dados**

Para começar a trabalhar com os recursos, você deve [criar uma conta de banco de dados do Banco de Dados de Documentos](documentdb-create-account.md) usando sua assinatura do Azure. Uma conta do banco de dados pode consistir em um conjunto de **bancos de dados**, cada um contendo diversas **coleções**, cada uma delas, por sua vez, contendo **procedimentos armazenados, gatilhos, UDFs, documentos** e **anexos** relacionados. Um banco de dados também tem **usuários** associados, cada um com um conjunto de **permissões** para acessar coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos e anexos possuem conteúdos JSON arbitrários, definidos pelo usuário.

|Recurso |Descrição
|-----------|-----------
|Conta de banco de dados |Uma conta de banco de dados está associada um conjunto de bancos de dados e uma quantidade fixa de armazenamento de blob para anexos (recurso de visualização). Você pode criar uma ou mais contas do banco de dados usando sua assinatura do Azure. Para cada conta de banco de dados padrão, é alocada a capacidade mínima de uma coleção S1. Para obter mais informações, visite nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
|Banco de dados |Um banco de dados é um contêiner lógico de armazenamento de documentos particionado em coleções. Ele também é um contêiner para usuários.
|Usuário |O namespace lógico para obter o escopo/particionamento das permissões. 
|Permissão |Um token de autorização associado a um usuário para acesso autorizado a um recurso específico.
|Coleção |Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o custo é determinado pelo nível de desempenho associado à coleção. Os níveis de desempenho (S1, S2 e S3) fornecem 10 GB de armazenamento e uma quantidade fixa de taxa de transferência. Para obter mais informações sobre níveis de desempenho, visite nossa [página de desempenho](documentdb-performance-levels.md).
|Procedimento armazenado |Lógica de aplicativo gravada no JavaScript que é registrada com uma coleção e executada de forma transacional dentro do mecanismo do banco de dados.
|Gatilho |Lógica de aplicativo gravada no JavaScript modelando efeitos colaterais associados a uma operação de inserção, substituição ou exclusão.
|UDF |Uma lógica de aplicativo livre de efeito colateral gravada em JavaScript. As UDFs permitem modelar um operador de consulta personalizada e, portanto, estender a linguagem de consulta principal do Banco de Dados de Documentos.
|Documento |Conteúdo JSON (arbitrário) definido pelo usuário. Por padrão, nenhum esquema precisa ser definido e nenhum índice secundário precisa ser fornecido a todos os documentos adicionados a uma coleção.
|(Visualização) Anexo |Um anexo é um documento especial que contém referências e metadados associados a blobs/mídias externos. O desenvolvedor pode optar por ter o blob gerenciado pelo Banco de Dados de Documentos ou armazená-lo com um provedor de serviços de blob externo, como OneDrive, Dropbox etc. 


##Recursos definidos pelo sistema versus usuário
Recursos como contas do banco de dados, bancos de dados, coleções, usuários, permissões, procedimentos armazenados, gatilhos e UDFs, todos têm um esquema fixo e são chamados de recursos do sistema. Em contraste, recursos como documentos e anexos não possuem restrições sobre o esquema e são exemplos de recursos definidos pelo usuário. No Banco de Dados de Documentos, ambos os recursos definidos pelo sistema e pelo usuário são representados e gerenciados como JSON em conformidade com o padrão. Todos os recursos, definidos pelo usuário ou pelo sistema, possuem as seguintes propriedades em comum.

>[AZURE.NOTE]Observe que todas as propriedades geradas pelo sistema em um recurso têm como prefixo um sublinhado (\_) na sua representação JSON.


Propriedade |Configurável pelo usuário ou gerada pelo sistema?|Finalidade
---|---|---
_\_rid|Gerado pelo sistema|Gerado pelo sistema, identificador exclusivo e hierárquico do recurso. \_etag|Gerado pelo sistema|etag do recurso necessário para o controle de simultaneidade otimista. \_ts|Gerado pelo sistema|Carimbo de data/hora da última atualização do recurso. \_self|Gerado pelo sistema|URI endereçável exclusivo do recurso. id|Configurável pelo usuário|Nome exclusivo do recurso definido pelo usuário.

###Representação da conexão dos recursos
O Banco de Dados de Documentos não obriga nenhuma extensão proprietária para o padrão JSON nem codificações especiais; ele trabalha com documentos JSON compatíveis padrão.
 
###Endereçamento de um recurso
Todos os recursos são endereçáveis pelo URI. O valor da propriedade **\_self** de um recurso representa o URI relativo do recurso. O formato do URI consiste nos segmentos do caminho /<feed>/{\_rid}:

|Valor da \_self |Descrição |-------------------|----------- |/dbs |Feed de banco de dados em uma conta de banco de dados |/dbs/{\_rid-db} |Banco de dados com a propriedade de ID exclusiva com o valor {\_rid-db} |/dbs/{\_rid-db}/colls/ |Feed de coleções em um banco de dados |/dbs/{\_rid-db}/colls/{\_rid-coll} |Coleção com a propriedade de ID exclusiva com o valor {\_rid-coll}|/dbs/{\_rid-db}/users/ |Feed de usuários em um banco de dados |/dbs/{\_rid-db}/users/{\_rid-user} |Usuário com a propriedade de ID exclusiva com o valor {\_rid-user} |/dbs/{\_rid-db}/users/{\_rid-user}/permissions |Feed de permissões em um banco de dados |/dbs/{\_rid-db}/users/{\_rid-user}/permissions/{\_rid-permission} |Permissão com a propriedade de ID exclusiva com o valor {\_rid-permission}.
  
Um recurso também possui um nome exclusivo definido pelo usuário exposto pela propriedade de ID do recurso. A ID é uma cadeia de caracteres definida pelo usuário, com até 256 caracteres e exclusiva no contexto de um recurso pai específico. Por exemplo, o valor da propriedade de ID de todos os documentos dentro de uma determinada coleção é exclusivo, mas não é garantido que seja exclusivo nas coleções. Do mesmo modo, o valor da propriedade de ID de todas as permissões para um determinado usuário é exclusivo, mas não é garantido que seja exclusivo em todos os usuários. A propriedade \_rid é usada para construir o link endereçável \_self de um recurso.

Cada recurso também tem um identificador de recurso hierárquico gerado pelo sistema (também chamado de RID), que é disponibilizado pela propriedade \_rid. O RID codifica a hierarquia inteira de um determinado recurso, sendo uma representação interna muito conveniente usada para impor integridade referencial de maneira distribuída. O RID é exclusivo dentro de uma conta de banco de dados, sendo usado internamente pelo Banco de Dados de Documentos para roteamento eficiente sem exigir pesquisas entre partições.

Os valores das propriedades \_self e \_rid são representações alternativas e canônicas de um recurso.

##Contas de banco de dados
Você pode provisionar uma ou mais contas do Banco de Dados de Documentos usando sua assinatura do Azure. Cada conta de banco de dados da camada Padrão terá a capacidade mínima de uma coleção S1.

Você pode [criar e gerenciar contas de banco de dados do Banco de Dados de Documentos](documentdb-create-account.md) por meio do portal do Azure em [http://portal.azure.com/](http://portal.azure.com/). Criar e gerenciar uma conta do banco de dados requer acesso administrativo e pode ser feito somente com sua assinatura do Azure.

###Propriedades de contas de banco de dados
Como parte do provisionamento e gerenciamento de uma conta do banco de dados, você pode configurar e ler as seguintes propriedades:

Nome da Propriedade|Descrição
---|---
Política de Consistência|Defina essa propriedade para configurar o nível de consistência padrão para todas as coleções em sua conta do banco de dados. Você pode substituir o nível de consistência com base na solicitação usando o cabeçalho de solicitação [x-ms-consistency-level]. No futuro, poderemos dar suporte à substituição do nível de consistência com base na coleção. <p><p>Observe que esta propriedade só se aplica aos <i>recursos definidos pelo usuário</i>. Todos os recursos definidos pelo sistema são configurados para oferecer suporte a leituras/consultas com uma coerência forte.
Chave primária e Chave secundária|Essas são as chaves primária e secundária, que oferecem acesso administrativo a todos os recursos na conta de banco de dados.
MaxMediaStorageUsageInMB (LEITURA)|Quantidade máxima de armazenamento de mídia disponível para a conta de banco de dados.
MediaStorageUsageInMB (READ)|Uso atual do armazenamento de mídia para a conta de banco de dados.

Observe que, além de provisionar, configurar e gerenciar sua conta de banco de dados por meio do Portal do Azure, também é possível criar e gerenciar programaticamente contas do Banco de Dados de Documentos por meio das [APIs REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) e das [SDKs clientes](https://msdn.microsoft.com/library/azure/dn781482.aspx).

##Bancos de dados
Um banco de dados do Banco de Dados de Documentos é um contêiner lógico de uma ou mais coleções e usuários, conforme mostrado no diagrama a seguir. Você pode criar qualquer número de bancos de dados em uma conta de banco de dados do Banco de Dados de Documentos, sujeito aos limites de oferta.

![Modelo hierárquico de coleções e conta de banco de dados][2] **Um banco de dados é um contêiner lógico de usuários e coleções**

Um banco de dados pode conter praticamente um armazenamento de documentos ilimitado, particionado por coleções, que formam os domínios de transação para os documentos contidos neles.

###Dimensionamento elástico de um banco de dados do Banco de Dados de Documentos
Um banco de dados do Banco de Dados de Documentos é elástico por padrão, indo de alguns GB a petabytes de armazenamento de documento com suporte de SSD e produtividade provisionada.

Diferentemente de um banco de dados em um RDBMS tradicional, um banco de dados no Banco de Dados de Documentos não é vinculado a uma única máquina. Com o Banco de Dados de Documentos, conforme a escala do seu aplicativo precisa crescer, você pode criar mais coleções ou bancos de dados, ou ambos. Na verdade, vários aplicativos primários na Microsoft utilizam o Banco de Dados de Documentos na escala do cliente ao criar bancos de dados do Banco de Dados de Documentos extremamente grandes, cada um contendo milhares de coleções com terabytes de armazenamento de documentos. É possível expandir ou reduzir um banco de dados adicionando ou removendo coleções para atender os requisitos de escada do seu aplicativo.

Você pode criar qualquer número de coleções dentro de um banco de dados sujeito à oferta. Cada coleção tem armazenamento com SSD e taxa de transferência provisionada para você, dependendo do nível de desempenho selecionado.

Um banco de dados do Banco de Dados de Documentos também é um contêiner de usuários. Um usuário, por sua vez, é um namespace lógico para um conjunto de permissões que oferece autorização e acesso refinado a coleções, documentos e anexos.
 
Assim como com outros recursos do modelo de recursos do Banco de Dados de Documentos, os bancos de dados podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as [APIs REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer [SDK do cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). O Banco de Dados de Documentos garante uma forte consistência para a leitura ou consulta de metadados de um recurso do banco de dados. Excluir um banco de dados automaticamente garante que você não possa acessar qualquer uma das coleções ou usuários contidos nele.

##Coleções
Uma coleção do Banco de Dados de Documentos é um contêiner para seus documentos JSON. Uma coleção também é uma unidade de dimensionamento para transações e consulta. Também é possível expandir um banco de dados do Banco de Dados de Documentos adicionando mais coleções. Se seu aplicativo precisar ser mais dimensionado, você poderá adicionar uma coleção, cada uma alocada com armazenamento com SSD e uma quantidade fixa de taxa de transferência, dependendo de sua camada de desempenho.
 
###Armazenamento de documentos com suporte de SSD elástico
Uma coleção é intrinsicamente elástica; ela cresce e é reduzida automaticamente conforme você adiciona ou remove documentos. Embora o uso primário do Banco de Dados de Documentos o tenha testado com milhares de coleções dentro de um banco de dados, cada um deles indo de alguns gigabytes a terabytes de tamanho, o Banco de Dados de Documentos atualmente limita a elasticidade de uma determinada coleção a 10 GB.

###Indexação automática de coleções
O Banco de Dados de Documentos é um verdadeiro sistema de banco de dados livre de esquema. Ele não assume nem requer qualquer esquema para os documentos JSON. Ao incluir documentos em uma coleção, o Banco de Dados de Documentos os indexa automaticamente e eles ficam disponíveis para consulta. A indexação automática de documentos sem exigir esquemas ou índices secundários é uma capacidade chave do Banco de Dados de Documentos e é ativada por técnicas de manutenção de índice com gravação otimizada, livres de bloqueios e estrutura de log. O Banco de Dados de Documentos oferece suporte a um volume permanente de gravações extremamente rápidas ao mesmo tempo em que oferece consultas consistentes. Ambos os armazenamentos de documentos e de índices são usados para calcular o armazenamento consumido por cada coleção. Você pode controlar os compromissos de armazenamento e desempenho associados à indexação configurando a política de indexação para uma coleção.

###Configurando a política de indexação de uma coleção
A política de indexação de cada coleção permite realizar compromissos de desempenho e armazenamento associados à indexação. As seguintes opções estão disponíveis como parte da configuração de indexação:

-	Escolha se a coleção indexa automaticamente todos os documentos ou não. Por padrão, todos os documentos são indexados automaticamente. Você pode escolher desativar a indexação automática e adicionar seletivamente somente documentos específicos para o índice. Da mesma forma, é possível escolher seletivamente excluir somente documentos específicos. Isso pode ser feito definindo a propriedade automática como verdadeira ou falsa na indexingPolicy de uma coleção e usando o cabeçalho de solicitação [x-ms-indexingdirective] ao inserir, substituir ou excluir um documento.  
-	Escolha se deseja incluir ou excluir caminhos ou padrões específicos em seus documentos do índice. Isso pode ser feito definindo includedPaths e excludedPaths na indexingPolicy de uma coleção, respectivamente. Também é possível configurar os compromissos de armazenamento e desempenho para consultas de intervalo e hash para padrões de caminho específicos. 
-	Escolha entre atualizações de índice síncronas (consistentes) e assíncronas (lentas). Por padrão, o índice é atualizado sincronamente em cada inserção, substituição ou exclusão de um documento para a coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos. Enquanto o Banco de Dados de Documentos é otimizado para gravação e suporta volumes constantes de gravações de documentos junto com a manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar seu índice, sem pressa. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa para coleções basicamente de leitura intensa.

A política de indexação pode ser configurada somente durante a criação de uma coleção. Uma vez que a coleção tenha sido criada, a política não pode ser atualizada.

###Consultando uma coleção
Os documentos dentro de uma coleção podem ter esquemas arbitrários e os documentos podem ser consultados dentro de uma coleção sem oferecer qualquer esquema ou índices secundários de início. É possível consultar a coleção usando a [linguagem de consulta SQL do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx), que oferece operadores hierárquicos e relacionais ricos e extensibilidade por meio de UDFs baseados em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore. Isso é explorado por técnicas de indexação automáticas do Banco de Dados de Documentos, bem como pelo dialeto da consulta SQL do Banco de Dados de Documentos. A linguagem de consulta do Banco de Dados de Documentos é formada por três aspectos principais:

1.	Um pequeno conjunto de operações de consulta que é mapeado naturalmente para a estrutura de árvore, incluindo projeções e consultas hierárquicas. 
2.	Um subconjunto de operações relacionais, incluindo composição, filtragem, projeções, agregados e junções automáticas. 
3.	UDFs puras baseadas em JavaScript que funcionam com (1) e (2).  

O modelo de consulta do Banco de Dados de Documentos tenta criar um equilíbrio entre funcionalidade, eficiência e simplicidade. O mecanismo de banco de dados do Banco de Dados de Documentos compila nativamente e executa as instruções de consulta SQL. Você pode consultar uma ação usando as [APIs REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer uma das [SDKs clientes](https://msdn.microsoft.com/library/azure/dn781482.aspx). O SDK do .NET é fornecido com o provedor LINQ.

Você pode experimentar o Banco de Dados de Documentos e executar consultas SQL em relação a nosso conjunto de dados no [Espaço de Consulta](https://www.documentdb.com/sql/demo).

###Transações entre vários documentos
As transações do banco de dados oferecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. No RDBMS, a maneira tradicional de gravar a lógica de negócios é gravar **procedimentos armazenados** e/ou **gatilhos** e enviá-los ao servidor do banco de dados para execução transacional. No RDBMS, o programador do aplicativo precisa lidar com duas linguagens de programação diferentes:

- A linguagem de programação (não transacional) do aplicativo (como JavaScript, Python, C#, Java etc.). 
- T-SQL, a linguagem de programação transacional que é executada nativamente pelo banco de dados.

Devido ao seu profundo compromisso com JavaScript e JSON diretamente dentro do mecanismo do banco de dados, o Banco de Dados de Documentos oferece um modelo de programação intuitivo para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções em termos de procedimentos armazenados e gatilhos. Isso permite o seguinte:

- Implementação eficaz de controle de simultaneidade, recuperação e indexação automática dos gráficos de objeto JSON diretamente no mecanismo do banco de dados. 
- Expressar naturalmente o fluxo de controle, escopo de variáveis, atribuição e integração de primitivos de manuseio de exceções com transações de bancos de dados diretamente em termos da linguagem de programação JavaScript.   

A lógica de JavaScript registrada no nível da coleção pode, então, emitir operações do banco de dados nos documentos da coleção determinada. O Banco de Dados de Documentos encapsula implicitamente os procedimentos de armazenamento baseados em JavaScript e os gatilhos dentro de transações ACID ambientes com isolamento de captura instantânea entre documentos dentro de uma coleção. Durante sua execução, se o JavaScript lançar uma exceção, então, toda a transação será abortada. O modelo de programação resultante é bastante simples, porém, poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação “durável”, ao mesmo tempo em que utilizam os construtores de linguagem e primitivas de biblioteca com os quais estão familiarizados.

A capacidade de executar JavaScript diretamente dentro do mecanismo do banco de dados no mesmo espaço de endereço que o conjunto de buffer permite uma execução de desempenho e transacional das operações do banco de dados em relação aos documentos de uma coleção. Além disso, o mecanismo de banco de dados do Banco de Dados de Documentos assume um forte compromisso com JSON, e o JavaScript elimina qualquer incompatibilidade de impedância entre os sistemas de tipos do aplicativo e do banco de dados.

Depois de criar uma coleção, você pode registrar os procedimentos armazenados, disparadores e UDFs com uma coleção usando as [APIs REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer [SDK cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Após o registro, você pode fazer referência a eles e executá-los. Considere que o seguinte procedimento armazenado gravado inteiramente em JavaScript pega dois argumentos (nome do livro e nome do autor) e cria um novo documento, consulta um documento e, então, o atualiza; tudo sob a égide de uma transação ACID implícita. A qualquer momento durante a execução, se uma exceção de JavaScript for lançada, toda a transação será abortada.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don’t need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

O cliente pode “enviar” a lógica de JavaScript acima para o banco de dados para execução transacional por meio do HTTP POST. Para saber mais sobre o uso de métodos HTTP, veja [interações RESTful com recursos do Banco de Dados de Documentos](documentdb-interactions-with-resources.md).

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


Observe que, como o banco de dados compreende nativamente JSON e JavaScript, não há incompatibilidade no sistema de tipos, nem é preciso um “mapeamento OR” ou mágica na geração de códigos.

Os procedimentos armazenados e gatilhos interagem com uma coleção e os documentos em uma coleção por meio de um modelo de objeto bem-definido que expõe o contexto de coleção atual.

Coleções no Banco de Dados de Documentos podem ser criadas, excluídas, lidas ou enumeradas facilmente usando [APIs REST do Banco de Dados de Documentos do Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer [SDK cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). O Banco de Dados de Documentos sempre oferece uma forte consistência para leitura ou consulta dos metadados de uma coleção. Excluir uma coleção automaticamente garante que você não possa acessar qualquer documento, anexo, procedimento armazenado, gatilho e UDFs contidos nela.
##Procedimentos armazenados, gatilhos e UDF (Funções Definidas pelo Usuário)
Conforme descrito na seção anterior, você pode gravar a lógica do aplicativo para ser executada diretamente de uma transação no mecanismo de banco de dados. A lógica do aplicativo pode ser gravada inteiramente em JavaScript e pode ser modelada como um procedimento armazenado, um gatilho ou uma UDF. O código de JavaScript em um procedimento armazenado ou um gatilho pode inserir, substituir, excluir, ler ou consultar documentos dentro de uma coleção. Por outro lado, o JavaScript em uma UDF somente pode realizar cálculos sem efeitos colaterais ao enumerar os documentos do conjunto de resultados da consulta e produzir outro conjunto de resultados. Para multilocatários, o Banco de Dados de Documentos realiza uma rígida governança de recursos baseada em reserva. Cada procedimento armazenado, gatilho ou UDF obtém um quantum fixo de recursos do sistema operacional para realizar esse trabalho. Além disso, os procedimentos armazenados, gatilhos ou UDFs não podem ser vinculados a bibliotecas de JavaScript externas e são incluídos em listas negras se excederem os orçamentos de recursos alocados para eles. Você pode registrar e cancelar o registro de procedimentos armazenados, gatilhos ou UDFs com uma coleção por meio das APIs REST. Após o registro, um procedimento armazenado, gatilho ou UDF é pré-compilado e armazenado como um código de byte que é executado posteriormente. A seção a seguir ilustra como você pode usar o SDK JavaScript do Banco de Dados de Documentos para registrar, executar e cancelar o registro de um gatilho, UDF ou procedimento armazenado. O SDK do JavaScript é um wrapper simples sobre as [APIs REST do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781481.aspx).

###Registrando um procedimento armazenado
O registro de um procedimento armazenado cria um novo recurso de procedimento armazenado em uma coleção por meio do HTTP POST.

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Executando um procedimento armazenado
A execução de um procedimento armazenado é feita com a emissão de um HTTP POST para um recurso de procedimento armazenado existente, transferindo parâmetros para o procedimento no corpo da solicitação.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

###Cancelando o registro de um procedimento armazenado
Cancelar o registro de um procedimento armazenado é um processo simples, feito com a emissão de um HTTP DELETE para um recurso de procedimento armazenado existente.

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


###Registrando um pré-gatilho
O registro de um gatilho é realizado ao criar um novo recurso de gatilho em uma coleção por meio do HTTP POST. Você pode especificar se o gatilho é um pré ou pós-gatilho e o tipo de operação à qual ele pode ser associado (p. ex., Criar, Substituir, Excluir ou Tudo).

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

###Executando um pré-gatilho
A execução de um gatilho é realizada especificando o nome de um gatilho existente ao mesmo tempo em que a solicitação POST/PUT/DELETE de um recurso de documento é emitida por meio do cabeçalho de solicitação.
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

###Cancelando o registro de um pré-gatilho
O cancelamento do registro de um gatilho é realizado simplesmente ao emitir um comando HTTP DELETE para um recurso de gatilho existente.

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

###Registrando uma UDF
O registro de uma UDF é realizado ao criar um novo recurso de UDF em uma coleção por meio do HTTP POST.

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Executando uma UDF como parte da consulta
Uma UDF pode ser especificada como parte da consulta SQL e é usada como uma maneira de estender a [linguagem de consulta SQL central do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx).

	var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

###Cancelando o registro de uma UDF 
O cancelamento do registro de uma UDF é realizado simplesmente ao emitir um comando HTTP DELETE em relação a um recurso de UDF existente.

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

Embora os trechos acima mostrem o registro (POST), cancelamento de registro (PUT) / leitura/lista (GET) e execução (POST) por meio do [SDK do JavaScript do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-js), você também pode usar [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou outros [SDKs cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx).

##Documentos
Você pode inserir, substituir, excluir, ler, enumerar e consultar documentos JSON arbitrários em uma coleção. O Banco de Dados de Documentos não exige nenhum esquema nem índices secundários a fim de oferecer suporte a consultas em relação a documentos em uma coleção.

Sendo um verdadeiro serviço de banco de dados aberto, o Banco de Dados de Documentos não inverte nenhum tipo de dados especializado (p. ex., de data e hora) ou codificações específicas para documentos JSON. Observe que o Banco de Dados de Documentos não requer nenhuma convenção JSON especial para codificar os relacionamentos entre vários documentos; a linguagem de consulta SQL do Banco de Dados de Documentos oferece operadores de consulta hierárquica e relacional bastante poderosos para consultar e projetar documentos sem nenhuma anotação especial ou necessidade de codificar relacionamentos entre documentos usando propriedades distintas.
 
Como ocorre com todos os outros recursos, documentos podem ser criados, substituídos, excluídos, lidos, enumerados e consultados facilmente usando APIs REST ou qualquer [SDK cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Excluir um documento libera imediatamente a cota correspondente a todos os anexos aninhados. O nível de consistência de leitura dos documentos segue a política de consistência da conta do banco de dados. Essa política pode ser substituída com base em cada solicitação, dependendo dos requisitos de consistência de dados de seu aplicativo. Ao consultar documentos, a consistência de leitura segue o conjunto do modo de indexação na coleção. Para fins de “consistência”, a política de consistência da conta é seguida.

##Anexos e mídia
>[AZURE.NOTE]Anexos e recursos de mídia são recursos de visualização.
 
O Banco de Dados de Documentos permite armazenar blobs/mídias binários no Banco de Dados de Documentos ou em seu próprio repositório de mídia. Ele também permite representar os metadados de uma mídia de acordo com um documento especial chamado anexo. Um anexo no Banco de Dados de Documentos é um documento especial (JSON) que faz referência à mídia/ao blob armazenada/o em outro lugar. Um anexo é simplesmente um documento especial que captura os metadados (p. ex., localização, autor etc.) de uma mídia em um armazenamento remoto de mídia.

Considere um aplicativo de leitura social que utiliza o Banco de Dados de Documentos para armazenar anotações de tintas e metadados incluindo comentários, destaques, favoritos, classificações, preferências/não preferências, etc., associados a um e-book de um determinado usuário.

-	O conteúdo do livro em si é armazenado no armazenamento de mídia disponível como parte da conta do Banco de Dados de Documentos ou de um armazenamento remoto de mídia. 
-	Um aplicativo poderá armazenar os metadados de cada usuário como um documento distinto, por exemplo, os metadados de Joe para o book1 são armazenados em um documento cuja referência é /colls/joe/docs/book1. 
-	Anexos indicando para as páginas de conteúdo de um determinado livro de um usuário são armazenados no documento correspondente, p. ex., /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2, etc. 

Observe que os exemplos utilizam IDs amigáveis para transmitir a hierarquia de recursos. Os recursos são acessados por meio das APIs REST usando IDs de recurso exclusivos.

Para a mídia gerenciada pelo Banco de Dados de Documentos, a propriedade \_media do anexo fará referência à mídia por meio de seu URI. O Banco de Dados de Documentos irá garantir que a mídia será jogada na lixeira quando todas as referências pendentes forem ignoradas. O Banco de Dados de Documentos gera automaticamente o anexo ao fazer o upload da nova mídia e preencher \_media para indicar a mídia recém-adicionada. Se escolher armazenar a mídia em um armazenamento de blob remoto por conta própria (p. ex., OneDrive, Azure Storage, DropBox, etc.), você ainda poderá usar os anexos para fazer referência à mídia. Nesse caso, você criará o anexo por conta própria e preencherá a propriedade its \_media.

Assim como todos os outros recursos, os anexos podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as APIs REST ou qualquer SDK do cliente. Assim como com os documentos, o nível de consistência de leitura dos anexos segue a política de consistência na conta do banco de dados. Essa política pode ser substituída com base em cada solicitação, dependendo dos requisitos de consistência de dados de seu aplicativo. Ao consultar anexos, a consistência de leitura segue o conjunto do modo de indexação na coleção. Para fins de “consistência”, a política de consistência da conta é seguida.
##Usuários
Um usuário do Banco de Dados de Documentos representa um namespace lógico para agrupar permissões. Um usuário do Banco de Dados de Documentos pode corresponder a um usuário em um sistema de gerenciamento de identidades ou uma função de aplicativo predefinida. Para o Banco de Dados de Documentos, um usuário simplesmente representa uma abstração para agrupar um conjunto de permissões em um banco de dados.

Para implementar multilocatários para seu aplicativo, você pode criar usuários no Banco de Dados de Documentos que correspondem aos seus usuários reais ou aos locatários de seu aplicativo. Você pode, então, criar permissões para um determinado usuário que corresponde ao controle de acesso em várias coleções, documentos, anexos etc.

Como seus aplicativos precisam ser escalados conforme o crescimento do usuário, você pode adotar várias maneiras de fragmentar seus dados. Você pode modelar seus usuários da seguinte forma:

-	Cada usuário é mapeado para um banco de dados.
-	Cada usuário é mapeado para uma coleção. 
-	Documentos correspondentes a vários usuários vão para uma coleção dedicada. 
-	Documentos correspondentes a vários usuários vão para um conjunto de coleções.   

Independentemente da estratégia de fragmentação específica escolhida, você pode modelar seus usuários reais como usuários no banco de dados do Banco de Dados de Documentos e associar permissões de refinamento a cada usuário.

![Coleções do usuário][3] **Estratégias de fragmentação e modelagem de usuários**

Assim como todos os outros recursos, os usuários no Banco de Dados de Documentos podem ser criados, substituídos, excluídos, lidos ou enumerados facilmente usando as APIs REST ou qualquer SDK do cliente. O Banco de Dados de Documentos sempre oferece uma forte consistência para leitura ou consulta dos metadados de um recurso do usuário. Vale destacar que excluir um usuário automaticamente assegura que você não poderá acessar nenhuma das permissões contidas nele. Embora o Banco de Dados de Documentos recupere a cota das permissões como parte do usuário excluído em segundo plano, as permissões excluídas estão disponíveis imediatamente mais uma vez para uso.

##Permissões
Da perspectiva de controle de acesso, recursos como contas do banco de dados, bancos de dados, usuários e permissões são considerados recursos *administrativos*, uma vez que requerem permissões administrativas. Por outro lado, recursos que incluem as coleções, documentos, anexos, procedimentos armazenados, gatilhos e UDFs têm seu escopo definido em um determinado banco de dados e são considerados como *recursos do aplicativo*. Correspondente aos dois tipos de recursos e à funções que os acessam (ou seja, o administrador e o usuário), o modelo de autorização define dois tipos de *chaves de acesso*: *chave mestra* e *chave de recurso*. A chave mestre é uma parte da conta do banco de dados e é fornecida ao desenvolvedor (ou administrador) que está provisionando a conta do banco de dados. Essa chave mestre possui uma semântica do administrador, e ela pode ser usada para autorizar o acesso aos recursos administrativos e do aplicativo. Em contraste, uma chave de recurso é uma chave de acesso granular que permite o acesso a um recurso de aplicativo *específico*. Portanto, ela captura a relação entre o usuário de um banco de dados e as permissões que o usuário possui para um recurso específico (p. ex., coleção, documento, anexo, procedimento armazenado, gatilho ou UDF).

A única maneira de obter uma chave de recurso é criar um recurso de permissão em um determinado usuário. Observe que, a fim de criar ou recuperar uma permissão, uma chave mestre deve ser apresentada no cabeçalho de autorização. Um recurso de permissão vincula o recurso, seu acesso e o usuário. Após criar um recurso de permissão, o usuário só precisa apresentar a chave de recurso associada para obter acesso ao recurso relevante. Portanto, uma chave de recurso pode ser visualizada como uma representação lógica e compacta do recurso de permissão.

Assim como todos os outros recursos, as permissões no Banco de Dados de Documentos podem ser criadas, substituídas, excluídas, lidas ou enumeradas facilmente usando as APIs REST ou qualquer SDK do cliente. O Banco de Dados de Documentos sempre oferece uma coerência forte para leitura ou consulta dos metadados de uma permissão.

##Próximas etapas
Saiba mais sobre como trabalhar com recursos usando comandos HTTP em [interações RESTful com recursos do Banco de Dados de Documentos](documentdb-interactions-with-resources.md).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png
 

<!---HONumber=Oct15_HO3-->
<properties 
	pageTitle="Introdução ao Banco de Dados de Documentos do Microsoft Azure | Azure" 
	description="Saiba mais sobre o Banco de Dados de Documentos do Microsoft Azure, um banco de dados de documentos NoSQL e seu valor para a nuvem e aplicativos móveis. Saiba como ele gerencia dados e como você pode usá-lo no desenvolvimento de aplicativos." 
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
	ms.date="05/20/2015" 
	ms.author="mimig"/>

#Introdução ao Banco de Dados de Documentos do Microsoft Azure

Este artigo fornece uma introdução ao Banco de Dados de Documentos do Microsoft Azure, um serviço de banco de dados de documentos NoSQL para desenvolvedores, profissionais de TI e tomadores de decisões comerciais.

Recomendamos que você comece assistindo ao vídeo de introdução a seguir, com duração de dois minutos, e experimente nosso [Espaço de Consulta](http://www.documentdb.com/sql/demo), no qual você pode experimentar o Banco de Dados de Documentos e executar consultas SQL em nosso conjunto de dados.

> [AZURE.VIDEO what-is-azure-documentdb]

Em seguida, volte a este artigo, onde você aprenderá as respostas para as seguintes perguntas:

-	[O que é o Banco de Dados de Documentos e qual é o valor que ele fornece para a nuvem e para os aplicativos móveis?](#what-is-docdb)
-	[Como são gerenciados os meus dados no Banco de Dados de Documentos e como acessá-los?](#data-management)
-	[Como posso desenvolver aplicativos usando o Banco de Dados de Documentos?](#develop)
-	[Quais são as próximas etapas para criar um aplicativo Banco de Dados de Documentos?](#next-steps)  

##<a name="what-is-docdb"></a>O que é o Banco de Dados de Documentos do Azure?  

Aplicativos modernos produzem, consomem e respondem rapidamente a volumes muito grandes de dados. Esses aplicativos evoluem rapidamente, assim como o esquema de dados subjacente. Em resposta a isso, os desenvolvedores têm escolhido cada vez mais bancos de dados de documentos NoSQL livres de esquema como soluções simples, rápidas e dimensionáveis para armazenar e processar dados enquanto preservam a capacidade de iterar rapidamente em modelos de dados de aplicativos e feeds de dados não estruturados. Porém, muitos bancos de dados livres de esquema não permitem o processamento transacional e de consultas complexas, dificultando o gerenciamento avançado de dados. Isso é onde entra o Banco de Dados de Documentos. A Microsoft desenvolveu o Banco de Dados de Documentos para atender a esses requisitos ao gerenciar dados para os aplicativos de hoje.

O Banco de Dados de Documentos é um serviço de banco de dados de documentos NoSQL projetado para aplicativos web e móveis modernos. O Banco de Dados de Documentos proporciona leituras e gravações consistentemente rápidas, flexibilidade de esquema e capacidade de expandir e reduzir um banco de dados sob demanda. O Banco de Dados de Documentos habilita consultas ad hoc complexas usando a linguagem SQL, dá suporte a níveis de consistência bem-definidos e oferece linguagem JavaScript integrada e processamento de transações com vários documentos usando o modelo de programação familiar de procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário).

O Banco de Dados de Documentos suporta nativamente documentos JSON, permitindo fácil iteração do esquema de aplicativos. Ele engloba a ubiquidade do JSON e JavaScript, eliminando incompatibilidades entre objetos definidos por aplicativos e o esquema do banco de dados. Ampla integração do JavaScript também permite que os desenvolvedores executem a lógica do aplicativo com eficiência e diretamente - no mecanismo de banco de dados em uma transação de banco de dados.

O Banco de Dados de Documentos do Azure oferece os principais recursos e benefícios a seguir:

-	**Consultas ad hoc com sintaxe SQL familiar:** armazene documentos JSON heterogêneos no Banco de Dados de Documentos e consulte-os com uma sintaxe SQL familiar. O Banco de Dados de Documentos utiliza uma tecnologia de indexação estruturada por log, sem bloqueio e altamente simultânea para indexar automaticamente todo o conteúdo do documento. Isso habilita consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições.

-	**Execução de JavaScript dentro do banco de dados:** expresse a lógica do aplicativo como procedimentos armazenados, gatilhos e UDFs usando JavaScript padrão. Isso permite que a lógica do aplicativo opere em dados JSON sem se preocupar com incompatibilidade entre o aplicativo e o esquema do banco de dados. O Banco de Dados de Documentos oferece execução transacional completa da lógica do aplicativo JavaScript diretamente dentro do mecanismo do banco de dados. A profunda integração do JavaScript habilita a execução das operações INSERT, REPLACE, DELETE e SELECT por meio de um programa JavaScript como uma transação isolada.

-	**Níveis de consistência ajustáveis:** escolha entre quatro níveis de consistência bem-definidos para a compensação ideal entre consistência e desempenho. Para consultas e operações de leitura, o Banco de Dados de Documentos oferece quatro níveis de consistência diferentes: strong, bounded-staleness, session e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência.

-	**Completamente gerenciado:** elimine a necessidade de gerenciar recursos de banco de dados e máquina. Como um serviço do Microsoft Azure totalmente gerenciado, você não precisa gerenciar máquinas virtuais, implantar e configurar software nem lidar com atualizações de camadas de dados complexas. Cada banco de dados é salvo em backup automaticamente e protegido contra falhas regionais. Você pode adicionar facilmente uma conta de Banco de Dados de Documentos e provisionar capacidade de acordo com a necessidade, permitindo que você se concentre no seu aplicativo, em vez de na operação e no gerenciamento do seu banco de dados.

-	**Taxa de transferência e armazenamento dimensionáveis:** escale ou reduza facilmente o Banco de Dados de Documentos para atender às necessidades do seu aplicativo. A escala é realizada através de unidades refinadas (coleções) de armazenamento com suporte de SSD reservado e taxa de transmissão. Você pode escalar elasticamente o Banco de Dados de Documentos com desempenho previsível criando mais unidades, à medida que seu aplicativo cresce.

-	**Aberto pelo design:** comece a usar o Banco de Dados de Documentos rapidamente, com habilidades e ferramentas já existentes. A programação no Banco de Dados de Documentos é simples, acessível e não requer a adoção de novas ferramentas nem a adesão a extensões personalizadas para JSON ou JavaScript. Você pode acessar toda a funcionalidade do banco de dados, incluindo CRUD, consulta e processamento do JavaScript em uma interface HTTP RESTful simples. O Banco de Dados de Documentos engloba formatos, linguagens e padrões existentes, oferecendo ainda recursos de banco de dados de alto valor.

Você pode usar o Banco de Dados de Documentos para armazenar conjuntos de dados flexíveis que exijam recuperação de consulta e processamento transacional. Os cenários do aplicativo podem incluir dados do usuário para aplicativos móveis e Web interativos, bem como armazenamento, recuperação e processamento de dados JSON do aplicativo. Um banco de dados pode armazenar qualquer número de documentos JSON. Desse modo, o Banco de Dados de Documentos é bem-adequado para aplicativos que sejam executados na Internet.

##<a name="data-management"></a>Recursos do Banco de Dados de Documentos do Azure
O Banco de Dados de Documentos do Azure gerencia dados por meio de recursos de banco de dados bem-definidos. Esses recursos são replicados para alta disponibilidade e são endereçáveis exclusivamente por seu URI lógico. O Banco de Dados de Documentos oferece um modelo de programação RESTful baseado em HTTP simples para todos os recursos.

A conta do Banco de Dados de Documentos é um namespace exclusivo que fornece acesso ao Banco de Dados de Documentos do Azure. Antes de criar uma conta de banco de dados, você deve ter uma assinatura do Azure, que dá acesso a uma variedade de serviços do Azure.

Todos os recursos no Banco de Dados de Documentos são modelados e armazenados como documentos JSON. Os recursos são gerenciados como itens, que são documentos JSON contendo metadados e como feeds que são coleções de itens. Conjuntos de itens estão contidos dentro de seus respectivos feeds.

A imagem abaixo mostra os relacionamentos entre os recursos do Banco de Dados de Documentos:

![][1]

Uma conta do banco de dados é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas pode conter procedimentos armazenados, gatilhos, UDFs, documentos e anexos relacionados. Um banco de dados também possui usuários associados, cada um com um conjunto de permissões para acessar várias outras coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos, procedimentos armazenados, gatilhos, UDFs e anexos possuem conteúdo JSON arbitrário e definido pelo usuário.

##<a name="develop"></a>Desenvolvendo no Banco de Dados de Documentos do Azure
O Banco de Dados de Documentos do Azure expõe recursos pode meio de uma API REST que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Banco de Dados de Documentos oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Banco de Dados de Documentos do Azure manipulando detalhes, como caching de endereço, gerenciamento de exceções, novas tentativas automáticas e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras a caminho:

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
- [Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
- [Java](http://go.microsoft.com/fwlink/?LinkID=402380)
- [JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
- [Python](http://go.microsoft.com/fwlink/?LinkID=402992)

Além das operações básicas de criação, leitura, atualização e exclusão, o Banco de dados de Documentos do Azure fornece uma interface de consulta SQL avançada para recuperar documentos JSON e suporte do servidor para a execução transacional da lógica JavaScript do aplicativo. As interfaces de execução de script e consulta estão disponíveis em todas as bibliotecas da plataforma, bem como as APIs REST.

###Consulta SQL
O Banco de Dados de Documentos do Azure suporta consulta de documentos usando uma linguagem SQL com raiz no sistema do tipo JavaScript e nas expressões que suportam consultas hierárquicas avançadas. A linguagem de consulta do Banco de Dados de Documentos é uma interface simples, mas poderosa para consultar documentos JSON. A linguagem suporta um subconjunto da gramática ANSI SQL e adiciona profunda integração do objeto JavaScript, matrizes, construção de objetos e invocação de funções. O Banco de Dados de Documentos fornece seu modelo de consulta sem nenhum esquema explícito ou dicas de indexação do desenvolvedor.

As UDFs podem ser registradas com o Banco de Dados de Documentos e referenciadas como parte de um consulta SQL, estendendo a gramática para dar suporte à lógica personalizada do aplicativo. Essas UDFs são gravadas como programas JavaScript e executadas no banco de dados.

Para desenvolvedores .NET, o Banco de Dados de Documentos também oferece um provedor de consultas LINQ como parte do SDK do .NET.

###Execução de transações e JavaScript
O Banco de Dados de Documentos permite gravar a lógica do aplicativo como programas nomeados gravados inteiramente em JavaScript. Esses programas são registrados para uma coleção e podem emitir operações de banco de dados nos documentos dentro de determinada coleção. O JavaScript pode ser registrado para execução como gatilho, procedimento armazenado ou função definida pelo usuário (UDF). Procedimentos armazenados e gatilhos podem criar, ler, atualizar e excluir documentos enquanto executar funções definidas pelo usuário como parte da lógica de execução de consulta sem acesso de gravação à coleção.

A execução do JavaScript dentro do Banco de Dados de Documentos é baseada nos conceitos suportados pelos sistemas do banco de dados relacional, com o JavaScript sendo uma substituição moderna para T-SQL. Toda lógica do JavaScript é executada em uma transação ACID ambiente com isolamento de instantâneo. Durante sua execução, se o JavaScript lançar uma exceção, então, toda a transação será abortada.

##<a name="next-steps"></a>Próximas etapas
Para começar a usar o Banco de Dados de Documentos do Azure, explore estes recursos:

-   [Experimente o Banco de Dados de Documentos agora](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-   [Espaço de Consulta](http://www.documentdb.com/sql/demo)
-	[Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)
-	[Interagir com recursos do Banco de Dados de Documentos](documentdb-interactions-with-resources.md)
-	[Criar uma conta de banco de dados do Banco de Dados de Documentos](documentdb-create-account.md)
-	[Introdução ao SDK do .NET do Banco de Dados de Documentos](documentdb-get-started.md)

[1]: ./media/documentdb-introduction/intro.png
 

<!---HONumber=July15_HO4-->
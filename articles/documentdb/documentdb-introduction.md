---
title: "Introdução ao DocumentDB, um banco de dados JSON | Microsoft Docs"
description: "Saiba mais sobre o Banco de Dados de Documentos do Azure, um banco de dados JSON NoSQL. Esse banco de dados de documentos foi desenvolvido para alta disponibilidade, escalabilidade elástica e big data."
keywords: banco de dados JSON, banco de dados de documentos
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 7f5e33b7f80e3c1e1e3e66b3cab879a5bc30e823
ms.openlocfilehash: 18f2c1876fab914073c8aea67f67724f281a65aa


---
# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introdução ao Banco de Dados de Documentos: um banco de dados JSON NoSQL
## <a name="what-is-documentdb"></a>O que é o DocumentDB?
O DocumentDB é um serviço de banco de dados NoSQL totalmente gerenciado criado para fornecer desempenho rápido e previsível, alta disponibilidade, dimensionamento elástico, distribuição global e facilidade de desenvolvimento. Como um banco de NoSQL sem esquema, o DocumentDB fornece recursos de consulta SQL avançados e familiares com menos latências consistentes em dados JSON, garantindo que 99% das leituras sejam feitas em até 10 milissegundos e 99% das suas gravações sejam feitas em 15 milissegundos. Esses benefícios exclusivos tornam o DocumentDB uma ótima opção para usos na Web, móveis, em jogos, na IoT e em muitos outros usos que precisam de dimensionamento perfeito e replicação global.

## <a name="how-can-i-learn-about-documentdb"></a>Como posso saber mais sobre o DocumentDB?
Uma maneira rápida de aprender sobre o Banco de Dados de Documentos e vê-lo em ação é seguir estas três etapas: 

1. Assista ao vídeo [O que é o Banco de Dados de Documentos?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) de dois minutos de duração, que apresenta os benefícios do uso do Banco de Dados de Documentos.
2. Assista ao vídeo [Criar o Banco de Dados de Documentos no Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) de três minutos de duração, que destaca como começar a usar o Banco de Dados de Documentos por meio do Portal do Azure.
3. Visite o [Query Playground](http://www.documentdb.com/sql/demo), onde você pode percorrer atividades diferentes para saber mais sobre a poderosa funcionalidade de consultas disponível no Banco de Dados de Documentos. Em seguida, vá até a guia Área Restrita e execute suas próprias consultas SQL personalizadas e experimente o Banco de Dados de Documentos.

Em seguida, retorne a este artigo para nos aprofundarmos.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Quais são os principais recursos oferecidos pelo DocumentDB?
O Banco de Dados de Documentos do Azure oferece os principais recursos e benefícios a seguir:

* **Taxa de transferência e armazenamento elasticamente escalonável:** escale ou reduza verticalmente o banco de dados JSON Banco de Dados de Documentos facilmente para atender às necessidades do seu aplicativo. Os dados são armazenados em SSD (discos de estado sólido) para fornecer latências baixas previsíveis. O Banco de Dados de Documentos dá suporte a contêineres para armazenar dados JSON chamados de coleções, que podem ser dimensionados para tamanhos de armazenamento praticamente ilimitados e taxa de transferência provisionada. Você pode dimensionar elasticamente o Banco de Dados de Documentos com desempenho previsível sem problemas, à medida que o aplicativo cresce. 
* **Replicação de várias regiões:** o DocumentDB replica de forma transparente os dados para todas as regiões que você associou à sua conta do DocumentDB, habilitando-o a desenvolver aplicativos que exigem acesso global aos dados e fornecendo compensações entre consistência, disponibilidade e desempenho, com garantias correspondentes. O Banco de Dados de Documentos fornece failover regional transparente com APIs de hospedagem múltipla e capacidade de dimensionar de forma elástica a taxa de transferência e o armazenamento em todo o mundo. Saiba mais em [Distribuir dados globalmente com o DocumentDB](documentdb-distribute-data-globally.md).
* **Consultas ad hoc com sintaxe SQL familiar:** armazene documentos JSON heterogêneos no Banco de Dados de Documentos e consulte-os com uma sintaxe SQL familiar. O Banco de Dados de Documentos utiliza uma tecnologia de indexação estruturada por log, sem bloqueio e altamente simultânea para indexar automaticamente todo o conteúdo do documento. Isso habilita consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições. Saiba mais em [Consultar o Banco de Dados de Documentos](documentdb-sql-query.md). 
* **Execução de JavaScript dentro do banco de dados:** expresse a lógica do aplicativo como procedimentos armazenados, gatilhos e UDFs usando JavaScript padrão. Isso permite que a lógica do aplicativo opere em dados sem se preocupar com incompatibilidade entre o aplicativo e o esquema do banco de dados. O Banco de Dados de Documentos oferece execução transacional completa da lógica do aplicativo JavaScript diretamente dentro do mecanismo do banco de dados. A profunda integração do JavaScript habilita a execução das operações INSERT, REPLACE, DELETE e SELECT por meio de um programa JavaScript como uma transação isolada. Saiba mais em [Programação no lado servidor do Banco de Dados de Documentos](documentdb-programming.md).
* **Níveis de consistência ajustáveis:** escolha entre quatro níveis de consistência bem-definidos para a compensação ideal entre consistência e desempenho. Para consultas e operações de leitura, o Banco de Dados de Documentos oferece quatro níveis de consistência diferentes: strong, bounded-staleness, session e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência. Saiba mais em [Usando níveis de consistência para maximizar a disponibilidade e o desempenho no Banco de Dados de Documentos](documentdb-consistency-levels.md).
* **Completamente gerenciado:** elimine a necessidade de gerenciar recursos de banco de dados e máquina. Como um serviço do Microsoft Azure totalmente gerenciado, você não precisa gerenciar máquinas virtuais, implantar e configurar software,gerenciar o dimensionamento nem lidar com atualizações de camadas de dados complexas. Cada banco de dados é salvo em backup automaticamente e protegido contra falhas regionais. Você pode adicionar facilmente uma conta de Banco de Dados de Documentos e provisionar capacidade de acordo com a necessidade, permitindo que você se concentre no seu aplicativo, em vez de na operação e no gerenciamento do seu banco de dados. 
* **Aberto pelo design:** comece a usar o Banco de Dados de Documentos rapidamente, com habilidades e ferramentas já existentes. A programação no Banco de Dados de Documentos é simples, acessível e não requer a adoção de novas ferramentas nem a adesão a extensões personalizadas para JSON ou JavaScript. Você pode acessar toda a funcionalidade do banco de dados, incluindo CRUD, consulta e processamento do JavaScript em uma interface HTTP RESTful simples. O Banco de Dados de Documentos engloba formatos, linguagens e padrões existentes, oferecendo ainda recursos de banco de dados de alto valor.
* **Indexação automática:** por padrão, o DocumentDB indexa automaticamente todos os documentos no banco de dados e não espera ou exige qualquer esquema ou criação de índices secundários. Não deseja indexar tudo? Não se preocupe, você também pode [recusar caminhos nos arquivos JSON](documentdb-indexing-policies.md) .

## <a name="a-namedata-managementahow-does-documentdb-manage-data"></a><a name="data-management"></a>Como o DocumentDB gerencia dados?
O Azure DocumentDB gerencia dados JSON por meio de recursos de banco de dados bem-definidos. Esses recursos são replicados para alta disponibilidade e são endereçáveis exclusivamente por seu URI lógico. O Banco de Dados de Documentos oferece um modelo de programação RESTful baseado em HTTP simples para todos os recursos. 

A conta do Banco de Dados de Documentos é um namespace exclusivo que fornece acesso ao Banco de Dados de Documentos do Azure. Antes de criar uma conta de banco de dados, você deve ter uma assinatura do Azure, que dá acesso a uma variedade de serviços do Azure. 

Todos os recursos no Banco de Dados de Documentos são modelados e armazenados como documentos JSON. Os recursos são gerenciados como itens, que são documentos JSON contendo metadados e como feeds que são coleções de itens. Conjuntos de itens estão contidos dentro de seus respectivos feeds.

A imagem abaixo mostra os relacionamentos entre os recursos do Banco de Dados de Documentos:

![A relação hierárquica entre os recursos no Banco de Dados de Documentos, um banco de dados JSON NoSQL][1] 

Uma conta do banco de dados é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas pode conter procedimentos armazenados, gatilhos, UDFs, documentos e anexos relacionados. Um banco de dados também possui usuários associados, cada um com um conjunto de permissões para acessar várias outras coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos, procedimentos armazenados, gatilhos, UDFs e anexos possuem conteúdo JSON arbitrário e definido pelo usuário.  

## <a name="a-namedevelopa-how-can-i-develop-apps-with-documentdb"></a><a name="develop"></a> Como posso desenvolver aplicativos com o DocumentDB?
O Banco de Dados de Documentos do Azure expõe recursos pode meio de uma API REST que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Banco de Dados de Documentos oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Banco de Dados de Documentos do Azure manipulando detalhes, como caching de endereço, gerenciamento de exceções, novas tentativas automáticas e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas:  

| Baixar | Documentação |
| --- | --- |
| [SDK .NET](http://go.microsoft.com/fwlink/?LinkID=402989) |[Biblioteca .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [SDK do Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Biblioteca do Node.js](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Biblioteca do Java](http://azure.github.io/azure-documentdb-java/) |
| [SDK do JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Biblioteca do JavaScript](http://azure.github.io/azure-documentdb-js/) |
| n/d |[SDK do JavaScript do lado servidor](http://azure.github.io/azure-documentdb-js-server/) |
| [SDK do Python](https://pypi.python.org/pypi/pydocumentdb) |[Biblioteca do Python](http://azure.github.io/azure-documentdb-python/) |

Com o [Emulador do DocumentDB do Azure](documentdb-nosql-local-emulator.md) você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura Azure ou incorrer em custos. Quando você estiver satisfeito com o funcionamento de seu aplicativo no Emulador do DocumentDB, alterne para uma conta do DocumentDB do Azure na nuvem.

Além das operações básicas de criação, leitura, atualização e exclusão, o Banco de dados de Documentos fornece uma interface de consulta SQL avançada para recuperar documentos JSON e suporte do servidor para a execução transacional da lógica JavaScript do aplicativo. As interfaces de execução de script e consulta estão disponíveis em todas as bibliotecas da plataforma, bem como as APIs REST. 

### <a name="sql-query"></a>Consulta SQL
O Banco de Dados de Documentos do Azure dá suporte à consulta de documentos usando uma linguagem SQL baseada no sistema do tipo JavaScript e em expressões com suporte a consultas relacionais, hierárquicas e espaciais. A linguagem de consulta do Banco de Dados de Documentos é uma interface simples, mas poderosa para consultar documentos JSON. A linguagem suporta um subconjunto da gramática ANSI SQL e adiciona profunda integração do objeto JavaScript, matrizes, construção de objetos e invocação de funções. O Banco de Dados de Documentos fornece seu modelo de consulta sem nenhum esquema explícito ou dicas de indexação do desenvolvedor.

As UDFs podem ser registradas com o Banco de Dados de Documentos e referenciadas como parte de um consulta SQL, estendendo a gramática para dar suporte à lógica personalizada do aplicativo. Essas UDFs são gravadas como programas JavaScript e executadas no banco de dados. 

Para desenvolvedores .NET, o Banco de Dados de Documentos também oferece um provedor de consultas LINQ como parte do [SDK do .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Execução de transações e do JavaScript
O Banco de Dados de Documentos permite gravar a lógica do aplicativo como programas nomeados gravados inteiramente em JavaScript. Esses programas são registrados para uma coleção e podem emitir operações de banco de dados nos documentos dentro de determinada coleção. O JavaScript pode ser registrado para execução como gatilho, procedimento armazenado ou função definida pelo usuário (UDF). Procedimentos armazenados e gatilhos podem criar, ler, atualizar e excluir documentos enquanto executar funções definidas pelo usuário como parte da lógica de execução de consulta sem acesso de gravação à coleção.

A execução do JavaScript dentro do Banco de Dados de Documentos é baseada nos conceitos suportados pelos sistemas do banco de dados relacional, com o JavaScript sendo uma substituição moderna para Transact-SQL. Toda lógica do JavaScript é executada em uma transação ACID ambiente com isolamento de instantâneo. Durante sua execução, se o JavaScript lançar uma exceção, então, toda a transação será abortada.

## <a name="next-steps"></a>Próximas etapas
Já possui uma conta do Azure? Se você já tiver uma conta do Azure, poderá começar a usar o DocumentDB no [Portal do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) [criando uma conta de banco de dados do DocumentDB](documentdb-create-account.md).

Não tem uma conta do Azure? Você pode:

* Inscrever-se em uma [avaliação gratuita do Azure](https://azure.microsoft.com/free/), que oferece a você 30 dias e US$ 200 para experimentar todos os serviços do Azure. 
* Se tiver uma assinatura do MSDN, você estará qualificado para receber [US$ 150 em créditos gratuitos do Azure por mês](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usar em qualquer serviço do Azure. 
* Baixe o [Emulador de DocumentDB do Azure](documentdb-nosql-local-emulator.md) para desenvolver seu aplicativo localmente.

Em seguida, quando você estiver pronto para saber mais, visite nosso [roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para navegar por todos os recursos de aprendizado disponíveis para você. 

[1]: ./media/documentdb-introduction/json-database-resources1.png




<!--HONumber=Jan17_HO2-->



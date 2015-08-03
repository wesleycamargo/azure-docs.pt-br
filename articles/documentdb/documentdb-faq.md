<properties 
	pageTitle="Perguntas frequentes sobre o Banco de Dados de Documentos | Azure" 
	description="Respostas para perguntas frequentes sobre o serviço de Banco de Dados de Documentos nosql do Banco de Dados de Documentos do Azure. Saiba mais sobre capacidade e unidades de solicitação e entender como atendem às necessidades do seu aplicativo." 
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
	ms.date="04/29/2015" 
	ms.author="mimig"/>


#Perguntas frequentes sobre o Banco de Dados de Documentos

## <a id="fundamentals"></a> Noções básicas sobre o Banco de Dados de Documentos do Microsoft Azure

###O que é o Banco de Dados de Documentos do Microsoft Azure? 
O Banco de Dados de Documentos do Microsoft Azure é um serviço de banco de dados de documentos NoSQL altamente escalonável que oferece consulta sofisticada de dados sem esquemas, ajuda a oferecer um desempenho confiável e configurável e habilita um desenvolvimento rápido – tudo isso por meio de uma plataforma gerenciada com o suporte e o alcance do Microsoft Azure. O Banco de Dados de Documentos é a solução certa para aplicativos Web e dispositivos móveis, nos quais taxa de transferência previsível, baixa latência e modelo de dados sem esquema são requisitos essenciais. O Banco de Dados de Documentos fornece flexibilidade de esquemas e indexação sofisticada por meio de um modelo de dados JSON nativo e inclui suporte de transações com diversos documentos com JavaScript integrado.
  
Para obter instruções de como implantar e usar esse serviço, consulte a [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###Que tipo de banco de dados é o Banco de Dados de Documentos?
O Banco de Dados de Documentos é um banco de dados voltado a documentos NoSQL que armazena dados no formato JSON. O Banco de Dados de Documentos oferece suporte a estruturas de dados autocontidos aninhados, que podem ser consultados com uma gramática sofisticada de consulta SQL do Banco de Dados de Documentos. O Banco de Dados de Documentos fornece processamento de transações de alto desempenho de JavaScript por parte do servidor com procedimentos armazenados, gatilhos e funções definidas pelo usuário. O banco de dados suporta também níveis de consistência ajustáveis pelo desenvolvedor, com níveis de desempenho associados.
 
###Os bancos de dados do Banco de Dados de Documentos têm tabelas como um RDBMS?
Não, o Banco de Dados de Documentos armazena dados em coleções de documentos JSON. Para obter informações sobre os recursos do Banco de Dados de Documentos, consulte o artigo [Modelo e conceitos de recursos do Banco de Dados de Documentos](documentdb-resources.md).

###Os bancos de dados do Banco de Dados de Documentos têm suporte para dados sem esquema?
Sim, o Banco de Dados de Documentos permite que os aplicativos armazenem documentos JSON arbitrários sem definições ou dicas de esquema. Os dados ficam disponíveis imediatamente para consulta com a interface de consulta SQL do Banco de Dados de Documentos.

###O Banco de Dados de Documentos tem suporte para transações ACID?
Sim, o Banco de Dados de Documentos suporta transações entre documentos expressas como procedimentos e gatilhos armazenados de JavaScript. As transações têm como escopo uma coleção única e são executados com semântica ACID como tudo ou nada, isolados de outras solicitações de códigos e de usuários executadas simultaneamente. Se exceções forem emitidas por parte da execução do código de aplicativo JavaScript pelo servidor, toda a transação será revertida.

###Quais são os casos de uso típicos do Banco de Dados de Documentos?  
O Banco de Dados de Documentos é uma boa opção para novos aplicativos Web e dispositivos móveis nos quais escala, desempenho e a capacidade de consultar dados sem esquema são importantes. O Banco de Dados de Documentos funciona bem em contextos de desenvolvimento rápido e dá suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são casos de uso comuns do Banco de Dados de Documentos.

###Quais são os limites de dimensionamento do Banco de Dados de Documentos?
Contas do Banco de Dados de Documentos podem ser dimensionadas em termos de armazenamento e taxa de transferência com a adição de coleções. Consulte [Limites do Banco de Dados de Documentos](documentdb-limits.md) para ver as cotas de serviço para o número de coleções. Se precisar de coleções adicionais, [entre em contato com o suporte](documentdb-increase-limits.md) para que a cota de sua conta seja aumentada.

###Quanto custa o Banco de Dados de Documentos do Microsoft Azure?
Consulte a página [Detalhes de preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317) para obter mais informações.

## <a id="setup"></a> Configurar o Banco de Dados de Documentos do Microsoft Azure

###Como me inscrevo no Banco de Dados de Documentos do Microsoft Azure?
O Banco de Dados de Documentos do Microsoft Azure está disponível no [Portal de visualização do Azure][azure-portal]. Primeiro, você precisa se inscrever para uma assinatura do Microsoft Azure. Após se inscrever para uma assinatura do Microsoft Azure, e possível adicionar uma conta do Banco de Dados de Documentos à sua assinatura do Azure usando o Marketplace.

###O que é uma chave mestra?
Uma chave mestra é um token de segurança para acessar todos os recursos de uma conta. As pessoas que possuem a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Seja cauteloso ao distribuir chaves mestra. A chave mestra primária e a secundária estão disponíveis na folha Chaves do [portal de Visualização do Azure][azure-portal].

###Como crio um banco de dados?
Você pode criar bancos de dados usando um dos SDKs do Banco de Dados de Documentos ou as APIs REST. Consulte a seção Desenvolvimento na [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319) para obter mais informações sobre como desenvolver aplicativos.

###O que é uma coleção?
Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. As consultas e transações estão dentro do escopo das coleções. Você pode armazenar um conjunto de documentos JSON heterogêneos em uma única coleção, sendo todos eles indexados automaticamente.

###Há limites para os bancos de dados e coleções?
Cada coleção vem com uma alocação de armazenamento do banco de dados e uma taxa de transferência em um dos níveis de desempenho com suporte. Para obter mais informações sobre os níveis de desempenho, consulte [Níveis de Desempenho](documentdb-performance-levels.md). Também há cotas para cada recurso gerenciado pelo serviço. Consulte [Limites do Banco de Dados de Documentos](documentdb-limits.md) para obter mais informações.

###Como configuro usuários e permissões?
Você pode criar usuários e permissões usando um dos SDKs do Banco de Dados de Documentos ou as APIs REST. Para obter detalhes, consulte a seção de Desenvolvimento na [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319) para obter mais informações sobre como desenvolver aplicativos.


## <a id="develop"></a>Desenvolver no Banco de Dados de Documentos do Microsoft Azure

###Como começo a desenvolver no Banco de Dados de Documentos?
Os SDKs estão disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem aproveitar as APIs HTTP RESTful para interagir com os recursos do Banco de Dados de Documentos em uma série de plataformas e linguagens. Para obter detalhes sobre como usar esses SDKs, consulte a seção Desenvolvimento na [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###O Banco de Dados de Documentos dá suporte ao SQL?
A linguagem de consulta SQL do Banco de Dados de Documentos fornece extensibilidade e operadores hierárquicos e relacionais por meio de JavaScript com base em UDFs (funções definidas por usuário). A gramática JSON permite modelar documentos JSON como árvores com rótulos como os nós da árvore, o que é usado pelas técnicas de indexação automática do Banco de Dados de Documentos, bem como pelo dialeto de consulta SQL do Banco de Dados de Documentos. Para obter detalhes sobre como usar a gramática SQL, consulte o artigo [Consultar usando SQL do Banco de Dados de Documentos][query].

###Quais são os tipos de dados com suporte pelo Banco de Dados de Documentos?
Os tipos de dados primitivos suportados no Banco de Dados de Documentos são os mesmos que os do JSON. O JSON tem um sistema de tipo simples que consiste em cadeias de caracteres, números (precisão dupla IEEE754) e boolianos – verdadeiro e falso e nulos. Os tipos de dados mais complexos como DateTime, Guid, Int64 e Geometria podem ser representados tanto no JSON quanto no Banco de Dados de Documentos com a criação de objetos aninhados usando o operador { } e matrizes usando o operador [ ].

###Como o Banco de Dados de Documentos fornece simultaneidade?
O Banco de Dados de Documentos suporta o controle de simultaneidade otimista (OCC) por meio de marcas de entidade HTTP ou ETags. Cada recurso do Banco de Dados de Documentos tem uma ETag, e os clientes do Banco de Dados de Documentos incluem sua versão de leitura mais recente em solicitações de gravação. Se a ETag for atual, a alteração é aplicada. Se o valor tiver sido alterado externamente, o servidor rejeita a gravação com um código de resposta "Falha de pré-condição HTTP 412". Os clientes devem ler a versão mais recente do recurso e repetir a solicitação.

###Como executo transações no Banco de Dados de Documentos?
O Banco de Dados de Documentos suporta transações integradas de linguagens com procedimentos e gatilhos armazenados de JavaScript. Todas as operações de bancos de dados dentro de scripts são executadas em isolamento instantâneo cujo escopo é a coleção. E obtido um instantâneo das versões do documento (ETags) no início da transação e confirmado somente se o script for bem-sucedido. Se o JavaScript emitir um erro, a transação será revertida. Consulte [Programação do Banco de Dados de Documentos no servidor](documentdb-programming.md).

###Como posso inserir documentos em massa no Banco de Dados de Documentos? 
O Banco de Dados de Documentos dá suporte a procedimentos armazenados que oferecem uma forma eficiente de inserção em lotes. Ao desenvolver um procedimento simples armazenado de JavaScript que aceite e insira documentos, você poderá realizar inserções em massa. Com isso, você tem a vantagem adicional de que a inserção em massa será executada como uma transação, deixando a coleção em um estado consistente. Para obter detalhes sobre o modelo de programação, consulte a seção Desenvolvimento na [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###O Banco de Dados de Documentos tem suporte para cache de link?
Sim, como o Banco de Dados de Documentos é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Clientes do Banco de Dados de Documentos podem especificar um cabeçalho "If-None-Match" para leituras em qualquer recurso, como documento e coleção, e atualizar suas cópias locais somente quando a versão do servidor for alterada.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=July15_HO4-->
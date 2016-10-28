<properties
	pageTitle="Quando usar NoSQL versus SQL | Microsoft Azure"
	description="Compare os benefícios do uso de soluções NoSQL não relacionais no que tange às soluções SQL. Saiba se um dos serviços do NoSQL do Microsoft Azure ou soluções do SQL Server é mais adequado ao seu cenário."
	keywords="nosql vs. sql, quando usar NoSQL, sql vs. nosql"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/24/2016"
	ms.author="mimig"/>

# NoSQL versus SQL

O SQL Server e os RDBMS (bancos de dados relacionais) foram os bancos de dados usados por mais de 20 anos. No entanto, com o aumento da necessidade de processar volumes maiores e vários tipos de dados a uma taxa rápida, foi alterada a natureza das necessidades de armazenamento de dados para os desenvolvedores de aplicativos. Para habilitar esse cenário, bancos de dados NoSQL que habilitam o armazenamento de dados não estruturados e heterogêneos em escala tornaram-se mais populares.

O NoSQL é uma categoria de bancos de dados distintamente diferente dos bancos de dados SQL. O NoSQL é geralmente usado para referir-se a sistemas de gerenciamento de dados que são "Não SQL" ou uma abordagem de gerenciamento de dados que inclui "Não apenas SQL". Há diversas tecnologias na categoria de NoSQL, incluindo bancos de dados de documentos, repositórios de valores de chaves, repositórios de famílias de colunas e bancos de dados de gráficos, que são populares para aplicativos de jogos, sociais e da IoT.

![Diagrama de visão geral do NoSQL versus SQL que demonstra modelos de dados e cenários comuns](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

O objetivo deste artigo é ajudá-lo a saber mais sobre as diferenças entre o NoSQL e o SQL e fornecer uma introdução às ofertas de SQL e NoSQL da Microsoft.

## Quando usar o NoSQL?

Vamos imaginar que você esteja criando um novo site de envolvimento social. Os usuários podem criar postagens e adicionar imagens, vídeos e música a elas. Outros usuários podem comentar sobre as postagens e dar pontos (curtidas) para classificá-las. A página inicial terá um feed de postagens que os usuários poderão compartilhar e com o qual poderão interagir.

Então, como você armazena esses dados? Se estiver familiarizado com o SQL, você poderá começar a criar algo assim:

![Diagrama do NoSQL versus SQL que mostra o modelo de dados relacional para um site de envolvimento social](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Até aqui, tudo bem, mas agora considere a estrutura de uma única postagem e como exibi-la. Para mostrar a postagem e imagens, áudio, vídeo, comentários, pontos e informações do usuário associados em um site ou aplicativo, você teria de executar uma consulta com oito junções de tabela apenas para recuperar o conteúdo. Agora, imagine um fluxo de postagens que são carregadas dinamicamente e exibidas na tela. Você pode prever facilmente que isso vai exigir milhares de consultas e muitas junções para que a tarefa seja concluída.

Você pode usar uma solução relacional como o SQL Server para armazenar os dados, mas há outra opção, uma opção NoSQL que simplifica a abordagem. Transformando a postagem em um documento JSON, como o exemplo a seguir, e armazenando-o no Banco de Dados de Documentos, um serviço de banco de dados de documentos NoSQL do Azure, você pode aumentar o desempenho e recuperar a postagem inteira com apenas uma consulta e sem junções. É uma opção mais simples, mais direta e mais eficaz.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Além disso, os dados podem ser particionados por id de postagem, permitindo que os dados sejam escalados horizontalmente de forma natural e tirem proveito das características de escala do NoSQL. Além disso, os sistemas NoSQL permitem aos desenvolvedores simplificar a consistência e oferecer aplicativos altamente disponíveis. Por fim, essa solução não exige que os desenvolvedores definam, gerenciem e mantenham o esquema na camada de dados que permite a iteração rápida.

Você pode então expandir essa solução usando outros serviços do Azure:

- A [Pesquisa do Azure](https://azure.microsoft.com/services/search/) pode ser usada por meio do aplicativo Web para habilitar os usuários a pesquisar postagens.
- Os [Serviços de Aplicativos do Azure](https://azure.microsoft.com/services/app-service/) podem ser usados para hospedar aplicativos e processos em segundo plano.
- O [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/) pode ser usado para armazenar perfis de usuário completos, incluindo imagens.
- O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) pode ser usado para armazenar enormes quantidades de dados, como informações de logon, e dados para análise de uso.
- O [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/) pode ser usado para criar o conhecimento e a inteligência que podem fornecer comentários para o processo e ajudar a fornecer o conteúdo certo aos usuários certos.

O site de envolvimento social é apenas um cenário no qual um banco de dados NoSQL é o modelo de dados correto para o trabalho. Se estiver interessado em ler mais sobre esse cenário e sobre como modelar os dados para o Banco de Dados de Documentos em aplicativos de mídia social, confira [Expandindo o Banco de Dados de Documentos para as redes sociais](documentdb-social-media-apps.md).

## Comparação entre o SQL e o NoSQL

A tabela a seguir compara as principais diferenças entre o NoSQL e o SQL.

![Diagrama do NoSQL versus SQL que mostra quando usar o NoSQL e quando usar o SQL. Comparação entre o NoSQL e o SQL](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Se um banco de dados NoSQL atende melhor às suas necessidades, continue na próxima seção para saber mais sobre os serviços do NoSQL disponíveis no Azure. Caso contrário, se um banco de dados SQL atender melhor às suas necessidades, vá para [Quais são as ofertas do Microsoft SQL?](#what-are-the-microsoft-sql-offerings)

## Quais são as ofertas do NoSQL do Microsoft Azure?

O Azure tem quatro serviços de NoSQL totalmente gerenciados:

- [Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/)
- [Armazenamento de Tabelas do Azure](https://azure.microsoft.com/services/storage/)
- [HBase do Azure como parte do HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Cache Redis do Azure](https://azure.microsoft.com/services/cache/)

O gráfico de comparação a seguir mapeia os principais diferenciais para cada serviço. Qual deles descreve com mais precisão as necessidades de seu aplicativo?

![Diagrama do NoSQL versus SQL mostrando quando usar as ofertas do NoSQL do Microsoft Azure, incluindo o Banco de Dados de Documentos, o Armazenamento de Tabelas, o HBase como parte do HDInsight e o Cache Redis](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Se um ou mais desses serviços podem atender às necessidades de seu aplicativo, saiba mais com os seguintes recursos:

- [Roteiro de aprendizagem do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/) e [Casos de uso do Banco de Dados de Documentos](documentdb-use-cases.md)
- [Introdução ao Armazenamento de Tabelas do Azure](../storage/storage-dotnet-how-to-use-tables.md)
- [O que é o HBase no HDInsight](../hdinsight/hdinsight-hbase-overview.md)
- [Roteiro de aprendizagem do Cache Redis](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

Em seguida, vá para [Próximas etapas](#next-steps) para obter informações sobre a avaliação gratuita.

## Quais são as ofertas do Microsoft SQL?

A Microsoft tem cinco ofertas de SQL:

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
- [SQL Server nas Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Azure SQL Data Warehouse (visualização)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [Analytics Platform System (dispositivo local)](https://www.microsoft.com/pt-BR/server-cloud/products/analytics-platform-system/)

Se você estiver interessado no SQL Server em uma Máquina Virtual ou em um Banco de Dados SQL, leia [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) para saber mais sobre as diferenças entre os dois.

Se o SQL parece ser a melhor opção, vá para [SQL Server](https://www.microsoft.com/server-cloud/products/) para saber mais sobre o que nossos produtos e serviços Microsoft SQL têm a oferecer.

Em seguida, vá para [Próximas etapas](#next-steps) para obter links de avaliação e de avaliação gratuita.

## Próximas etapas

Convidamos você a saber mais sobre nossos produtos SQL e NoSQL experimentando-os gratuitamente.

- Para todos os serviços do Azure, você pode se inscrever em uma [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/) e receber US$ 200 para gastar em qualquer um dos serviços do Azure.
    - [Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/)
    - [HBase do Azure como parte do HDInsight](https://azure.microsoft.com/services/hdinsight/)
    - [Cache Redis do Azure](https://azure.microsoft.com/services/cache/)
    - [Azure SQL Data Warehouse (visualização)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
    - [Armazenamento de Tabelas do Azure](https://azure.microsoft.com/services/storage/)

- Você pode criar uma [versão de avaliação do SQL Server 2016 em uma máquina virtual](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) ou baixar uma [versão de avaliação do SQL Server](https://www.microsoft.com/pt-BR/evalcenter/evaluate-sql-server-2016).
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server nas Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)

<!---HONumber=AcomDC_0727_2016-->
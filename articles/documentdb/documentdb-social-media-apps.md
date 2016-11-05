---
title: 'Padrão de design do Banco de Dados de Documentos: aplicativos de mídia social | Microsoft Docs'
description: Saiba mais sobre um padrão de design para Redes Sociais, aproveitando a flexibilidade de armazenamento do Banco de Dados de Documentos e outros serviços do Azure.
keywords: aplicativos de mídia social
services: documentdb
author: ealsur
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mimig

---
# <a name="going-social-with-documentdb"></a>Expandindo o Banco de Dados de Documentos para as redes sociais
Viver em uma sociedade amplamente interconectada significa que, em determinado momento da vida, você acaba se tornando parte de uma **rede social**. Usamos redes sociais para manter contato com amigos, colegas, família ou, às vezes, para compartilhar nosso entusiasmo com pessoas que têm os mesmos interesses.

Como engenheiros ou desenvolvedores, talvez já tenhamos nos perguntado como essas redes armazenam e interconectam nossos dados ou, até mesmo, já tenhamos recebido a tarefa de criar ou estruturar uma nova rede social para determinado nicho de mercado. É nesse momento que surge a grande pergunta: Como todos esses dados são armazenados?

Vamos supor que estamos criando uma nova rede social, na qual nossos usuários podem postar artigos com mídia relacionada como imagens, vídeos ou, até mesmo, música. Os usuários podem fazer comentários sobre as postagens e fornecer pontos para classificações. Haverá um feed de postagens que será visto pelos usuários e com o qual poderão interagir na página de aterrissagem do site principal. Isso não parece realmente complexo (logo de início), mas para simplificar, vamos parar por aqui (poderíamos nos aprofundar em feeds do usuário personalizados e afetados por relacionamentos, mas isso iria além do objetivo deste artigo).

Então, como e onde armazenamos tudo isso?

É provável que muitos de vocês conheçam a fundo os bancos de dados SQL ou, pelo menos, têm uma noção de [modelagem relacional de dados](https://en.wikipedia.org/wiki/Relational_model) e podem ficar tentados a começar a esboçar algo do tipo:

![Diagrama ilustrando um modelo relacional relativo](./media/documentdb-social-media-apps/social-media-apps-sql.png) 

Uma bela estrutura de dados perfeitamente normalizada... que não pode ser dimensionada. 

Não me entendam mal: sempre trabalhei com bancos de dados SQL; eles são ótimos, mas como acontece com cada padrão, prática e plataforma de software, eles não são perfeitos para todos os cenários.

Por que o SQL não é a melhor opção nesse cenário? Vamos examinar a estrutura de uma única postagem. Se eu quisesse mostrar essa postagem em um site ou aplicativo, teria que fazer uma consulta com... oito junções de tabela (!) para mostrar uma única postagem. Imagine uma transmissão de postagens carregadas dinamicamente e exibidas na tela e você poderá entender a que estou me referindo.

É claro que, para atender às necessidades de nosso conteúdo, poderíamos usar uma instância SQL gigantesca com capacidade suficiente para resolver milhares de consultas com essas várias junções, mas, sinceramente, por que faríamos isso quando há uma solução mais simples?

## <a name="the-nosql-road"></a>O caminho NoSQL
Existem bancos de dados de graficos especiais que podem ser [executados no Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure), mas eles não são baratos e exigem serviços de IaaS (Infraestrutura como Serviço, principalmente Máquinas Virtuais), bem como manutenção. Meu objetivo neste artigo é fornecer uma solução de custo mais baixo que funcionará para a maioria dos cenários e que é executada no banco de dados NoSQL do Azure, o [DocumentDB](https://azure.microsoft.com/services/documentdb/). Usando uma abordagem [NoSQL](https://en.wikipedia.org/wiki/NoSQL), armazenando os dados no formato JSON e aplicando a [desnormalização](https://en.wikipedia.org/wiki/Denormalization), nossa postagem anteriormente complicada pode ser transformada em um único [Documento](https://en.wikipedia.org/wiki/Document-oriented_database):

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

E isso pode ser obtido com uma única consulta, sem junções. É muito mais simples e econômico, além de exigir menos recursos para alcançar um resultado melhor.

O Azure DocumentDB garante que todas as propriedades sejam indexadas com a [indexação automática](documentdb-indexing.md), que pode até mesmo ser [personalizada](documentdb-indexing-policies.md). A abordagem sem esquemas nos permite armazenar Documentos com estruturas diferentes e dinâmicas; talvez amanhã, vamos querer que as postagens tenham uma lista de categorias ou hashtags associadas a elas, e o Banco de Dados de Documentos manipulará os novos Documentos com os atributos adicionados sem que precisemos fazer nenhum trabalho extra.

Os comentários em uma postagem podem ser tratados da mesma forma como outras postagens com uma propriedade pai (isso simplifica nosso mapeamento de objeto). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Além disso, todas as interações sociais podem ser armazenadas em um objeto separado como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

A criação de feeds resume-se à criação de documentos que podem reter uma lista de IDs da postagem com determinada ordem de relevância:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Poderíamos ter um fluxo “mais recentes” com as postagens ordenadas por data de criação, um fluxo “mais populares” com as postagens com o maior número de curtidas nas últimas 24 horas... Poderíamos, até mesmo, implementar um fluxo personalizado para cada usuário baseado em lógica como seguidores e interesses, e ainda seria uma lista de postagens. É uma questão de como criar essas listas, mas fazer com que o desempenho de leitura permaneça ilimitado. Depois de adquirirmos uma dessas listas, emitimos uma única consulta para o DocumentDB usando o [operador IN](documentdb-sql-query.md#where-clause) para obter páginas de postagens de uma só vez.

As transmissões de feed podem ser criadas usando processos em segundo plano dos [Serviços de Aplicativos do Azure](https://azure.microsoft.com/services/app-service/): [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Depois de criar uma postagem, o processamento em segundo plano pode ser disparado usando o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), ao passo que as [Filas](../storage/storage-dotnet-how-to-use-queues.md) e os Webjobs podem ser disparados usando o [SDK do Azure Webjobs](../app-service-web/websites-dotnet-webjobs-sdk.md), implementando a propagação de postagem nas transmissões, de acordo com nossa própria lógica personalizada. 

Os pontos e as curtidas de uma postagem podem ser processados de forma adiada usando essa mesma técnica, a fim de criar um ambiente que, no final das contas, seja consistente.

Os seguidores são mais complicados. O Banco de Dados de Documentos tem um limite de tamanho de documento de 512Kb, portanto, pense em armazenar seguidores como um documento com essa estrutura:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Isso pode funcionar para um usuário com alguns milhares seguidores, mas, no caso de algumas celebridades, essa abordagem eventualmente atingirá o limite de tamanho do documento.

Para resolver isso, podemos usar uma abordagem mista. Como parte do documento de Estatísticas do Usuário, podemos armazenar o número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

E o gráfico real de seguidores pode ser armazenado nas Tabelas do Armazenamento do Azure usando uma [extensão](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) que permite o armazenamento e recuperação simples ao estilo "A-segue-B". Dessa forma, podemos delegar o processo de recuperação da lista exata de seguidores (quando for necessário) às Tabelas de Armazenamento do Azure, mas para uma pesquisa rápida de números, continuamos usando o Banco de Dados de Documentos.

## <a name="the-“ladder”-pattern-and-data-duplication"></a>O padrão de “Escada” e a duplicação de dados
Como vocês devem ter observado no documento JSON que faz referência a uma postagem, há várias ocorrências de um usuário. E devem ter acertado: isso significa que as informações que representam um usuário, considerando essa desnormalização, podem estar presentes em mais de um local.

Para possibilitar consultas mais rápidas, ficamos sujeitos à duplicação de dados. O problema com esse efeito colateral é que, se por alguma ação, ocorrerem alterações aos dados de um usuário, precisaremos encontrar todas as atividades já realizadas por ele e atualizá-las. Não parece muito prático, certo?

Os bancos de dados de gráfico resolvem isso do seu próprio jeito, mas nós vamos resolver isso identificando os principais atributos de um usuário mostrados em nosso aplicativo para cada atividade. Se mostramos visualmente uma postagem em nosso aplicativo e mostramos apenas o nome e a imagem do criador, por que armazenar todos os dados do usuário no atributo “createdBy”? Se, para cada comentário, mostramos apenas a imagem do usuário, realmente, não precisamos do restante de suas informações. É nesse momento que entra em cena o que chamo de “padrão de Escada”.

Vamos usar as informações do usuário como exemplo:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Examinando essas informações, podemos detectar rapidamente quais informações são críticas e quais não são, criando, assim, uma “Escada”:

![Diagrama de um padrão de escada](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

A menor etapa é chamada de UserChunk, a informação mínima que identifica um usuário e que é usada para a duplicação de dados. Ao reduzir o tamanho dos dados duplicados somente às informações que serão “mostradas”, reduzimos a possibilidade de atualizações em massa.

A etapa intermediária é chamada de User: são os dados completos que serão usados na maioria das consultas dependentes de desempenho no Banco de Dados de Documentos, os mais acessados e críticos. Ela inclui as informações representadas por um UserChunk.

A maior etapa é Extended User. Ela inclui todas as informações críticas do usuário, bem como outros dados que realmente não precisam ser lidos rapidamente ou cujo uso é eventual (como o processo de logon). Esses dados podem ser armazenados fora do Banco de Dados de Documentos, no Banco de Dados SQL do Azure ou nas Tabelas de Armazenamento do Azure.

Por que dividiríamos o usuário e, até mesmo, armazenaríamos essas informações em locais diferentes? Porque o espaço de armazenamento do Banco de Dados de Documentos [não é infinito](documentdb-limits.md) e, da perspectiva de desempenho, quanto maiores os documentos, mais caras as consultas. Mantenha os documentos simples, com as informações certas para fazer todas as suas consultas dependentes de desempenho para sua rede social, e armazene as outras informações extras para eventuais cenários como edições de perfil completo, logons e, até mesmo, mineração de dados para análise de uso e iniciativas de Big Data. Realmente, não nos importamos se a coleta de dados para mineração de dados é mais lenta porque está em execução no Banco de Dados SQL do Azure; o que nos preocupa é fazer com que nossos usuários tenham uma experiência rápida e descomplicada. Um usuário, armazenado no Banco de Dados de Documentos, teria esta aparência:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

E um Post teria a seguinte aparência:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

E quando surgir uma edição em que um dos atributos da parte é afetado, será fácil encontrar os documentos afetados usando consultas que apontam para os atributos indexados (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”) e, em seguida, atualizando as partes.

## <a name="the-search-box"></a>A caixa de pesquisa
Felizmente, os usuários vão gerar muito conteúdo. Além disso, devemos ser capazes de fornecer os meios pelos quais seja possível pesquisar e encontrar o conteúdo que, talvez, não esteja disponível diretamente em seus fluxos de conteúdo, provavelmente, porque não seguimos os criadores ou porque estamos apenas tentando encontrar aquela mensagem antiga que postamos há seis meses.

Felizmente, tendo em vista que estamos usando o Banco de Dados de Documentos, em poucos minutos, podemos implementar com facilidade um mecanismo de pesquisa com a [Pesquisa do Azure](https://azure.microsoft.com/services/search/) , sem digitar uma única linha de código (a não ser, evidentemente, a interface do usuário e o processo da pesquisa).

Por que isso é tão fácil?

O Azure Search implementa o que é chamado de [Indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), processos em segundo plano que se anexam aos repositórios de dados e adicionam, atualizam ou removem automaticamente os objetos nos índices. Eles dão suporte a [indexadores do Banco de Dados SQL do Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexadores dos Blobs do Azure](../search/search-howto-indexing-azure-blob-storage.md) e, ainda bem, [indexadores do Azure DocumentDB](documentdb-search-indexer.md). A transição de informações do DocumentDB para o Azure Search é simples, já que ambos armazenam as informações em formato JSON; basta [criarmos nosso Índice](../search/search-create-index-portal.md) e mapearmos quais atributos de nossos Documentos queremos indexar e... pronto! Em questão de minutos (dependendo do tamanho dos dados), todo o nosso conteúdo estará disponível para ser pesquisado pela melhor solução de Pesquisa como Serviço da infraestrutura de nuvem. 

Para obter mais informações sobre a Pesquisa do Azure, visite o [Hitchhiker’s Guide to Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>O conhecimento subjacente
Depois de armazenar todo esse conteúdo que aumenta a cada dia, talvez comecemos a pensar: O que posso fazer com todo esse fluxo de informações de meus usuários?

A resposta é simples: colocá-lo em prática e aprender com ele.

Mas o que podemos aprender? Alguns exemplos simples incluem a [análise de sentimento](https://en.wikipedia.org/wiki/Sentiment_analysis), recomendações de conteúdo de acordo com as preferências de um usuário ou, até mesmo, um moderador de conteúdo automatizado que garante que todo o conteúdo publicado pela nossa rede social é seguro para a família.

Agora que prendi a atenção de vocês, é provável que achem que é preciso contratar um PhD em ciência matemática para extrair esses padrões e essas informações de arquivos e bancos de dados simples, mas esse não é o caso.

O [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), parte do [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), é um serviço de nuvem totalmente gerenciado que permite a criação de fluxos de trabalho usando algoritmos em uma interface simples do tipo "arrastar e soltar", a codificação de seus próprios algoritmos em [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) ou o uso de algumas das APIs já criadas e prontas para uso, como: [Análise de Texto](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator](https://www.microsoft.com/moderator) ou [Recomendações](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Para conseguir qualquer um desses cenários do Machine Learning, podemos usar o [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) para ingerir informações de fontes diferentes e usar [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para processar as informações e gerar uma saída que possa ser processada pelo Azure Machine Learning.

Outra opção disponível é usar os [Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services) para analisar o conteúdo dos usuários. Não apenas podemos compreendê-los melhor (analisando o que eles escrevem com a [API de Análise de Texto](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), mas também podemos detectar o conteúdo indesejado ou maduro e agir de acordo com a [API da Pesquisa Visual Computacional](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Os Serviços Cognitivos incluem muitas soluções prontas para uso que não exigem qualquer tipo de dados de conhecimento de Machine Learning.

## <a name="conclusion"></a>Conclusão
Este artigo tenta esclarecer as alternativas para a criação completa de redes sociais no Azure com serviços de baixo custo e para a entrega de excelentes resultados com o incentivo do uso de uma solução de armazenamento em várias camadas e da distribuição de dados chamada “Escada”.

![Diagrama de interação entre os serviços do Azure para rede social](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

A verdade é que não há nenhuma solução milagrosa para esses tipos de cenários: é a sinergia criada pela combinação de serviços excepcionais que nos permite proporcionar experiências incríveis: a velocidade e a liberdade possibilitadas pelo Banco de Dados de Documentos do Azure para fornecer um excelente aplicativo social, a inteligência por trás de uma solução de pesquisa de primeira classe como a Pesquisa do Azure, a flexibilidade dos Serviços de Aplicativos do Azure para não hospedar sequer aplicativos independentes de linguagem, mas eficientes processos em segundo plano e os expansíveis Armazenamento do Azure e o Banco de Dados SQL do Azure para armazenar grandes quantidades de dados, bem como o poder de análise do Aprendizado de Máquina do Azure para criar conhecimento e inteligência que podem fornecer comentários para nossos processos e nos ajudar a entregar o conteúdo certo para os usuários certos.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a modelagem de dados lendo o artigo [Modelando dados no DocumentDB](documentdb-modeling-data.md) . Se estiver interessado em outros casos de uso do DocumentDB, veja [Casos de uso comuns do DocumentDB](documentdb-use-cases.md).

Caso contrário, saiba mais sobre o DocumentDB seguindo o [Roteiro de aprendizagem do DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

<!--HONumber=Oct16_HO2-->



<properties pageTitle="Recomendações para "Frequentemente comprados juntos" da Pesquisa do Azure | Microsoft Azure | Apache Mahout | Aprendizado de Máquina do Azure" description="Exemplo de código para adição de recomendações Frequentemente comprados juntos, Novidades para você ou Também compraram para um aplicativo de Pesquisa do Azure usando Apache Mahout ou o Aprendizado de Máquina do Azure" services="search" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Recomendações para "Frequentemente comprados juntos" da Pesquisa do Azure

Se você já fez compras online, provavelmente percebeu “Frequentemente comprados juntos” ou “Clientes que compraram este item também compraram” ou até mesmo uma lista “Novidades para você” que apresenta mercadorias ou serviços adicionais para sua consideração. Geralmente, esses dados são compilados por varejistas online com base no histórico de compras de muitos clientes, ou apenas com base nos dados de preferência de consumidor exclusivos a você. A capacidade de oferecer recomendações relevantes e direcionadas ao consumidor por meio da pesquisa é um acréscimo valioso não apenas para aplicativos comerciais, mas também para sites que fornecem conteúdo multimídia ou informações.

Em aplicativos da Pesquisa do Azure, é possível implementar recomendações escrevendo um código extra que traz dados e serviços usados com o objetivo de identificar padrões e relações nos dados e, em seguida, transformá-los em recomendações relevantes para seus usuários.

[Este exemplo demonstra como fazer isso](https://github.com/liamca/azure-search-recommendations), usando o [Apache Mahout]() como mecanismo de recomendações.

No restante deste artigo, eu orientarei você pelo exemplo de código que adiciona recomendações de “Frequentemente alugados juntos” usando exemplos de dados sobre locações de filme.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## O que é uma recomendação?

*Recomendações* fazem parte de uma técnica para mostrar mais itens de um catálogo com base em atividades de pesquisa existentes do Usuário (como logs da Web), e é usada para recomendar itens e melhorar a conversão.

Geralmente, os mecanismos de recomendação são treinados usando atividades prévias do cliente ou por meio da coleta de dados diretamente de um repositório digital. Neste exemplo, eu uso o Apache Mahout para compilar os dados de recomendações.

As recomendações comuns incluem: - Frequentemente comprados juntos: um cliente que comprou este item também comprou aquele; - Recomendações de item para o cliente: um cliente como você também comprou isto

## Criando um índice da Pesquisa do Azure

- Abra a solução AzureSearchMovieRecommendations.sln e defina ImportAzureSearchIndexData como o Projeto Padrão.  
- Abra Program.cs em ImportAzureSearchIndexData e alter SearchServiceName e SearchApiKey a fim de apontar para o serviço de Pesquisa do Azure
- Baixe hetrec2011-movielens-2k.zip de http://grouplens.org/datasets/hetrec-2011/ e copie os arquivos Movies.dat e user\_ratedmovies.dat para \\ImportAzureSearchIndexData\\data
- Execute o projeto para criar um índice e carregar os dados de filmes 
- No final, o aplicativo executará uma pesquisa de teste

## Criar um aplicativo HTML simples para pesquisa de filmes

É possível encontrar um aplicativo Web JavaScript completo que permite a consulta do índice da Pesquisa do Azure em: \\WebSite\\starter-template-complete

Se você quiser percorrer a demonstração desde o início, o CSS original pode ser encontrado aqui: \\WebSite\\starter-template

Abra o arquivo search.js em \\WebSite\\starter-template-complete e atualize a apiKey e o azureSearchService com os detalhes do serviço da Pesquisa do Azure.

Agora, é possível abrir esse arquivo em um navegador, como o Chrome, para ver filmes digitando na caixa de pesquisa.

## Comando para executar a Criação de recomendações usando o Mahout

- Carregue o arquivo data\\movie\_usage.txt no Armazenamento de Blobs do Azure 
- Crie uma instância do HDInsight (habilitando a Área de Trabalho Remota) e conecte-a à máquina por meio da Área de Trabalho Remota (disponível no Portal do Azure)
- Na máquina com o HDInsight, abra a "Linha de Comando do Hadoop"
- Altere para o diretório bin do Mahout em c:\\apps\\dist. O meu tem esta aparência, mas você pode obter uma versão mais recente do Mahout C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin
- Execute a seguinte linha de comando,na qual você substitui [CONTAINER] e [STORAGEACT] pelos detalhes do Armazenamento do Azure (onde você colocou o arquivo movie\_usage.txt):

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

Isso levará alguns minutos para ser concluído, mas quando isso acontecer, seu contêiner de armazenamento deverá conter o seguinte arquivo, que incluirá suas recomendações de filme: /movies/output/part-r-00000

Esse arquivo tem 3 colunas: [Item ID of Movie], [Item ID of Recommendations related to this Movie], [Similarity Percentage]

## Importando dados do Mahout para a Pesquisa do Azure

O programa que criou o índice da Pesquisa do Azure também criou um campo chamado `Recommendations`, que é do tipo Coleção (algo como um conjunto delimitado por vírgulas de cadeias de caracteres). Mesclaremos os dados criados na etapa anterior com este índice da Pesquisa do Azure.

- Na solução do Visual Studio, AzureSearchMovieRecommendations.sln, abra Program.cs dentro do MahoutOutputLoader.
- Atualize SearchServiceName e SearchApiKey com os detalhes do serviço da Pesquisa do Azure
- Atualize StorageApiKey e StorageAccountName com os detalhes da conta de Armazenamento do Azure na qual você armazenou o arquivo de recomendações de produto do Mahout
- Execute o aplicativo para mesclar os dados
 
## Visualizando as recomendações
Neste ponto, você poderá voltar ao aplicativo Web e clicar em qualquer um dos filmes para ver as recomendações.

Se você quiser ver como as recomendações foram retornadas ao clicar na imagem, abra Search.js e examine a função openMovieDetails().

## Crédito

Os dados foram fornecidos pelo GroupLens (http://grouplens.org/datasets/hetrec-2011/)

Confira esta página para obter detalhes sobre o licenciamento destes dados: http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt

<!---HONumber=Nov15_HO4-->
---
title: Melhores práticas – QnA Maker
titlesuffix: Azure Cognitive Services
description: Use essas melhores práticas para melhorar a base de dados de conhecimento e fornecer melhores resultados aos usuários finais do aplicativo/chat.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d51cbc7bd88fd9f4baf066210e7b7da8cd175cd6
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603116"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Melhores práticas de uma base de dados de conhecimento do QnA Maker
O [ciclo de vida de desenvolvimento da base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) informa como gerenciar a base de dados de conhecimento de ponta a ponta. Use essas melhores práticas para melhorar a base de dados de conhecimento e fornecer melhores resultados aos usuários finais do aplicativo/chat.

## <a name="extraction"></a>Extração
O serviço QnA Maker está melhorando continuamente os algoritmos que extraem perguntas e respostas do conteúdo e está expandindo a lista de formatos de arquivo e de HTML com suporte. Siga as [diretrizes](../Concepts/data-sources-supported.md) de extração de dados com base em seu tipo de documento. 

Em geral, as páginas de perguntas frequentes devem ser independentes e não combinadas com outras informações. Manuais de produtos devem ter títulos claros e, de preferência, uma página de índice. 

## <a name="chit-chat"></a>Bate-papo
Adicione bate-papo ao seu bot para torná-lo mais conversacional e envolvente com pouco esforço. Ao criar sua base de dados de conhecimento, você pode adicionar facilmente conjuntos de dados de bate-papo com três personalidades predefinidas pode e alterá-los a qualquer momento. Saiba como [adicionar bate-papo à base de dados de conhecimento](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Escolhendo uma personalidade
Há suporte para o bate-papo com três personalidades predefinidas: 

|Personalidades|
|--|
|O profissional|
|O amigo|
|O comediante|

As respostas vão de formal e informal a irreverente. Selecione a personalidade mais alinhada com o tom que você deseja que o bot tenha. Você pode exibir os conjuntos de dados e escolher qual servirá como base para o bot e, em seguida, personalizar as respostas. 

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas do bot
Há algumas perguntas específicas do bot que fazem parte do conjunto de dados de bate-papo e que foram preenchidas com respostas genéricas. Altere essas respostas para refletir melhor os detalhes de seu bot. 

Recomendamos que você torne as seguintes perguntas e respostas do bate-papo mais específicas:

* Quem é você?
* O que você pode fazer?
* Quantos anos você tem?
* Quem criou você?
* Olá
   

## <a name="rankingscoring"></a>Classificação/pontuação
Certifique-se de que você está utilizando da melhor forma os recursos de classificação com suporte do QnA Maker. Isso aumentará a probabilidade de que uma determinada consulta de usuário seja respondida com uma resposta apropriada.

### <a name="choosing-a-threshold"></a>Escolhendo um limite
A pontuação de confiança padrão usada como limite é 50, no entanto, você pode alterá-la para sua base de dados de conhecimento dependendo das necessidades. Como cada base de dados de conhecimento é diferente, você precisa testar e escolher o limite mais adequado à sua. Leia mais sobre a [pontuação de confiança](../Concepts/confidence-score.md). 


### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Perguntas alternativas](../How-To/edit-knowledge-base.md) melhoram a probabilidade de uma correspondência com uma consulta de usuário. Perguntas alternativas são úteis quando há várias maneiras de como a mesma pergunta pode ser feita. Isso pode incluir alterações na estrutura da frase e no estilo das expressões.

|Consulta original|Consultas alternativas|Alterar| 
|--|--|--|
|Há estacionamento disponível?|Vocês têm estacionamento?|estrutura da frase|
 |Oi|E aí<br>Olá!|estilo das expressões ou gírias|

<a name="#use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Use marcas de metadados para filtrar perguntas e respostas

Os [Metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de restringir os resultados de uma consulta do usuário com base em marcas de metadados. A resposta da base de dados de conhecimento poderá variar com base na marca de metadados, mesmo se a consulta for a mesma. Por exemplo, *"onde o estacionamento está localizado"* poderá ter uma resposta diferente se a localização da filial do restaurante for diferente – ou seja, se os metadados forem *Localização: Seattle* versus *Localização: Redmond*.

### <a name="use-synonyms"></a>Usar sinônimos
Embora haja algum suporte para sinônimos no idioma inglês, use [alterações de palavras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) para adicionar sinônimos a palavras-chave que apresentam formas diferentes. Os sinônimos devem ser adicionados no nível de serviço do QnA Maker e compartilhados por todas as bases de dados de conhecimento no serviço.

|Palavra original|Sinônimos|
|--|--|
|comprar|comprar<br>internetnetbanking<br>Internet banking|

### <a name="use-distinct-words-to-differentiate-questions"></a>Use palavras distintas para diferenciar perguntas
Os algoritmos de correspondência e classificação do QnA Maker, que fazem a correspondência entre uma consulta do usuário e uma pergunta na base de dados de conhecimento, funcionam melhor se cada pergunta trata de uma necessidade diferente. A repetição do mesmo conjunto de palavras entre as perguntas reduz a probabilidade da resposta correta ser escolhida para uma determinada consulta de usuário com essas palavras. 

Por exemplo, você pode ter duas perguntas e respostas separadas com as seguintes perguntas:

|Perguntas e respostas|
|--|
|qual é a *localização* do estacionamento|
|qual é a *localização* do caixa eletrônico|

Uma vez que essas perguntas e respostas são formuladas com palavras muito semelhantes, essa semelhança poderia levar a pontuações muito semelhantes para muitas consultas do usuário formuladas como *"qual é a localização de `<x>`"*. Em vez disso, tente diferenciar claramente usando consultas como *"onde fica o estacionamento"* e *"onde fica o caixa eletrônico"*, evitando palavras como "localização", que poderiam estar em muitas perguntas em sua base de dados de conhecimento. 


## <a name="collaborate"></a>Colaborar
O QnA Maker permite que os usuários [colaborem](../How-to/collaborate-knowledge-base.md) em uma base de dados de conhecimento. Os usuários precisam de acesso ao grupo de recursos do QnA Maker do Azure para acessar as bases de dados de conhecimento. Algumas organizações podem querer terceirizar a edição e manutenção da base de dados de conhecimento e ainda proteger o acesso aos recursos do Azure. Esse modelo de editor-aprovador é realizado configurando dois [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) idênticos em assinaturas diferentes e designando um deles para o ciclo de teste e edição. Quando o teste estiver concluído, o conteúdo da base de dados de conhecimento será transferido com um processo de [importação-exportação](../Tutorials/migrate-knowledge-base.md) para o serviço do QnA Maker do aprovador que, por fim, publicará a base de dados de conhecimento e atualizará o ponto de extremidade.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)

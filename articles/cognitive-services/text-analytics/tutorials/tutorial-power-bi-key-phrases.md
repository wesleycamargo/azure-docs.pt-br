---
title: Análise de Texto com o Power BI – Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como usar a Análise de Texto para extrair frases-chave de textos armazenados no Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889292"
---
# <a name="text-analytics-with-power-bi"></a>Análise de Texto com o Power BI

O Microsoft Power BI extrai dados organizacionais para criar belos relatórios, que você pode distribuir em sua organização para obter informações mais rápidas e aprofundadas. O serviço Análise de Texto, parte dos Serviços Cognitivos no Microsoft Azure, pode extrair as frases mais importantes do texto por meio de sua API de frases-chave. Juntas, essas ferramentas podem ajudar você a ver rapidamente sobre o que seus clientes estão falando e como eles se sentem sobre isso.

Neste tutorial, você verá como integrar o Power BI Desktop e a API de frases-chave para extrair as frases mais importantes de comentários do cliente usando uma função personalizada do Power Query. Também criamos uma nuvem de palavras com essas frases.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, você precisará do seguinte:

> [!div class="checklist"]
> * Microsoft Power BI Desktop. [Faça download sem qualquer custo](https://powerbi.microsoft.com/get-started/).
> * Uma conta do Microsoft Azure. [Inicie uma avaliação gratuita](https://azure.microsoft.com/free/) ou [entre](https://portal.azure.com/).
> * Uma chave de acesso para a Análise de Texto. [Inscreva-se](../../cognitive-services-apis-create-account.md) e [obtenha sua chave](../how-tos/text-analytics-how-to-access-key.md).
> * Comentários do cliente. [Obtenha nossos dados de exemplo](https://aka.ms/cogsvc/ta) ou use seus próprios dados.

## <a name="loading-customer-data"></a>Carregando dados do cliente

Para começar, abra o Power BI Desktop e carregue o arquivo CSV (valores separados por vírgulas) **FabrikamComments.csv**. Esse arquivo representa um dia inteiro de atividades hipotéticas no fórum de suporte de uma pequena empresa fictícia.

> [!NOTE]
> O Power BI pode usar dados de uma ampla variedade de fontes, como o Facebook ou um banco de dados SQL. Saiba mais em [Integração do Facebook com o Power BI](https://powerbi.microsoft.com/integrations/facebook/) e [Integração do SQL Server com o Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Na janela principal do Power BI Desktop, localize o grupo Dados Externos da faixa de opções Página Inicial. Escolha o **Texto/CSV** no menu suspenso **Obter Dados** desse grupo.

![[O botão Obter Dados]](../media/tutorials/power-bi/get-data-button.png)

O diálogo Abrir é exibido. Navegue até a pasta Downloads ou qualquer outra que contenha o arquivo de dados de exemplo. Clique em `FabrikamComments.csv` e no botão **Abrir**. O diálogo de importação do CSV é exibido.

![[O diálogo de importação do CSV]](../media/tutorials/power-bi/csv-import.png)

O diálogo de importação do CSV permite verificar se o Power BI Desktop detectou conjunto de caracteres, delimitadores, linhas de cabeçalho e tipos de coluna corretamente. Essas informações estão corretas e, portanto, vamos clicar em **Carregar**.

Para ver os dados carregados, alterne para o modo de exibição Dados usando o botão Exibição de Dados ao longo da borda esquerda do espaço de trabalho do Power BI. Uma tabela é aberta com nossos dados, como ocorre no Microsoft Excel.

![[A exibição inicial dos dados importados]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Preparando os dados

Talvez seja necessário transformar seus dados no Power BI Desktop antes que eles possam ser processados pelo serviço de frases-chave.

Nossos dados, por exemplo, contêm um campo `subject` e um campo `comment`. Deveríamos considerar o texto em ambos os campos, não apenas em `comment`, na hora de extrair frases-chave. A função Mesclar Colunas no Power BI Desktop facilita essa tarefa.

Abra o Editor de Consulta na janela principal do Power BI Desktop clicando em **Editar Consultas** no grupo Dados Externos na faixa de opções Página Inicial. 

![[O grupo Dados Externos na faixa de opções Página Inicial]](../media/tutorials/power-bi/edit-queries.png)

Selecione "FabrikamComments" na lista de consultas no lado esquerdo da janela se já não estiver selecionado.

Agora, selecione as colunas `subject` e `comment` na tabela. Talvez seja necessário rolar horizontalmente para ver essas colunas. Primeiro, clique no cabeçalho da coluna `subject`, em seguida, mantenha pressionada a tecla Control e clique no cabeçalho da coluna `comment`.

![[Selecionando campos a serem mesclados]](../media/tutorials/power-bi/select-columns.png)

No grupo Colunas de Texto da faixa de opções Transformação, clique em **Mesclar Colunas**. O diálogo Mesclar Colunas é exibido.

![[Mesclando campos usando o diálogo Mesclar Colunas]](../media/tutorials/power-bi/merge-columns.png)

No diálogo Mesclar Colunas, escolha Guia como separador e clique em **OK.** Feito!

Você também pode considerar a filtragem de mensagens em branco usando o filtro Remover Vazio ou removendo os caracteres que não podem ser impressos usando a transformação Limpar. Se seus dados contiverem uma coluna como a `spamscore` em nosso arquivo de exemplo, você poderá ignorar comentários "spam" usando um filtro de número.

## <a name="understanding-the-api"></a>Noções básicas sobre a API

A API de frases-chave pode processar até mil documentos de texto por solicitação HTTP. No entanto, o Power BI prefere lidar com um registro por vez, para que nossas chamadas à API contenham apenas um único documento. [A API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) requer os campos a seguir para cada documento que está sendo processado.

| | |
| - | - |
| `id`  | Um identificador exclusivo para este documento dentro da solicitação. A resposta também contém esse campo. Dessa forma, se você processar vários documentos, poderá associar facilmente as frases-chave extraídas ao documento que as originou. Estamos processando um documento por solicitação e, portanto, já sabemos a qual documento a resposta está associada. Assim, a `id` pode ser a mesma em cada solicitação.|
| `text`  | O texto a ser processado. Podemos obtê-lo na coluna `Merged` que criamos, que contém o texto de comentário e a linha do assunto combinados. A API de frases-chave requer que esses dados tenham menos de 5 mil caracteres.|
| `language` | O código que representa o idioma do documento. Todas as nossas mensagens estão em inglês, ou seja, podemos embutir o idioma `en` no código de nossa consulta.|

Precisamos combinar esses campos em um documento JSON (JavaScript Object Notation) para enviá-lo à API de frases-chave. A resposta dessa solicitação também é um documento JSON e devemos analisá-lo e pescar as frases-chave.

## <a name="creating-a-custom-function"></a>Criando uma função personalizada

Agora estamos prontos para criar a função personalizada que se integrará no Power BI e na Análise de Texto. O Power BI Desktop invoca a função para cada linha da tabela e cria uma nova coluna com os resultados.

A função recebe o texto a ser processado como um parâmetro. Ela converte os dados de e para o JavaScript Object Notation (JSON) necessário e faz a solicitação HTTP ao ponto de extremidade da API de frases-chave. Após a análise da resposta, a função retorna uma cadeia de caracteres que contém uma lista separada por vírgulas com as frases-chave extraídas.

> [!NOTE]
> As funções personalizadas do Power BI Desktop são escritas na [linguagem de fórmula Power Query M](https://msdn.microsoft.com/library/mt211003.aspx), ou sua forma abreviada "M”. M é uma linguagem de programação funcional com base em [F#](https://docs.microsoft.com/dotnet/fsharp/). Você não precisa ser um programador para concluir este tutorial, pois o código necessário está incluído abaixo.

Você ainda deve estar na janela do Editor de Consultas. Na faixa de opções Página Inicial, clique em **Nova Fonte** (no grupo Nova Consulta) e escolha **Consulta em branco** no menu suspenso. 

Uma nova consulta, nomeada inicialmente Query1, aparece na lista de consultas. Clique duas vezes nessa entrada e nomeie-a `KeyPhrases`.

Agora, abra a janela do Editor Avançado clicando em **Editor Avançado** no grupo Consulta da faixa de opções Página Inicial. Exclua o código que já está nessa janela e cole o código a seguir. 

> [!NOTE]
> Nos exemplos a seguir, vamos supor que o ponto de extremidade está em https://westus.api.cognitive.microsoft.com.  A Análise de Texto dá suporte à criação de uma assinatura em 13 regiões diferentes. Se você se inscreveu no serviço em uma região diferente, use o ponto de extremidade da região selecionada. Ele deve ser mostrado na página Visão Geral no portal do Azure quando você seleciona a assinatura da Análise de Texto.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Cole também sua chave da API de Análise de Texto obtida no painel do Microsoft Azure, na linha que começa com `apikey`. (Deixe as aspas antes e após a chave.) Em seguida, clique em **Concluído.**

## <a name="using-the-function"></a>Usando a função

Agora podemos usar a função personalizada para obter as frases-chave contidas em cada um dos comentários de clientes e armazená-las em uma nova coluna na tabela. 

Ainda no Editor de Consultas, alterne para a consulta FabrikamComments, mude para a faixa de opções Adicionar Coluna e clique no botão **Invocar Função Personalizada** no grupo Geral.

![[Botão Invocar Função Personalizada]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

No diálogo Invocar Função Personalizada, insira `keyphrases` como o nome da nova coluna. Escolha a função personalizada, `KeyPhrases`, como a consulta de função. 

Um novo campo aparece no diálogo perguntando qual campo queremos passar para nossa função como o parâmetro `text`. Escolha `Merged` (a coluna que criamos anteriormente mesclando os campos de assunto e mensagem) no menu suspenso.

![[Invocando uma função personalizada]](../media/tutorials/power-bi/invoke-custom-function.png)

Por fim, clique em **OK.**

Se tudo estiver pronto, o Power BI chamará nossa função uma vez para cada linha na tabela, executará as consultas de frases-chave e adicionará a nova coluna à tabela. Mas antes que isso ocorra, talvez seja necessário especificar as configurações de autenticação e privacidade.

## <a name="authentication-and-privacy"></a>Autenticação e privacidade

Depois de fechar o diálogo Invocar Função Personalizada, uma faixa poderá aparecer solicitando que você especifique a conexão ao ponto de extremidade de frases-chave.

![[faixa de credenciais]](../media/tutorials/power-bi/credentials-banner.png)

Clique em **Editar Credenciais**, verifique se Anônimo está selecionado no diálogo e clique em **Conectar.** 

> [!NOTE]
> Por que anônimo? O serviço Análise de Texto faz a autenticação usando a chave de API e, portanto, o Power BI não precisa fornecer credenciais para a solicitação HTTP em si.

![[definindo a configuração como anônima]](../media/tutorials/power-bi/access-web-content.png)

Se você vê a faixa Editar Credenciais mesmo depois de escolher o acesso anônimo, é possível que tenha esquecido de colar sua chave de API. Verifique a função personalizada `KeyPhrases` no Editor Avançado para verificar se ela está lá.

Em seguida, uma faixa pode aparecer solicitando que você forneça informações sobre a privacidade de suas fontes de dados. 

![[faixa de privacidade]](../media/tutorials/power-bi/privacy-banner.png)

Clique em **Continuar** e escolha Público em cada uma das fontes de dados no diálogo. Em seguida, clique em **Salvar.**

![[configurando a privacidade da fonte de dados]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Criando a nuvem de palavras

Depois de lidar com eventuais faixas exibidas, clique em **Fechar e Aplicar** na faixa de opções Página Inicial para fechar o Editor de Consultas.

O Power BI Desktop leva algum tempo para fazer as solicitações HTTP necessárias. Para cada linha na tabela, a nova coluna `keyphrases` contém as frases-chave detectadas no texto pela API de frases-chave. 

Vamos usar essa coluna para gerar uma nuvem de palavras. Para começar, clique no botão Relatório na janela principal do Power BI Desktop, à esquerda do espaço de trabalho.

> [!NOTE]
> Por que usar frases-chave extraídas para gerar uma nuvem de palavras em vez de usar o texto completo de cada comentário? As frases-chave nos fornecem as palavras *importantes* dos comentários dos clientes, não apenas as palavras *mais comuns*. Além disso, o dimensionamento das palavras na nuvem gerada não é distorcido pelo uso frequente de uma palavra em um número relativamente pequeno de comentários.

Se você ainda não tem o objeto visual personalizado da Nuvem de Palavras instalado, instale-o. No painel Visualizações à direita do espaço de trabalho, clique nas reticências (**...** ) e escolha **Importar da Store**. Em seguida, pesquise "nuvem" e clique no botão **Adicionar** ao lado do objeto visual Nuvem de Palavras. O Power BI instala o objeto visual Nuvem de Palavras e avisa que ele foi instalado com êxito.

![[adicionando um objeto visual personalizado]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Agora, vamos criar nossa nuvem de palavras!

![[Ícone da Nuvem de Palavras no painel Visualizações]](../media/tutorials/power-bi/visualizations-panel.png)

Primeiro, clique no ícone da Nuvem de Palavras no painel Visualizações. Um novo relatório aparece no espaço de trabalho. Arraste o campo `keyphrases` do painel Campos para o campo Categoria no painel Visualizações. A nuvem de palavras é exibida no relatório.

Agora, alterne para a página Formato do painel Visualizações. Na categoria Palavras irrelevantes, ative **Palavras Irrelevantes Padrão** para eliminar palavras curtas e comuns como "de" da nuvem. 

![[ativando palavras irrelevantes padrão]](../media/tutorials/power-bi/default-stop-words.png)

Um pouco mais abaixo no painel, desative **Girar Texto** e **Título**.

![[ativar modo de foco]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Clique na ferramenta Modo de Foco no relatório para obter uma visão melhor da nuvem de palavras. A ferramenta expande a nuvem de palavras para preencher o espaço de trabalho inteiro, conforme mostrado abaixo.

![[Uma nuvem de palavras]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Mais serviços da Análise de Texto

O serviço Análise de Texto, um dos serviços Cognitivos oferecidos pelo Microsoft Azure, também fornece detecção de idioma e análise de sentimento. A detecção de idioma é particularmente útil se os comentários do cliente não são todos em inglês.

Essas outras APIs são muito semelhantes à API de frases-chave. Portanto, funções personalizadas quase idênticas podem ser usadas para integrá-las ao Power BI Desktop. Basta criar uma consulta em branco e colar o código apropriado abaixo no Editor Avançado, como feito anteriormente. (Não se esqueça da chave de acesso!) Em seguida, como feito anteriormente, use a função para adicionar uma nova coluna à tabela.

A função Análise de Sentimento abaixo retorna uma pontuação que indica o grau de positividade do sentimento expressado no texto.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Aqui estão duas versões de uma função Detecção de Idioma. A primeira retorna o código de idioma ISO (por exemplo, `en` para inglês) e a segunda retorna o nome "amigável" (por exemplo, `English`). Você deve notar que a última linha do corpo é diferente entre as duas versões.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Por fim, aqui está uma variante da função Frases-chave já apresentada que retorna as frases como um objeto de lista, em vez de uma única cadeia de caracteres de frases separada por vírgulas. 

> [!NOTE]
> O retorno de uma única cadeia de caracteres simplificou nosso exemplo de nuvem de palavras. Por outro lado, o formato de lista é mais flexível para funcionar com as frases retornadas no Power BI. Você pode manipular objetos de lista no Power BI Desktop usando o grupo Coluna Estruturada na faixa de opções Transformação do Editor de Consultas.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço Análise de Texto, a linguagem de fórmula Power Query M ou o Power BI.

> [!div class="nextstepaction"]
> [Referência de API de Análise de Texto](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Referência do Power Query M](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Documentação do Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)

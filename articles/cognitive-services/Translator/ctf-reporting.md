---
title: Estrutura de tradução colaborativa (CTF) relatórios - API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Como usar o relatório do CTF (Collaborative Translation Framework).
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 69fbcad92d31744cae798b7ff376e77f655f6440
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877551"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Como usar o relatório do CTF (Collaborative Translation Framework)

> [!NOTE]
> Este método foi preterido. Ele não está disponível na versão V3.0 da API de Tradução de Texto.

> O CTF (Collaborative Translations Framework), disponível anteriormente para V2.0 da API de Tradução de Texto, foi preterido em 1º de fevereiro de 2018. As funções AddTranslation e AddTranslationArray permitem que os usuários habilitem as correções através do Collaborative Translation Framework. Após 31 de janeiro de 2018, essas duas funções não aceitaram novos envios de sentença e os usuários receberam uma mensagem de erro. Essas funções estavam sendo desativadas e não serão substituídas.

>Uma funcionalidade semelhante está disponível na API do Hub do Microsoft Translator, permitindo que você compile um sistema de tradução personalizado com sua terminologia e estilo e invocá-lo usando a ID da Categoria na API de Tradução de Texto. Hub do Microsoft Translator: [https://hub.microsofttranslator.com](https://hub.microsofttranslator.com). API do Hub do Microsoft Translator: [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger).

A API de Relatório do CTF (Collaborative Translation Framework) retorna estatísticas e o conteúdo real no armazenamento do CTF. Essa API é diferente do método GetTranslations() porque:
* Retorna o conteúdo traduzido e a contagem total somente da conta (conta do Microsoft Azure Marketplace ou appId).
* Retorna o conteúdo traduzido e a contagem total sem exigir uma correspondência da sentença de origem.
* Não retorna a tradução automática (tradução por computador).

## <a name="endpoint"></a>Ponto de extremidade
O ponto de extremidade da API de Relatório do CTF é http://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Métodos
| NOME |    DESCRIÇÃO|
|:---|:---|
| Método GetUserTranslationCounts | Obtenha contagens das traduções criadas pelo usuário. |
| Método GetUserTranslations | Recupera as traduções criadas pelo usuário. |

Esses métodos permitem que você:
* Recupere o conjunto completo de traduções de usuário e correções na ID da conta para download.
* Obtenha a lista dos colaboradores frequentes. Certifique-se de que o nome de usuário correto seja fornecido em AddTranslation().
* Compile uma UI (interface do usuário), a qual permite que os usuários confiáveis vejam todos os candidatos disponíveis, se necessário restrito a uma parte do site, com base no prefixo URI.

> [!NOTE]
> Ambos os métodos são relativamente lentos e caros. É recomendável utilizá-los com moderação.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Esse método obtém a contagem de traduções criadas pelo usuário. Ele fornece a lista de contagens de tradução agrupadas pelos parâmetros de solicitação uriPrefix, from, to, user, minRating e maxRating.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslationCount[]GetUserTranslationCounts(
           string appId,
           string uriPrefix,
           string from,
           string to,
           int? minRating,
           int? maxRating,
           string user,
           string category
           DateTime? minDateUtc,
           DateTime? maxDateUtc,
           int? skip,
           int? take);
```

**Parâmetros**

| Parâmetro | DESCRIÇÃO |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de Autorização for usado, deixe o campo appid vazio, caso contrário, especifique uma cadeia de caracteres contendo "token de portador" + " " + token de acesso.|
| uriPrefix | **Opcional** Uma cadeia de caracteres contendo o prefixo de URI da tradução.|
| de | **Opcional** Uma cadeia de caracteres representando o código de idioma do texto da tradução. |
| para | **Opcional** Uma cadeia de caracteres representando o código de idioma para traduzir o texto.|
| minRating| **Opcional** Um valor inteiro representando a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor padrão é 1.|
| maxRating| **Opcional** Um valor inteiro representando a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor padrão é 1.|
| usuário | **Opcional** Uma cadeia de caracteres usada para filtrar o resultado com base no originador do envio. |
| categoria| **Opcional** Uma cadeia de caracteres contendo a categoria ou domínio da tradução. Esse parâmetro dá suporte apenas à opção padrão geral.|
| minDateUtc| **Opcional** A data de quando você quer recuperar as traduções. A data deve estar no formato UTC. |
| maxDateUtc| **Opcional** A data até quando você quer recuperar as traduções. A data deve estar no formato UTC. |
| skip| **Opcional** O número de resultados que você quer ignorar em uma página. Por exemplo, se você quiser ignorar as primeiras 20 linhas dos resultados e exibir do 21º registro de resultados, especifique 20 para esse parâmetro. O valor padrão para esse parâmetro é 0.|
| take | **Opcional** O número de resultados que você quer recuperar. O número máximo de cada solicitação é 100. O padrão é 100.|

> [!NOTE]
> Os parâmetros de solicitação skip e take permitem a paginação para um grande número de registros de resultados.

**Valor de retorno**

O conjunto de resultados contém uma matriz do **UserTranslationCount**. Cada UserTranslationCount possui os seguintes elementos:

| Campo | DESCRIÇÃO |
|:---|:---|
| Contagem| O número de resultados recuperados|
| Da | O idioma de origem|
| Classificação| A classificação que é aplicada pelo remetente na chamada do método AddTranslation()|
| Para| O idioma de destino|
| Uri| O URI aplicado na chamada de método AddTranslation()|
| Usuário| O nome de usuário|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é menor que o valor do parâmetro **minDateUtc**.|
| TranslateApiException | IP excede a cota.| <ul><li>O limite para o número de solicitações por minuto é alcançado.</li><li>O tamanho da solicitação permanece limitado a 10000 caracteres.</li><li>Uma cota horária e uma cota diária limitam o número de caracteres que a API do Microsoft Translator aceitará.</li></ul>|
| TranslateApiException | AppId excede a cota.| A ID do aplicativo excedeu a cota horária ou diária.|

> [!NOTE]
> A cota será ajustada para garantir a equidade entre todos os usuários do serviço.

**Exibir exemplos de código no GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Esse método recupera as traduções criadas pelo usuário. Ele fornece as traduções agrupadas pelos parâmetros de solicitação uriPrefix, from, to, user, e minRating e maxRating.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslation[] GetUserTranslations (
            string appId,
            string uriPrefix,
            string from,
            string to,
            int? minRating,
            int? maxRating,
            string user,
            string category
            DateTime? minDateUtc,
            DateTime? maxDateUtc,
            int? skip,
            int? take);
```

**Parâmetros**

| Parâmetro | DESCRIÇÃO |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de Autorização for usado, deixe o campo appid vazio, caso contrário, especifique uma cadeia de caracteres contendo "token de portador" + " " + token de acesso.|
| uriPrefix| **Opcional** Uma cadeia de caracteres contendo o prefixo de URI da tradução.|
| de| **Opcional** Uma cadeia de caracteres representando o código de idioma do texto da tradução.|
| para| **Opcional** Uma cadeia de caracteres representando o código de idioma para traduzir o texto.|
| minRating| **Opcional** Um valor inteiro representando a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor padrão é 1.|
| maxRating| **Opcional** Um valor inteiro representando a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor padrão é 1.|
| usuário| **Opcional. Uma cadeia de caracteres usada para filtrar o resultado com base no originador do envio**|
| categoria| **Opcional** Uma cadeia de caracteres contendo a categoria ou domínio da tradução. Esse parâmetro dá suporte apenas à opção padrão geral.|
| minDateUtc| **Opcional** A data de quando você quer recuperar as traduções. A data deve estar no formato UTC.|
| maxDateUtc| **Opcional** A data até quando você quer recuperar as traduções. A data deve estar no formato UTC.|
| skip| **Opcional** O número de resultados que você quer ignorar em uma página. Por exemplo, se você quiser ignorar as primeiras 20 linhas dos resultados e exibir do 21º registro de resultados, especifique 20 para esse parâmetro. O valor padrão para esse parâmetro é 0.|
| take| **Opcional** O número de resultados que você quer recuperar. O número máximo de cada solicitação é 100. O padrão é 50.|

> [!NOTE]
> Os parâmetros de solicitação skip e take permitem a paginação para um grande número de registros de resultados.

**Valor de retorno**

O conjunto de resultados contém a matriz do **UserTranslation**. Cada UserTranslation possui os elementos a seguir:

| Campo | DESCRIÇÃO |
|:---|:---|
| CreatedDateUtc| A data de criação da entrada usando AddTranslation()|
| Da| O idioma de origem|
| OriginalText| O texto do idioma de origem usado ao enviar a solicitação|
|Classificação |A classificação que é aplicada pelo remetente na chamada do método AddTranslation()|
|Para|    O idioma de destino|
|TranslatedText|    A tradução como enviada na chamada de método AddTranslation()|
|Uri|   O URI aplicado na chamada de método AddTranslation()|
|Usuário   |O nome de usuário|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é menor que o valor do parâmetro **minDateUtc**.|
| TranslateApiException | IP excede a cota.| <ul><li>O limite para o número de solicitações por minuto é alcançado.</li><li>O tamanho da solicitação permanece limitado a 10000 caracteres.</li><li>Uma cota horária e uma cota diária limitam o número de caracteres que a API do Microsoft Translator aceitará.</li></ul>|
| TranslateApiException | AppId excede a cota.| A ID do aplicativo excedeu a cota horária ou diária.|

> [!NOTE]
> A cota será ajustada para garantir a equidade entre todos os usuários do serviço.

**Exibir exemplos de código no GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)

<properties
    pageTitle="Adicionar a API do Microsoft Translator ao PowerApps Enterprise ou aplicativos lógicos | Microsoft Azure"
    description="Visão geral do conector do Microsoft Translator com parâmetros da API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Introdução ao conector do Microsoft Translator
Conecte-se ao Microsoft Translator para traduzir texto, detectar um idioma e muito mais. O conector do Microsoft Translator pode ser usado em:

- Aplicativos lógicos 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Com o Microsoft Translator, você pode:

- Compile seu fluxo de negócios baseado nos dados obtidos do Microsoft Translator. 
- Use ações para traduzir texto, detectar um idioma e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo arquivo é criado no Dropbox, você pode traduzir o texto contido no arquivo para outro idioma usando o Microsoft Translator.
- Adicione o conector do Microsoft Translator ao PowerApps Enterprise. Assim, seus usuários poderão usar esse conector em seus próprios aplicativos. 

Para saber mais sobre como adicionar um conector ao PowerApps Enterprise, acesse [Registrar um conector no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Microsoft Translator inclui as seguintes ações. Não há nenhum gatilho.

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Detectar idioma</li><li>Conversão de texto em fala</li><li>Traduzir texto</li><li>Obter idiomas</li><li>Obter idiomas de fala</li></ul>

Todos os conectores dão suporte a dados nos formatos JSON e XML.


## Criar uma conexão com o Microsoft Translator

>[AZURE.INCLUDE [Etapas para criar uma conexão com o Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Detectar o idioma    
Detecta o idioma de origem de determinado texto. ```GET: /Detect```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |O texto cujo idioma será identificado|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Texto em fala    
Converte determinado texto em fala como uma transmissão de áudio no formato wave. ```GET: /Speak```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser convertido|
|idioma|string|sim|query|nenhum |Código de idioma para gerar fala (exemplo: “pt-BR”)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Traduzir o texto    
Traduz o texto para um idioma especificado usando o Microsoft Translator. ```GET: /Translate```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser traduzido|
|languageTo|string|sim|query| nenhum|Código do idioma de destino (exemplo: “fr”)|
|languageFrom|string|não|query|nenhum |Idioma de origem; se não tiver sido fornecido, o Microsoft Translator tentará detectá-lo automaticamente. (exemplo: en)|
|categoria|string|não|query|geral |Categoria de tradução (padrão: “geral”)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter linguagens    
Recupera todos os idiomas com suporte do Microsoft Translator. ```GET: /TranslatableLanguages```

Não existem parâmetros para esta chamada.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter idiomas de fala    
Recupera os idiomas disponíveis para sintetização de voz. ```GET: /SpeakLanguages```

Não existem parâmetros para esta chamada.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|

## Definições de objeto

#### Idioma: modelo de idioma para os idiomas traduzíveis do Microsoft Translator

|Nome da Propriedade | Tipo de Dados | Obrigatório|
|---|---|---|
|Código|string|não|
|Nome|string|não|


## Próximas etapas

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0525_2016-->
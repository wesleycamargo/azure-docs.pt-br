<properties
   pageTitle="Uso de Azure Functions com Aplicativos Lógicos | Microsoft Azure"
   description="Veja como usar Azure Functions com Aplicativos Lógicos"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Usando Azure Functions com Aplicativos Lógicos

Você pode executar trechos personalizados do C# ou do Node.js ao utilizar Azure Functions de um Aplicativo Lógico. [Azure Functions](../azure-functions/functions-overview.md) é uma oferta que permite a computação sem servidor no Microsoft Azure. Isso é útil em Aplicativos Lógicos para muitos cenários abaixo:

* A formatação de um valor de uma ação (por exemplo, converter de DateTime em uma cadeia de caracteres de data)
* Executar cálculos em um fluxo de trabalho
* Extensão da funcionalidade de Aplicativos Lógicos com funções com suporte no C# ou no Node.js

## Criação de uma Azure Function para Aplicativos Lógicos

É recomendável que você crie uma nova Azure Function no portal de funções usando os modelos “Webhook Genérico de Nó” ou “Webhook Genérico do C#". Isso preencherá automaticamente um modelo que aceita `application/json` de um Aplicativo Lógico, e as funções que estejam usando esses modelos serão automaticamente descobertas e listadas no designer de Aplicativos Lógicos em "Azure Functions na minha região".

As funções do Webhook aceitam uma solicitação e a passam para o método por meio de uma variável `data`. Você pode acessar as propriedades da carga usando a notação de ponto como `data.foo`. Por exemplo, uma função javascript simples que converte um valor DateTime em uma cadeia de caracteres de data terá a seguinte aparência:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Chamada a Azure Functions de um Aplicativo Lógico

No designer, se você clicar no menu suspenso para ações, poderá selecionar "Azure Functions na minha região". Isso listará os contêineres em sua assinatura e permitirá que você escolha a função que deseja chamar. Após a seleção, será solicitada a especificação de um objeto de carga de entrada. Esta é a mensagem que o Aplicativo Lógico enviará para a função e deverá ser um objeto JSON. Por exemplo, se eu quisesse passar a data da "Última Modificação" de um gatilho do salesforce, a carga de função poderia ser assim:

![][1]

## Acionamento de Aplicativos Lógicos de uma Azure Function

Também é possível acionar um Aplicativo Lógico de dentro de uma função. Para fazer isso, basta criar um Aplicativo Lógico com um gatilho manual (detalhes [aqui](app-service-logic-http-endpoint.md)). Em seguida, em sua Azure Function, gere um POST HTTP para a URL de gatilho manual com a carga que deseja enviar para o Aplicativo Lógico.

### Criação de uma função no Designer

Você também pode criar uma função de webhook do node.js de dentro do designer. Primeiro, selecione "Azure Functions na minha região" e escolha um contêiner para sua função. Se você ainda tiver um contêiner, precisará criar do [Portal de Azure Functions](https://functions.azure.com/signin). Você pode selecionar "Criar Novo". Para gerar um modelo com base nos dados que você deseja calcular, especifique o objeto de contexto que você planeja passar para uma função. Ele deve ser um objeto JSON. Por exemplo, se eu estiver passando o conteúdo do arquivo de uma ação FTP, minha carga de contexto terá esta aparência:

![][2]

>[AZURE.NOTE] Já que esse objeto não foi convertido em uma cadeia de caracteres com a adição de aspas, o conteúdo será adicionado diretamente à carga JSON. Isso apresentará um erro se não for um token JSON (ou seja, uma cadeia de caracteres ou um objeto/matriz JSON). Para converter uma cadeia de caracteres, basta adicionar aspas como no exemplo do Salesforce acima.

O designer gerará então um modelo de função que você pode criar embutido. As variáveis são previamente criadas com base no contexto que você planeja passar para a função.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->
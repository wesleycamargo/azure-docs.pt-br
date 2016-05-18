<properties
   pageTitle="Usando o aplicativo de API de JavaScript em um aplicativo lógico | Microsoft Azure"
   description="Conector ou aplicativo de API de JavaScript"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/22/2016"
   ms.author="stepsic"/>

#Aplicativo de API de JavaScript

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

O aplicativo de API de JavaScript oferece uma maneira fácil de executar expressões de JavaScript simples *enquanto seu aplicativo lógico é executado*.

##Quando você deve usar esse aplicativo de API?
O cenário principal para esse aplicativo de API é quando você deseja que o ciclo de vida do código que você escreveu seja igual ao aplicativo lógico e você *não* quer que o código seja chamado em outros cenários.

Por outro lado, se você quiser um trecho de código reutilizável que tem um ciclo de vida independente do aplicativo lógico, você deve usar o aplicativo de API WebJobs para criar expressões de código simples e chamá-las por meio de seu aplicativo lógico.

Por fim, se quiser incluir algum pacote adicional, você também precisará usar o aplicativo de API do WebJobs, já que não pode adicionar bibliotecas usando o aplicativo de API de JavaScript.

Use o [aplicativo de API de C#](app-service-logic-cs-api.md) se você preferir escrever as expressões em C#.

##Criando um aplicativo de API de JavaScript
Para usar o aplicativo de API de JavaScript, você precisa primeiro criar uma instância do aplicativo de API de JavaScript. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou selecionando o aplicativo de API de JavaScript no Azure Marketplace.

##Usando o aplicativo de API de JavaScript na superfície do designer de aplicativos lógicos
###Gatilho
Você pode criar um gatilho que o serviço de aplicativo lógico vai sondar (em um intervalo que você definir) e, se ele retornar algum conteúdo, o aplicativo lógico será executado; caso contrário, ele aguardará até o próximo intervalo de sondagem para verificar novamente.

As entradas para o gatilho são:
- **Expressão JavaScript** - uma expressão que será avaliada. Ela é invocada dentro de uma função e deve retornar `false` quando você não quiser que o Aplicativo lógico seja executado, podendo retornar qualquer outra coisa quando você quiser que o Aplicativo lógico seja executado. Você pode usar o conteúdo da resposta nas ações do aplicativo lógico.
- **Objeto de contexto** - um objeto opcional que pode ser passado para o gatilho. Você pode definir quantas propriedades quiser, mas a entidade de nível superior deve ser um objeto, por exemplo, `{ "bar" : 0}`.

Você pode ter um gatilho simples que somente executa seu aplicativo lógico entre os minutos 15 e 30 da hora:

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###Ação

Da mesma forma, você pode fornecer uma ação a ser executada.

As entradas para a ação são:
- **Expressão JavaScript** - uma expressão que será avaliada. Você precisa incluir a instrução `return` para obter qualquer conteúdo. 
- **Objeto de contexto** - um objeto opcional que pode ser passado para o gatilho. Você pode definir quantas propriedades quiser, mas a entidade de nível superior deve ser um objeto, por exemplo, `{ "bar" : 0}`.

Por exemplo, imagine que você está usando o gatilho do Office 365 **Novo Email**. Isso retorna o seguinte objeto:
```
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

Mas é recomendável carregar esses anexos em uma postagem do Yammer. Infelizmente, o esquema dos anexos do Yammer é ligeiramente diferente. Agora você pode analisar isso dentro do Aplicativo lógico. Para o objeto de contexto, passe: `@triggerBody()`, e para a expressão, passe:

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

A ação retorna o JSON retornado da sua função. Portanto, no aplicativo de API do Yammer, você pode fazer referência a `@body('javascriptapi')` para a propriedade **Anexos**.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

 

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0413_2016-->
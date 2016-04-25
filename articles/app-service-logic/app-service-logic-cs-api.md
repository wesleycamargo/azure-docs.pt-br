<properties
   pageTitle="Executar expressões C# em um aplicativo de API em C# em um aplicativo lógico | Microsoft Azure"
   description="Aplicativo de Api ou conector C#"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/22/2016"
   ms.author="jehollan"/>

#Aplicativo de API de C#

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

O aplicativo de API de C# oferece uma maneira fácil de executar expressões C# simples *enquanto seu aplicativo lógico* é executado.

##Quando você deve usar esse aplicativo de API?
O cenário principal para esse aplicativo de API é quando você deseja que o ciclo de vida do código que você escreveu seja igual ao aplicativo lógico e você *não* quer que o código seja chamado em outros cenários.

Por outro lado, se você quiser um trecho de código reutilizável que tem um ciclo de vida independente do aplicativo lógico, você deve usar o aplicativo de API WebJobs para criar expressões de código simples e chamá-las por meio de seu aplicativo lógico.

Por fim, se quiser incluir qualquer pacote adicional, você precisaria transmitir o assembly (. dll) para o conector como uma cadeia de caracteres binários Base64 codificada (como a saída do armazenamento de blob). Se você quiser mais flexibilidade em pacotes e assemblies, um WebJob provavelmente seria uma opção melhor.

Use o [aplicativo de API de JavaScript](app-service-logic-javascript-api.md) se você preferir escrever as expressões em JS.

##Criando um aplicativo de API de C#
Para usar o aplicativo de API de C#, você precisa primeiro criar uma instância do aplicativo de API de C#. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou selecionando o aplicativo de API de C# do Azure Marketplace.

##Usando o aplicativo de API de C# na superfície do designer de aplicativos lógicos
###Gatilho
Você pode criar um gatilho que o serviço de aplicativo lógico vai sondar (em um intervalo que você definir) e, se ele retornar algo diferente de `false`, o Aplicativo Lógico será executado; caso contrário, ele aguardará até o próximo intervalo de sondagem para verificar novamente.

As entradas para o gatilho são:
- **Expressão C#** - uma expressão que será avaliada. Ela é invocada dentro de uma função e deve retornar `false` quando você não quiser que o Aplicativo lógico seja executado, podendo retornar qualquer outra coisa quando você quiser que o Aplicativo lógico seja executado. Você pode usar o conteúdo da resposta nas ações do aplicativo lógico.

Você pode ter um gatilho simples que somente executa seu aplicativo lógico entre os minutos 15 e 30 da hora:

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###Ação

Da mesma forma, você pode fornecer uma ação a ser executada.

As entradas para a ação são:
- **Expressão C#** - uma expressão que será avaliada. Você precisa incluir a instrução `return` para obter qualquer conteúdo. 
- **Objeto(s) de contexto** - um objeto de contexto opcional que pode ser passado para o gatilho. Você pode definir quantas propriedades quiser, mas a base deve ser um JObject `{ ... }`, e os objetos podem ser referenciados no script por meio do nome da chave (o valor é transmitido como um JToken correspondente ao nome).
- **Bibliotecas** - uma matriz opcional de arquivos .dll para incluir ao compilar o script. A matriz usa a seguinte estrutura e funciona melhor ao lado de um conector de armazenamento de blob com o arquivo. dll como a saída:

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

Por exemplo, imagine que você está usando o gatilho do Office 365 **Novo Email**. Isso retorna o seguinte objeto:

```javascript
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

Mas é recomendável carregar esses anexos em uma postagem do Yammer. Infelizmente, o esquema dos anexos do Yammer é ligeiramente diferente. Agora, você pode analisar isso dentro do Aplicativo lógico. Para o objeto de contexto, passe: `@triggerBody()`, e para a expressão, passe:

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

A ação retorna o objeto retornado da sua função em um objeto de resultados. Portanto, no aplicativo de API do Yammer, você pode fazer referência a `@body('csapi').results` para a propriedade **Anexos**.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

 

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0413_2016-->
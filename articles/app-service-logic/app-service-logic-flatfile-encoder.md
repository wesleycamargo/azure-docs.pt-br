<properties 
   pageTitle="Codificador de Arquivo Simples do BizTalk" 
   description="Codificador de Arquivo Simples do BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/01/2015"
   ms.author="rajram"/>

# Codificador de Arquivo Simples do BizTalk

O conector de Codificação e Decodificação de Arquivo Simples do BizTalk auxilia na interoperabilidade de seu aplicativo entre dados de arquivo simples (por exemplo, excel, csv) e dados XML. Ele pode converter uma instância de arquivo simples em XML e vice-versa.

##Usando o Codificador de Arquivo Simples do BizTalk
1. Para usar o Codificador de Arquivo Simples do BizTalk, primeiro você precisa criar uma instância do aplicativo de API do Codificador de Arquivo Simples do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Codificador de Arquivo Simples do BizTalk no Azure Marketplace.

###Configurar o Codificador de Arquivo Simples do BizTalk
O Codificador de Arquivo Simples do BizTalk usa esquemas como parte de sua configuração. Os usuários podem iniciar a folha de configuração do aplicativo de API iniciando o aplicativo de API diretamente do portal do Azure ou clicando duas vezes no aplicativo de App na superfície do designer.

![Configuração do Codificador de Arquivo Simples do BizTalk][1]

Na folha do aplicativo de App, os usuários podem configurar esquemas clicando na parte *Esquemas*.

![Parte de esquemas do Codificador de Arquivo Simples do BizTalk][2]

Os usuários podem carregar esquemas do disco ou gerar um com base em uma instância de arquivo simples ou em uma instância JSON.

![Parte de esquemas do Codificador de Arquivo Simples do BizTalk][3]


###Usando o Codificador de Arquivo Simples do BizTalk na superfície do designer
Uma vez configurado, os usuários podem clicar em *->* e escolher uma ação em uma lista de ações.

![Lista de ações do Codificador de Arquivo Simples do BizTalk][4]

####Arquivo Simples para Xml

![Lista de ações do Codificador de Arquivo Simples do BizTalk][5]

<table>
	<tr>
		<th>Parâmetro</th>
		<th>Tipo</th>
		<th>Descrição do parâmetro</th>
	</tr>
	<tr>
		<td>Arquivo simples</td>
		<td>string</td>
		<td>Conteúdo do arquivo simples de entrada</td>
	</tr>
	<tr>
		<td>Nome do Esquema</td>
		<td>string</td>
		<td>Nome do esquema que representa o arquivo simples de entrada</td>
	</tr>
	<tr>
		<td>Nome Raiz</td>
		<td>string</td>
		<td>Nome do nó raiz do esquema de arquivo simples</td>
	</tr>
</table>


A ação retorna a saída como uma cadeia de caracteres ‒ Xml de saída. O Xml de saída contém a representação em xml do conteúdo do arquivo simples de entrada.

####Exportar para Arquivo Simples

![Lista de ações do Codificador de Arquivo Simples do BizTalk][6]

<table>
	<tr>
		<th>Parâmetro</th>
		<th>Tipo</th>
		<th>Descrição do parâmetro</th>
	</tr>
	<tr>
		<td>XML de entrada</td>
		<td>string</td>
		<td>Conteúdo do Xml de entrada</td>
	</tr>
</table>

A ação retorna a saída como uma cadeia de caracteres ‒ Arquivo Simples. A saída contém a representação de arquivo simples do conteúdo xml de entrada.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=July15_HO3-->
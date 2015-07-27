<properties
   pageTitle="Validador de XML do BizTalk"
   description="Validador de XML do BizTalk"
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

# Validador de XML do BizTalk

O conector de validação de XML do BizTalk ajuda seu aplicativo a validar dados XML em relação a esquemas XML predefinidos. Os usuários podem usar esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou em conectores existentes.

##Usando o Validador de XML do BizTalk
1. Para usar o Validador de XML do BizTalk, primeiro você precisa criar uma instância do aplicativo de API do Validador de XML do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Validador de XML do BizTalk do Azure Marketplace.

###Configurar o Validador de XML do BizTalk
O Validador de XML do BizTalk usa esquemas como parte de sua configuração. Os usuários podem iniciar a folha de configuração do aplicativo de API iniciando o aplicativo de API diretamente do portal do Azure ou clicando duas vezes no aplicativo de App na superfície do designer.

![Configuração do Validador de XML do BizTalk][1]

Na folha do aplicativo de API, os usuários podem configurar esquemas clicando na parte *Esquemas*

![Parte Esquemas do Validador de XML do BizTalk][2]

Os usuários podem carregar esquemas do disco ou gerar um com base em uma instância de arquivo simples ou em uma instância JSON.

![Esquemas do Validador de XML do BizTalk][3]


###Usando o Codificador de Arquivo Simples do BizTalk na superfície do designer
Uma vez configurado, os usuários podem clicar em *->* e escolher uma ação em uma lista de ações.

![Lista de ações do Validador de XML do BizTalk][4]

####Validar Xml

A ação Validar Xml valida uma entrada xml específica com base em esquemas pré-configurados.

![Validar Xml no Validador de XML do BizTalk][5]

<table>
	<tr>
		<th>Parâmetro</th>
		<th>Tipo</th>
		<th>Descrição do parâmetro</th>
	</tr>
	<tr>
		<td>XML de entrada</td>
		<td>string</td>
		<td>Xml de entrada a ser validado</td>
	</tr>
</table>


A ação retorna a saída como um objeto. A saída contém o modelo que representa a resposta do Validador de Xml. Ela consiste em resultado, nome do esquema, nó raiz e descrição do erro.

![6]

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG
[6]: ./media/app-service-logic-xml-validator/img1.PNG
 

<!---HONumber=July15_HO3-->
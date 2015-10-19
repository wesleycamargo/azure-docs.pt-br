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
   ms.date="10/01/2015"
   ms.author="rajram"/>

# Validador de XML do BizTalk

Use o conector do Validador de XML do BizTalk em seu aplicativo para validar os dados XML com base em esquemas XML predefinidos. Os usuários podem utilizar qualquer um dos esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou conectores existentes.

##Usando o Validador de XML do BizTalk
Para usar o Validador de XML do BizTalk, primeiro crie uma instância do aplicativo de API do Validador de XML do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Validador de XML do BizTalk do Azure Marketplace.

###Configurar o Validador de XML do BizTalk
O Validador de XML do BizTalk usa esquemas como parte de sua configuração. Os usuários podem iniciar a lâmina de configuração do Aplicativo de API iniciando o Aplicativo de API diretamente do Portal do Azure, ou clicando duas vezes no Aplicativo de API na superfície do designer.

![Configuração do Validador de XML do BizTalk][1]

Na lâmina Aplicativo de API, os usuários podem configurar esquemas selecionando *Esquemas*

![Parte Esquemas do Validador de XML do BizTalk][2]

Os usuários podem carregar esquemas do disco ou gerar um com base em uma instância de arquivo simples ou em uma instância JSON.

![Esquemas do Validador de XML do BizTalk][3]


###Usando o Codificador de Arquivo Simples do BizTalk na superfície de design
Depois de configurado, os usuários podem selecionar *->* e escolher uma ação na lista.

![Lista de ações do Validador de XML do BizTalk][4]

####Validar Xml

A ação Validar Xml valida uma entrada xml específica com base em esquemas pré-configurados.

![Validar Xml no Validador de XML do BizTalk][5]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
XML de entrada|string|Xml de entrada a ser validado

A ação retorna a saída como um objeto. A saída contém o modelo que representa a resposta do Validador de Xml. Ela consiste em resultado, nome do esquema, nó raiz e descrição do erro.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=Oct15_HO2-->
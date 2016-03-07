<properties
   pageTitle="Usando o Extrator de XPath do BizTalk em aplicativos lógicos do Serviço de Aplicativo do Azure | Microsoft Azure"
   description="Extrator de XPath do BizTalk"
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
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Extrator de XPath do BizTalk

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

O conector do Extrator de XPath do BizTalk ajuda seu aplicativo a pesquisar e extrair dados de conteúdo XML com base em determinado XPath.

## Usando o Extrator de Xpath do BizTalk
1. Para usar o Extrator de Xpath do BizTalk, primeiro você precisa criar uma instância do aplicativo de API do Extrator de Xpath do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Extrator de Xpath do BizTalk do Azure Marketplace.

	>[AZURE.NOTE] Não há definições de configuração associadas com o BizTalk Xpath Extractor
2. [Criar um novo aplicativo lógico]. Abra "Gatilhos e Ações" no Aplicativo Lógico para abrir o Designer de Aplicativos Lógicos para configurar seu fluxo.
3. No designer, o painel direito mostra os Aplicativos de API disponíveis para compilar seu fluxo. Encontre o "Extrator de XPath do BizTalk". Selecionar isso adiciona o Extrator de XPath ao seu fluxo e provisiona uma instância dele.
4. Depois de configurado, o designer mostra a ação associada ao aplicativo de API do BizTalk do Extrator XPath:
	![Escolher Ação do Extrator de XPath do BizTalk][1]

5. Escolha "Extrair Usando XPath". "Extrair Usando XPath" avalia a expressão xpath de entrada em um determinado XML de entrada: ![Entrada do Extrator de XPath do BizTalk][2]

	Parâmetro|Tipo|Descrição do parâmetro
---|---|---
XPath|string|Caminho de consulta dentro do xml.
Xml de entrada|string|Conteúdo do Xml de entrada.

A ação retorna a saída como uma cadeia de caracteres ‒ Resultado. O resultado contém o valor do caminho da consulta dentro do XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->
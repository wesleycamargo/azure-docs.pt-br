<properties
   pageTitle="Exemplos e Cenários dos Aplicativos Lógicos | Microsoft Azure"
   description="Consultar exemplos comuns de aplicativos Lógicos e aprender a implementar cenários comuns"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# Exemplos de Aplicativos Lógicos e Cenários Comuns

Esse documento detalha cenários e exemplos comuns para ajudá-lo a entender algumas das maneiras que você pode usar os aplicativos Lógicos para automatizar processos de negócios.

## Disparando um Aplicativo Lógico

Há várias maneiras que você pode disparar um aplicativo Lógico de outro aplicativo. Estas são algumas das maneiras:

- [Solicitação HTTP (POST)](app-service-logic-http-endpoint.md)
- [Receber um webhook](app-service-logic-create-api-app.md)
- [Pesquisar um ponto de extremidade](app-service-logic-create-api-app.md)

### Cenários

- [Resposta Síncrona de Solicitação do Aplicativo Lógico](app-service-logic-http-endpoint.md)

## Ações Personalizadas e de Execução Longa

- [Criando uma Ação Personalizada](app-service-logic-create-api-app.md)

## Implantando e Gerenciando um Aplicativo Lógico

- [Criar um Modelo de Implantação do Aplicativo Lógico](app-service-logic-create-deploy-template.md)
- [Diagnosticando Problemas com um Aplicativo Lógico](app-service-logic-diagnosing-failures.md)
- [Implantar um Aplicativo Lógico a partir do Visual Studio](app-service-logic-deploy-from-vs.md)
- [Monitorar um Aplicativo Lógico](app-service-logic-monitor-your-logic-apps.md)

## Tipos de conteúdo, Conversões e Transformações

A [linguagem de definição do fluxo de trabalho](http://aka.ms/logicappsdocs) dos Aplicativos Lógicos contém muitas funções para permitir que você converta e trabalhe com diferentes tipos de conteúdo. Além disso, o mecanismo fará tudo o que pode para preservar os tipos de conteúdo como fluxos de dados no fluxo de trabalho.

- [Manipulação dos Tipos de Conteúdo](app-service-logic-content-type.md) como application/json, application/xml e plain/text
- [Criando Definições do Fluxo de Trabalho](app-service-logic-author-definitions.md)
- [Referência da Linguagem de Definição do Fluxo de Trabalho](http://aka.ms/logicappsdocs)

## Lotes e Loops

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Integração com as Azure Functions

- [Integração com o Azure Functions](app-service-logic-azure-functions.md)

### Cenários

- [Azure Functions como um Gatilho do Barramento de Serviço](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST e SOAP

 - [Chamando SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Continuaremos adicionando cenários e exemplos a esse documento. Use a seção de comentários abaixo para nos informar sobre quais exemplos ou cenários você gostaria de ver aqui.

<!---HONumber=AcomDC_0601_2016-->
<properties
   pageTitle="Cenários e exemplos de aplicativos Lógicos | Microsoft Azure"
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

# Exemplos de aplicativos Lógicos e cenários comuns

Esse documento detalha cenários e exemplos comuns para ajudá-lo a entender algumas das maneiras que você pode usar os aplicativos Lógicos para automatizar processos de negócios.

## Disparar um aplicativo lógico

Há várias maneiras que você pode disparar um aplicativo Lógico de outro aplicativo. Estas são algumas das maneiras:

- [Solicitação HTTP (POST)](app-service-logic-http-endpoint.md)
- [Receber um webhook](app-service-logic-create-api-app.md)
- [Pesquisar um ponto de extremidade](app-service-logic-create-api-app.md)

### Cenários

- [Resposta Síncrona de Solicitação do Aplicativo Lógico](app-service-logic-http-endpoint.md)

## Tipos de conteúdo, Conversões e Transformações

A [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) dos Aplicativos Lógicos contém muitas funções para permitir que você converta diferentes tipos de conteúdo e trabalhe com eles. Além disso, o mecanismo fará tudo o que pode para preservar os tipos de conteúdo como fluxos de dados no fluxo de trabalho.

- [Manipulação de Tipos de Conteúdo](app-service-logic-content-type.md) como application/json, application/xml e plain/text
- [Linguagem de Definição de Fluxo de Dados](http://aka.ms/logicappsdocs)

## Ações de Execução Longa

- [Criação de uma ação de execução longa](app-service-logic-create-api-app.md)

## Lotes e Loops

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Integração com Fluxos de Trabalho Aninhados e o Azure Functions

- [Integração com o Azure Functions](app-service-logic-azure-functions.md)

## HTTP, REST e SOAP

 - [Chamando SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Continuaremos adicionando cenários e exemplos a esse documento. Use a seção de comentários abaixo para nos informar sobre quais exemplos ou cenários você gostaria de ver aqui.

<!---HONumber=AcomDC_0525_2016-->
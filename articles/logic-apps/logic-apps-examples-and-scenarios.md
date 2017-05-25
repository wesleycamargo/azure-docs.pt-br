---
title: "Exemplos e cenários – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Revise exemplos de aplicativos lógicos para cenários comuns"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: .net,nodejs,java
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/14/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dcf089d680249d0a2f9d748b315076d91c8c78e8
ms.contentlocale: pt-br
ms.lasthandoff: 03/30/2017


---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Exemplos e cenários comuns para Aplicativos Lógicos do Azure

Para ajudar você a conhecer mais sobre os vários padrões e recursos no Aplicativo Lógico do Azure, confira aqui exemplos e cenários comuns.

## <a name="key-scenarios-for-logic-apps"></a>Principais cenários de aplicativos lógicos

O Aplicativo Lógico do Azure fornece orquestração e integração resiliente para serviços diferentes. O serviço Aplicativo Lógico "não em servidor", portanto, você não precisa se preocupar com a escala ou instâncias - basta definir o fluxo de trabalho (gatilho e ações). A plataforma subjacente lida com a escala, disponibilidade e desempenho. Qualquer cenário o qual você precise coordenar várias ações, especialmente entre vários sistemas, é um excelente caso de uso para o Aplicativo Lógico do Azure. Confira aqui alguns padrões e exemplos.

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a gatilhos e estender ações

Cada aplicativo lógico começa com um gatilho. Por exemplo, seu fluxo de trabalho pode começar com um evento de agendamento, uma invocação manual ou um evento de um sistema externo, como o gatilho "quando um arquivo for adicionado a um servidor FTP". No momento, o Aplicativo Lógico do Azure oferece suporte a mais de 100 conectores prontos para uso, desde SAP local até Serviços Cognitivos do Azure. Para sistemas e serviços que talvez não tenham conectores publicados, também é possível estender os aplicativos lógicos.

* [Tutorial: Compilar um painel social capacitado por IA em minutos com o Aplicativo Lógicos e o Power BI](http://aka.ms/logicappsdemo)
* [Criar gatilhos ou ações personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configurar ações de longa execução para execução de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos e ações externas com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, disparar ou aninhar fluxos de trabalho com respostas síncronas a solicitações HTTP](logic-apps-http-endpoint.md)
* [Tutorial: Responder a webhooks de SMS do Twilio e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>Tratamento de erros, registro em log e recursos de fluxo de controle

Os aplicativos lógicos incluem recursos avançados para o fluxo de controle avançado, como comutadores, condições, loops e escopos. Para garantir soluções resilientes, implemente também a manipulação de erros e exceções em seus fluxos de trabalho. Para receber notificação e logs de diagnóstico para status de execução de fluxo de trabalho, o Aplicativo Lógico do Azure também fornece monitoramento e alertas.

* [Executar ações diferentes com instruções switch](logic-apps-switch-case.md)
* [Processar itens em matrizes e coleções com loops e lotes em aplicativos lógicos](logic-apps-loops-and-scopes.md)
* [Manipulação de erro e exceções do autor em um fluxo de trabalho](logic-apps-exception-handling.md)
* [Configurar alertas e diagnóstico do Azure](logic-apps-monitor-your-logic-apps.md)
* [Caso de uso: Como uma empresa de assistência médica usa a manipulação de exceção do aplicativo lógico para fluxos de trabalho HL7 FHIR](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>Implantar e gerenciar aplicativos lógicos

Você pode desenvolver e implantar totalmente aplicativos lógicos com o Visual Studio, o Visual Studio Team Services ou qualquer outra ferramenta de controle do código-fonte e compilação automatizada. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode conferir para controlar o código-fonte a fim de controlar a versão.

* [Criar modelos um modelo de implantação automatizado](../logic-apps/logic-apps-create-deploy-template.md)
* [Compilar e implantar aplicativos lógicos do Visual Studio](logic-apps-deploy-from-vs.md)
* [Monitorar a integridade da seus aplicativos lógicos](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações em uma execução

Você pode acessar, converter e transformar vários tipos de conteúdo usando diversas funções na [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) do Aplicativo Lógico do Azure . Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O mecanismo do Aplicativo Lógico preserva os tipos de conteúdo a fim de dar suporte à transferência de conteúdo entre serviços sem perdas.

* [Lidar com tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream` e `multipart/formdata`
* [Como funcionam as expressões de fluxo de trabalho em aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Referência: Linguagem de definição de fluxo de trabalho do Aplicativo Lógico do Azure](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outros recursos e integrações

Os aplicativos lógicos também oferecem integração com muitos serviços, como o Azure Functions, O Gerenciamento de API do Azure, Serviços de Aplicativo do Azure e pontos de extremidade HTTP personalizados, por exemplo, REST e SOAP.

* [Criar um painel social em tempo real com o Azure sem servidor](logic-apps-scenario-social-serverless.md)
* [Chamar o Azure Functions de aplicativos lógicos](../logic-apps/logic-apps-azure-functions.md)
* [Cenário: Disparar aplicativos lógicos com o Azure Functions](logic-apps-scenario-function-sb-trigger.md)
* [Blog: Chamar pontos de extremidade SOAP de aplicativos lógicos](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="next-steps"></a>Próximas etapas

- [Tratar erros e exceções em aplicativos lógicos](logic-apps-exception-handling.md)
- [Criar definições de fluxo de trabalho com a linguagem de definição de fluxo de trabalho](logic-apps-author-definitions.md)
- [Envie seus comentários, dúvidas ou sugestões para podermos aprimorar o Aplicativo Lógico do Azure](https://feedback.azure.com/forums/287593-logic-apps)

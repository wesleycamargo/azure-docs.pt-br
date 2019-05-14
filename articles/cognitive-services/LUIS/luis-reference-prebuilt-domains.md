---
title: Referência de domínio predefinido
titleSuffix: Azure
description: Referência para os domínios predefinidos, que são conjuntos predefinidos de tentativas e entidades de idioma Noções básicas sobre inteligente serviços (LUÍS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233884"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio predefinida para seu aplicativo LUIS
Esta referência contém informações sobre o [domínios pré-compilada](luis-how-to-use-prebuilt-domains.md), que são conjuntos predefinidos de entidades que oferece LUÍS e tentativas.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, iniciar sem tentativas e modelos. Você pode adicionar quaisquer tentativas de domínio pré-compilada e entidades a um modelo personalizado.

# <a name="supported-domains-across-cultures"></a>Domínios com suporte entre culturas

A única cultura com suporte é o inglês. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Tipo de entidade|description|
|--|--|
|Calendário|Calendário é nada sobre reuniões particulares e compromissos, _não_ eventos públicos (como agendas de Copa do mundo, os calendários de eventos de Seattle ou calendários genéricos (como que dia é hoje em dia, o que for outono iniciado, quando é dia de trabalho).|
|Comunicação|As solicitações para fazer chamadas, enviar mensagens ou mensagens instantâneas, localizar e adicionar contatos e várias outras solicitações relacionadas à comunicação (geralmente de saída). Nome de contato apenas as consultas não pertencem ao domínio de comunicação.|
|Email|Email é um subdomínio do domínio de comunicação. Ele contém principalmente as solicitações para enviar e receber mensagens por meio de emails.|
|HomeAutomation|O domínio de HomeAutomation fornece intenções e entidades relacionadas ao controle de dispositivos inteligentes de base. Ele é compatível basicamente com o comando de controle relacionado ao luzes e ar-condicionado, mas ele tem algumas capacidades de generalização para outros dispositivos de energia elétricas.|
|Observações|Observação de domínio fornece intenções e entidades para criar anotações e anotar itens para os usuários.|
|Locais|Locais incluem as empresas, instituições, restaurantes, espaços públicos e endereços. O domínio dá suporte ao local Localizando e solicitando sobre as informações de um local público, como local, horas e distância operacionais.|
|RestaurantReservation|Domínio de reserva do restaurante suporta intenções para lidar com as reservas de restaurantes.|
|ToDo|ToDo domínio fornece tipos de listas de tarefas para os usuários adicionar, marcar e excluir seus itens de tarefas.|
|Utilidades|Utilitários é um domínio geral entre LUIS todos os modelos predefinidos, que contém declarações em cenários de diferença e intenções comuns.|
|Clima|Domínio de clima se concentra na verificação de condição de clima e comunicados com local e a hora ou verificação de tempo por condições climáticas.|
|Web|O domínio da Web fornece a intenção e as entidades de pesquisa para um site.|

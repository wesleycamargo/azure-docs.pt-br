---
title: Configuração padrão de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre a configuração padrão do Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364207"
---
# <a name="default-values-and-boundaries"></a>Limites e valores padrão

Este documento descreve a configuração padrão de Aprendiz de Conversa e limites de serviço de chave.

## <a name="default-configuration"></a>Configuração padrão

Parâmetro | Valor padrão
--- | --- 
Tempo limite da sessão padrão | 30 minutos

## <a name="boundaries"></a>Limites

Parâmetro | Limite
--- | --- 
API de Criação, máximo de chamadas HTTP por mês | 5 M
API de Criação, máximo de chamadas HTTP por segundo | 25
API de Sessão, máximo de chamadas HTTP por mês | 500K
API de Sessão, máximo de chamadas HTTP por mês | 10
Número máximo de entidades por aplicativo personalizadas (não programático) | Consulte a [documentação de limites do LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries) pois, na prática, o número real pode ser um pouco menor
Número máximo de entidades pré-compiladas por aplicativo | Consulte a [documentação de limites do LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Número máximo de entidades por aplicativo (no total)  | 100
Número máximo de ações por aplicativo | 32
Número máximo de diálogos de treinamento por aplicativo | 1000
Número máximo de turnos de usuário por diálogo de treinamento | 100
Número máximo de diálogos de log por aplicativo | Nenhum limite predefinido, mas os diálogos de log somente serão retidos por um período fixo antes de serem descartadas.  Além disso, a interface do usuário do Aprendiz de Conversa mostrará 100 diálogos de log de cada vez. 
Número máximo de aplicativos por usuário | Nenhum limite pré-definido
Número máximo de ações de não espera sequenciais | 5 (*)

(*) Após 5 ações de não espera sequenciais, todas as ações que não forem de espera serão mascaradas e o Aprendiz de Conversa escolherá entre as ações de espera disponíveis.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao Aprendiz de Conversa](./quickstart.md)

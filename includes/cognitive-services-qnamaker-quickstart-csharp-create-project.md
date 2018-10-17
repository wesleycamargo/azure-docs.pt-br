---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019074"
---
1. Abra o Visual Studio 2017 Community Edition.
1. Crie um novo projeto de **Aplicativo de console (.NET Core)** e nomeie-o `QnaMakerQuickstart`. Aceite os padrões para as configurações restantes.
1. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto, **QnaMakerQuickstart**, depois selecione **Gerenciar Pacotes NuGet...**.
1. Na janela do NuGet, selecione **Navegador** e, em seguida, procure por **Newtonsoft.JSON** e instale o pacote. Esse pacote é usado para analisar o JSON retornado da resposta HTTP QnaMaker. 
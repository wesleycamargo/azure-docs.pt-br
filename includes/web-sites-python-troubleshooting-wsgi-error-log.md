---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394019"
---
Se o Python encontrar um erro ao iniciar seu aplicativo, apenas uma página simples de erro será retornada (por exemplo, "A página não pode ser exibida porque ocorreu um erro interno do servidor.").

Para capturar erros de aplicativo Python:

1. No Portal do Azure, no seu aplicativo Web, selecione **Configurações**.
2. Na guia **Configurações**, selecione **Configurações do aplicativo**.
3. Em **Configurações do aplicativo**, digite o seguinte par de chave/valor:
    * Chave: WSGI_LOG
    * Valor: D:\home\site\wwwroot\logs.txt (Insira o nome de arquivo da sua escolha)

Agora você verá erros no arquivo logs.txt na pasta wwwroot.

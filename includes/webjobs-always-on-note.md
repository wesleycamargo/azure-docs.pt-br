---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832187"
---
> [!NOTE]
> Um aplicativo Web pode atingir o tempo limite após 20 minutos de inatividade. Somente as solicitações para o aplicativo web real reiniciam o temporizador. Exibindo a configuração do aplicativo no portal do Azure ou fazer solicitações para o site de ferramentas avançadas (`https://<app_name>.scm.azurewebsites.net`) não reiniciam o temporizador. Se o aplicativo executar WebJobs contínuos ou agendados, habilite o **AlwaysOn** para garantir que os WebJobs sejam executados de modo confiável. Este recurso está disponível apenas nos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Standard e Premium.

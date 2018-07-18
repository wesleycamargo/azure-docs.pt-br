---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236460"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação com suporte oficialmente têm algo em comum: fazem alterações para os arquivos da pasta `/site/home/wwwroot` do seu aplicativo. Esses são os mesmos arquivos que são executados em produção. Portanto, a implantação pode falhar devido a arquivos bloqueados, ou o aplicativo em produção pode ter um comportamento inesperado durante a implantação porque nem todos os arquivos são atualizados ao mesmo tempo. Há algumas maneiras diferentes para evitar esses problemas:

- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [Lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/web-sites-staged-publishing.md) com [troca automática](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) habilitada. 
- Use [Run-from-Zip](https://github.com/Azure/app-service-announcements/issues/84) em vez disso.

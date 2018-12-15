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
ms.openlocfilehash: b8e3d32f0e230673ecbc043597afe8e7b5f25e06
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742386"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação com suporte oficialmente têm algo em comum: fazem alterações para os arquivos da pasta `/home/site/wwwroot` do seu aplicativo. Esses são os mesmos arquivos que são executados em produção. Portanto, a implantação pode falhar devido a arquivos bloqueados, ou o aplicativo em produção pode ter um comportamento inesperado durante a implantação porque nem todos os arquivos são atualizados ao mesmo tempo. Há algumas maneiras diferentes para evitar esses problemas:

- Interrompa o aplicativo ou habilite o modo offline para o aplicativo durante a implantação. Para obter mais informações, consulte [Lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implantar um [slot de preparo](../articles/app-service/web-sites-staged-publishing.md) com [troca automática](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) habilitada. 
- Use [Executar do pacote](https://github.com/Azure/app-service-announcements/issues/84).

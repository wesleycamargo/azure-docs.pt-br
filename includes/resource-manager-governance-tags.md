---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2be7e4d2f3697649df669a4f20ba4db62c1fc486
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401559"
---
Você pode aplicar marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Depois de aplicar marcas, você pode recuperar todos os recursos em sua assinatura com esse nome e valor de marca. As marcas permitem que você recupere recursos relacionados de diferentes grupos de recursos. Esta abordagem será útil quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga nos usuários e aumentar a precisão.

As seguintes limitações se aplicam a marcas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nome/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de nome/valor de marca. Se você tiver mais de 15 valores que você precisa associar a um recurso, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter diversos valores que são aplicados a um único nome de marca. Este artigo mostra um exemplo de atribuição de uma cadeia de caracteres JSON para a marca.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* As máquinas virtuais são limitadas ao total de 2048 caracteres para todos os valores e nomes de marca.
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos como Serviços de Nuvem.
* Os nomes das marcas não podem conter esses caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

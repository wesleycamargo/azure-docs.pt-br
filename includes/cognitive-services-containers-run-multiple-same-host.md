---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520920"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Execução de vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Você pode ter esse contêiner e um contêiner de serviço cognitivo diferente em execução no HOST juntos ou você pode ter vários contêineres do mesmo contêiner de serviço cognitivo em execução.

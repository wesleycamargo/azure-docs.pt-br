---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 4d2cfb8a39defec9d0d429bc80bfa6abf4f62164
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65031755"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Execução de vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta exposta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Você pode ter esse contêiner e um contêiner de serviço cognitivo diferente em execução no HOST juntos ou você pode ter vários contêineres do mesmo contêiner de serviço cognitivo em execução. 



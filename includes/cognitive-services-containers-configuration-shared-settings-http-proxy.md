---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: fd5354491254a216e720dac6487b331f047bd5cb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479242"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| NOME | Tipo de dados | DESCRIÇÃO |
|--|--|--|
|HTTP_PROXY|string|O proxy a ser usado, por exemplo, http://proxy:8888|
|HTTP_PROXY_CREDS|string|As credenciais necessárias para autenticação no proxy, por exemplo, nomedeusuario:senha.|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
mcr.microsoft.com/azure-cognitive-services/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```
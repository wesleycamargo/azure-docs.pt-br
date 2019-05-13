---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151092"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| NOME | Tipo de dados | DESCRIÇÃO |
|--|--|--|
|HTTP_PROXY|string|O proxy a ser usado, por exemplo, `http://proxy:8888`<br><url-proxy>|
|HTTP_PROXY_CREDS|string|Quaisquer credenciais necessárias para autenticação no proxy, por exemplo, nomedeusuario:senha.|
|`<proxy-user>`|string|O usuário para o proxy.|
|`proxy-password`|string|A senha associada ao `<proxy-user>` para o proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028869"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Usar a CLI do Docker para autenticar o registro de contêiner privado

Há várias maneiras de autenticar com o registro de contêiner particular para Contêineres de Serviços Cognitivos, mas o método recomendado a partir da linha de comandos é usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o comando [login do docker](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para efetuar login no `containerpreview.azurecr.io`, o registro do contêiner particular para Contêineres de Serviços Cognitivos. Substitua *\<nome de usuário\>* pelo nome de usuário e *\<senha\>* com a senha fornecida nas credenciais recebidas da equipe do Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você tiver protegido suas credenciais em um arquivo de texto, poderá concatenar o conteúdo desse arquivo de texto, usando o `cat` comando, para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* pelo caminho e nome do arquivo de texto que contém a senha e o *\<nome de usuário\>* com o nome de usuário fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

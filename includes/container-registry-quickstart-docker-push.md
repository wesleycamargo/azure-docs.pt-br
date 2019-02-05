---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7ed8b96a528d56b28262936c4b200762b3e93b8e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302233"
---
## <a name="push-image-to-registry"></a>Efetuar push de imagem para registro

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando [docker pull][docker-pull] a seguir para efetuar pull de uma imagem existente de Hub do Docker. Neste exemplo, extraia a imagem `hello-world`.

```Docker
docker pull hello-world
```

Antes de poder enviar uma imagem por push no registro, você deve marcá-lo com o nome totalmente qualificado do seu servidor de logon ACR. O nome do servidor de logon está no formato *\<nome-do-registro\>.azurecr.io* (tudo em letras minúsculas), por exemplo, *mycontainerregistry007.azurecr.io*.

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR.

```Docker
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Por fim, use [docker push][docker-push] para enviar a imagem por push para a instância do ACR. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR. Este exemplo cria o repositório **Olá, mundo**, que contém a imagem `hello-world:v1`.

```Docker
docker push <acrLoginServer>/hello-world:v1
```

Depois de efetuar push da imagem no registro de contêiner, remova a imagem `hello-world:v1` de seu ambiente do Docker local. (Observe que este comando [docker rmi][docker-rmi] não remove a imagem do repositório **Olá, mundo** em seu Registro de Contêiner do Azure.)

```Docker
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


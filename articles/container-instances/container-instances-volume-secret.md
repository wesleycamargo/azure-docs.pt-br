---
title: "Montar um volume secreto em Instâncias de Contêiner do Azure"
description: "Saiba como montar um volume secreto para armazenar informações confidenciais de acesso pelas instâncias de contêiner"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 6f8e1b6faac11b668a143f8013a198831a428c51
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montar um volume secreto em Instâncias de Contêiner do Azure

Saiba como montar um volume *secreto* nas instâncias de contêiner para o armazenamento e a recuperação de informações confidenciais pelos contêineres nos grupos de contêineres.

> [!NOTE]
> A montagem de um volume *secreto* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="secret-volume"></a>volume secreto

Você pode usar um volume *secreto* para fornecer informações confidenciais para os contêineres em um grupo de contêineres. O volume *secreto* armazena os segredos especificados em arquivos no volume, que os contêineres no grupo de contêineres podem acessar. Usando segredos em um volume *secreto*, você pode evitar colocar dados confidenciais, como chaves SSH ou credenciais de banco de dados no código do aplicativo.

O backup de todos os volumes *secretos* é feito por [tmpfs][tmpfs], um sistema de arquivos com suporte de RAM. O conteúdo jamais é gravado em armazenamento não volátil.

## <a name="mount-a-secret-volume"></a>Montar um volume secreto

Para montar um volume *secreto* em uma instância de contêiner, você deve implantar usando um [Modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, popule a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *secreto*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em um único contêiner. O contêiner monta um volume *secreto* que consiste em dois segredos codificados em Base64.

[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em Instâncias de Contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume gitRepo em Instâncias de Contêiner do Azure](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs
---
title: "Montar um volume gitRepo em Instâncias de Contêiner do Azure"
description: "Saiba como montar um volume gitRepo para clonar um repositório Git em suas instâncias de contêiner"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montar um volume gitRepo em Instâncias de Contêiner do Azure

Saiba como montar um volume *gitRepo* para clonar um repositório Git em suas instâncias de contêiner.

> [!NOTE]
> A montagem de um volume *gitRepo* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>Volume gitRepo

O volume *gitRepo* monta um diretório e clona para dentro dele o repositório Git especificado, na inicialização do contêiner. Usando um volume *gitRepo* em suas instâncias de contêiner, você pode evitar adicionar o código para fazer isso em seus aplicativos.

Quando você monta um volume *gitRepo*, você pode definir três propriedades para configurar o volume:

| Propriedade | Obrigatório | DESCRIÇÃO |
| -------- | -------- | ----------- |
| `repository` | sim | A URL completa, incluindo `http://` ou `https://`, do repositório Git a ser clonado.|
| `directory` | Não  | O diretório no qual o repositório deve ser clonado. O caminho não deve conter nem começar com "`..`".  Se você especificar "`.`", o repositório será clonado no diretório do volume. Caso contrário, o repositório do Git será clonado em um subdiretório com o nome fornecido, dentro do diretório do volume. |
| `revision` | Não  | O hash de confirmação da revisão a ser clonada. Se não for especificado, a revisão `HEAD` será clonada. |

## <a name="mount-a-gitrepo-volume"></a>Montar um volume gitRepo

Para montar um volume *gitRepo* em uma instância de contêiner, você deve implantar usando um [Modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, popule a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *gitRepo*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em um único contêiner. O contêiner clona dois repositórios do GitHub especificados pelos blocos de volume do *gitRepo*. O segundo volume inclui propriedades adicionais, especificando um diretório para o qual clonar e também o hash de confirmação de uma revisão específica a ser clonada.

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretório resultante dos dois repositórios clonados definida no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em Instâncias de Contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md)

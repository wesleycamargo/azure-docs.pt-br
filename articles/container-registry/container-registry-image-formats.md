---
title: Formatos de conteúdo do Registro de Contêiner do Azure
description: Saiba mais sobre os formatos de conteúdo com suporte no Registro de Contêiner do Azure.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634807"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formatos de conteúdo com suporte no Registro de Contêiner do Azure

Use um repositório privado no Registro de Contêiner do Azure para gerenciar um dos seguintes formatos de conteúdo. 

## <a name="docker-compatible-container-images"></a>Imagens de contêiner compatíveis com Docker

* [Manifesto de imagem de Docker V2, Esquema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifesto de imagem de Docker V2, Esquema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclui Listas de Manifestos que permitem que os registros armazenem imagens multiplataforma sob uma única referência "image:tag"

* [Especificação de formato de imagem de OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Gráficos Helm

O Registro de Contêiner do Azure também pode hospedar repositórios para [Gráficos Helm](https://helm.sh/), um formato de empacotamento usado para gerenciar e implantar aplicativos rapidamente para Kubernetes. Há suporte para [Cliente Helm](https://docs.helm.sh/using_helm/#installing-helm) versão 2.11.0 ou posterior.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [efetuar push e efetuar pull](container-registry-get-started-docker-cli.md) de imagens com Registro de Contêiner do Azure.

* Use [tarefas do ACR](container-registry-tasks-overview.md) para compilar e testar imagens de contêiner. 

* Use o [Moby BuildKit](https://github.com/moby/buildkit) para compilar e empacotar contêineres no formato de OCI.

* Configure um [repositório Helm](container-registry-helm-repos.md) hospedado no Registro de Contêiner do Azure. 



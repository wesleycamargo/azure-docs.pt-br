---
title: Como usar o kubectl com Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 978274a6059a327c8596df6776eaa47a27ea4a34
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "42140997"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usar kubectl com um Azure Dev Space

É possível acessar o cluster do Kubernetes em um Azure Dev Space e usar ferramentas do Kubernetes existentes, como `kubectl`.

Executar o comando `az aks use-dev-spaces` adicionará automaticamente um contexto de configuração `kubectl`, portanto, o kubectl já deverá estar conectado ao cluster do Kubernetes do Azure Dev Spaces. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar contexto: `kubectl config use-context <context-name>`
- Exiba o painel do Kubernetes: execute `kubectl proxy` e, em seguida, abra o navegador no endereço que este comando emitir (acrescente `/ui` à URL para navegar até o painel do Kubernetes).
- Liste os serviços em execução no Azure Dev Spaces padrão do Azure nomeado *padrão*: `kubectl get services --namespace=default`


---
title: Obter logs de Kubelet do AKS (Serviço de Contêiner do Azure)
description: Obter logs de Kubelet dos nós de cluster do AKS (Serviço de Contêiner do Azure)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 56e20a9f9d17eac01e6f85007db41dcc417f83e4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="get-kubelet-logs-from-azure-container-service-aks-cluster-nodes"></a>Obter logs de Kubelet dos nós de cluster do AKS (Serviço de Contêiner do Azure)

Ocasionalmente, talvez seja necessário obter logs de Kubelet de um nó do AKS (Serviço de Contêiner do Azure) para fins de solução de problemas. Este documento detalha uma opção para efetuar pull desses logs.

## <a name="create-an-ssh-connection"></a>Criar uma conexão SSH

Primeiro, crie uma conexão SSH com o nó que é necessário efetuar pull dos logs de kubelet. Essa operação está detalhada no documento [SSH nos nós de cluster do AKS (Serviço de Contêiner do Azure)][aks-ssh].

## <a name="get-kubelet-logs"></a>Obter logs de kubelet

Após ter conectado o nó, execute o comando a seguir para efetuar pull dos logs de kubelet.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Saída de exemplo:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md
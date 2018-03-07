---
title: "SSH nos nós de cluster do AKS (Serviço de Contêiner do Azure)"
description: "Crie uma conexão SSH com um nó de cluster do AKS (Serviço de Contêiner do Azure)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH nos nós de cluster do AKS (Serviço de Contêiner do Azure)

Ocasionalmente, talvez seja necessário acessar um nó do AKS (Serviço de Contêiner do Azure) para manutenção, coleção de logs ou outras operações de solução de problemas. Os nós do AKS (Serviço de Contêiner do Azure) não estão expostos à internet. Use as etapas detalhadas neste documento para criar uma conexão SSH com um nó do AKS.

## <a name="configure-ssh-access"></a>Configurar o acesso SSH

 SSH em um nó específico, um pod é criado com acesso `hostNetwork`. Um serviço também é criado para acessar pod. Esta configuração é privilegiada e deverá ser removida após o uso.

Crie um arquivo nomeado `aks-ssh.yaml` e copie neste manifesto. Atualize o nome do nó com o nome do nó do AKS de destino.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Execute o manifesto para criar o pod e o serviço.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Obter o endereço IP externo do serviço exposto. Pode demorar um minuto para a configuração do endereço IP para concluir. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Crie a conexão SSH. 

O nome de usuário padrão para um cluster do AKS é `azureuser`. Se essa conta foi alterada no tempo de criação do cluster, substitua o nome de usuário do administrador apropriado. 

Se sua chave não estiver em `~/ssh/id_rsa`, forneça o local correto usando o argumento `ssh -i`.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Remover o acesso SSH

Ao concluir, exclua o pod e o serviço de acesso ao SSH.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```
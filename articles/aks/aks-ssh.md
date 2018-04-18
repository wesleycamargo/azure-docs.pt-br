---
title: SSH nos nós de cluster do AKS (Serviço de Contêiner do Azure)
description: Crie uma conexão SSH com um nó de cluster do AKS (Serviço de Contêiner do Azure)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH nos nós de cluster do AKS (Serviço de Contêiner do Azure)

Ocasionalmente, talvez seja necessário acessar um nó do AKS (Serviço de Contêiner do Azure) para manutenção, coleção de logs ou outras operações de solução de problemas. Os nós do AKS (Serviço de Contêiner do Azure) não estão expostos à internet. Use as etapas detalhadas neste documento para criar uma conexão SSH com um nó do AKS.

## <a name="get-aks-node-address"></a>Obter o endereço do nó AKS

Obtenha o endereço IP de um nó de cluster AKS usando o comando `az vm list-ip-addresses`. Substitua o nome do grupo de recursos pelo nome do seu grupo de recursos do AKS.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Criar conexão SSH

Execute o contêiner `debian` da imagem e anexe uma sessão de terminal a ele. O contêiner, em seguida, pode ser usado para criar uma sessão SSH com qualquer nó no cluster AKS.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Instale um cliente SSH no contêiner.

```console
apt-get update && apt-get install openssh-client -y
```

Abra um segundo terminal e liste todos os compartimentos para obter o nome do pod recém-criado.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Copie a chave SSH para o pod, substitua o nome do pod pelo valor adequado.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Atualize o arquivo `id_rsa` para que ele seja somente leitura para o usuário.

```console
chmod 0600 id_rsa
```

Agora, crie uma conexão SSH para o nó AKS. O nome de usuário padrão para um cluster do AKS é `azureuser`. Se essa conta foi alterada no tempo de criação do cluster, substitua o nome de usuário do administrador apropriado.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

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

Quando terminar, saia da sessão SSH e, em seguida, da sessão interativa do contêiner. Essa ação exclui o pod usado para acesso SSH do cluster AKS.
---
title: Criar um servidor do Ubuntu NFS (Network File System) para uso pelo pods do serviço de Kubernetes do Azure (AKS)
description: Saiba como criar manualmente um volume do NFS Ubuntu Linux Server para uso com pods no serviço de Kubernetes do Azure (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468493"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Manualmente, criar e usar um volume do NFS (Network File System) Linux Server com o serviço de Kubernetes do Azure (AKS)
Compartilhar dados entre contêineres geralmente é um componente necessário de aplicativos e serviços baseados em contêiner. Você normalmente tem vários pods que precisam acessar as mesmas informações em um volume persistente externo.    
Enquanto os arquivos do Azure são uma opção, criando um servidor NFS em uma VM do Azure é uma outra forma de armazenamento compartilhado persistente. 

Este artigo mostra como criar um servidor NFS em uma máquina virtual do Ubuntu. E também dar seu acesso aos contêineres do AKS com este sistema de arquivos compartilhado.

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tenha um Cluster AKS existente. Se você precisar um Cluster do AKS, consulte o guia de início rápido do AKS [usando a CLI do Azure] [ aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Cluster do AKS precisará live nas redes virtuais emparelhadas ou mesmas que o servidor NFS. O cluster deve ser criado em uma VNET existente, que pode ser a mesma rede virtual que sua VM.

As etapas para a configuração com uma rede virtual existente são descritas na documentação: [criar Cluster AKS na rede virtual existente] [ aks-virtual-network] e [conectando redes virtuais com emparelhamento de rede virtual][peer-virtual-networks]

Ele também pressupõe que você criou uma máquina de Virtual do Linux Ubuntu (por exemplo, 18.04 LTS). As configurações e o tamanho podem ser de sua preferência e podem ser implantados por meio do Azure. Para o início rápido do Linux, consulte [gerenciamento de VM do linux][linux-create].

Se você implantar primeiro o Cluster do AKS, Azure populará automaticamente o campo de rede virtual durante a implantação de seu computador Ubuntu, tornando-os em tempo real na mesma VNET. Mas se você deseja trabalhar com redes emparelhadas, consulte a documentação acima.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Implantar o servidor NFS em uma máquina Virtual
Aqui está o script para configurar um servidor NFS em sua máquina virtual do Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
O servidor será reiniciado (por causa de script) e você pode montar o servidor NFS no AKS

>[!IMPORTANT]  
>Certifique-se de substituir os **AKS_SUBNET** com aquele correto do seu cluster ou se encontram "*" abrirá seu servidor NFS para todas as portas e conexões.

Depois de criar sua VM, copie o script acima em um arquivo. Em seguida, você pode movê-lo em seu computador local, ou onde quer que o script é, na VM usando: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Depois que o script estiver em sua VM, você poderá ssh na VM e executá-lo por meio do comando:
```console
sudo ./nfs-server-setup.sh
```
Se sua execução falhar devido a um erro de permissão negada, defina a permissão de execução por meio do comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Cluster do AKS está se conectando ao servidor NFS
Podemos pode conectar o servidor NFS nosso cluster por meio do provisionamento de um volume persistente e a declaração de volume persistente que especifica como acessar o volume.  
É necessário conectar os dois serviços nas redes virtuais emparelhadas ou mesmos. Instruções sobre como configurar o cluster na mesma rede virtual está aqui: [criar Cluster AKS na rede virtual existente][aks-virtual-network]

Depois que eles estão na mesma rede virtual (ou emparelhada), você precisará provisionar que um volume persistente e um volume persistente de declaração no Cluster do AKS. Os contêineres, em seguida, podem montar a unidade NFS para seu diretório local.

Aqui está um exemplo de definição de kubernetes para o volume persistente (essa definição pressupõe o que seu cluster e a VM estiverem na mesma rede virtual):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Substitua **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** com informações do servidor NFS.

Você também precisará de um arquivo de declaração de volume persistente. Aqui está um exemplo do que incluem:

>[!IMPORTANT]  
>**"storageClassName"** deve permanecer vazio cadeia de caracteres ou a declaração não funcionará.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>solução de problemas
Se você não pode se conectar ao servidor de um cluster, um problema pode ser o diretório de exportação, ou seu pai, não tem permissões suficientes para acessar o servidor.

Verifique se o seu diretório de exportação e seu diretório pai tem 777 permissões.

Você pode verificar permissões executando o comando a seguir e os diretórios devem ter *'drwxrwxrwx'* permissões:
```console
ls -l
```

## <a name="more-information"></a>Mais informações
Para obter um passo a passo completa ou para ajudá-lo a depurar seu programa de instalação do servidor NFS, aqui está um tutorial detalhado:
  - [Tutorial do NFS][nfs-tutorial]

## <a name="next-steps"></a>Próximas etapas

Para práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md

---
title: "Compartilhamento de arquivos para o cluster do sistema operacional e do controlador de domínio do Azure | Microsoft Docs"
description: "Criar e montar um compartilhamento de arquivos em um cluster de controlador de domínio/sistema operacional no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Criar e montar um compartilhamento de arquivos em um cluster de controlador de domínio/sistema operacional
Neste artigo, exploraremos como criar um compartilhamento de arquivos no Azure e montá-lo em cada agente e mestre do cluster de controlador de domínio/sistema operacional. A configuração de um compartilhamento de arquivos facilita o compartilhamento de arquivos no cluster, como configuração, acesso, logs e muito mais.

Antes de trabalhar neste exemplo, você precisa de um cluster de controlador de domínio/sistema operacional configurado no Serviço de Contêiner do Azure. Consulte [Implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md).

## <a name="create-a-file-share-on-microsoft-azure"></a>Criar um compartilhamento de arquivos no Microsoft Azure
### <a name="using-the-portal"></a>Usando o portal

1. Faça logon no portal.
2. Criar uma conta de armazenamento.
   
  ![Serviço de Contêiner do Azure cria a Conta de Armazenamento](media/container-service-dcos-fileshare/createSA.png)

3. Após sua criação, clique em **Arquivos** na seção **Serviços**.
   
  ![Seção Arquivos do Serviço de Contêiner do Azure](media/container-service-dcos-fileshare/filesServices.png)

4. Clique em **+ Compartilhamento de arquivos** e insira um nome para esse novo compartilhamento (**Cota** não é obrigatório).
   
  ![Serviço de Contêiner do Azure + Compartilhamento de Arquivos](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Usando a CLI do Azure 2.0

Se precisar, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli.md).

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montar o compartilhamento no cluster

Em seguida, precisamos montar esse compartilhamento em todas as máquinas virtuais do cluster usando o protocolo e a ferramenta cifs. Fazemos isso com a seguinte linha de comando: `mount -t cifs`.

Este é um exemplo que usa:
* Nome da conta de armazenamento **`anystorageaccountname`**
* A chave da conta fictícia **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* O ponto de montagem **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

Executaremos esse comando em todas as máquinas virtuais de nosso cluster (nós mestre e do agente). Caso você tenha um grande número de agentes, recomendamos automatizar esse processo criando scripts.  

### <a name="set-up-scripts"></a>Configurar scripts

1. Primeiro, use o SSH no mestre (ou o primeiro mestre) do cluster baseado em controlador de domínio/sistema operacional. Por exemplo, `ssh userName@masterFQDN –A –p 22`, em que o masterFQDN é o nome de domínio totalmente qualificado da VM mestra.

2. Copie a chave privada para o diretório de trabalho (~) no mestre.

3. Altere as permissões nele com o seguinte comando: `chmod 600 yourPrivateKeyFile`.

4. Importe a chave privada usando o comando `ssh-add yourPrivateKeyFile`. Talvez você precise executar `eval ssh-agent -s` se ele não funcionar na primeira vez.

5. No mestre, crie dois arquivos, usando seu editor favorito, como VI, Nano ou VIM: 
  
  * Um com o script a ser executado em cada VM, chamado **cifsMount.sh** 
  * Outro para iniciar todas as conexões SSH que chamarão o primeiro script, chamado **mountShares.sh**


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> Você precisa alterar o comando **'mount'** com suas próprias configurações, como o nome da conta de armazenamento e a senha.
>  

A pasta em que você criou os scripts anteriores agora deve ter 3 arquivos:  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>Executar os scripts

Execute o arquivo **mountShares.sh** com o seguinte comando: `sh mountShares.sh`.

Você deverá ver o resultado sendo impresso no terminal. Após a conclusão dos scripts, é possível usar o compartilhamento de arquivos no cluster.

É possível otimizar os scripts, mas este exemplo é simples e seu objetivo é fornecer diretrizes.

> [!NOTE] 
> Esse método não é recomendado para cenários que exigem uma IOPS alta, mas é muito útil para compartilhar documentos e informações no cluster.
>

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [como gerenciar seus contêineres DC/OS](container-service-mesos-marathon-ui.md).
* Gerenciamento de contêiner de controlador de domínio/sistema operacional por meio da [API REST do Marathon](container-service-mesos-marathon-rest.md).

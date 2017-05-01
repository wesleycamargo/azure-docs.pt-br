---
title: Usando ACR com um cluster DC/OS do Azure | Microsoft Docs
description: "Usar um Registro de Contêiner do Azure com um cluster DC/OS no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Usar ACR com um cluster DC/OS para implantar seu aplicativo

Neste artigo, exploraremos como usar um registro de contêiner particular como ACR (Registro de Contêiner do Azure) com um cluster DC/OS. O uso do ACR permite armazenar imagens em particular e manter o controle sobre ele, como as versões e/ou as atualizações, por exemplo.

Antes de trabalhar por meio deste exemplo, será necessário: 
* Um cluster DC/OS configurado no Serviço de Contêiner do Azure. Consulte [Implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md).
* Um Serviço de Contêiner do Azure implantado. Consulte [Criar um Registro de contêiner do Docker Privado usando o portal do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) ou [Create a private Docker container registry using the Azure CLI 2.0 (Criar um Registro de contêiner do Docker Privado usando a CLI 2.0 do Azure)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
* Um compartilhamento de arquivos configurado no seu cluster DC/OS. Consulte [Criar e montar um compartilhamento de arquivos em um cluster DC/OS](container-service-dcos-fileshare.md)
* Para entender como implantar uma imagem do Docker em um cluster DC/OS usando a [Interface do usuário da Web](container-service-mesos-marathon-ui.md) ou a [API REST](container-service-mesos-marathon-rest.md)

## <a name="manage-the-authentication-inside-your-cluster"></a>Gerenciar a autenticação em seu cluster

A maneira convencional de enviar uma imagem por push e de efetuar pull dela de um Registro privado é ser, em primeiro lugar, autenticada nele. Para fazer isso, é necessário usar a linha de comando `docker login` em qualquer processo do cliente do docker que precisará usar seu Registro particular.
Quando se trata do mundo de produção, usando o DC/OS, no nosso caso, você deseja certificar-se de que você consegue efetuar pull de imagens de qualquer nó. Isso significa que você deseja automatizar o processo de autenticação e não executar a linha de comando em cada máquina, porque, como você pode imaginar, dependendo do tamanho do cluster, essa operação pode ser pesada e problemática. 

Supondo que você já [configurou um compartilhamento de arquivos dentro do DC/OS](container-service-dcos-fileshare.md), aproveitaremos isso fazendo:

### <a name="from-any-client-machine-recommended-method"></a>Em qualquer computador cliente [método recomendado]

Os comandos a seguir são executáveis em quaisquer ambientes (Windows/Mac/Linux):

1. Certifique-se de cumprir os seguintes pré-requisitos:
  * Ferramenta TAR
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [Outros](https://www.docker.com/get-docker)
  * Compartilhamento de arquivos montado dentro do seu cluster, [com o seguinte método](container-service-dcos-fileshare.md)

2. Inicie a autenticação em seu serviço ACR usando o comando a seguir com seu terminal favorito: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. É necessário substituir as variáveis `USERNAME`, `PASSWORD` e `ACR-REGISTRY-NAME` pelos valores fornecidos no seu portal do Azure

3. É interessante saber que, quando você estiver fazendo uma operação `docker login`, os valores serão armazenados localmente no computador em sua pasta base (`cd ~/.docker` no Mac e Linux ou `cd %HOMEPATH%` no Windows). Compactaremos o contêiner dessa pasta usando o comando `tar czf`.

4. A última etapa é copiar o arquivo tar que acabamos de criar, dentro do compartilhamento de arquivos [que você deveria ter criado como pré-requisito](container-service-dcos-fileshare.md). É possível fazer isso usando a CLI do Azure com o seguinte comando `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

Para resumir, abaixo há um exemplo que usa a configuração a seguir (usando um ambiente do Windows):
* Nome de ACR: **`demodcos`**
* Nome de usuário: **`demodcos`**
* Senha: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Nome da conta de armazenamento: **`anystorageaccountname`**
* Chave de conta de armazenamento: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* Nome do compartilhamento criado dentro da conta de armazenamento: **`share`**
* Caminho do arquivo tar a ser carregado: **`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>No mestre [método não recomendado]

Executar a operação no mestre não é recomendado para evitar erros e o impacto em todos os ambientes.

1. Primeiro, use o SSH no mestre (ou o primeiro mestre) do cluster baseado em controlador de domínio/sistema operacional. Por exemplo, `ssh userName@masterFQDN –A –p 22`, em que o masterFQDN é o nome de domínio totalmente qualificado da VM mestra. [Mais informações clicando aqui](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. Inicie a autenticação em seu serviço ACR usando o comando a seguir: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. É necessário substituir as variáveis `USERNAME`, `PASSWORD` e `ACR-REGISTRY-NAME` pelos valores fornecidos no seu portal do Azure

3. É interessante saber que, quando você estiver fazendo uma operação `docker login`, os valores serão armazenados localmente no computador em sua pasta base `~/.docker`. Compactaremos o contêiner dessa pasta usando o comando `tar czf`.

4. A última etapa é copiar o arquivo tar que acabamos de criar, dentro do compartilhamento de arquivos. Esta operação permitirá que você use, em todas as máquinas virtuais dentro do nosso cluster, essa credencial e seja autenticado em nosso Registro de Contêiner do Azure.

Para resumir, abaixo há um exemplo que usa a configuração a seguir:
* Nome de ACR: **`demodcos`**
* Nome de usuário: **`demodcos`**
* Senha: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Ponto de montagem dentro do cluster: **`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Implantar uma imagem de ACR com Marathon

Você supostamente já enviou por push as imagens que você deseja implantar dentro de seu Registro de contêiner. Consulte [Push your first image to a private Docker container registry using the Docker CLI (Enviar por push sua primeira imagem para um Registro de contêiner do Docker Privado usando a CLI do Docker)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

Vamos supor que nós desejamos implantar a imagem **simple-web**, com a marcação **2.1**, em nosso Registro privado hospedado no Azure (ACR), nós usaremos a seguinte configuração:

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> Como você pode ver, estamos usando a opção **uris** para especificar onde estão armazenadas as nossas credenciais.
>

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [como gerenciar seus contêineres DC/OS](container-service-mesos-marathon-ui.md).
* Gerenciamento de contêiner de controlador de domínio/sistema operacional por meio da [API REST do Marathon](container-service-mesos-marathon-rest.md).

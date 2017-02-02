---
title: "Conectar a um cluster do Serviço de Contêiner do Azure | Microsoft Docs"
description: "Conectar-se a um cluster Kubernetes, DC/SO ou Docker Swarm no Serviço de Contêiner do Azure de um computador remoto"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: ea59ff3f527d051e01baf12f596ff44af8a0dfc1
ms.openlocfilehash: 7fe3bc6a5eab1d1b9a8b73ab3c88f9808817369a


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Conectar a um cluster do Serviço de Contêiner do Azure
Depois de criar um cluster do serviço de contêiner do Azure, você precisa se conectar ao cluster para implantar e gerenciar cargas de trabalho. Este artigo descreve como conectar-se à VM mestre do cluster de um computador remoto. Os clusters Kubernetes DC/SO e Docker Swarm expõem pontos de extremidade REST. Para Kubernetes, esse ponto de extremidade é exposto de maneira segura na Internet, e você pode acessá-lo executando a ferramenta de linha de comando `kubectl` de qualquer computador conectado à Internet. Para DC/SO e Docker Swarm, você deve criar um túnel SSH (secure shell) para se conectar com segurança ao ponto de extremidade REST. 

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.
>

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Kubernetes, CD/SO ou Swarm [implantado no Serviço de Contêiner do Azure](container-service-deployment.md).
* Arquivo de chave privada SSH, correspondente à chave pública adicionada ao cluster durante a implantação. Esses comandos pressupõem que a chave privada do SSH esteja em `$HOME/.ssh/id_rsa` em seu computador. Confira estas instruções para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) para obter mais informações. Se a conexão SSH não está funcionando, você talvez precise [redefinir suas chaves SSH](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Conectar-se a um cluster Kubernetes

Siga estas etapas para instalar e configurar `kubectl` no computador.

> [!NOTE] 
> No Linux ou OS X, talvez seja necessário executar os comandos essa seção usando `sudo`.
> 

### <a name="install-kubectl"></a>Instalar kubectl
Uma maneira de instalar essa ferramenta é usar o comando `az acs kubernetes install cli` da CLI do Azure 2.0 (Visualização). Para executar esse comando, verifique se você [instalou](/cli/azure/install-az-cli2) a CLI do Azure 2.0 (Visualização) mais recente e conectou-se a uma conta do Azure (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Como alternativa, você pode baixar o cliente diretamente na [página de versões](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146).

### <a name="download-cluster-credentials"></a>Baixar credenciais de cluster
Uma vez que `kubectl` esteja instalado, você precisa copiar as credenciais de cluster em seu computador. Uma maneira de obter as credenciais é com o comando `az acs kubernetes get-credentials`. Passe o nome do grupo de recursos e o nome do recurso de serviço de contêiner:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Esse comando baixa as credenciais do cluster para `$HOME/.kube/config`, em que `kubectl` espera que estejam.

Como alternativa, você pode usar `scp` para copiar o arquivo de forma segura de `$HOME/.kube/config` para a VM mestre em seu computador local. Por exemplo:

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Se estiver no Windows, você precisará usar o Bash no Ubuntu no Windows, o cliente de cópia de arquivo segura PuTTy ou uma ferramenta semelhante.



### <a name="use-kubectl"></a>Usar kubectl

Uma vez que `kubectl` estiver configurado, você poderá testar essa conexão listando os nós no cluster:

```console
kubectl get nodes
```

Você pode tentar outros comandos `kubectl`. Por exemplo, você pode exibir o Painel de Kubernetes. Primeiro, execute um proxy para o servidor de API Kubernetes:

```console
kubectl proxy
```

A interface do usuário do Kubernetes agora está disponível em: `http://localhost:8001/ui`.

Para obter mais informações, confira [Início rápido do Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Conectar-se a um cluster de DC/SO ou Swarm

Os clusters DC/OS e Docker Swarm implantados pelo Serviço de Contêiner do Azure expõem os pontos de extremidade REST. No entanto, esses pontos de extremidade não estão abertos para o mundo exterior. Para gerenciar esses pontos de extremidade, você deve criar um túnel Secure Shell (SSH). Após o estabelecimento de um túnel SSH, você pode executar comandos em relação aos pontos de extremidade do cluster e exibir a interface do usuário do cluster por meio de um navegador em seu próprio sistema. As seções a seguir o orientarão na criação de um túnel SSH de computadores que executam sistemas operacionais Windows, OS X e Linux.

> [!NOTE]
> Você pode criar uma sessão SSH com um sistema de gerenciamento de cluster. No entanto, isso não é recomendado. Trabalhar diretamente em um sistema de gerenciamento acarreta o risco de alterações de configuração acidentais.
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Criar um túnel SSH no Linux ou no OS X
A primeira coisa que você faz quando cria um túnel SSH no Linux ou no OS X é localizar o nome DNS público de mestres de balanceamento de carga. Siga estas etapas:


1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos que contém o cluster de serviço do contêiner. Expanda o grupo de recursos para que todos os recursos sejam exibidos. 

2. Localize e selecione a máquina virtual do mestre. Em um cluster de SO/DC, esse recurso tem um nome que começa com **dcos-master-**. 

    A folha **Máquina Virtual** contém informações sobre o endereço IP público, que inclui o nome DNS. Salve o nome para uso posterior. 

    ![Nome DNS público](media/pubdns.png)

3. Agora abra um shell e execute o comando `ssh` especificando os seguintes valores: 

    **PORTA** é a porta do ponto de extremidade que você deseja expor. Para a nuvem, use a porta 2375. Para o DC/OS, use a porta 80.  
    **NOME DE USUÁRIO** é o nome de usuário fornecido quando você implantou o cluster.  
    **PREFIXODEDNS** é o prefixo de DNS que você forneceu ao implantar o cluster.  
    **REGIÃO** é a região em que o grupo de recursos está localizado.  
    **CAMINHO_PARA_CHAVE_PRIVADA** [OPCIONAL] é o caminho para a chave privada correspondente à chave pública que você forneceu ao criar o cluster. Use essa opção com o sinalizador `-i`.

    ```bash
    ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
    ```
    > [!NOTE]
    > A porta de conexão SSH é 2200, não a porta padrão 22. Em um cluster com mais de uma VM mestre, essa é a porta de conexão para a primeira VM mestre.
    > 

Confira os exemplos de DC/SO e nuvem nas seções a seguir.    

### <a name="dcos-tunnel"></a>Túnel DC/OS
Para abrir um túnel para pontos de extremidade relacionados a DC/OS, execute um comando semelhante ao seguinte:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode acessar os pontos de extremidade relacionados ao DC/OS:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Da mesma forma, as APIs rest para cada aplicativo podem ser acessadas por este túnel.

### <a name="swarm-tunnel"></a>Túnel do Swarm
Para abrir um túnel para o ponto de extremidade de nuvem, execute um comando semelhante ao seguinte:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode definir a variável de ambiente DOCKER_HOST da maneira a seguir. Você pode continuar a usar a interface de linha de comando (CLI) do Docker da maneira normal.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Criar um túnel SSH no Windows
Há várias opções para a criação de túneis SSH no Windows. Esta seção descreve como usar PuTTY para criar o túnel.

1. [Baixe o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para o sistema Windows.

2. Execute o aplicativo.

3. Insira um nome de host que consista no nome de usuário do administrador do cluster e no nome DNS público do primeiro mestre no cluster. O **Nome do Host** é semelhante a `adminuser@PublicDNSName`. Insira 2200 em **Porta**.

    ![Configuração do PuTTY 1](media/putty1.png)

4. Selecione **SSH > Auth**. Adicione um caminho para o arquivo de chave privada (formato .ppk) para autenticação. Você pode usar uma ferramenta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para gerar esse arquivo da chave SSH usada para criar o cluster.

    ![Configuração do PuTTY 2](media/putty2.png)

5. Selecione **SSH > Túnel** e configure as seguintes portas encaminhadas:

    * **Porta de Origem:** use 80 para DC/SO ou 2375 para nuvem.
    * **Destino:** use localhost:80 para o DC/OS ou localhost:2375 para o Swarm.

    O exemplo a seguir é configurado para o DC/OS, mas seria semelhante para o Docker Swarm.

    > [!NOTE]
    > A porta 80 não deve estar em uso quando você criar esse túnel.
    > 

    ![Configuração do PuTTY 3](media/putty3.png)

6. Quando terminar, clique em **Sessão > Salvar** para salvar a configuração de conexão.

7. Para se conectar à sessão do PuTTY, clique em **Abrir**. Quando você se conectar, poderá ver a configuração da porta no log de eventos do PuTTY.

    ![Log de eventos do PuTTY](media/putty4.png)

Após configurar o túnel para o DC/OS, você poderá acessar o ponto de extremidade relacionado em:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Depois de configurar o túnel de Docker Swarm, abra as configurações do Windows para configurar uma variável de ambiente do sistema denominada `DOCKER_HOST` com um valor de `:2375`. Em seguida, você pode acessar o cluster do Swarm por meio da CLI do Docker.

## <a name="next-steps"></a>Próximas etapas
Implantar e gerenciar contêineres no cluster:

* [Trabalhar com o Serviço de Contêiner do Azure e o Kubernetes](container-service-kubernetes-ui.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o DC/SO](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Jan17_HO3-->



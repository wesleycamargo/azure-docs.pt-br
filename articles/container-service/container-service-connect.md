---
title: "Conectar a um cluster do Serviço de Contêiner do Azure | Microsoft Docs"
description: "Conecte-se a um cluster do Serviço de Contêiner do Azure usando um Túnel SSH."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 5296586b9266f432042f847f4dff9e6ff62ebc8b


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Conectar a um cluster do Serviço de Contêiner do Azure
Todos os clusters DC/OS, Kubernetes e Docker Swarm implantados pelo Serviço de Contêiner do Azure expõem os pontos de extremidade REST.  Para Kubernetes, esse ponto de extremidade é exposto de maneira segura na internet e você pode acessá-lo diretamente de qualquer computador conectado à Internet. Para DC/OS e Docker Swarm, você deve criar um túnel SSH para se conectar com segurança para o ponto de extremidade REST. Cada uma dessas conexões é descrita abaixo.

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.
>

## <a name="connecting-to-a-kubernetes-cluster"></a>Conectando a um cluster Kubernetes.
Para se conectar a um cluster Kubernetes, você precisa ter a ferramenta de linha de comando `kubectl` instalada.  A maneira mais fácil de instalar essa ferramenta é usar a ferramenta de linha de comando `az` do Azure 2.0.

```console
az acs kubernetes install cli [--install-location=/some/directory]
```

Como alternativa, você pode baixar o cliente diretamente na [página de versões](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146)

Uma vez que `kubectl` esteja instalado, você precisa copiar as credenciais de cluster em seu computador.  O modo mais fácil de fazer isso novamente é a ferramenta de linha de comando `az`:

```console
az acs kubernetes get-credentials --dns-prefix=<some-prefix> --location=<some-location>
```

Isso baixará as credenciais do cluster em `$HOME/.kube/config`, em que `kubectl` espera que elas estejam localizadas.

Como alternativa, você pode usar `scp` para copiar o arquivo de forma segura de `$HOME/.kube/config` para a VM mestre em seu computador local.

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Se você estiver usando o Windows, você precisará usar Bash no Ubuntu no Windows ou a ferramenta Putty 'pscp'.

Uma vez que `kubectl` estiver configurado, você poderá testar isso listando os nós no cluster:

```console
kubectl get nodes
```

Finalmente, você pode exibir o Painel do Kubernetes. Primeiro, execute:

```console
kubectl proxy
```

A interface do usuário do Kubernetes agora está disponível em: http://localhost:8001/ui

Para obter mais instruções, você poderá ver o [início rápido do Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/)

## <a name="connecting-to-a-dcos-or-swarm-cluster"></a>Conectar-se a um cluster DC/SO ou do Swarm

Os clusters DC/OS e Docker Swarm implantados pelo Serviço de Contêiner do Azure expõem os pontos de extremidade REST. No entanto, esses pontos de extremidade não estão abertos para o mundo exterior. Para gerenciar esses pontos de extremidade, você deve criar um túnel Secure Shell (SSH). Após o estabelecimento de um túnel SSH, você pode executar comandos em relação aos pontos de extremidade do cluster e exibir a interface do usuário do cluster por meio de um navegador em seu próprio sistema. Este documento orienta você ao longo da criação de um túnel SSH do Linux, do OSX e do Windows.

> [!NOTE]
> Você pode criar uma sessão SSH com um sistema de gerenciamento de cluster. No entanto, isso não é recomendado. Trabalhar diretamente em um sistema de gerenciamento acarreta o risco de alterações de configuração acidentais.   
> 
> 

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Criar um túnel SSH no Linux ou no OS X
A primeira coisa que você faz quando cria um túnel SSH no Linux ou no OS X é localizar o nome DNS público de mestres de balanceamento de carga. Para fazer isso, expanda o grupo de recursos, de modo que todos os recursos sejam exibidos. Localize e selecione o endereço IP público do mestre. Isso abrirá uma folha com informações sobre o endereço IP público, o que incluirá o nome DNS. Salve o nome para uso posterior. <br />

![Nome DNS público](media/pubdns.png)

Agora, abra um shell e execute o seguinte comando, em que:

**PORTA** é a porta do ponto de extremidade que você deseja expor. Para o Swarm, é 2375. Para o DC/OS, use a porta 80.  
**NOME DE USUÁRIO** é o nome de usuário fornecido quando você implantou o cluster.  
**PREFIXODEDNS** é o prefixo de DNS que você forneceu ao implantar o cluster.  
**REGIÃO** é a região em que o grupo de recursos está localizado.  
**CAMINHO_PARA_CHAVE_PRIVADA** [OPCIONAL] é o caminho para a chave privada correspondente à chave pública que você forneceu ao criar o cluster do Serviço de Contêiner. Use essa opção com o sinalizador -i.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> A porta de conexão SSH é 2200, não a porta padrão 22.
> 
> 

## <a name="dcos-tunnel"></a>Túnel DC/OS
Para abrir um túnel para pontos de extremidade relacionados a DC/OS, execute um comando semelhante ao seguinte.

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode acessar os pontos de extremidade relacionados ao DC/OS:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Da mesma forma, as APIs rest para cada aplicativo podem ser acessadas por este túnel.

## <a name="swarm-tunnel"></a>Túnel do Swarm
Para abrir um túnel para o ponto de extremidade do Swarm, execute um comando semelhante ao seguinte:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode definir a variável de ambiente DOCKER_HOST da maneira a seguir. Você pode continuar a usar a interface de linha de comando (CLI) do Docker da maneira normal.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Criar um túnel SSH no Windows
Há várias opções para a criação de túneis SSH no Windows. Este documento descreverá como usar o PuTTY para fazer isso.

Baixe o PuTTY para seu sistema Windows e execute o aplicativo.

Insira um nome de host que consista no nome de usuário do administrador do cluster e no nome DNS público do primeiro mestre no cluster. O **Nome de Host** será semelhante a: `adminuser@PublicDNS`. Insira 2200 em **Porta**.

![Configuração do PuTTY 1](media/putty1.png)

Selecione **SSH** e **Autenticação**. Adicione o arquivo de chave privada para autenticação.

![Configuração do PuTTY 2](media/putty2.png)

Selecione **Túneis** e configure as seguintes portas encaminhadas:

* **Porta de Origem:** sua preferência -- use 80 para DC/OS e 2375 para o Swarm.
* **Destino:** use localhost:80 para o DC/OS ou localhost:2375 para o Swarm.

O exemplo a seguir é configurado para o DC/OS, mas seria semelhante para o Docker Swarm.

> [!NOTE]
> A porta 80 não deve estar em uso quando você criar esse túnel.
> 
> 

![Configuração do PuTTY 3](media/putty3.png)

Quando tiver terminado, salve a configuração de conexão e conecte a sessão do PuTTY. Quando você se conectar, poderá ver a configuração da porta no log de eventos do PuTTY.

![Log de eventos do PuTTY](media/putty4.png)

Quando você tiver configurado o túnel para o DC/OS, poderá acessar o ponto de extremidade relacionado em:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Quando você tiver configurado o túnel para o Docker Swarm, poderá acessar o cluster do Swarm por meio da CLI do Docker. Primeiro, você precisará configurar uma variável de ambiente do Windows chamada `DOCKER_HOST` com o valor ` :2375`.

## <a name="next-steps"></a>Próximas etapas
Implantar e gerenciar contêineres com DC/SO ou Swarm:

* [Trabalhar com o Serviço de Contêiner do Azure e o DC/SO](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Serviço de Contêiner do Azure e o Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Dec16_HO3-->



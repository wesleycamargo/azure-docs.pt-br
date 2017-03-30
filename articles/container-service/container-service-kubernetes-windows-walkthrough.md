---
title: Cluster Kubernetes do Azure para Windows | Microsoft Docs
description: "Implantar e começar a usar um cluster Kubernetes para contêineres do Windows no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c43648dae95d90d0ee9f3d6b5bedfad7ab4889ca
ms.lasthandoff: 03/22/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introdução ao Kubernetes e aos contêineres do Windows em Serviço de Contêiner


Este artigo mostra como criar um cluster Kubernetes no Serviço de Contêiner do Azure que contenha nós do Windows para executar contêineres do Windows. 

> [!NOTE]
> O suporte para contêineres do Windows com Kubernetes no Serviço de Contêiner do Azure está em versão prévia. Use o Portal do Azure ou um modelo do Resource Manager para criar um cluster Kubernetes com nós do Windows. Atualmente não há suporte para esse recurso na CLI do Azure 2.0.
>



A imagem a seguir mostra a arquitetura de um cluster Kubernetes no Serviço de Contêiner do Azure com um nó mestre do Linux e dois nós de agente do Windows. 

* O mestre do Linux serve à API REST do Kubernetes e está acessível por meio de SSH na porta 22 ou por `kubectl` na porta 443. 
* Os nós de agente do Windows estão agrupados em um conjunto de disponibilidade do Azure e executam os seus contêineres. Os nós do Windows podem ser acessados por meio de um túnel RDP SSH através do nó mestre. As regras do Azure Load Balancer são adicionadas dinamicamente ao cluster dependendo dos serviços expostos.


![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Todas as VMs estão na mesma rede virtual privada e são totalmente acessíveis entre si. Todas as VMs executam um kubelet, um Docker e um proxy.

## <a name="prerequisites"></a>Pré-requisitos


* **Chave pública SSH RSA**: durante a implantação por meio do portal ou de um dos modelos de início rápido do Azure, você precisa fornecer uma chave pública SSH RSA para autenticação nas máquinas virtuais do Serviço de Contêiner do Azure. Para criar chaves SSH (Secure Shell) RSA, consulte as diretrizes para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID e segredo do cliente da entidade de serviço**: para obter mais informações e diretrizes, consulte [Sobre a entidade de serviço para um cluster Kubernetes](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Criar o cluster

Você pode usar o Portal do Azure para [criar um cluster Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) com nós de agente do Windows. Ao criar o cluster, observe as seguintes configurações:

* Na folha **Noções Básicas**, no **Orchestrator**, selecione **Kubernetes**. 

  ![Selecione o orquestrador do Kubernetes](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* Na folha **Configuração do mestre**, insira as credenciais de usuário e as credenciais de entidade de serviço dos nós mestres do Linux. Escolha 1, 3 ou 5 mestres.

* Na folha **Configuração do agente**, em **Sistema operacional**, selecione **Windows (versão prévia)**. Insira as credenciais de administrador para os nós de agente do Windows.

  ![Selecione agentes do Windows](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

Para obter detalhes, consulte [Implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Use a ferramenta de linha de comando `kubectl` para conectar-se do seu computador local ao nó mestre do cluster Kubernetes. Para saber as etapas para instalar e configurar o `kubectl`, consulte [Conectar-se a um cluster do Serviço de Contêiner do Azure](container-service-connect.md#connect-to-a-kubernetes-cluster). Você pode usar comandos `kubectl` para acessar a interface do usuário da Web do Kubernetes e para criar e gerenciar cargas de trabalho do contêiner do Windows.

## <a name="create-your-first-kubernetes-service"></a>Criar seu primeiro serviço Kubernetes

Depois de criar o cluster e conectar-se com `kubectl`, você pode tentar iniciar um aplicativo Web básico do Windows e expô-lo à Internet. Neste exemplo, você especifica os recursos de contêiner usando um arquivo YAML e, em seguida, cria o contêiner usando `kubctl apply`.

1. Para ver uma lista dos nós, digite `kubectl get nodes`. Se desejar os detalhes completos dos nós, digite:  

    ```
    kubectl get nodes -o yaml
    ```

2. Crie um arquivo chamado `simpleweb.yaml` e copie o seguinte. Esse arquivo define um aplicativo Web usando a imagem básica do Sistema Operacional do Server Core do Windows Server 2016 do [Hub do Docker](https://hub.docker.com/r/microsoft/windowsservercore/).  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> A configuração inclui `type: LoadBalancer`. Essa configuração faz com que o serviço seja exposto à Internet por meio de um Azure Load Balancer. Para obter mais informações, consulte [Balancear a carga de contêineres em um cluster Kubernetes no Serviço de Contêiner do Azure](container-service-kubernetes-load-balancing.md).
>

## <a name="start-the-application"></a>Iniciar o aplicativo

1. Para iniciar o aplicativo, digite:  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. Para verificar a implantação do serviço (que leva aproximadamente 30 segundos), digite:  

    ```
    kubectl get pods
    ```

3. Depois que o serviço estiver em execução, para ver os endereços IP internos e externos do serviço, digite:

    ```
    kubectl get svc
    ``` 
  
    ![Endereços IP do serviço Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    A adição dos endereços IP externos leva alguns minutos. Antes que o balanceador de carga configure o endereço externo, ele aparece como `<pending>`.

4. Depois que o endereço IP externo estiver disponível, você pode acessar o serviço em seu navegador da Web.

    ![Aplicativo do Windows Server no navegador](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Acessar os nós do Windows
Os nós do Windows podem ser acessados de um computador local do Windows por meio da Conexão de Área de Trabalho Remota. É recomendável usar um túnel RDP SSH através do nó mestre. 

Há várias opções para a criação de túneis SSH no Windows. Esta seção descreve como usar PuTTY para criar o túnel.

1. [Baixe o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para o sistema Windows.

2. Execute o aplicativo.

3. Insira um nome de host que seja composto pelo nome de usuário do administrador do cluster e pelo nome DNS público do primeiro mestre no cluster. O **Nome do Host** é semelhante a `adminuser@PublicDNSName`. Insira 22 em **Porta**.

  ![Configuração do PuTTY 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Selecione **SSH > Auth**. Adicione um caminho para o arquivo de chave privada (formato .ppk) para autenticação. Você pode usar uma ferramenta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para gerar esse arquivo da chave SSH usada para criar o cluster.

  ![Configuração do PuTTY 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Selecione **SSH > Túneis** e configure as portas encaminhadas. Uma vez que seu computador Windows local já está usando a porta 3389, é recomendável usar as seguintes configurações para alcançar o nó 0 e o nó 1 do Windows. (Continue esse padrão para nós adicionais do Windows).

    **Nó 0 do Windows**

    * **Porta de origem:** 3390
    * **Destino:** 10.240.245.5:3389

    **Nó 1 do Windows**

    * **Porta de origem:** 3391
    * **Destino:** 10.240.245.6:3389

    ![Imagem de túneis do RDP do Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Quando terminar, clique em **Sessão > Salvar** para salvar a configuração de conexão.

7. Para se conectar à sessão do PuTTY, clique em **Abrir**. Conclua a conexão com o nó mestre.

8. Iniciar a Conexão de Área de Trabalho Remota. Para conectar-se ao primeiro nó do Windows, em **Computador**, especifique `localhost:3390` e clique em **Conectar**. (Para conectar-se ao segundo, especifique `localhost:3390`, e assim por diante.) Para concluir a conexão, forneça a senha de administrador local do Windows que você configurou durante a implantação.


## <a name="next-steps"></a>Próximas etapas

Aqui estão links recomendados para saber mais sobre Kubernetes:

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) – Mostra como implantar, dimensionar, atualizar e depurar aplicativos em contêineres.
* [Guia do Usuário do Kubernetes](http://kubernetes.io/docs/user-guide/) – fornece informações sobre como executar programas em um cluster Kubernetes existente.
* [Exemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – fornece exemplos de como executar aplicativos reais com Kubernetes.

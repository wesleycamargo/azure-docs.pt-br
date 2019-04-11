---
title: Acessar o painel do Kubernetes no Azure Stack | Microsoft Docs
description: Saiba como acessar o painel do Kubernetes no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: fa663cce10a39ef60a0efa5838b81b257fd02b46
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255930"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Acessar o painel do Kubernetes no Azure Stack 

*Aplicável a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack* 
> [!Note]   
> Kubernetes no Azure Stack está em visualização. Cenário desconectado da pilha do Azure não é suportado pelo preview. 

Kubernetes inclui um painel da web que você pode usar para operações básicas de gerenciamento. Este painel permite exibir o status de integridade básico e as métricas dos seus aplicativos, criar e implantar serviços e editar aplicativos existentes. Este artigo mostra como configurar o painel do Kubernetes no Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Pré-requisitos para o painel do Kubernetes

* Cluster de Kubernetes de pilha do Azure

    Você precisará implantar um cluster Kubernetes no Azure Stack. Para obter mais informações, consulte [implantar Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Cliente SSH

    Você precisará de um cliente SSH à segurança se conectar ao seu nó mestre no cluster. Se você estiver usando o Windows, você pode usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Será necessário a chave privada usada quando você implantou o cluster Kubernetes.

* FTP (PSCP)

    Talvez também seja necessário um cliente FTP que dá suporte a SSH e SSH File Transfer Protocol para transferir os certificados no nó mestre para seu computador de gerenciamento do Azure Stack. Você pode usar [FileZilla](https://filezilla-project.org/download.php?type=client). Será necessário a chave privada usada quando você implantou o cluster Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Visão geral das etapas para habilitar o painel de controle

1.  Exporte os certificados de Kubernetes do nó mestre no cluster. 
2.  Importe os certificados no computador de gerenciamento do Azure Stack.
2.  Abra o painel da web do Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportar o certificado do servidor mestre 

Você pode recuperar a URL para o painel de nó mestre no cluster.

1. Obtenha o endereço IP público e o nome de usuário para o mestre do cluster no painel do Azure Stack. Para obter essas informações:

    - Entrar para o [portal do Azure Stack](https://portal.local.azurestack.external/)
    - Selecione **todos os serviços** > **todos os recursos**. Encontre o mestre no seu grupo de recursos de cluster. O mestre é chamado `k8s-master-<sequence-of-numbers>`. 

2. Abra o nó mestre no portal. Cópia de **IP público** endereço. Clique em **Connect** para obter seu nome de usuário na **logon usando a conta local da VM** caixa. Isso é o mesmo nome de usuário definida ao criar o cluster. Use o endereço IP público em vez do endereço IP listado na folha do connect.

3.  Abra um cliente SSH para se conectar ao mestre. Se você estiver trabalhando no Windows, você pode usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) para criar a conexão. Você usa o endereço IP público para o nó mestre, o nome de usuário e adicionar a chave privada que você usou ao criar o cluster.

4.  Quando se conecta o terminal, digite `kubectl` para abrir o cliente de linha de comando do Kubernetes.

5. Execute o comando a seguir:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Encontre a URL para o painel. Por exemplo:   `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extraia o certificado autoassinado e convertê-lo para o formato PFX. Execute o comando a seguir:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Obter a lista de segredos na **kube-system** namespace. Execute o comando a seguir:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Anote o kubernetes-dashboard-token -\<XXXXX > valor. 

8.  Obter o token e salve-o. Atualização de `kubernetes-dashboard-token-<####>` com o valor do segredo da etapa anterior.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importar o certificado

1. Abra o Filezilla e conectar-se ao nó mestre. Você precisará de:

    - o IP público do nó mestre
    - o nome de usuário
    - o segredo privado
    - Use **SFTP - SSH protocolo de transferência de arquivos**

2. Cópia `/etc/kubernetes/certs/client.pfx` e `/etc/kubernetes/certs/ca.crt` à sua máquina de gerenciamento do Azure Stack.

3. Anote os locais de arquivo. Atualize o script com os locais e, em seguida, abra o PowerShell com um prompt com privilégios elevados. Execute o script atualizado:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Abra o painel do Kubernetes 

1. Desabilite o Bloqueador de pop-up no navegador da Web.

2. Ponto de seu navegador para a URL observado quando você executou o comando `kubectl cluster-info`. Por exemplo: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / proxy 
3. Selecione o certificado do cliente.
4. Insira o token. 
5. Reconecte-se à linha de comando do bash no nó mestre e conceder permissões para `kubernetes-dashboard`. Execute o comando a seguir:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    O script fornece `kubernetes-dashboard` privilégios de administrador de nuvem. Para obter mais informações, consulte [habilitados para RBAC clusters](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Você pode usar o painel. Para obter mais informações sobre o painel do Kubernetes, consulte [painel de interface do usuário Web Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Painel do Kubernetes do Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Próximas etapas 

[Implantar Kubernetes no Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Adicionar um cluster Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  

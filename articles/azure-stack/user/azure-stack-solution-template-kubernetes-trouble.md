---
title: Solucionar problemas de implantação para Kubernetes (K8) para o Azure Stack | Microsoft Docs
description: Saiba como solucionar problemas de implantação para Kubernetes (K8) para o Azure Stack.
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
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 7071e22d703ab7ec3a51eff02d1694fc04cb3417
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231229"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Solucionar problemas de implantação no Kubernetes no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização.

O artigo a seguir examina o cluster Kubernetes de solução de problemas. Você pode revisar o alerta de implantação e revisar o status da sua implantação examinando os elementos necessários para a implantação. Talvez você precise coletar os logs de implantação do Azure Stack ou VMs do Linux que hospedam Kubernetes. Além disso, você talvez precise trabalhar com o administrador do Azure Stack para recuperar os logs de um ponto de extremidade administrativo.

## <a name="overview-of-deployment"></a>Visão geral da implantação

Antes de conhecer as etapas para solucionar problemas de cluster, você talvez queira examinar o processo de implantação de cluster do Kubernetes do Azure Stack. A implantação usa um modelo de solução do Azure Resource Manager para criar as máquinas virtuais e instala o mecanismo de ACS para seu cluster.

### <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O diagrama a seguir mostra o processo geral para implantar o cluster.

![Implantar o processo de Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Etapas de implantação.

1. Coleta parâmetros de entrada do item do marketplace.

    Insira os valores que você precisa configurar o cluster Kubernetes, incluindo:
    -  **Nome de usuário** nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster Kubernetes e DVM.
    -  **Chave pública SSH** a chave usada para autorização em todas as máquinas Linux criado como parte do cluster Kubernetes e DVM
    -  **Princípio de serviço** a ID usada pelo provedor de nuvem do Azure do Kubernetes. A ID do cliente foi identificada como a ID do aplicativo quando você cria seu serviço principal. 
    -  **Segredo do cliente** eles chave que você criou ao criar a entidade de serviço.

2. Cria a implantação de VM e a extensão de script personalizado.
    -  Cria a implantação de VM do Linux usando a imagem do Linux do marketplace **Ubuntu Server 16.04-LTS**.
    -  Baixe e execute a extensão de script de cliente do marketplace. O script é o **Script personalizado para Linux 2.0**.
    -  Executa o script personalizado do DVM. O script:
        1. Obtém o ponto de extremidade da Galeria do ponto de extremidade de metadados do Azure Resource Manager.
        2. Obtém a ID de recurso do Active Directory do ponto de extremidade de metadados do Azure Resource Manager.
        3. Carrega o modelo de API para o mecanismo do ACS.
        4. Implanta o mecanismo do ACS para o cluster Kubernetes e salva o perfil de nuvem do Azure Stack para `/etc/kubernetes/azurestackcloud.json`.
3. Cria VMs mestres.

    Baixa e executa a extensão de script de cliente.

4. Executa o script mestre.

    O script:
    - Instala etcd, Docker e Kubernetes recursos como kubelet. etcd é um repositório de valor de chave distribuído que fornece uma maneira de armazenar dados em um cluster de computadores. Docker dá suporte à virtualização de nível de sistema operacional do básica conhecidas como contêineres. Kubelet é o agente do nó que é executado em cada nó de Kubernetes.
    - Configura o serviço etcd.
    - Configura o serviço de Kubelet.
    - Inicia o kubelet. Isso envolve o seguinte:
        1. Inicia o serviço de API.
        2. Inicia o serviço de controlador.
        3. Inicia o serviço de Agendador.
5. Cria o agente de VMs.

    Baixa e executa a extensão de script de cliente.

6. Executa o script de agente. O script personalizado do agente:
    - Instale etcd.
    - Configure o serviço de Kubelet.
    - Une o cluster Kubernetes.

## <a name="steps-for-troubleshooting"></a>Etapas para solução de problemas

Você pode coletar logs nas máquinas virtuais que dão suporte a cluster Kubernetes. Você também pode examinar o log de implantação. Você também precisará falar com o administrador do Azure Stack para verificar a versão do Azure Stack, você está usando e para obter os logs do Azure Stack relacionadas à implantação.

1. Examine a [status de implantação](#review-deployment-status) e o [recuperar os logs](#get-logs-from-a-vm) do nó mestre no seu cluster Kubernetes.
2. Você precisa usar a versão mais recente do Azure Stack. Se você não tiver certeza da sua versão do Azure Stack, entre em contato com seu administrador do Azure Stack. O tempo de marketplace do Kubernetes Cluster 0.3.0 que requer a versão do Azure Stack 1808 ou maior.
3.  Examine os arquivos de criação da VM. Você pode ter encontrado problemas a seguir:  
    - A chave pública pode ser inválida. Examine a chave que você criou.  
    - Criação da VM pode ter disparado um erro interno ou disparou um erro de criação. Erros podem ser causados por um número de fatores, incluindo limitações de capacidade para sua assinatura do Azure Stack.
    - O nome de domínio totalmente qualificado (FQDN) para a VM é iniciada com um prefixo duplicado?
4.  Se a VM estiver **Okey**, em seguida, avaliar o DVM. Se o DVM tem uma mensagem de erro:

    - A chave pública pode ser inválida. Examine a chave que você criou.  
     - Você precisará entrar em contato com seu administrador do Azure Stack para recuperar os logs para o Azure Stack usando os pontos de extremidade com privilégios. Para obter mais informações, consulte [das ferramentas de diagnóstico do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Se você tiver dúvidas sobre sua implantação, você pode postar a pergunta ou ver se alguém já tem respondeu à pergunta na [Fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Examine o status da implantação

Quando você implanta o cluster Kubernetes para revisar os problemas, você pode examinar o status da implantação.

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).
2. Selecione **grupos de recursos**, e, em seguida, selecione o nome do grupo de recursos usada quando implantar o cluster Kubernetes.
3. Selecione **implantações** e, em seguida, o **nome da implantação**.

    ![Solução de problemas](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consulte a janela de solução de problemas. Cada recurso implantado fornece as informações a seguir.
    
    | Propriedade | Descrição |
    | ----     | ----        |
    | Grupos | O nome do recurso. |
    | Tipo | O provedor de recursos e o tipo de recurso. |
    | Status | O status do item. |
    | Carimbo de Data/hora | O carimbo de hora UTC do tempo. |
    | Detalhes da operação | Os detalhes da operação, como o provedor de recursos envolvido na operação, o ponto de extremidade do recurso e o nome do recurso. |

    Cada item terá um ícone de status de verde ou vermelha.

## <a name="get-logs-from-a-vm"></a>Obter logs de uma VM

Você será necessário para se conectar a VM mestre para seu cluster, abra um prompt de bash e executar um script para gerar os logs. O mestre pode ser encontrado no seu grupo de recursos de cluster e é denominado `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Pré-requisitos

Você precisará de um bash prompt no computador de seu uso para gerenciar o Azure Stack. Use o bash para executar os scripts que acessam os logs. Em um computador Windows, você pode usar o prompt do bash instalado com o Git. Para obter a versão mais recente do git, consulte [downloads do git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Obter logs

1. Abra um prompt do bash. Se você estiver usando o git em um computador Windows, você pode abrir um prompt do bash no seguinte caminho: `c:\programfiles\git\bin\bash.exe`.
2. Execute os comandos de bash a seguir:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > No Windows, você não precisa executar `sudo` e podem usar apenas `chmod 744 getkuberneteslogs.sh`.

3. Na mesma sessão, execute o comando a seguir com os parâmetros atualizados para corresponder ao seu ambiente.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Revise os parâmetros e defina os valores com base em seu ambiente.
    | Parâmetro           | Descrição                                                                                                      | Exemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,-arquivo de identidade | O arquivo de chave privada do RSA para conectar-se a VM mestre de kubernetes. A chave devem começar com `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.PEM                                                        |
    | -h, --host          | O ip público ou o nome de domínio totalmente qualificado (FQDN) do mestre do cluster de Kubernetes VM. O nome da VM começa com `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --usuário          | O nome de usuário do mestre do cluster de Kubernetes VM. Defina esse nome ao configurar o item do marketplace.                                                                    | azureuser                                                                     |
    | -d, - vmdhost       | O ip público ou o FQDN do DVM. O nome da VM começa com `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd dnsk8 frog.local.cloudapp.azurestack.external |

   Quando você adiciona os valores de parâmetros, ele pode ser algo assim:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Uma execução bem-sucedida cria os logs.

    ![Logs gerados](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Recupere os logs nas pastas criadas pelo comando. O comando criará uma nova pasta e o carimbo de hora-lo.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Próximas etapas

[Implantar Kubernetes no Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Adicionar um Kubernetes no Marketplace (para o operador do Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)

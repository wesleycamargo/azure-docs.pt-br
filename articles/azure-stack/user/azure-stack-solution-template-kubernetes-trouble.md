---
title: Solucionar problemas de sua implantação do Kubernetes para o Azure Stackk | Microsoft Docs
description: Saiba como solucionar problemas de sua implantação do Kubernetes para o Azure Stack.
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
ms.date: 01/24/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 9a65ef9acf7a1a133b3f479fe75921191949eeb8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55237844"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Solucionar problemas de sua implantação do Kubernetes para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização.

O artigo a seguir examina o cluster Kubernetes de solução de problemas. Você pode revisar o alerta de implantação e revisar o status da implantação, os elementos necessários para a implantação. Talvez você precise coletar os logs de implantação do Azure Stack ou VMs do Linux que hospedam Kubernetes. Você também precisará trabalhar com o administrador do Azure Stack para recuperar os logs de um ponto de extremidade administrativo.

## <a name="overview-of-deployment"></a>Visão geral da implantação

Antes de iniciar a solução de problemas de seu cluster, você talvez queira examinar o processo de implantação de cluster do Kubernetes do Azure Stack. A implantação usa um modelo de solução do Azure Resource Manager para criar as VMs e instalar o mecanismo do ACS para seu cluster.

### <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O diagrama a seguir mostra o processo geral para implantar o cluster.

![Implantar o processo de Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Etapas de implantação.

1. Colete parâmetros de entrada do item do marketplace.

    Insira os valores que você precisa configurar o cluster Kubernetes, incluindo:
    -  **Nome de usuário**: O nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster do Kubernetes e DVM.
    -  **Chave pública SSH**: A chave que é usada para a autorização de todas as máquinas Linux que foram criados como parte do cluster do Kubernetes e DVM.
    -  **Entidade de serviço**: A ID que é usada pelo provedor de nuvem do Azure do Kubernetes. A ID do cliente identificada como a ID do aplicativo quando você criou a entidade de serviço. 
    -  **Segredo do cliente**: Os principais que você criou ao criar a entidade de serviço.

2. Criar a implantação de VM e a extensão de script personalizado.
    -  Criar a implantação de VM do Linux usando a imagem do marketplace Linux **Ubuntu Server 16.04-LTS**.
    -  Baixar e executar a extensão de script de cliente do marketplace. O script está **Script personalizado para Linux 2.0**.
    -  Execute o script personalizado do DVM. O script faz as seguintes tarefas:
        1. Obtém o ponto de extremidade da Galeria do ponto de extremidade de metadados do Azure Resource Manager.
        2. Obtém a ID de recurso do Active Directory do ponto de extremidade de metadados do Azure Resource Manager.
        3. Carrega o modelo de API para o mecanismo do ACS.
        4. Implanta o mecanismo do ACS para o cluster Kubernetes e salva o perfil de nuvem do Azure Stack para `/etc/kubernetes/azurestackcloud.json`.
3. Crie as VMs mestres.

4. Baixar e executar extensões de script de cliente.

5. Execute o script mestre.

    O script faz as seguintes tarefas:
    - Instala etcd, Docker e Kubernetes recursos como kubelet. etcd é um repositório de valor de chave distribuído que fornece uma maneira de armazenar dados em um cluster de computadores. Docker oferece suporte para virtualização de nível de sistema operacional básica conhecidas como contêineres. Kubelet é o agente do nó que é executado em cada nó de Kubernetes.
    - Configura o serviço etcd.
    - Configura o serviço de kubelet.
    - Inicia o kubelet. Essa tarefa envolve as seguintes etapas:
        1. Inicia o serviço de API.
        2. Inicia o serviço de controlador.
        3. Inicia o serviço de Agendador.
6. Crie VMs de agente.

7. Baixar e executar a extensão de script de cliente.

7. Execute o script de agente. O script personalizado do agente faz as seguintes tarefas:
    - Instala etcd
    - Configura o serviço de kubelet
    - Ingressado no cluster do Kubernetes

## <a name="steps-for-troubleshooting"></a>Etapas para solução de problemas

Você pode coletar logs nas máquinas virtuais que dão suporte ao seu cluster Kubernetes. Você também pode examinar o log de implantação. Você talvez precise se comunicar com o administrador do Azure Stack para verificar a versão do Azure Stack que você precisa usar e para obter os logs do Azure Stack que estão relacionadas à sua implantação.

1. Examine a [status de implantação](#review-deployment-status) e [recuperar os logs](#get-logs-from-a-vm) do nó mestre no seu cluster Kubernetes.
2. Certifique-se de que você está usando a versão mais recente do Azure Stack. Se você não tiver certeza de qual versão você está usando, contate o administrador do Azure Stack. O tempo de marketplace de cluster do Kubernetes 0.3.0 que requer a versão do Azure Stack 1808 ou maior.
3.  Examine os arquivos de criação da VM. Você pode ter tido os seguintes problemas:  
    - A chave pública pode ser inválida. Examine a chave que você criou.  
    - Criação da VM pode ter disparado um erro interno ou disparou um erro de criação. Uma série de fatores pode causar erros, incluindo limitações de capacidade para sua assinatura do Azure Stack.
    - Certifique-se de que o nome de domínio totalmente qualificado (FQDN) para a VM começa com um prefixo duplicado.
4.  Se a VM estiver **Okey**, em seguida, avaliar o DVM. Se o DVM tem uma mensagem de erro:

    - A chave pública pode ser inválida. Examine a chave que você criou.  
    - Você precisa entrar em contato com seu administrador do Azure Stack para recuperar os logs para o Azure Stack, usando os pontos de extremidade com privilégios. Para obter mais informações, consulte [das ferramentas de diagnóstico do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Se você tiver uma pergunta sobre a implantação, você pode postá-lo ou ver se alguém já tem respondeu à pergunta na [Fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Examine o status da implantação

Quando você implanta o cluster Kubernetes, você pode examinar o status da implantação para verificar se há quaisquer problemas.

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).
2. Selecione **grupos de recursos**e, em seguida, selecione o nome do grupo de recursos que você usou ao implantar o cluster Kubernetes.
3. Selecione **implantações**e, em seguida, selecione o **nome da implantação**.

    ![solução de problemas](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consulte a janela de solução de problemas. Cada recurso implantado fornece as seguintes informações:
    
    | Propriedade | DESCRIÇÃO |
    | ----     | ----        |
    | Recurso | O nome do recurso. |
    | Type | O provedor de recursos e o tipo de recurso. |
    | Status | O status do item. |
    | TimeStamp | O carimbo de hora UTC do tempo. |
    | Detalhes da operação | Os detalhes da operação, como o provedor de recursos que estavam envolvidos na operação, o ponto de extremidade de recursos e o nome do recurso. |

    Cada item tem um ícone de status de verde ou vermelho.

## <a name="get-logs-from-a-vm"></a>Obter logs de uma VM

Para gerar os logs, você precisa para se conectar a VM mestre para seu cluster, abra um prompt do bash e, em seguida, executar um script. O mestre de VM pode ser encontrado no seu grupo de recursos de cluster e é denominado `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Pré-requisitos

É necessário um bash prompt no computador em que você usa para gerenciar o Azure Stack. Use o bash para executar os scripts que acessam os logs. Em um computador Windows, você pode usar o prompt de bash que é instalado com o Git. Para obter a versão mais recente do git, consulte [downloads do Git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Obter logs

Para obter logs, execute as seguintes etapas:

1. Abra um prompt do bash. Se você estiver usando o Git em um computador Windows, você pode abrir um prompt do bash no seguinte caminho: `c:\programfiles\git\bin\bash.exe`.
2. Execute os comandos de bash a seguir:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > No Windows, você não precisa executar `sudo`. Em vez disso, você pode simplesmente usar `chmod 744 getkuberneteslogs.sh`.

3. Na mesma sessão, execute o comando a seguir com os parâmetros atualizados para corresponder ao seu ambiente:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. Revise os parâmetros e defina os valores com base em seu ambiente.
    | Parâmetro           | DESCRIÇÃO                                                                                                      | Exemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,-arquivo de identidade | RSA arquivo de chave privada para se conectar a VM mestre do Kubernetes. A chave devem começar com `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h, --host          | O IP público ou o nome de domínio totalmente qualificado (FQDN) do mestre do cluster de Kubernetes VM. O nome da VM começa com `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | O nome de usuário do mestre do cluster de Kubernetes VM. Você definir esse nome quando você configura o item do marketplace.                                                                    | azureuser                                                                     |
    | -d, --vmdhost       | O IP público ou o FQDN do DVM. O nome da VM começa com `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Quando você adiciona os valores de parâmetros, ele poderia ser algo semelhante ao seguinte código:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Uma execução bem-sucedida cria os logs.

    ![Logs gerados](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Recupere os logs nas pastas que foram criados pelo comando. O comando cria novas pastas e de data / hora-los.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Próximas etapas

[Implantar Kubernetes no Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Adicionar um cluster Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)

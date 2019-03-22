---
title: Solucionar problemas de sua implantação do Kubernetes para o Azure Stack | Microsoft Docs
description: Saiba como solucionar problemas de sua implantação do Kubernetes para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 01a9405c98160149782ab2cf248f64818d631dde
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293780"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Solucionar problemas de sua implantação do Kubernetes para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização. Cenário desconectado da pilha do Azure não é suportado pelo preview.

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
    -  Baixe e execute a extensão de script personalizado do marketplace. O script está **Script personalizado para Linux 2.0**.
    -  Execute o script personalizado do DVM. O script faz as seguintes tarefas:
        1. Obtém o ponto de extremidade da Galeria do ponto de extremidade de metadados do Azure Resource Manager.
        2. Obtém a ID de recurso do Active Directory do ponto de extremidade de metadados do Azure Resource Manager.
        3. Carrega o modelo de API para o mecanismo do ACS.
        4. Implanta o mecanismo do ACS para o cluster Kubernetes e salva o perfil de nuvem do Azure Stack para `/etc/kubernetes/azurestackcloud.json`.
3. Crie as VMs mestres.

4. Baixar e executar extensões de script personalizado.

5. Execute o script mestre.

    O script faz as seguintes tarefas:
    - Instala etcd, Docker e Kubernetes recursos como kubelet. etcd é um repositório de valor de chave distribuído que fornece uma maneira de armazenar dados em um cluster de computadores. Docker oferece suporte para virtualização de nível de sistema operacional básica conhecidas como contêineres. Kubelet é o agente do nó que é executado em cada nó de Kubernetes.
    - Configura a **etcd** service.
    - Configura a **kubelet** service.
    - Inicia o kubelet. Essa tarefa envolve as seguintes etapas:
        1. Inicia o serviço de API.
        2. Inicia o serviço de controlador.
        3. Inicia o serviço de Agendador.
6. Crie VMs de agente.

7. Baixe e execute a extensão de script personalizado.

7. Execute o script de agente. O script personalizado do agente faz as seguintes tarefas:
    - Instala **etcd**.
    - Configura a **kubelet** service.
    - Une o cluster Kubernetes.

## <a name="steps-for-troubleshooting"></a>Etapas para solução de problemas

Você pode coletar logs nas máquinas virtuais que dão suporte ao seu cluster Kubernetes. Você também pode examinar o log de implantação. Você talvez precise se comunicar com o administrador do Azure Stack para verificar a versão do Azure Stack que você precisa usar e para obter os logs do Azure Stack que estão relacionadas à sua implantação.

1. Examine a [status de implantação](#review-deployment-status) e [recuperar os logs](#get-logs-from-a-vm) do nó mestre no seu cluster Kubernetes.
2. Certifique-se de que você está usando a versão mais recente do Azure Stack. Se você não tiver certeza de qual versão você está usando, contate o administrador do Azure Stack.
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

## <a name="review-deployment-logs"></a>Examine os logs de implantação

Se o portal do Azure Stack não fornecer informações suficientes para que você possa solucionar problemas ou superar uma falha de implantação, a próxima etapa é examinar os logs do cluster. Para recuperar manualmente os logs de implantação, você normalmente precisa se conectar a uma das máquinas virtuais da mestre do cluster. Uma abordagem alternativa mais simples seria fazer o download e execute o seguinte [script Bash](https://aka.ms/AzsK8sLogCollectorScript) fornecidas pela equipe do Azure Stack. Esse script se conecta ao DVM e máquinas virtuais do cluster, coleta relevantes do sistema e logs do cluster e baixa-os de volta para sua estação de trabalho.

### <a name="prerequisites"></a>Pré-requisitos

Você precisará de um prompt do Bash na máquina que você usa para gerenciar o Azure Stack. Em um computador Windows, você pode obter um Bash prompt instalando [Git para Windows](https://git-scm.com/downloads). Uma vez instalado, procure _Git Bash_ no seu menu Iniciar.

### <a name="retrieving-the-logs"></a>Recuperar os logs

Siga estas etapas para coletar e baixar os logs do cluster:

1. Abra um prompt do Bash. Em um computador Windows, abra _Git Bash_ ou execute: `C:\Program Files\Git\git-bash.exe`.

2. Baixe o script de coletor de log, executando os seguintes comandos no prompt do Bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Procure as informações exigidas pelo script e executá-lo:

    | Parâmetro           | DESCRIÇÃO                                                                                                      | Exemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | O IP público ou o nome de domínio totalmente qualificado (FQDN) do DVM. O nome da máquina virtual é iniciado com `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Imprima o uso do comando. | |
    | -i,-arquivo de identidade | O arquivo de chave privada RSA passado para o item do marketplace ao criar o cluster Kubernetes. Necessário para a conexão remota com os nós de Kubernetes. | C:\data\id_rsa.PEM (Putty)<br>~/.SSH/id_rsa (SSH)
    | -m, --master-host   | O IP público ou o nome de domínio totalmente qualificado (FQDN) de um nó mestre de Kubernetes. O nome da máquina virtual é iniciado com `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | O nome de usuário passado para o item do marketplace ao criar o cluster Kubernetes. Necessário para a conexão remota com os nós de Kubernetes | azureuser (valor padrão) |


   Quando você adiciona os valores de parâmetros, o comando pode ser algo parecido com isto:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Depois de alguns minutos, o script produzirá os logs coletados em um diretório chamado `KubernetesLogs_{{time-stamp}}`. Lá você encontrará um diretório para cada máquina virtual que pertence ao cluster.

    O script do coletor de log também procurar por erros nos arquivos de log e incluir etapas de solução de problemas se ele ocorre encontrar um problema conhecido. Verifique se que você estiver executando a versão mais recente do script para aumentar as chances de encontrar os problemas conhecidos.

> [!Note]  
> Fazer check-out do GitHub [repositório](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) para saber mais detalhes sobre o script do coletor de log.

## <a name="next-steps"></a>Próximas etapas

[Implantar Kubernetes no Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Adicionar um cluster Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)

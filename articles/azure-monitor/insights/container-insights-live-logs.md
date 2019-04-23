---
title: Exibição do Azure Monitor para logs de contêineres em tempo real | Microsoft Docs
description: Este artigo descreve o modo de exibição em tempo real dos logs de contêiner (stdout/stderr) sem usar kubectl com o Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788828"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Como exibir logs de contêiner em tempo real com o Azure Monitor para contêineres (versão prévia)
Esse recurso, que está atualmente em versão prévia, fornece uma exibição em tempo real nos logs de contêiner do AKS (Serviço de Kubernetes do Azure) (stdout/stderr) sem ter de executar comandos kubectl. Quando você seleciona essa opção, o novo painel aparece abaixo da tabela de dados de desempenho de contêineres na exibição **Contêineres**.  Ela mostra o log em tempo real gerado pelo mecanismo de contêiner para auxiliar mais ainda na solução de problemas em tempo real. **Colaborador** acesso para o recurso de cluster é necessário para esse recurso funcione.

Os logs em tempo real dão suporte a três métodos diferentes para controlar o acesso aos logs:

1. AKS sem autorização do RBAC do Kubernetes habilitada 
2. AKS habilitado com autorização do RBAC do Kubernetes
3. AKS habilitado com logon único baseado em SAML do Azure AD (Active Directory) 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster do Kubernetes sem RBAC habilitado
 
Se você tiver um cluster do Kubernetes que não está configurado com autorização do RBAC do Kubernetes ou integrado ao Azure AD com logon único, não precisará seguir estas etapas. Como a autorização do Kubernetes usa a kube-api, são necessárias permissões somente leitura.

## <a name="kubernetes-rbac-authorization"></a>Autorização do RBAC do Kubernetes
Se você habilitou a autorização do RBAC do Kubernetes, precisará aplicar a associação de função de cluster. As etapas de exemplo a seguir demonstram como configurar a associação de função de cluster com base no modelo de configuração yaml.   

1. Copie e cole o arquivo yaml e salve-o como LogReaderRBAC.yaml.  

   ```
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRole 
   metadata: 
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""] 
        resources: ["pods/log"] 
        verbs: ["get"] 
   --- 
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRoleBinding 
   metadata: 
      name: containerHealth-read-logs-global 
   roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
   subjects: 
      - kind: User 
        name: clusterUser 
        apiGroup: rbac.authorization.k8s.io 
   ```

2. Crie a associação de regra de cluster executando o seguinte comando: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Configurar o AKS com o Azure Active Directory
O AKS pode ser configurado para usar o Azure AD (Active Directory) na autenticação do usuário. Se você estiver configurando isso pela primeira vez, confira [Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure](../../aks/aad-integration.md). Durante as etapas para criar o [aplicativo cliente](../../aks/aad-integration.md#create-client-application) e especificar o **URI de redirecionamento**, você precisará adicionar outro URI à lista `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>A configuração da autenticação com o Azure Active Directory para logon único só pode ser realizada durante a implantação inicial de um novo cluster do AKS. Não é possível configurar o logon único em um cluster do AKS já implantado.  
> 

## <a name="view-live-logs"></a>Exibir logs em tempo real
Quando você estiver exibindo **Contêineres**, poderá **Exibir Logs de contêiner** ou **Exibir logs de contêiner ao vivo**.  Quando você seleciona **Exibir logs de contêiner ao vivo**, um novo painel aparece abaixo da tabela de dados de desempenho dos contêineres mostrando o log em tempo real gerado pelo mecanismo de contêiner para auxiliar mais ainda na solução de problemas em tempo real.  
1. Entre no [Portal do Azure](https://portal.azure.com). 
2. No menu **Microsoft Azure**, selecione **Monitor** e selecione **Contêineres**.  
3. Selecione um contêiner na lista no modo de exibição **Contêineres monitorados**.  
4. Selecione o modo de exibição **Contêineres** e, no painel Propriedades de um contêiner selecionado, o link **Exibir logs de contêiner ao vivo** estará listado.  
5. Se o cluster do AKS estiver configurado com SSO usando o AAD, você precisará se autenticar no primeiro uso durante a sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.  

Depois de autenticar com êxito, o painel de log em tempo real será exibido na seção inferior do painel central. Se o indicador de status de busca mostrar uma marca de seleção verde, que está à direita do painel, significa que ele poderá recuperar dados.
    
  ![Dados recuperados do painel de logs em tempo real](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na barra de pesquisa, você pode filtrar por palavra-chave para realçar o texto no log.   

  ![Exemplo de filtro do painel de logs em tempo real](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Para suspender a rolagem automática e controlar o comportamento do painel, permitindo que você percorra manualmente os novos dados de log de leitura, clique na opção **Rolar**.  Para habilitar novamente a rolagem automática, basta clicar na opção **Rolar** novamente.  Você também pode pausar a recuperação de dados do log clicando na opção **Pausar** e, quando estiver pronto para continuar, basta clicar em **Reproduzir**.  

![Pausar exibição em tempo real no painel de logs em tempo real](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Próximas etapas
Para continuar aprendendo a usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, confira [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).

---
title: Registrar em log uma instância de contêiner com Azure Log Analytics
description: Saiba como enviar saída de contêiner (STDOUT e STDERR) para o Azure Log Analytics.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 4dbcccc1a4b23ca37918495dc536df08a70cade7
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337879"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Registrar em log uma instância de contêiner com Azure Log Analytics

Os workspaces do Log Analytics fornecem um local centralizado para armazenar e consultar dados de log não apenas dos recursos do Azure, mas também dos recursos locais e recursos em outras nuvens. As Instâncias de Contêiner do Azure incluem suporte interno para enviar dados ao Log Analytics.

Para enviar dados da instância de contêiner ao Log Analytics, é necessário criar um grupo de contêineres usando a CLI do Azure (ou Cloud Shell) e um arquivo YAML. As seções a seguir descrevem a criação de um grupo de contêineres com registro em log habilitado e consulta de logs.

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar o registro em log nas instâncias de contêiner, serão necessários o seguinte:

* [Workspace do Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [CLI do Azure](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obter credenciais do Log Analytics

As Instâncias de Contêiner do Azure precisam de permissão para enviar dados ao workspace do Log Analytics. Para conceder essa permissão e habilitar o registro em log, será necessário fornecer a ID do workspace do Log Analytics e uma de suas chaves (primária ou secundária) ao criar o grupo de contêineres.

Para obter a ID do workspace do Log Analytics e a chave primária:

1. Navegue até o workspace do Log Analytics no portal do Azure
1. Em **CONFIGURAÇÕES**, selecione **Configurações avançadas**
1. Selecione **Fontes Conectadas** > **Servidores Windows** (ou **Servidores Linux**--a ID e as chaves são as mesmas para ambos)
1. Anote:
   * **ID DO WORKSPACE**
   * **CHAVE PRIMÁRIA**

## <a name="create-container-group"></a>Criar grupo de contêineres

Agora que você tem a ID e a chave primária do workspace do Log Analytics, você está pronto para criar um grupo de contêineres com registro em log habilitado.

Os exemplos a seguir demonstram duas maneiras de criar um grupo de contêineres com um único contêiner [fluentd][fluentd]: CLI do Azure e CLI do Azure com um modelo YAML. O contêiner Fluentd produz várias linhas de saída na configuração padrão. Como essa saída é enviada para o workspace do Log Analytics, ela funciona bem para demonstrar a visualização e a consulta de logs.

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar com a CLI do Azure, especifique os parâmetros `--log-analytics-workspace` e `--log-analytics-workspace-key` no comando [az container create][az-container-create]. Substitua os dois valores de workspace pelos valores que você obteve na etapa anterior (e atualize o nome do grupo de recursos) antes de executar o comando a seguir.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implantar com YAML

Use esse método se você preferir implantar grupos de contêineres com YAML. O YAML a seguir define um grupo de contêineres com um único contêiner. Copie o YAML para em um novo arquivo, então substitua `LOG_ANALYTICS_WORKSPACE_ID` e `LOG_ANALYTICS_WORKSPACE_KEY` pelos valores obtidos na etapa anterior. Salve o arquivo como **deploy-aci.yaml**.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Em seguida, execute o comando a seguir para implantar o grupo de contêineres; substitua `myResourceGroup` por um grupo de recursos na assinatura (ou crie primeiro um grupo de recursos nomeado "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Você deverá receber uma resposta do Azure contendo detalhes da implantação, logo após a emissão do comando.

## <a name="view-logs-in-log-analytics"></a>Exibir logs no Log Analytics

Após implantar o grupo de contêineres, poderá demorar vários minutos (até 10) para que as primeiras entradas de log apareçam no portal do Azure. Para exibir os logs do grupo de contêineres, abra o workspace do Log Analytics e:

1. Na visão geral do **Workspace OMS**, selecione **Pesquisa de Logs**. Os workspaces do OMS agora são chamados de workspaces do Log Analytics.  
1. Em **Mais algumas consultas para tentar**, selecione o link **Todos os dados coletados**

Você deverá ver vários resultados exibidos pela consulta `search *`. Caso não veja nenhum resultado, aguarde alguns minutos e, em seguida, selecione o botão **EXECUTAR** para executar a consulta novamente. Por padrão, as entradas de log são exibidas no modo de exibição "Lista"--selecione **Tabela** para ver as entradas de log em um formato mais condensado. Em seguida, você poderá expandir uma linha para ver o conteúdo de uma entrada de log individual.

![Pesquisa de Logs no portal do Azure][log-search-01]

## <a name="query-container-logs"></a>Consulta de logs do contêiner

O Log Analytics inclui uma [linguagem de consulta][query_lang] extensa para efetuar pull das informações de potencialmente milhares de linhas de saída de log.

O agente de log das Instâncias de Contêiner do Azure envia entradas para a tabela `ContainerInstanceLog_CL` no workspace do Log Analytics. A estrutura básica de uma consulta é a tabela de origem (`ContainerInstanceLog_CL`) seguida por uma série de operadores separados pelo caractere de pipe (`|`). É possível encadear vários operadores para refinar os resultados e executar funções avançadas.

Para ver os resultados da consulta de exemplo, cole a consulta a seguir na caixa de texto da consulta (em "Mostrar conversor da linguagem herdada") e selecione o botão **EXECUTAR** para executar a consulta. Esta consulta exibe todas as entradas de log cujo campo de "Mensagem" contém a palavra "warn":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Consultas mais complexas também têm suporte. Por exemplo, essa consulta exibe apenas as entradas de log do grupo de contêineres "mycontainergroup001" gerado na última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Próximas etapas

### <a name="log-analytics"></a>Log Analytics

Para obter mais informações sobre como consultar logs e configurar alertas no Azure Log Analytics, consulte:

* [Compreendendo as pesquisas de logs no Log Analytics](../log-analytics/log-analytics-log-search.md)
* [Alertas unificados no Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorar memória e CPU do contêiner

Para obter informações sobre monitoramento de recursos de memória e CPU da instância de contêiner, consulte:

* [Monitorar os recursos de contêiner em Instâncias de Contêiner do Azure](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
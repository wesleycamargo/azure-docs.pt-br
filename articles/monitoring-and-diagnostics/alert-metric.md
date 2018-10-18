---
title: Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor
description: Saiba como usar a CLI ou o portal do Azure para criar, exibir e gerenciar regras de alerta de métrica.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: a6c38ce465b8e4e68694f2a6cc0d467e34a09852
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388136"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor

Os alertas de métrica no Azure Monitor possibilitam que você seja notificado quando uma das suas métricas ultrapassar um limite. Os alertas de métrica funcionam com uma variedade de métricas de plataforma multidimensionais, métricas personalizadas, métricas padrão do Application Insights e métricas personalizadas. Neste artigo, descreveremos como criar, exibir e gerenciar regras de alerta de métrica usando o portal do Azure e a CLI do Azure. Também é possível criar regras de alerta de métrica usando modelos do Azure Resource Manager, processo que é descrito em [outro artigo](monitoring-enable-alerts-using-template.md).

Saiba mais sobre como os alertas de métrica funcionam conferindo a [visão geral dos alertas de métrica](alert-metric-overview.md).

## <a name="create-with-azure-portal"></a>Criar com o portal do Azure

O procedimento a seguir descreve como criar uma regra de alerta de métrica no portal do Azure:

1. No [portal do Azure](https://portal.azure.com), clique em **Monitor**. A folha Monitor consolida todas as suas configurações e dados de monitoramento em uma exibição.

2. Clique em **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **Alertas** no menu de recursos, em **Monitoramento**, e você pode criar alertas de lá também.

3. Clique em **Selecionar destino**, no painel de contexto que é carregado, e selecione um recurso de destino que você deseja modificar. Use os menus suspensos **Assinatura** e **Tipo de recurso** para localizar o recurso que deseja monitorar. Também é possível usar a barra de pesquisa para localizar o recurso.

4. Se o recurso selecionado tiver métricas com base nas quais você pode criar alertas, os **Sinais disponíveis** na parte inferior direita incluirão as métricas. Você pode exibir a lista completa dos tipos de recurso com suporte para alertas de métrica neste [artigo](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

5. Após selecionar um recurso de destino, clique em **Adicionar critérios**

6. Você verá uma lista dos sinais com suporte para o recurso. Selecione a métrica na qual deseja criar um alerta.

7. Você verá um gráfico da métrica referente às últimas 6 horas. Defina o **Período**, a **Frequência**, o **Operador** e o **Limite**, que determinarão a lógica que a regra de alerta de métrica avaliará.

8. Usando o gráfico da métrica, é possível determinar qual seria um limite razoável.

9. Opcionalmente, se a métrica tiver dimensões, a Tabela de dimensões será apresentada. Selecione um ou mais valores por dimensão. O alerta de métrica será executado e avaliará a condição com relação a todas as combinações de valores selecionadas. [Saiba mais sobre como os alertas de métricas multidimensionais funcionam](alert-metric-overview.md). Também é possível **Selecionar \*** para qualquer uma das dimensões. **Selecionar \*** dimensionará dinamicamente a seleção para todos os valores atuais e futuros de uma dimensão.

10. Clique em **Concluído**

11. Opcionalmente, adicione outro critério se quiser monitorar uma regra de alerta complexa

12. Preencha os **Detalhes do alerta**, como **Nome da Regra de Alerta**, **Descrição** e **Gravidade**

13. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.

14. Clique em **Concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> Regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

## <a name="view-and-manage-with-azure-portal"></a>Exibir e gerenciar com o portal do Azure

É possível exibir e gerenciar regras de alerta de métrica usando a folha Gerenciar Regras, em Alertas. O procedimento a seguir mostra como exibir suas regras de alerta de métrica e editar uma delas.

1. No portal do Azure, navegue até **Monitor**

2. Clique em **Alertas** e **Gerenciar regras**

3. Na folha **Gerenciar regras**, você pode exibir todas as regras de alertas configuradas em suas assinaturas. Você pode filtrar as regras com base no **Grupo de recursos**, no **Tipo de recurso** e no **Recurso**. Se quiser ver somente os alertas de métrica, selecione Métricas como o **Tipo de sinal**.

    > [!TIP]
    > Na folha **Gerenciar regras**, você pode selecionar várias regras de alerta e habilitar ou desabilitá-las. Isso poderá ser útil quando for necessário colocar determinados recursos de destino em manutenção.

4. Clique no nome da regra de alerta de métrica que deseja editar.

5. Em Editar Regra, clique nos **Critérios de alerta** que deseja editar. Você pode alterar a métrica, o limite e os outros campos conforme o necessário

    > [!NOTE]
    > Não é possível editar o **Recurso de destino** e o **Nome da Regra de Alerta** após o alerta de métrica ser criado.

6. Clique em **Concluído** para salvar suas edições.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As seções anteriores descreveram como criar, exibir e gerenciar regras de alerta de métrica usando o portal do Azure. Esta seção descreve como fazer o mesmo usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) de multiplataforma. O modo mais rápido de começar a usar a CLI do Azure é com o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Neste artigo, usaremos o Cloud Shell.

1. Vá até o portal do Azure e clique em **Cloud Shell**.

2. No prompt, você pode usar comandos com a opção ``--help`` para saber mais sobre o comando e como usá-lo. Por exemplo, o comando a seguir mostra a lista de comandos disponíveis para criar, exibir e gerenciar alertas de métrica

    ```azurecli
    az monitor metrics alert --help
    ```

3. É possível criar uma regra de alerta de métrica simples que monitora se o Percentual de CPU médio em uma VM é maior que 70

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90"
    ```

4. É possível exibir todos os alertas de métrica em um grupo de recursos usando o seguinte comando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Você pode ver os detalhes de uma regra de alerta de métrica específica usando o nome ou a ID de recurso da regra.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. É possível desabilitar uma regra de alerta de métrica usando o comando a seguir.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} -enabled false
    ```

7. É possível excluir uma regra de alerta de métrica usando o comando a seguir.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} -enabled false
    ```

## <a name="next-steps"></a>Próximas etapas

- [Criar alertas de métrica usando modelos do Azure Resource Manager](monitoring-enable-alerts-using-template.md).
- [Compreender como os alertas de métrica funcionam](alert-metric-overview.md).
- [Compreender o esquema de web hook para alertas de métrica](monitoring-near-real-time-metric-alerts.md#payload-schema)

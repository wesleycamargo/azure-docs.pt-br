---
title: Criar, exibir e gerenciar alertas do log de atividades no Azure Monitor
description: Como criar alertas do log de atividades por meio do portal do Azure, do modelo de recurso e do PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 526c50fa4d261a30738c3f24d537fe5e0d765f6d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951297"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor  

## <a name="overview"></a>Visão geral
Alertas do log de atividades são os alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta.

Esses alertas são para recursos do Azure e podem ser criados usando um modelo do Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Normalmente, você cria alertas do log de atividades para receber notificações quando ocorrem alterações específicas nos recursos da sua assinatura do Azure, muitas vezes com escopo para recursos de recursos específicos. Por exemplo, talvez você queira ser notificado quando qualquer máquina virtual em (grupo de recursos de exemplo) **myProductionResourceGroup** for excluída, ou ser notificado se quaisquer novas funções forem atribuídas a um usuário em sua assinatura.

> [!IMPORTANT]
> Não é possível criar alertas de notificação de Integridade do Serviço por meio da interface para a criação de alerta do log de atividades. Para saber mais sobre a criação e o uso de notificações de Integridade do Serviço, veja [Receber alertas do log de atividades sobre as notificações de Integridade do Serviço](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="manage-alert-rules-for-activity-log-using-azure-portal"></a>Gerenciar regras de alerta do log de atividades usando o portal do Azure

> [!NOTE]

>  Ao criar as regras de alerta, assegure o seguinte:

> - A assinatura no escopo não é diferente da assinatura onde o alerta foi criado.
- Os critérios devem ser nível/status/chamador/grupo de recursos/ID do recurso/tipo de recurso/categoria de eventos no qual o alerta é configurado.
- Não há nenhuma condição "anyOf" ou condições aninhadas na configuração de alerta JSON (basicamente, apenas um allOf é permitido sem nenhum allOf/anyOf adicional).
- Quando a categoria é "administrativa". Você deve especificar pelo menos um dos critérios anteriores em seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.

### <a name="create-an-alert-rule-for-an-activity-log-using-azure-portal"></a>Criar uma regra de alerta para um log de atividades usando o portal do Azure

Use este procedimento:

1. No Portal do Azure, selecione **Monitor** > **Alertas**
2. Clique em **Nova Regra de Alerta**na parte superior da janela **Alertas**.

     ![nova regra de alerta](./media/monitor-alerts-unified/AlertsPreviewOption.png)

     A janela **Criar regra** é exibida.

      ![opções de nova regra de alerta](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. Em **Definir a condição de alerta**, forneça as informações a seguir e clique em **Concluído**.

    - **Destino de Alerta:** para exibir e selecionar o destino do novo alerta, use **Filtrar por assinatura** / **Filtrar por tipo de recurso** e selecione o recurso ou grupo de recursos da lista exibida.

    > [!NOTE]

    > Você pode selecionar um recurso, um grupo de recursos ou uma assinatura inteira para o sinal do log de atividades.

    **Exibição de exemplo de destino do alerta** ![Selecionar destino](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - Em **Critérios de Destino**, clique em **adicionar critérios** e todos os sinais disponíveis para o destino serão exibidos, incluindo aqueles das várias categorias do **Log de Atividades**, com o nome da categoria anexado no nome do **Serviço do Monitor**.

    - Selecione o sinal na lista exibida com as várias operações possíveis para o tipo **Log de Atividades**.

    É possível selecionar a linha do tempo do histórico de log e a lógica de alerta correspondente para esse sinal de destino:

    **Tela Adicionar critérios**

    ![adicionar critérios](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Tempo de histórico**: os eventos disponíveis para a operação selecionada podem ser plotados durante as últimas 6/12/24 horas (ou) na Durante a última semana.

    **Lógica de Alerta**:

     - **Nível de evento** – o nível de gravidade do evento. _Detalhado_, _Informativo_, _Aviso_, _Erro_ ou _Crítico_.
     - **Status**: o status do evento. _Iniciado_, _Com falha_ ou _Bem-sucedido_.
     - **Evento iniciado por**: o endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

        Gráfico de sinal de exemplo com a lógica de alerta aplicada:

        ![ critérios selecionados](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Em **definir detalhes de regras de alerta,** forneça os detalhes a seguir:

    - **Nome da regra de alerta** – nome da nova regra de alerta
    - **Descrição** – Descrição para a nova regra de alerta
    - **Salvar alerta para o grupo de recursos** – Selecione o Grupo de Recursos, onde deseja salvar essa nova regra.

5. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Alternativamente, [crie um novo grupo de ações](monitoring-action-groups.md) e atribua à nova regra. Para criar um novo grupo, clique em **+ Novo grupo**.

6. Para habilitar as regras após criá-lo, clique em **Sim** da opção **Habilitar regra após a criação**.
7. Clique em **Criar regra de alerta**.

    A nova regra de alerta para o log de atividades é criada e uma mensagem de confirmação aparece no canto superior direito da janela.

    É possível habilitar, desabilitar, editar ou excluir uma regra. [Saiba mais](#view-and-manage-activity-log-alert-rules-in-azure-portal) sobre o gerenciamento de regras do log de atividades.


Como alternativa, uma analogia simples para entender as condições nas quais as regras de alerta podem ser criadas no log de atividades é explorar ou filtrar eventos por meio do [Log de atividades no portal do Azure](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). No Azure Monitor – Log de atividades, é possível filtrar ou localizar os eventos necessários e, em seguida, criar um alerta usando o botão **Adicionar alerta do log de atividades** e, em seguida, seguir as etapas 4 em diante, conforme mencionado no tutorial acima.
    
 ![ adicionar alerta do log de atividades](./media/monitoring-activity-log-alerts-new-experience/add-activity-log.png)
    

### <a name="view-and-manage-activity-log-alert-rules-in-azure-portal"></a>Exibir e gerenciar regras de alerta do log de atividades no portal do Azure

1. Do Portal do Azure, clique em **Monitor** > **Alertas** e clique em **Gerenciar regras** na parte superior esquerda da janela.

    ![ gerenciar regras de alerta](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    A lista das regras disponíveis é exibida.

2. Pesquise a regra do log de atividades para modificar.

    ![ pesquisar regras de alerta do log de atividades](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Você pode usar os filtros disponíveis – _Assinatura_, _Grupo de recursos_, _Recurso_, _Tipo de Sinal_ ou _Status_ para localizar a regra de atividade que deseja editar.

    > [!NOTE]

    > É possível editar apenas a **Descrição**, os **Critérios de destino** e os **Grupos de ações**.

3.  Selecione a regra e clique duas vezes para editar as opções da regra. Faça as alterações necessárias e clique em **Salvar**.

    ![ gerenciar regras de alerta](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  É possível desabilitar, habilitar ou excluir uma regra. Selecione a opção apropriada na parte superior da janela após selecionar a regra, conforme detalhado na etapa 2.


## <a name="manage-alert-rules-for-activity-log-using-azure-resource-template"></a>Gerenciar regras de alerta para o log de atividades usando o modelo de recurso do Azure
Para criar um alerta do log de atividades usando um modelo do Resource Manager, você cria um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, você preencherá todas as propriedades relacionadas. Veja abaixo um modelo que cria um alerta do log de atividades.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
O json de exemplo acima pode ser salvo como, por exemplo, sampleActivityLogAlert.json para este passo a passo e pode ser implantado usando o [Azure Resource Manager no portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Pode levar até cinco minutos para que uma nova regra de alerta do log de atividades fique ativa

## <a name="manage-alert-rules-for-activity-log-using-powershell-cli-or-api"></a>Gerenciar regras de alerta para o log de atividades usando o PowerShell, a CLI ou a API
[Azure Monitor – API de alertas do Log de atividades](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma API REST totalmente compatível com a API REST do Azure Resource Manager. Portanto, pode ser usado por meio do Powershell usando o cmdlet do Gerenciador de Recursos, bem como a CLI do Azure.

Está ilustrado abaixo o uso por meio do cmdlet do PowerShell do Azure Resource Manager para o modelo de recurso de exemplo mostrado anteriormente (sampleActivityLogAlert.json) na [seção de modelo de recurso](#manage-alert-rules-for-activity-log-using-azure-resource-template):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Em que o sampleActivityLogAlert.parameters.json tem os valores fornecidos para os parâmetros necessários para a criação da regra de alerta.

Está ilustrado abaixo o uso por meio do comando do Azure Resource Manager na CLI do Azure para o modelo de recurso de exemplo mostrado anteriormente (sampleActivityLogAlert.json) na [seção de modelo de recurso](#manage-alert-rules-for-activity-log-using-azure-resource-template):

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
Em que o sampleActivityLogAlert.parameters.json tem os valores fornecidos para os parâmetros necessários para a criação da regra de alerta.


## <a name="next-steps"></a>Próximas etapas

- [Esquema de webhook para os logs de atividades](monitoring-activity-log-alerts-webhook.md)
- [Visão geral dos logs de atividades](monitoring-activity-log-alerts.md) 
- Saiba mais sobre [grupos de ação](monitoring-action-groups.md).  
- Saiba mais sobre as [notificações de integridade do serviço](monitoring-service-notifications.md).

---
title: Criar, exibir e gerenciar alertas usando o Azure Monitor
description: Use a nova experiência unificada de alertas do Azure para criar, exibir e gerenciar regras de alerta de métrica e de log em um único lugar.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 9ade63c07003ead55e63093ae74e876589ec8d61
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287830"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas usando o Azure Monitor  

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas usando a nova interface de alertas no Portal do Azure. A definição de uma regra de alerta é realizada em três partes:
- Destino: recurso do Azure específico, que deve ser monitorado
- Critério: condição ou lógica específica que quando aparecer no sinal, deverá disparar uma ação
- Ação: chamada específica enviada a um destinatário de uma notificação – email, SMS, webhook etc.

Os alertas do Azure também fornecem uma exibição unificada de todas as regras de alerta e a capacidade de gerenciá-las em um único local, incluindo a exibição de alertas não resolvidos. Saiba mais sobre a funcionalidade em [Alertas do Azure - Visão geral](monitoring-overview-unified-alerts.md).

O alerta usa o termo **Alertas de Log** para descrever alertas em que o sinal é baseado em consulta personalizada no [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-analytics.md). O [a nova funcionalidade de alerta de métrica](monitoring-near-real-time-metric-alerts.md) fornece a capacidade de alertar em [métricas multidimensionais](monitoring-metric-charts.md) para recursos específicos do Azure. Os alertas para esse recurso podem usar filtros adicionais nas dimensões que criam **Alertas de métricas multidimensionais** .


> [!NOTE]
> Enquanto os alertas do Azure oferecem uma experiência nova e aprimorada para a criação de alertas no Azure. A experiência [de alertas (clássica)](monitoring-overview-alerts.md) existente permanece utilizável.
>

A seguir é detalhado o guia passo a passo sobre o uso de alertas do Azure.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta com o Portal do Azure
1. No [portal](https://portal.azure.com/), selecione **Monitor** e na seção MONITOR, escolha **Alertas**.  
    ![Monitoramento](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Selecione o botão **Nova Regra de Alerta** para criar um novo alerta no Azure.
    ![Adicionar Alerta](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. A seção Criar Alerta é mostrada com três partes que consistem em: *Definir condição de alerta*, *Definir detalhes do alerta* e *Definir grupo de ação*.

    ![Criar regra](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Defina a condição de alerta usando o link **Selecionar Recurso** e especificando o destino, ao selecionar um recurso. Filtre, escolhendo a *Assinatura*, o *Tipo de Recurso* e finalmente selecionando o *Recurso* necessário.

    >[!NOTE]

    > Antes de prosseguir, verifique os sinais disponíveis para o recurso selecionado.

    ![Selecionar recurso](./media/monitor-alerts-unified/Alert-SelectResource.png)

 A interface do usuário permite que você crie vários tipos de alertas em um só lugar. Com base no tipo de alerta desejado, escolha a próxima etapa como:

    - Para **Alertas de Métrica**: siga as etapas 5 a 7 e, em seguida, acesse diretamente a etapa 11
    - Para **Alertas de Log**, vá para a etapa 8.

    > [!NOTE]

    > Os alertas do log de atividades também têm suporte, mas estão em versão prévia. [Saiba mais](monitoring-activity-log-alerts-new-experience.md).

5. *Alertas de Métrica: verifique se **Tipo de Recurso** está selecionado com o tipo de sinal **Métrica**; em seguida, após escolher o **recurso** apropriado, clique no botão *Concluído* para retornar a Criar Alerta. Em seguida, use o botão **Adicionar Critérios** para escolher o sinal específico na lista de opções de sinal, o serviço de monitoramento e o tipo listado – disponíveis para o recurso selecionado anteriormente.

    ![Selecionar um recurso](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Todos os recursos compatíveis com [alertas quase em tempo real](monitoring-near-real-time-metric-alerts.md) são listados com o serviço de monitor **Plataforma** e o tipo de sinal **Métrica**

6. *Alertas de Métrica*: depois que o sinal for selecionado, a lógica para o alerta poderá ser declarada. Para referência, os dados históricos do sinal são mostrados com a opção de ajustar a janela de tempo usando **Mostrar Histórico**, variando das últimas seis horas até a última semana. Com a visualização exibida, a **Lógica de Alerta** pode ser selecionada nas opções de condição, agregação e, finalmente, limite mostradas. Como uma visualização da lógica fornecida, a condição é mostrada na visualização juntamente com o histórico de sinal para indicar quando o alerta teria sido disparado. Finalmente, especifique por qual tempo de duração, o alerta deve buscar a condição especificada escolhendo a opção **Período** juntamente com frequência em que o alerta deve ser executado, selecionando **Frequência**.

    ![Configurar a lógica de sinal para métrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alertas de Métrica*: se o sinal for uma métrica, a janela de alerta poderá ser filtrada usando vários pontos de dados ou dimensões para o tal recurso do Azure. 

    a. Escolha uma duração na lista suspensa **Mostrar histórico** para visualizar um período de tempo diferente. É possível escolher dimensões para as métricas compatíveis para filtrar em uma série temporal. Escolher dimensões é opcional e até cinco dimensões podem ser utilizadas. 

    b. A **Lógica de Alerta** pode ser selecionada a partir das opções mostradas da *Condição*, *Agregação e *Limite*. Como uma visualização da lógica fornecida, a condição é mostrada na visualização juntamente com o histórico de sinal para indicar quando o alerta já teria sido disparado. 

    c. Para especificar a duração do tempo, escolha **Período** juntamente com a frequência com que o alerta deve ser executado, selecionando **Frequência**.

    ![Configurar lógica de sinal para métrica multidimensional](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Alertas de Log*: verifique se **Tipo de Recurso** é uma fonte de análise como *Log Analytics* ou *Application Insights* e se o tipo de sinal é **Log**; em seguida, depois de escolher o **recurso** apropriado, clique em *Concluído*. Em seguida, use o botão **Adicionar critérios** para exibir uma lista de opções de sinais disponíveis para o recurso e na opção **Pesquisa de logs personalizada**para o serviço de monitoramento de log escolhido como *Log Analytics* ou *Application Insights*.

   ![Selecione um recurso – pesquisa de logs personalizada](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Listas de alertas podem importar consulta analítica como tipo de sinal - **Log (Consulta Salva)**, como mostrado na ilustração acima. Dessa forma, os usuários podem aperfeiçoar sua consulta no Analytics e, em seguida, salvá-la para uso futuro em alertas - mais detalhes sobre o uso de consulta salvas disponível em [usando a pesquisa de log no Log Analytics](../log-analytics/log-analytics-log-searches.md) ou [consulta compartilhada na análise do Application Insights](../log-analytics/log-analytics-overview.md). 

9.  *Alertas de Log*: depois de selecionado, a consulta de alerta poderá ser declarada no campo **Consulta de Pesquisa**. Se a sintaxe de consulta estiver incorreta, o campo exibirá o erro em vermelho. Se a sintaxe de consulta estiver correta – para referência, os dados históricos da consulta indicada serão mostrados como um gráfico com a opção de ajustar a janela de tempo das últimas seis horas até a última semana.

 ![Configurar regra de alerta](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > A visualização de dados históricos somente poderá ser mostrada se os resultados da consulta tiverem detalhes de tempo. Se a consulta resultar em dados resumidos ou em valores de coluna específicos, isso será mostrado como um único gráfico.

    >  Para o tipo de medida da métrica de alertas de log usando o Application insights, você pode especificar qual variável específica para agrupar os dados usando a opção **Agregar**; conforme ilustrado abaixo:

    ![opção de agregação](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Alertas de Log*: com a visualização exibida, a **Lógica de Alerta** poderá ser selecionada nas opções de condição, agregação e, finalmente, limite mostradas. Finalmente, especifique na lógica, o tempo para avaliar a condição especificada, usando a opção **Período**. Juntamente com frequência em que o Alerta deve ser executado, selecionando **Frequência**.
Para **Alertas de Log**, os alertas podem ser baseados em:
   - *Número de Registros*: um alerta será criado se a contagem de registros retornada pela consulta for maior ou menor que o valor fornecido.
   - *Medição de Métrica*: um alerta será criado se cada *valor agregado* nos resultados exceder o valor limite fornecido e estiver *agrupado por* valor escolhido. O número de violações de um alerta é o número de vezes que o limite é excedido no período escolhido. Especifique o Total de violações para qualquer combinação de violações no conjunto de resultados ou as Violações consecutivas para exigir que as violações ocorram em amostras consecutivas. Saiba mais sobre [Alertas de Log e seus tipos](monitor-alerts-unified-log.md).

    > [!TIP]
    > Atualmente em alertas – alertas de pesquisa de logs podem usar valores de *período* e *frequência* personalizados em minuto(s). Os valores podem variar de 5 minutos a 1440 minutos (ou seja) 24 horas. Então, se você desejar que o período de alerta seja de três horas, converta-o em minutos, ou seja, 180 minutos, antes de usar

11. Na segunda etapa, defina um nome para o alerta no campo **Nome da regra de alerta** juntamente com uma **Descrição**, detalhando as especificações do alerta, e o valor de **Gravidade** nas opções fornecidas. Esses detalhes são reutilizados em todos os emails de alerta, notificações ou pushs realizados pelo Azure Monitor. Além disso, o usuário pode optar por ativar a regra de alerta imediatamente na criação, ativando adequadamente a opção **Habilitar regra após a criação**.

    Para **Alertas de Log** apenas, algumas funcionalidades adicionais estão disponíveis em detalhes do alerta:

    - **Suprimir Alertas**: quando você ativa a supressão da regra de alerta, as ações da regra são desabilitadas durante um período definido após a criação de um novo alerta. A regra ainda é executada e cria registros de alerta quando os critérios são atendidos. Permitindo que você tenha tempo para corrigir o problema sem executar ações duplicadas.

        ![Suprimir Alertas para Alertas de Log](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Especifique um valor de supressão de alerta maior do que a frequência do alerta para garantir que as notificações sejam interrompidas sem sobreposição

12. Na terceira e última etapa, especifique se algum **Grupo de Ação** precisa ser acionado para a regra de alerta quando a condição de alerta é atendida. Você pode escolher qualquer Grupo de Ação existente com alerta ou criar um novo Grupo de Ação. De acordo com o Grupo de Ação selecionado, quando o alerta for disparado, o Azure vai: enviar email(s), enviar SMS(s), chamar Webhook(s), corrigir o uso de Runbooks do Azure, enviar por push para a ferramenta ITSM, etc. Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md).

    Para **Alertas de Log**, algumas funcionalidades adicionais estão disponíveis para substituir as ações padrão:

    - **Notificação por Email**: substitui o *assunto do email* no email, enviado pelo Grupo de Ação, se há uma ou mais ações de email no Grupo de Ação. Não é possível modificar o corpo do email e esse campo **não** se destina ao endereço de email.
    - **Incluir conteúdo personalizado do JSON**: substitui o webhook JSON usado pelos Grupos de Ação se há uma ou mais ações de webhook no Grupo de Ação. O usuário pode especificar o formato do JSON a ser usado para todos os webhooks configurados no Grupo de Ação associado; para obter mais informações sobre formatos de webhook, consulte [Ação de webhook para Alertas de Log](monitor-alerts-unified-log-webhook.md). A opção Testar Webhook é fornecida para verificar o formato e o processamento pelo destino usando o JSON de exemplo e essa opção rotulada destina-se apenas a fins de **teste**.

        ![Substituições de ação para alertas de Log](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Para que a opção **Testar Webhook** funcione, o ponto de extremidade deve dar suporte a [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/), e o usuário pode usar o proxy CORS para resolver problemas de "Nenhum cabeçalho Access-Control-Allow-Origin"

13. Se todos os campos forem válidos e tiverem um tique verde, o botão **criar regra de alerta** poderá ser clicado e o alerta será criado no Azure Monitor – Alertas. Todos os alertas podem ser exibidos no painel do Alertas.

    ![Criação de regra](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="view-your-alerts-in-azure-portal"></a>Exibir os alertas no Portal do Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e na seção MONITOR, escolha **Alertas**.  

2. O **Painel de Alertas** é exibido, contendo todos os alertas do Azure unificados e exibidos em um painel singular ![Painel de Alerta](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Na parte superior da esquerda para a direita, o painel mostra uma visão rápida do o seguinte, que pode ser clicado para exibir uma listagem detalhada:
    - *Alertas Disparados*: o número de alertas no momento que atenderam à lógica e estão no estado disparado
    - *Total de Regras de Alerta*: o número de regras de alerta criadas e no subtexto, o número que está habilitado no momento 
    

        > [!NOTE]
        > Para garantir um painel consistente com detalhes sobre todos os alertas acionados, incluindo alertas do log para insights de aplicativos e análises do log, o [Alerta unificado avançado (versão prévia)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) deverá ser usado
  
  
4. É mostrada uma lista de todos os alertas disparados nos quais o usuário pode clicar para exibir detalhes
5. Para ajudar a localizar especificações dos alertas, é possível usar as opções suspensas na parte superior para filtrar as especificações *assinatura, grupo de recursos e/ou recurso*. Além disso, para qualquer alerta não resolvido, é possível usar a opção *Filtrar alerta* para localizar a palavra-chave fornecida, alertas correspondentes específicos com *nome, critérios de alerta, grupo de recursos e recurso de destino*

## <a name="managing-your-alerts-in-azure-portal"></a>Gerenciando alertas no Portal do Azure
1. No [portal](https://portal.azure.com/), selecione **Monitor** e na seção MONITOR, escolha **Alertas**.  
2. Selecione o botão **Gerenciar regras** na barra superior, para navegar até a seção de gerenciamento de regra, na qual todas as regras de alerta criadas são listadas, incluindo os alertas que foram desabilitados.
3. Para localizar regras de alerta específicas, é possível usar os filtros da lista suspensa na parte superior, que mostram uma lista reduzida de especificações *assinatura, grupos de recursos e/ou recurso*. Como alternativa ao uso do painel de pesquisa acima da lista de regras de alerta marcada como *Filtrar alertas*, é possível fornecer uma palavra-chave, que é correspondida a *nome do alerta, condição e o recurso de destino*, para mostrar apenas as regras de correspondência.
   ![Regras de Gerenciamento de Alerta](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Para exibir ou modificar a regra de alerta específica, clique em seu nome que será mostrado como um link clicável.
5. O alerta definido é mostrado, na estrutura de três estágios: 1) Condição do Alerta 2) Detalhe do Alerta 3) Grupo de Ação. **Critérios de Destino** pode ser clicado para modificar a lógica do alerta ou um novo critério pode ser adicionado após o uso do ícone de lixeira para excluir a lógica anterior. Da mesma forma, na seção de detalhes do alerta, **Descrição** e **Gravidade** podem ser modificadas. Além disso, o Grupo de Ação pode ser alterado ou um novo Grupo de Ação pode ser criado para ser vinculado ao alerta, usando o botão **Novo grupo de ação**.

   ![Modificar regra de alerta](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Usando o painel superior, as alterações no alerta podem incluir de forma refletiva: **Salvar** para confirmar alterações feitas no alerta, **Descartar** para retornar sem confirmar as alterações feitas no alerta, **Desabilitar** para desativar o alerta e, nesse caso, ele não será mais executado nem disparará nenhuma ação. E, finalmente, **Excluir** para remover permanentemente toda a regra de alerta do Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os novos [alertas de métrica quase em tempo real](monitoring-near-real-time-metric-alerts.md)
- Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
- Saiba mais sobre os [Alertas de log nos alertas do Azure](monitor-alerts-unified-log.md)
- [Saiba mais sobre os alertas do log de atividade na experiência Alertas (Versão Prévia)](monitoring-activity-log-alerts-new-experience.md)

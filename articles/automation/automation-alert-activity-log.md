---
title: Monitorar runbooks de Automação do Azure com alertas de logs de atividade
description: Este artigo mostra como monitorar runbooks de Automação do Azure com o log de atividades
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a0bd291af98477308cda898580fd52e33b1e6bbd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356930"
---
# <a name="monitoring-runbooks-with-azure-activity-log-alerts"></a>Monitorando runbooks com alertas do log de Atividades do Azure

Neste artigo, você aprenderá como criar alertas com base no status da conclusão de runbooks.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Fazer logon no Azure em https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Os alertas permitem que você defina uma condição para monitorar e uma ação a ser tomada quando essa condição for atendida.

No portal do Azure, navegue até **Todos os serviços** e selecione **Monitor**. Na página Monitor, selecione **Alertas** e clique em **+ Nova Regra de Alerta**.

### <a name="define-the-alert-condition"></a>Definir a condição de alerta

1. Em **1. Defina a condição de alerta**, clique em **+ Selecionar destino**. Escolha sua assinatura e, em **Filtrar por tipo de recurso**, selecione **Contas de Automação**. Escolha sua Conta de Automação e clique em **Concluído**.

   ![Selecionar um recurso para o alerta](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Configurar critérios de alerta

1. Clique em **+ Adicionar critérios**. Selecione **Métricas** para o **Tipo de sinal** e escolha **Total de trabalhos** na tabela.

1. A página **Configurar lógica de sinal** é onde você define a lógica que aciona o alerta. Sob o gráfico de histórico, são apresentadas duas dimensões, **Nome do Runbook** e **Status**. As dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar os resultados. Para **Nome do Runbook**, selecione o runbook sobre o qual você deseja alertar ou deixe em branco para alertar em todos os runbooks. Para **Status**, selecione um status na lista suspensa que deseja monitorar. Os valores de nome e status de runbook que aparecem na lista suspensa são somente para trabalhos que foram executados na semana anterior.

   Se quiser alertar sobre o status ou o runbook que não é mostrado na lista suspensa, clique em **\+** ao lado da dimensão. Isso abre uma caixa de diálogo que permite que você insira um valor personalizado, que não foi emitido para a dimensão recentemente. Se você inserir um valor que não existe uma propriedade, o alerta não será disparado.

1. Em **Lógica de alerta**, defina a condição e o limite para o alerta. Uma visualização da sua condição definida é mostrada abaixo.

1. Em **Avaliado com base em**, selecione o intervalo de tempo para a consulta e frequência e a frequência de execução dessa consulta. Por exemplo, se você escolher **Nos últimos 5 minutos** para **Período** e **Cada 1 minuto** para **Frequência**, o alerta procurará o número de runbooks que atenderam aos seus critérios últimos 5 minutos. Essa consulta é executado a cada minuto, e quando os critérios de alerta definidos não forem mais encontrados em um intervalo de 5 minutos, o alerta será resolvido automaticamente. Ao terminar, clique em **Concluído**.

   ![Selecionar um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir os detalhes do alerta

1. Em **2. Definir os detalhes do alerta**, dê ao alerta um nome e descrição fáceis. Defina a **Gravidade** para coincidir com a condição de alerta. Há cinco gravidades variando de 0 a 5. Os alertas são tratados da mesma forma, independentemente da gravidade, e você pode combinar a gravidade para que corresponda à sua lógica de negócios.

1. Na parte inferior da seção, há um botão que permite que você habilite a regra após a conclusão. Por padrão, as regras são habilitadas no momento da criação. Se selecionar Não, você poderá criar o alerta, e ele será criado em um estado **Desabilitado**. Na página **Regras** no Azure Monitor, você poderá selecioná-lo e clicar em **Habilitar** para habilitar o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a ser executada

1. Em **3. Definir o grupo de ação**, clique em **+ Novo grupo de ação**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. Podem incluir, mas não estão limitados a notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../monitoring-and-diagnostics/monitoring-action-groups.md).

1. Na caixa **Nome do grupo de ação** caixa, dê a ele um nome fácil e curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

1. Na seção **Ações** em **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**.

1. Na página **Email/SMS/Envio/Voz** página, dê um nome a ele. Marque a caixa de seleção **Email** e insira um endereço de email válido a ser usado.

   ![Configurar o grupo de ação de email](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **Ok** na página **Email/SMS/Envio/Voz** para fechá-la e clique em **Ok** para fechar a página **Adicionar grupo de ação**. O nome especificado nesta página é salvo como o **NOME DA AÇÃO**.

1. Quando concluir, clique em **Salvar**. Isso cria a regra que o alerta quando um runbook foi concluído com um determinado status.

> [!NOTE]
> Na adição de um endereço de email a um Grupo de Ações, um email de notificação é enviado informando que o endereço foi adicionado a um Grupo de Ações.

## <a name="notification"></a>Notificação

Quando os critérios de alerta são atendidos, o grupo de ações executa a ação definida. No exemplo deste artigo, um email é enviado. A imagem a seguir é um exemplo de um email que você recebe após o alerta ser disparado:

![Alerta de email](./media/automation-alert-activity-log/alert-email.png)

Depois que a métrica não estiver mais fora do limite definido, o alerta será desativado, e o grupo de ações executará a ação definida. Se um tipo de ação de email for selecionado, um email de resolução será enviado informando que ele foi resolvido.

## <a name="next-steps"></a>Próximas etapas

Continue e avance para o seguinte artigo para saber mais sobre as outras maneiras como você pode integrar alertas à sua Conta de Automação.

> [!div class="nextstepaction"]
> [Usar um alerta para disparar um runbook de Automação do Azure](automation-create-alert-triggered-runbook.md)
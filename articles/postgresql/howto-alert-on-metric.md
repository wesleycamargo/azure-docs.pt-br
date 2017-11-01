---
title: "Configurar alertas de métricas para o Banco de Dados do Azure para PostgreSQL no Portal do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar e acessar os alertas de métricas para o Banco de Dados do Azure para PostgreSQL usando o Portal do Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 3a09be8131b57381eb470027a134109c116467ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Usar o Portal do Azure para configurar alertas de métricas no Banco de Dados do Azure para PostgreSQL 

Este artigo mostra como configurar alertas do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure. Você pode receber um alerta com base em métricas de monitoramento para seus serviços do Azure.

O alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite atribuído por você. Ele dispara tanto quando a condição é atendida pela primeira vez e quanto posteriormente, quando essa condição não está mais sendo atendida. 

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:
* Enviar notificações por email para o administrador e coadministradores de serviços.
* Enviar um email para outros emails que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre as regras de alerta usando:
* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [CLI (Interface da linha de comando)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure
1. No [Portal do Azure](https://portal.azure.com/), selecione o servidor do Banco de Dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Regras de alerta** como mostrado abaixo:

   ![Selecionar Regras de alerta](./media/howto-alert-on-metric/1-alert-rules.png)

3. Selecione **Adicionar alerta de métrica** (ícone +). 

4. A página **Adicionar regra** é aberta, conforme mostrado abaixo.  Preencha as informações obrigatórias:

   ![Formulário Adicionar alerta de métrica](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Configuração | Descrição  |
   |---------|---------|
   | Nome | Forneça um nome para a regra de alerta. Esse valor é enviado no email de notificação de alerta. |
   | Descrição | Forneça uma breve descrição da regra de alerta. Esse valor é enviado no email de notificação de alerta. |
   | Alerta ativado | Escolha **Métricas** para esse tipo de alerta. |
   | Assinatura | Este campo será pré-populado com a assinatura que hospeda seu Banco de Dados do Azure para PostgreSQL. |
   | Grupo de recursos | Este campo será pré-populado com o grupo de recursos do seu Banco de Dados do Azure para PostgreSQL. |
   | Recurso | Este campo será pré-populado com o nome do seu Banco de Dados do Azure para PostgreSQL. |
   | Métrica | Selecione a métrica para a qual você deseja emitir um alerta. Por exemplo, **Percentual de armazenamento**. |
   | Condição | Escolha a condição de comparação da métrica. Por exemplo, **Maior que**. |
   | Limite | Valor de limite para a métrica, por exemplo 85 (porcentagem). |
   | Período | O período durante o qual a regra de métrica deverá ser atendida antes que o alerta seja disparado. Por exemplo, **Nos últimos 30 minutos**. |

   Com base no exemplo, o alerta procura por percentual de Armazenamento acima 85% durante um período de 30 minutos. Esse alerta é disparado quando o percentual médio de Armazenamento estiver acima de 85% por 30 minutos. Após a ocorrência do primeiro disparo, ele disparará novamente quando o percentual médio de Armazenamento estiver abaixo de 85% durante 30 minutos.

5. Escolha o método de notificação que você deseja para a regra de alerta. 

   Marque a opção **Proprietários, contribuidores e leitores** se quiser que os administradores e coadministradores da assinatura recebam um email quando o alerta disparar.

   Se você quiser que outros emails recebam uma notificação quando o alerta for disparado, adicione-os ao campo **Emails de administradores adicionais**. Vários emails separados com ponto e vírgula – *email@contoso.com;email2@contoso.com*

   Opcionalmente, forneça um URI válido no campo **Webhook** se você quiser chamá-lo quando o alerta for disparado.

6. Selecione **OK** para criar o alerta.

   Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas
Depois de criar um alerta, você poderá selecioná-lo e executar as seguintes ações:

* Exibir um gráfico mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [configurar webhooks em alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Tenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.

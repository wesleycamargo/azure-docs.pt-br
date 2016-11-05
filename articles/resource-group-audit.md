---
title: Operações de auditoria com o Gerenciador de Recursos | Microsoft Docs
description: Use o log de atividade no Gerenciador de Recursos para examinar erros e ações do usuário. Mostra o Portal do Azure, PowerShell, CLI do Azure e REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz

---
# Operações de auditoria com o Gerenciador de Recursos
Com os logs de atividade, você pode determinar:

* quais operações foram executadas nos recursos em sua assinatura
* quem iniciou a operação (embora as operações iniciadas por um serviço de back-end não retornem um usuário como o chamador)
* quando a operação ocorreu
* o status da operação
* os valores de outras propriedades que podem ajudar você a pesquisar a operação

[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Este tópico concentra-se na auditoria de operações. Para saber mais sobre como usar os logs de atividade para solucionar problemas de uma implantação, confira [Solução de problemas de implantações de grupos de recursos no Azure](resource-manager-troubleshoot-deployments-portal.md).

Você pode recuperar informações dos logs de atividade por meio do Portal, do PowerShell, da CLI do Azure, da API REST do Insights ou da [Biblioteca .NET do Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Portal para exibir os logs de atividade
1. Para exibir os logs de atividade por meio do portal, selecione **Mais Serviços** e **Logs de Atividade**.
   
    ![selecionar logs de atividade](./media/resource-group-audit/select-audit-logs.png)
2. Na folha **Logs de Atividade**, você verá um resumo das operações recentes de todos os grupos de recursos em sua assinatura. Ele inclui uma lista das operações recentes.
   
    ![mostrar ações](./media/resource-group-audit/audit-summary.png)
3. Para restringir o número de operações exibidas, selecione condições diferentes. Por exemplo, a imagem a seguir mostra os campos **Intervalo de tempo** e **Evento iniciado por** alterados para exibir as ações realizadas por um determinado usuário ou aplicativo no mês passado.
   
    ![definir opções de filtragem](./media/resource-group-audit/set-filter.png)
4. Selecione **Aplicar** para exibir os resultados da consulta.
5. Se você precisar executar a consulta novamente mais tarde, selecione **Salvar** e nomeie a consulta.
   
    ![salvar consulta](./media/resource-group-audit/save-query.png)
6. Para filtrar automaticamente para um determinado recurso ou grupo de recursos, selecione **Log de atividades** da folha desse recurso. Observe que o log de atividades é automaticamente filtrado pelo recurso selecionado.
   
    ![filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)

## PowerShell para exibir os logs de atividade
1. Para recuperar as entradas de log, execute o comando **Get-AzureRmLog**. Forneça parâmetros adicionais para filtrar a lista de entradas. Se você não especificar uma hora de início e de término, as entradas da última hora retornarão. Por exemplo, para recuperar as operações de um grupo de recursos durante a execução na última hora:
   
        Get-AzureRmLog -ResourceGroup ExampleGroup
   
    O exemplo a seguir mostra como usar o log de auditoria para operações de pesquisa executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
   
    Outra opção é usar funções de data para especificar o intervalo de datas, como os últimos 14 dias.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
2. Dependendo da hora de início que você especificar, os comandos anteriores poderão retornar uma longa lista de operações para o grupo de recursos. Você pode, pelo fornecimento de critérios de pesquisa, filtrar os resultados para o que você está procurando. Por exemplo, se estiver tentando pesquisar como um aplicativo Web foi interrompido, você poderá executar o comando a seguir.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
   
    Que, para este exemplo, mostra que a ação de parada foi executada por someone@contoso.com.
   
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK
3. Você pode procurar as ações realizadas por um determinado usuário, mesmo para um grupo de recursos que não existe mais.
   
        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## CLI do Azure para exibir os logs de atividade
1. Para recuperar as entradas de log, execute o comando **azure group log show**.
   
        azure group log show ExampleGroup
2. É possível filtrar os resultados com um utilitário do JSON como o [jq](http://stedolan.github.io/jq/download/). O exemplo a seguir mostra como procurar operações que atualizaram um arquivo de configuração da Web.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"
3. Você pode procurar as ações de um usuário específico.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## API REST para exibir os logs de auditoria
As operações de REST para trabalhar com o log de atividade fazem parte da [API REST do Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar os eventos de log de atividade, confira [Listar os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Próximas etapas
* Os logs de atividade do Azure podem ser usados com o Power BI para obter mais informações sobre as ações em sua assinatura. Confira [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Exibir e analisar logs de atividade do Azure no Power BI e muito mais).
* Para aprender sobre como definir políticas de segurança, confira [Controle de acesso baseado em função do Azure](active-directory/role-based-access-control-configure.md).
* Para saber mais sobre os comandos para solucionar problemas de implantações, confira [Solucionando problemas de implantações de grupos de recursos no Azure](resource-manager-troubleshoot-deployments-portal.md).
* Para saber como impedir exclusões em um recurso para todos os usuários, confira [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0824_2016-->
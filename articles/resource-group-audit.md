<properties
	pageTitle="Operações de auditoria com o Gerenciador de Recursos | Microsoft Azure"
	description="Use o log de auditoria no Gerenciador de Recursos para analisar erros e ações do usuário. Mostra o Portal do Azure, PowerShell, CLI do Azure e REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="tomfitz"/>

# Operações de auditoria com o Gerenciador de Recursos

Com os logs de auditoria, você pode determinar:

- quais operações foram executadas nos recursos em sua assinatura
- quem iniciou a operação (embora as operações iniciadas por um serviço de back-end não retornem um usuário como o chamador)
- quando a operação ocorreu
- o status da operação
- os valores de outras propriedades que podem ajudar você a pesquisar a operação

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Este tópico concentra-se na auditoria de operações. Para saber mais sobre como usar os logs de auditoria para solucionar problemas, confira [Solução de problemas de implantações de grupos de recursos no Azure](resource-manager-troubleshoot-deployments-portal.md).

Você pode recuperar informações dos logs de auditoria por meio do Portal do Azure, do Azure PowerShell, da CLI do Azure, API REST do Insights ou da [Biblioteca .NET do Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Portal para exibir os logs de auditoria

1. Para exibir os logs de auditoria no portal, escolha **Procurar** e **Logs de Auditoria**.

    ![selecionar logs de auditoria](./media/resource-group-audit/select-audit-logs.png)

2. Na folha **Logs de Auditoria**, você verá um resumo das operações recentes para todos os grupos de recursos em sua assinatura. Ele incluirá uma representação gráfica do tempo e do status das operações, bem como uma lista das operações.

    ![mostrar ações](./media/resource-group-audit/audit-summary.png)

3. Para procurar um tipos específico de ação, você pode filtrar quais operações são exibidas na folha de logs de auditoria. Escolha **Filtrar**, na parte superior da folha.

    ![filtrar logs](./media/resource-group-audit/filter-logs.png)

4. Na folha **Filtro**, você pode selecionar várias condições diferentes para restringir o número de operações exibidas. Por exemplo, você pode ver todas as ações executadas por um usuário específico na semana passada.

    ![definir opções de filtragem](./media/resource-group-audit/set-filter.png)

Depois de atualizar a exibição dos logs de auditoria, você verá somente as operações que atendem à condição especificada. Essas configurações serão mantidas na próxima vez que você exibir os logs de auditoria. Portanto, talvez seja necessário alterar esses valores para ampliar a exibição das operações.

Também é possível filtrar automaticamente um determinado recurso, selecionando os logs de auditoria na folha desse recurso. No portal, escolha o recurso de auditoria e selecione **Logs de auditoria**.

![recursos de auditoria](./media/resource-group-audit/audit-by-resource.png)

Observe que o log de auditoria é automaticamente filtrado pelo recurso selecionado da semana passada.

![filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)

## PowerShell para exibir os logs de auditoria

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

## CLI do Azure para exibir os logs de auditoria

1. Para recuperar as entradas de log, execute o comando **azure group log show**.

        azure group log show ExampleGroup

2. É possível filtrar os resultados com um utilitário do JSON como o [jq](http://stedolan.github.io/jq/download/). O exemplo a seguir mostra como procurar operações que atualizaram um arquivo de configuração da Web.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. Você pode procurar as ações de um usuário específico.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## API REST para exibir os logs de auditoria

As operações REST para trabalhar com o log de auditoria fazem parte da [API REST do Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar os eventos de log de auditoria, veja [Listar os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Próximas etapas

- Os logs de auditoria do Azure podem ser usados com o Power BI para obter mais informações sobre as ações em sua assinatura. Consulte [Exibir e analisar logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
- Para aprender sobre como definir políticas de segurança, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).
- Para saber mais sobre os comandos para solucionar problemas de implantações, consulte [Solucionando problemas de implantações de grupos de recursos no Azure](resource-manager-troubleshoot-deployments-portal.md).
- Para saber como impedir exclusões em um recurso para todos os usuários, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0615_2016-->
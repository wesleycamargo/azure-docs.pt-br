<properties 
	pageTitle="Operações de auditoria com o Gerenciador de Recursos | Microsoft Azure" 
	description="Use o log de auditoria no Gerenciador de Recursos para analisar erros e ações do usuário. Mostra o PowerShell, CLI do Azure e REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="tomfitz"/>

# Operações de auditoria com o Gerenciador de Recursos

Quando você encontra um problema durante a implantação ou durante o tempo de vida de sua solução, você precisa descobrir o que deu errado. O Gerenciador de Recursos fornece duas maneiras de descobrir o que aconteceu e por que isso aconteceu. Você pode usar comandos de implantação para recuperar informações sobre implantações e operações específicas. Ou então você pode usar os logs de auditoria para recuperar informações sobre implantações e outras ações realizadas durante o tempo de vida da solução. Este tópico concentra-se nos logs de auditoria.

O log de auditoria contém todas as ações executadas em seus recursos. Portanto, se um usuário em sua organização modificar um recurso, você poderá identificar a ação, o horário e o usuário.

Você pode recuperar informações de logs de auditoria por meio do Azure PowerShell, CLI do Azure, API REST ou do portal de visualização do Azure.

## PowerShell

Para recuperar as entradas de log, execute o comando **Get-AzureRmLog** (ou **Get-AzureResourceGroupLog** para versões do PowerShell anteriores à 1.0 Preview). Fornecer parâmetros adicionais para filtrar a lista de entradas.

O exemplo a seguir mostra como usar o log de auditoria para ações de pesquisa executadas durante o ciclo de vida da solução. Você pode ver quando a ação ocorreu e quem a solicitou.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00

Dependendo do tempo de início que você especificar, o comando anterior pode retornar uma longa lista de ações para esse grupo de recursos. Você pode, pelo fornecimento de critérios de pesquisa, filtrar os resultados para o que você está procurando. Por exemplo, se você estiver tentando pesquisar como um aplicativo Web foi interrompido, você pode executar o comando a seguir e ver que uma ação de parada foi realizada por someone@example.com.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

No próximo exemplo, vamos procurar ações com falha somente após a hora de início especificada. Também incluiremos o parâmetro **DetailedOutput** para ver as mensagens de erro.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-27T12:00 -Status Failed –DetailedOutput
    
Se esse comando retorna um número excessivo de entradas e propriedades, você pode concentrar seus esforços de auditoria recuperando a propriedade **properties**.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

Além disso, você pode refinar ainda mais os resultados examinando a mensagem de status.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## CLI do Azure

Para recuperar as entradas de log, você deve executar o comando **azure group log show**.

    azure group log show ExampleGroup

Você pode filtrar os resultados com um utilitário JSON como o [jq](http://stedolan.github.io/jq/download/). O exemplo a seguir mostra como procurar operações que envolveram a atualização de um arquivo de configuração da Web.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Você pode adicionar o parâmetro **–-last-deployment** para limitar as entradas retornadas somente às operações da última implantação.

    azure group log show ExampleGroup --last-deployment

Se a lista de operações da última implantação é muito longa, você pode filtrar os resultados para apenas operações que falharam.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## API REST

As operações REST para trabalhar com o log de auditoria são parte da [API REST do Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar os eventos de log de auditoria, consulte [Listar os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Portal de visualização

Você também pode exibir as operações registradas em log por meio do portal de visualização. É só selecionar a folha de logs de auditoria.

![selecionar logs de auditoria](./media/resource-group-audit/select-audit.png)

Em seguida, exiba a lista das operações mais recentes.

![mostrar ações](./media/resource-group-audit/show-actions.png)

Você pode selecionar qualquer operação para obter mais detalhes sobre ela.

## Próximas etapas

- Para aprender sobre a especificação de políticas de segurança, consulte [Gerenciar e auditar o acesso a recursos](./azure-portal/resource-group-rbac.md).
- Para aprender sobre a obtenção de acesso a uma entidade de serviço, consulte [Autenticar uma entidade de serviço com o Gerenciador de Recursos do Azure](resource-group-authenticate-service-principal.md).
- Para saber como realizar ações em um recurso para todos os usuários, consulte [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

<!---HONumber=Oct15_HO2-->
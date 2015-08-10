<properties 
	pageTitle="Vinculando recursos no Gerenciador de Recursos do Azure" 
	description="Crie um vínculo entre recursos em diferentes grupos de recursos no Gerenciador de Recursos do Azure." 
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
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Vinculando recursos no Gerenciador de Recursos do Azure

Após a implantação, você talvez queira consultar as relações ou os vínculos entre os recursos. Dependências informam a implantação, mas esse ciclo de vida termina na implantação. Após a conclusão da implantação, não há nenhuma relação identificada entre recursos dependentes.

Em vez disso, o Gerenciador de Recursos do Azure fornece uma nova funcionalidade chamada vinculação de recursos para estabelecer e consultar relações entre os recursos. Você pode determinar quais recursos estão vinculados a um recurso ou quais recursos estão vinculados a partir de um recurso.

O escopo de um vínculo de recursos pode ser uma assinatura, um grupo de recursos ou um recurso específico. Isso significa que os vínculos de recursos podem documentar relações que abrangem vários grupos de recursos. Quando você começar a decompor sua solução em vários grupos de recursos e vários modelos, ter um mecanismo para identificar esses vínculos de recursos será útil. Por exemplo, é comum colocar um banco de dados com seu próprio ciclo de vida em um grupo de recursos e um aplicativo com um ciclo de vida diferente em outro grupo de recursos. O aplicativo se conecta ao banco de dados para que haja um vínculo entre os recursos em grupos de recursos diferentes.

Todos os recursos vinculados devem pertencer à mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. Se um dos recursos vinculados for excluído ou movido, o proprietário do vínculo deverá remover o vínculo restante.

## Vinculando em modelos

O exemplo a seguir mostra um modelo que cria um recurso do tipo "Microsoft.AppService/apiapps" com um conjunto de relações unidirecionais para um site, um hub de notificação e bancos de dados SQL.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Vinculando com a API REST

Para definir um vínculo entre recursos implantados, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Substitua {subscription-id} pelo ID da assinatura. Substitua {resource-group}, {provider-namespace}, {resource-type} e {resource-name} pelos valores que identificam o primeiro recurso no vínculo. Substitua {link-name} pelo nome do vínculo a ser criado. Use 2015-01-01 para a versão de API.

Na solicitação, inclua um objeto que define o segundo recurso no vínculo:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

O elemento properties contém o identificador do segundo recurso.

Para obter mais exemplos, incluindo como recuperar informações sobre vínculos, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Próximas etapas

- Você também pode organizar seus recursos com marcas. Para saber mais sobre marcação de recursos, consulte [Usando marcas para organizar os recursos](resource-group-using-tags.md).
- Para obter uma descrição de como criar modelos e definir os recursos a serem implantados, consulte [Criando modelos](resource-group-authoring-templates.md).

<!---HONumber=July15_HO5-->
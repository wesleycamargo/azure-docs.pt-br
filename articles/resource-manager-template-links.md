<properties
   pageTitle="Modelo de vinculação de recursos do Gerenciador de Recursos | Microsoft Azure"
   description="Mostra o esquema de modelo do gerenciador de recursos para criar vínculos entre os recursos relacionados."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="tomfitz"/>

# Vínculos de recursos - esquema do modelo

Cria um vínculo entre dois recursos. O vínculo é aplicado a um recurso conhecido como o recurso de origem. O segundo recurso no vínculo é conhecido como o recurso de destino.

## Formato de esquema

Para criar um vínculo, adicione o esquema a seguir à seção de recursos do seu modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sim | **{namespace}/{tipo}/providers/links** | O tipo de recurso a ser criado. Os valores {namespace} e {tipo} referem-se ao tipo de recurso e ao namespace do provedor do recurso de origem. |
| apiVersion | enum | Sim | **01-01-2015** | A versão da API a ser usada para criar o recurso. |  
| name | cadeia de caracteres | Sim | **{recurso}/Microsoft.Resources/{nomevínculo}**<br /><br />até 64 caracteres<br />Não pode conter <, >, %, &,? ou caracteres de controle. | Um valor que especifica o nome do recurso da fonte e um nome para o vínculo. |
| dependsOn | array | Não | Uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. | A coleção de recursos do qual este vínculo depende. Se os recursos que estiver vinculando forem implantados no mesmo modelo, inclua esses nomes de recurso neste elemento para garantir que eles sejam implantados primeiro. | 
| propriedades | objeto | Sim | (mostrado abaixo) | Um objeto que identifica o recurso para o qual vincular e observações sobre o vínculo. |  

### properties object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ------- | ---- | ---------------- | -------- | ----------- |
| targetId | string | Sim | | O identificador do recurso de destino para o qual vincular. |
| HDInsight | cadeia de caracteres | Não | 512 caracteres | Descrição do bloqueio. |


## Como usar o recurso de lin

Aplique um vínculo entre dois recursos quando os recursos tiverem uma dependência que continua após a implantação. Por exemplo, um aplicativo pode se conectar a um banco de dados em outro grupo de recursos. Você pode definir essa dependência criando um vínculo do aplicativo para o banco de dados. Os vínculos permitem que você documente a relação entre dois recursos. Posteriormente, você ou outra pessoa em sua organização pode consultar um recurso para obter vínculos para descobrir como o recurso funciona com outros recursos.

Todos os recursos vinculados devem pertencer à mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. Se um dos recursos vinculados for excluído ou movido, o proprietário do vínculo deverá remover o vínculo restante.

Para trabalhar com vínculos por meio da REST, veja [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Use o comando do Azure PowerShell a seguir para ver todos os vínculos em sua assinatura. É possível fornecer outros parâmetros para limitar os resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

## Exemplos

O exemplo a seguir aplica um bloqueio somente leitura a um aplicativo Web.

    {
	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"hostingPlanName": {
      			"type": "string"
   		}
	},
	"variables": {
		"siteName": "[concat('site',uniqueString(resourceGroup().id))]"
	},
	"resources": [
	    {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
	    {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
	        "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
	    },
	    {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
	],
	"outputs": {}
    }


## Próximas etapas

- Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->
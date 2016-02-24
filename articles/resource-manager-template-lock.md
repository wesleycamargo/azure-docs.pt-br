<properties
   pageTitle="Modelo de bloqueios de recursos do Gerenciador de Recursos | Microsoft Azure"
   description="Mostra o esquema do Gerenciador de Recursos para a implantação de bloqueios de recursos por meio de um modelo."
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
   ms.date="01/21/2016"
   ms.author="tomfitz"/>

# Esquema do modelo de bloqueios de recursos

Cria um novo bloqueio em um recurso e em seus recursos filho.

## Formato de esquema

Para criar um bloqueio, adicione o esquema a seguir à seção de recursos do seu modelo.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sim | Para recursos: <br />**{namespace}/{tipo}/providers/locks**<br /><br />Para grupos de recursos:<br />**Microsoft.Authorization/locks** | O tipo de recurso a ser criado. |
| apiVersion | enum | Sim | **01-01-2015** | A versão da API a ser usada para criar o recurso. |  
| name | cadeia de caracteres | Sim | Para recursos:<br />**{recurso}/Microsoft.Authorization/{nomebloqueio}**<br /><br />Para grupos de recursos:<br />**{nomebloqueio}****<br /><br />até 64 caracteres<br />Não pode conter <, >, %, &, ? ou caracteres de controle. | Um valor que especifica o recurso a ser bloqueado e um nome para o bloqueio. |
| dependsOn | array | Não | Uma lista separada por vírgulas de nomes de um recurso ou identificadores exclusivos do recurso. | A coleção de recursos do qual o bloqueio depende. Se o recurso que estiver bloqueando for implantado no mesmo modelo, inclua o nome desse recurso neste elemento para garantir que o recurso seja implantado primeiro. | 
| properties | object | Sim | (mostrado abaixo) | Um objeto que identifica o tipo de bloqueio e observações sobre o bloqueio. | 

### properties object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | Sim | **CannotDelete** | O tipo de bloqueio a ser aplicado ao escopo. CanNotDelete permite a modificação, mas impede a exclusão. |
| HDInsight | cadeia de caracteres | Não | 512 caracteres | Descrição do bloqueio. |


## Como usar o recurso de bloqueio

Esse recurso é adicionado ao seu modelo para impedir as ações especificadas em um recurso. O bloqueio se aplica a todos os usuários e grupos. Normalmente, você aplica um bloqueio apenas por um tempo limitado, como quando um processo está sendo executado e você deseja garantir que alguém em sua organização não modifique ou exclua um recurso inadvertidamente.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações **Microsoft.Authorization/*** ou **Microsoft.Authorization/locks/***. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem permissão para realizar essas ações. Para saber mais sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

O bloqueio é aplicado ao recurso especificado e a todos os recursos filho.

É possível remover um bloqueio com o comando **Remove-AzureRmResourceLock** do PowerShell ou com a [operação de exclusão](https://msdn.microsoft.com/library/azure/mt204562.aspx) da API REST.

## Exemplos

O exemplo a seguir aplica um bloqueio contra exclusão a um aplicativo Web.

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

O próximo exemplo aplica um bloqueio contra exclusão ao grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Próximas etapas

- Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para obter mais informações sobre bloqueios, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0128_2016-->
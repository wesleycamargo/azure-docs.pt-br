<properties
   pageTitle="Modelo de armazenamento do Gerenciador de Recursos | Microsoft Azure"
   description="Mostra o esquema de contas de armazenamento do Gerenciador de Recursos."
   services="azure-resource-manager,storage"
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
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# Conta de armazenamento - esquema do modelo

Cria uma conta de armazenamento.

## Formato de esquema

Para criar uma conta de armazenamento, adicione o esquema a seguir à seção de recursos do seu modelo.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sim | **Microsoft.Storage/storageAccounts** | O tipo de recurso a ser criado. |
| apiVersion | enum | Sim | **15/6/2015** <br /> **1/5/2015-visualização** | A versão da API a ser usada para criar o recurso. | 
| name | cadeia de caracteres | Sim | Entre 3 e 24 caracteres, somente números e letras minúsculas | O nome da conta de armazenamento a ser criada. O nome deve ser exclusivo em todo o Azure. Considere o uso da função [uniqueString](resource-group-template-functions.md#uniquestring) com a convenção de nomenclatura, como mostrado no exemplo abaixo. |
| location | cadeia de caracteres | Sim | Para determinar as regiões válidas, veja [regiões com suporte](resource-manager-supported-services.md#supported-regions). | A região para hospedar a conta de armazenamento. |
| propriedades | objeto | Sim | (mostrado abaixo) | Um objeto que especifica o tipo de conta de armazenamento a ser criada.

### properties object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | cadeia de caracteres | Sim | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | O tipo de conta de armazenamento. Os valores permitidos correspondem ao armazenamento com Redundância Local Standard, armazenamento com Redundância de Zona Standard, armazenamento com Redundância Geográfica Standard, armazenamento com Redundância Geográfica com Acesso de Leitura Standard e armazenamento com Redundância Local Premium. Para obter informações sobre esses tipos de conta, veja [Replicação do Armazenamento do Azure](./storage/storage-redundancy.md). |

	
## Exemplos

O exemplo a seguir implementa uma conta de armazenamento com Redundância Local Standard com um nome exclusivo baseado na ID de grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
		         "location": "[resourceGroup().location]",
        	     "properties": 
        	     {
        		      "accountType": "Standard_LRS"
        	     }
	        }
	    ],
	    "outputs": {}
    }

## Próximas etapas

- Para obter informações gerais sobre armazenamento, veja [Introdução ao Armazenamento do Microsoft Azure](./storage/storage-introduction.md).
- Por ver exemplos de modelos que usam uma nova conta de armazenamento com uma Máquina Virtual, veja [Implantar uma VM simples do Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [Implantar uma VM simples do Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=Nov15_HO1-->
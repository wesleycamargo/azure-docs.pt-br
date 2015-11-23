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
	ms.date="11/06/2015" 
	ms.author="tomfitz"/>

# Vinculando recursos no Gerenciador de Recursos do Azure

Após a implantação, você talvez queira consultar as relações ou os vínculos entre os recursos. Dependências informam a implantação, mas esse ciclo de vida termina na implantação. Após a conclusão da implantação, não há nenhuma relação identificada entre recursos dependentes.

Em vez disso, o Gerenciador de Recursos do Azure fornece uma nova funcionalidade chamada vinculação de recursos para estabelecer e consultar relações entre os recursos. Você pode determinar quais recursos estão vinculados a um recurso ou quais recursos estão vinculados a partir de um recurso.

O escopo de um vínculo de recursos pode ser uma assinatura, um grupo de recursos ou um recurso específico. Isso significa que os vínculos de recursos podem documentar relações que abrangem vários grupos de recursos. Quando você começar a decompor sua solução em vários grupos de recursos e vários modelos, ter um mecanismo para identificar esses vínculos de recursos será útil. Por exemplo, é comum colocar um banco de dados com seu próprio ciclo de vida em um grupo de recursos e um aplicativo com um ciclo de vida diferente em outro grupo de recursos. O aplicativo se conecta ao banco de dados para que haja um vínculo entre os recursos em grupos de recursos diferentes.

Todos os recursos vinculados devem pertencer à mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. Se um dos recursos vinculados for excluído ou movido, o proprietário do vínculo deverá remover o vínculo restante.

## Vinculando em modelos

Para definir um vínculo entre os recursos em um modelo, consulte [Links de recursos - esquema do modelo](resource-manager-template-links.md).

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

<!---HONumber=Nov15_HO3-->
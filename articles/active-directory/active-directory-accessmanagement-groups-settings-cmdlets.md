<properties
	pageTitle="Cmdlets do Azure Active Directory para definir configurações de grupo | Microsoft Azure"
	description="Como gerenciar as configurações de grupos usando cmdlets do Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/21/2016"
	ms.author="curtand"/>


# Cmdlets do Azure Active Directory para definir configurações de grupo

As seguintes configurações para grupos unificados podem ser configuradas em seu diretório:

1.  Classificações: a lista separada por vírgulas de classificações que os usuários podem definir em um grupo. Exemplos seriam "Sigiloso", "Secreto" e "Confidencial".

2.  URL de Diretrizes de Uso: uma URL que aponta usuários para os termos de uso para o uso de Grupos Unificados, conforme definido pela sua organização. Essa URL será exibida na interface do usuário em que os usuários usam grupos.

3.  Criação de grupo habilitada: se nenhum, alguns ou todos os usuários têm permissão para criar Grupos Unificados. Quando definido como ativado, todos os usuários podem criar grupos. Quando definido como desativado, nenhum usuário pode criar grupos. Quando desativado, você pode também especificar um grupo de segurança cujos usuários ainda têm permissão para criar grupos.

Essas configurações são definidas usando objetos Configurações e SettingsTemplate. Inicialmente, você não verá objetos de Configurações em seu diretório. Isso significa que o diretório está configurado com as configurações padrão. Para alterar as configurações padrão, você criará um novo objeto de configurações usando um modelo de configurações. Modelos de configurações são definidos pela Microsoft.

Você pode baixar o módulo que contém os cmdlets usados para essas operações no [site Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## Criar configurações no nível do diretório

Estas etapas criam configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Se você não souber qual SettingTemplate usar, esse cmdlet retornará a lista de modelos de configurações:

	`Get-MsolAllSettingTemplate`

	![Lista de modelos de configurações](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Para adicionar uma URL de diretrizes de uso, primeiro você precisa obter o objeto SettingsTemplate que define o valor de URL da diretriz de uso; ou seja, o modelo de Group.Unified:

	`$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Em seguida, crie um novo objeto de configurações com base no modelo:

	`$setting = $template.CreateSettingsObject()`

4. Então, atualize o valor de diretriz de uso:

	`$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Por fim, aplique as configurações:

	`New-MsolSettings –SettingsObject $setting`

	![Adicionar uma URL de diretriz de uso](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Aqui estão as configurações definidas no Group.Unified SettingsTemplate.

 **Configuração** | **Descrição**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Tipo: Cadeia de Caracteres<li>Padrão: “” | Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados a Grupos Unificados.                
 <ul><li>EnableGroupCreation<li>Tipo: Booliano<li>Padrão: True | O sinalizador que indica se é permitida a criação de Grupo Unificado no diretório.                               
 <ul><li>GroupCreationAllowedGroupId<li>Tipo: Cadeia de Caracteres<li>Padrão: "" | O GUID do grupo de segurança que tem permissão para criar Grupos Unificados mesmo quando EnableGroupCreation = = false.
 <ul><li>UsageGuidelinesUrl<li>Tipo: Cadeia de Caracteres<li>Padrão: “” | Um link para as Diretrizes de Uso do Grupo.                                                                       

## Ler configurações no nível do diretório

Estas etapas leem configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Leia todas as configurações existentes do diretório:

	`Get-MsolAllSettings`

2. Leia todas as configurações para um grupo específico:

	`Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Ler as configurações do diretório específico, usando o GUID SettingId:

	`Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

	![GUID da ID de configurações](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## Atualizar configurações no nível do diretório

Estas etapas atualizam as configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Obter o objeto de Configurações existente:

	`$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obtenha o valor que você deseja atualizar:

	`$value = $Setting.GetSettingsValue()`

3. Atualize o valor:

	`$value["AllowToAddGuests"] = "false"`

4. Atualize a configuração:

	`Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## Remover configurações no nível do diretório

Esta etapa remove configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

	`Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## Referência de sintaxe de cmdlet

Você pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## Referência de objeto SettingsTemplate (objeto Group.Unified SettingsTemplate)

- "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "Um sinalizador booliano indicando se o recurso de criação do Grupo Unificado está ativado."

- "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "GUID do grupo de segurança que está na lista de permissões para criar Grupos Unificados."

- "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "Uma lista delimitada por vírgulas de valores de classificação válidos que pode ser aplicada aos Grupos Unificados."

- "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "Um link para as Diretrizes de Uso de Grupo."

name | type | defaultValue | description
----------  | ----------  | ---------  | ----------
"EnableGroupCreation" | "System.Boolean" | "true" | "Um sinalizador booliano que indica se o recurso de criação de Grupo Unificado está ativado."
"GroupCreationAllowedGroupId" | "System.Guid" | "" | "O GUID do grupo de segurança que está na lista de permissões para criar Grupos Unificados."
"ClassificationList" | "System.String" | "" | "Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados a Grupos Unificados."
"UsageGuidelinesUrl" | "System.String" | "" | "Um link para as Diretrizes de Uso do Grupo."

## Próximas etapas

Você pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Instruções adicionais do gerente de programa da Microsoft Rob de Jong estão disponíveis no [Blog de Grupos do Rob](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0622_2016-->
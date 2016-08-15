<properties
	pageTitle="Cmdlets de visualização do PowerShell do Azure Active Directory para gerenciamento de grupos no Azure AD | Microsoft Azure"
	description="Esta página fornece exemplos do PowerShell para ajudar no gerenciamento de seus grupos no Azure Active Directory"
    keywords="Azure AD, Azure Active Directory, PowerShell, Grupos, Gerenciamento de grupos"
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
	ms.date="07/11/2016"
	ms.author="curtand"/>

# Cmdlets de visualização do Azure Active Directory para gerenciamento de grupos

O documento a seguir fornece exemplos de como usar o PowerShell para gerenciar os grupos no Azure AD (Azure Active Directory). Ele também fornece informações sobre como configurar usando o módulo de visualização PowerShell do Azure AD. Primeiramente, você deve [baixar o módulo PowerShell do Azure AD](http://go.microsoft.com/fwlink/p/?linkid=236297).

## Instalando o módulo PowerShell do Azure AD

Para instalar o módulo de visualização PowerShell do Azure AD, use os seguintes comandos:

	PS C:\Windows\system32> install-module azureadpreview

Para verificar se o módulo de visualização foi instalado, use o seguinte comando:

	PS C:\Windows\system32> get-module azureadpreview

	ModuleType Version    Name                                ExportedCommands
	---------- -------    ----                                ----------------
	Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Agora você pode começar a usar os cmdlets do módulo. Para obter uma descrição completa dos cmdlets no módulo de visualização do Azure AD, confira a [documentação de referência online](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## Conectando-se ao diretório
Antes de começar a gerenciar grupos usando cmdlets de visualização do PowerShell do Azure AD, você deve conectar a sessão do PowerShell ao diretório que deseja gerenciar. Para isso, use o seguinte comando:

	PS C:\Windows\system32> Connect-AzureAD -Force

O cmdlet solicitará as credenciais que você deseja usar para acessar o diretório. Neste exemplo, estamos usando karen@drumkit.onmicrosoft.com para acessar o diretório de demonstração. O cmdlet retornará uma confirmação para mostrar que a conexão da sessão com o diretório foi bem-sucedida:

	Account                       Environment Tenant
	-------                       ----------- ------
	Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Agora você pode começar a usar os cmdlets de visualização do Azure AD para gerenciar grupos no diretório.

## Recuperando grupos
Para recuperar grupos existentes do diretório, você pode usar o cmdlet Get-AzureADGroups. Para recuperar todos os grupos no diretório, use o cmdlet sem parâmetros:

	PS C:\Windows\system32> get-azureadgroup

O cmdlet retornará todos os grupos no diretório conectado.

Você pode usar o parâmetro -objectID para recuperar um grupo específico, para o qual especifica o objectID do grupo:

	PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Agora, o cmdlet retornará o grupo cujo objectID corresponde ao valor do parâmetro inserido:

	DeletionTimeStamp            :
	ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
	ObjectType                   : Group
	Description                  :
	DirSyncEnabled               :
	DisplayName                  : Pacific NW Support
	LastDirSyncTime              :
	Mail                         :
	MailEnabled                  : False
	MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
	OnPremisesSecurityIdentifier :
	ProvisioningErrors           : {}
	ProxyAddresses               : {}
	SecurityEnabled              : True

Você pode procurar um grupo específico usando o parâmetro -filter. Esse parâmetro usa uma cláusula de filtro ODATA e retorna todos os grupos que correspondem ao filtro, como no exemplo a seguir:

	PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


	DeletionTimeStamp            :
	ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
	ObjectType                   : Group
	Description                  : Intune Administrators
	DirSyncEnabled               :
	DisplayName                  : Intune Administrators
	LastDirSyncTime              :
	Mail                         :
	MailEnabled                  : False
	MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
	OnPremisesSecurityIdentifier :
	ProvisioningErrors           : {}
	ProxyAddresses               : {}
	SecurityEnabled              : True

Observe que os cmdlets do PowerShell do AzureAD implementam o padrão de consulta OData. Mais informações podem ser encontradas [aqui](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## Criando grupos
Para criar um novo grupo no seu diretório, use o cmdlet New-AzureADGroup. Esse cmdlet cria um novo grupo de segurança chamado "Marketing":

	PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## Atualizando grupos
Para atualizar um grupo existente, use o cmdlet Set-AzureADGroup. Neste exemplo, estamos alterando a propriedade DisplayName do grupo "Intune Administrators". Primeiramente, vamos localizar o grupo usando o cmdlet Get-AzureADGroup e filtrar usando o atributo DisplayName:

	PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


	DeletionTimeStamp            :
	ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
	ObjectType                   : Group
	Description                  : Intune Administrators
	DirSyncEnabled               :
	DisplayName                  : Intune Administrators
	LastDirSyncTime              :
	Mail                         :
	MailEnabled                  : False
	MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
	OnPremisesSecurityIdentifier :
	ProvisioningErrors           : {}
	ProxyAddresses               : {}
	SecurityEnabled              : True

Em seguida, vamos mudar a propriedade Description para o novo valor "Intune Device Administrators":

	PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Agora, se localizarmos o grupo novamente, veremos que a propriedade Description foi atualizada para refletir o novo valor:

	PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


	DeletionTimeStamp            :
	ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
	ObjectType                   : Group
	Description                  : Intune Device Administrators
	DirSyncEnabled               :
	DisplayName                  : Intune Administrators
	LastDirSyncTime              :
	Mail                         :
	MailEnabled                  : False
	MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
	OnPremisesSecurityIdentifier :
	ProvisioningErrors           : {}
	ProxyAddresses               : {}
	SecurityEnabled              : True

## Excluindo grupos
Para excluir grupos do diretório, use o cmdlet Remove-AzureADGroup da seguinte maneira:

	PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## Gerenciando membros dos grupos
Se você precisa adicionar novos membros a um grupo, use o cmdlet Add-AzureADGroupMember. Esse comando adiciona um membro ao grupo Intune Administrators que usamos no exemplo anterior:

	PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro -ObjectId é o ObjectID do grupo ao qual queremos adicionar um membro, e -RefObjectId é o ObjectID do usuário que queremos adicionar como um membro ao grupo.

Para obter os membros existentes de um grupo, use o cmdlet Get-AzureADGroupMember, como neste exemplo:

	PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

	DeletionTimeStamp ObjectId                             ObjectType
	----------------- --------                             ----------
                  		72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                  		8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para remover o membro que adicionamos anteriormente ao grupo, use o cmdlet Remove-AzureADGroupMember, conforme mostrado aqui:

	PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para verificar as associações de um usuário do grupo, use o cmdlet Select-AzureADGroupIdsUserIsMemberOf. Esse cmdlet utiliza como seus parâmetros o ObjectId do usuário do qual verifica as associações de grupo e uma lista de grupos da qual verifica as associações. A lista de grupos deve ser fornecida na forma de uma variável complexa do tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck". Desse modo, devemos criar primeiro uma variável com esse tipo:

	PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Em seguida, fornecemos valores de groupIds para verificação no atributo "GroupIds" dessa variável complexa:

	PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Agora, se quisermos verificar as associações de grupo de um usuário com o ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea nos grupos em $g, devemos usar:

	PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

	OdataMetadata 																								Value
	-------------      																							-----
	https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String) 			{31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


O valor retornado é uma lista de grupos dos quais esse usuário é membro. Você também pode aplicar esse método para verificar a associação de Contatos, Grupos ou Entidades de Serviço para uma determinada lista de grupos, usando Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipalIsMemberOf

## Gerenciando proprietários de grupos
Para adicionar proprietários a um grupo, use o cmdlet Add-AzureADGroupOwner:

	PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro -ObjectId é o ObjectID do grupo ao qual queremos adicionar um proprietário, e -RefObjectId é o ObjectID do usuário que queremos adicionar como um proprietário do grupo.

Para recuperar os proprietários de um grupo, use o Get-AzureADGroupOwner:

	PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

O cmdlet retornará a lista de proprietários para o grupo especificado:

	DeletionTimeStamp ObjectId                             ObjectType
	----------------- --------                             ----------
                  		e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se você quer remover um proprietário de um grupo, use Remove-AzureADGroupOwner:

	PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## Próximas etapas

Você pode encontrar mais documentação do PowerShell do Azure Active Directory em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0803_2016-->
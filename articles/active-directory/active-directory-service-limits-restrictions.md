<properties 
	pageTitle="Restrições e limites de serviço do AD do Azure" 
	description="Restrições de uso e outros limites de serviço para o serviço Active Directory do Azure." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Restrições e limites de serviço do AD do Azure

Aqui estão as restrições de uso e outros limites de serviço para o serviço Active Directory do Azure. Se você estiver procurando o conjunto completo de limites de serviço do Microsoft Azure, consulte [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

## Diretórios

Um único usuário só pode ser associado a um máximo de 20 diretórios do Active Directory do Azure. Esse limite pode ser contatado em qualquer um dos exemplos a seguir:

- Um único usuário cria 20 diretórios.
- Um único usuário é adicionado a 20 diretórios como um membro.
- Um único usuário cria 10 diretórios e posteriormente é adicionado por outros 10 diretórios diferentes.

## Objetos

- Não há nenhum limite para assinantes Azure Active Directory Premium ou Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Microsoft Intune ou qualquer outro serviço online da Microsoft que dependa do Active Directory do Azure para serviços de diretório.
- Um máximo de 500.000 objetos pode ser usado em um único diretório com a edição do Active Directory do Azure Gratuito.
- Um usuário não administrador pode criar até 250 objetos.

##Extensões de esquema

Atualmente, as entidades "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" podem ser estendidas com atributos de valor único de tipo "cadeia de caracteres" ou "binário". Elas incluem as seguintes limitações:

- Extensões de tipo de cadeia de caracteres podem ter no máximo 256 caracteres.
- Extensões de tipo binário são limitadas a 256 bytes.
- É possível gravar 100 valores de extensão (entre todos os tipos e todos os aplicativos) em um único objeto.
- As extensões de esquema estão disponíveis apenas na versão de visualização 1.21 da Graph API. É preciso conceder acesso de gravação para poder registrar uma extensão.

## Aplicativos

Um máximo de 10 usuários podem ser proprietários de um único aplicativo.

## Grupos 

- Um máximo de 10 usuários podem ser proprietários de um único grupo.
- Qualquer número de objetos podem ser membros de um único grupo no Active Directory do Azure.


> [AZURE.NOTE]
> 
Há um limite para o número de objetos que você pode sincronizar do seu Active Directory local ao Active Directory do Azure. - Se você estiver usando o DirSync, o limite é de 15 mil usuários. - Se você estiver usando o Connect do AD do Azure, o limite é 50 mil usuários.

## Painel de acesso

- Não há nenhum limite para o número de aplicativos que podem ser vistos por cada usuário final no painel de acesso para os assinantes do das edições Premium ou Basic do AD do Azure ou do Enterprise Mobility Suite.
- Um máximo de 10 aplicativos SaaS pré-integrados (exemplos: Box, Salesforce ou Dropbox) podem ser vistos no painel de acesso para cada usuário final com a edição do Active Directory do Azure Gratuito. Os usuários finais podem ver mais de 10 aplicativos, se sua organização desenvolver aplicativos que foram integrados posteriormente ao Active Directory do Azure. Esse limite não se aplica às contas de administrador.

## Relatórios

Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Dados adicionais serão truncados.

## O que vem a seguir
- [Inscrever-se no Azure como uma organização](sign-up-organization.md)
- [Como as assinaturas do Azure estão associadas ao AD do Azure](active-directory-how-subscriptions-associated-directory.md)
- [Terminologia do AD do Azure](active-directory-terminology.md)


 

<!---HONumber=58_postMigration-->
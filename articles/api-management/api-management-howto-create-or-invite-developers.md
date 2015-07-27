<properties 
	pageTitle="Como gerenciar contas de usuário no Gerenciamento de API do Azure" 
	description="Saiba como criar ou convidar usuários no Gerenciamento de API do Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Como gerenciar contas de usuário no Gerenciamento de API do Azure

No Gerenciamento de API, os desenvolvedores são os usuários das APIs que você expõe utilizando o Gerenciamento d API. Este guia mostra como criar e convidar desenvolvedores a utilizarem as APIs e os produtos que você disponibilizar para eles com sua instância do Gerenciamento da API.

## <a name="create-developer"> </a>Criar um novo desenvolvedor

Para criar um novo desenvolvedor, clique em **Gerenciar** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API. Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][] no tutorial [Introdução ao Gerenciamento de API do Azure][].

![Portal do editor][api-management-management-console]

Clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e depois clique em **adicionar usuário**.

![Criar desenvolvedor][api-management-create-developer]

Insira o **Email**, a **Senha** e o **Nome** do novo desenvolvedor e clique em **Salvar**.

![Criar desenvolvedor][api-management-add-new-user]

Por padrão, as contas de desenvolvedor criadas recentemente têm o estado **Ativa** e são associadas ao grupo **Desenvolvedores**.

![Novo desenvolvedor][api-management-new-developer]

As contas de desenvolvedor que estão com estado **ativa** podem ser utilizadas para acessar todas as APIs nas quais estão inscritas. Para associar um desenvolvedor recém-criado a grupos adicionais, consulte [Como associar grupos a desenvolvedores][].

## <a name="invite-developer"> </a>Convidar um desenvolvedor

Para convidar um desenvolvedor, clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e depois clique em **Convidar Usuário**.

![Convidar desenvolvedor][api-management-invite-developer]

Insira o nome e o endereço de email do desenvolvedor e clique em **Convidar**.

![Convidar desenvolvedor][api-management-invite-developer-window]

Uma mensagem de confirmação é exibida, mas o desenvolvedor recém-convidado não aparece na lista até que aceite o convite.

![Confirmação de convite][api-management-invite-developer-confirmation]

Quando um desenvolvedor é convidado, um email é enviado a ele. O email é gerado utilizando um modelo e pode ser personalizado. Para obter mais informações, consulte [Configurar modelos de email][].

Após o convite ser aceito, a conta se torna ativa.

## <a name="block-developer"> </a>Desativar ou reativar uma conta de desenvolvedor

Por padrão, as contas de desenvolvedor criadas ou convidadas recentemente têm o estado **Ativa**. Para desativar uma conta de desenvolvedor, clique em **Bloquear**. Para reativar uma conta de desenvolvedor bloqueada, clique em **Ativar**. Uma conta de desenvolvedor bloqueada não pode acessar o portal do desenvolvedor ou chamar quaisquer APIs.

![Bloquear desenvolvedor][api-management-new-developer]

## <a name="next-steps"> </a>Próximas etapas

Após criar uma conta de desenvolvedor, você pode associá-la a funções e inscrevê-la em produtos e APIs. Para obter mais informações, consulte [Como criar e utilizar grupos][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-]: ./media/api-management-howto-create-or-invite-developers/api-management-.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Como criar e utilizar grupos]: api-management-howto-create-groups.md
[Como associar grupos a desenvolvedores]: api-management-howto-create-groups.md#associate-group-developer

[Introdução ao Gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de Gerenciamento de API]: api-management-get-started.md#create-service-instance
[Configurar modelos de email]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=July15_HO3-->
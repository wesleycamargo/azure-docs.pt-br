<properties 
	pageTitle="Como gerenciar contas de desenvolvedor no Gerenciamento de API do Azure" 
	description="Saiba como criar ou convidar desenvolvedores no Gerenciamento de API do Azure" 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Como gerenciar contas de desenvolvedor no Gerenciamento da API do Azure

No Gerenciamento da API (versão de visualização), os desenvolvedores são os usuários das APIs que você expõe utilizando o Gerenciamento da API. Este guia mostra como criar e convidar desenvolvedores a utilizarem as APIs e os produtos que você disponibilizar para eles com sua instância do Gerenciamento da API.

## Neste tópico

-   [Criar um novo desenvolvedor][Criar um novo desenvolvedor]
-   [Convidar um desenvolvedor][Convidar um desenvolvedor]
-   [Desativar ou reativar uma conta de desenvolvedor][Desativar ou reativar uma conta de desenvolvedor]
-   [Próximas etapas][Próximas etapas]

## <a name="create-developer"> </a>Criar um novo desenvolvedor

Para criar um novo desenvolvedor, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento da API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de Gerenciamento de API][Console de Gerenciamento de API]

Clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e depois clique em **adicionar usuário**.

![Criar desenvolvedor][Criar desenvolvedor]

Insira o **Email**, a **Senha** e o **Nome** do novo desenvolvedor e clique em **Salvar**.

![Criar desenvolvedor][1]

Por padrão, as contas de desenvolvedor criadas recentemente têm o estado **Ativa** e são associadas ao grupo **Desenvolvedores**.

![Novo desenvolvedor][Novo desenvolvedor]

As contas de desenvolvedor que estão com estado **ativa** podem ser utilizadas para acessar todas as APIs nas quais estão inscritas. Para associar um desenvolvedor recém-criado a grupos adicionais, consulte [Como associar grupos a desenvolvedores][Como associar grupos a desenvolvedores].

## <a name="invite-developer"> </a>Convidar um desenvolvedor

Para convidar um desenvolvedor, clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e depois clique em **Convidar Usuário**.

![Convidar desenvolvedor][Convidar desenvolvedor]

Insira o nome e o endereço de email do desenvolvedor e clique em **Convidar**.

![Convidar desenvolvedor][2]

Uma mensagem de confirmação é exibida, mas o desenvolvedor recém-convidado não aparece na lista até que aceite o convite.

![Confirmação de convite][Confirmação de convite]

> Quando um desenvolvedor é convidado, um email é enviado a ele. O email é gerado utilizando um modelo e pode ser personalizado. Para obter mais informações, consulte [Configurar modelos de email][Configurar modelos de email].

Após o convite ser aceito, a conta se torna ativa.

## <a name="block-developer"> </a> Desativar ou reativar uma conta de desenvolvedor

Por padrão, as contas de desenvolvedor criadas ou convidadas recentemente têm o estado **Ativa**. Para desativar uma conta de desenvolvedor, clique em **Bloquear**. Para reativar uma conta de desenvolvedor bloqueada, clique em **Ativar**. Uma conta de desenvolvedor bloqueada não pode acessar o portal do desenvolvedor ou chamar quaisquer APIs.

![Bloquear desenvolvedor][Novo desenvolvedor]

## <a name="next-steps"> </a>Próximas etapas

Após criar uma conta de desenvolvedor, você pode associá-la a funções e inscrevê-la em produtos e APIs. Para obter mais informações, consulte [Como criar e utilizar grupos][Como criar e utilizar grupos].

  [Criar um novo desenvolvedor]: #create-developer
  [Convidar um desenvolvedor]: #invite-developer
  [Desativar ou reativar uma conta de desenvolvedor]: #block-developer
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Criar desenvolvedor]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [Novo desenvolvedor]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Como associar grupos a desenvolvedores]: ../api-management-howto-create-groups/#associate-group-developer
  [Convidar desenvolvedor]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Confirmação de convite]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [Configurar modelos de email]: ../api-management-howto-configure-notifications/#email-templates
  [Como criar e utilizar grupos]: ../api-management-howto-create-groups

<!--HONumber=46--> 
 
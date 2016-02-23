<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="curtand"/>


#Gerenciamento de grupos de segurança no Active Directory do Azure

No Active Directory do Azure (AD do Azure), um dos principais recursos é a capacidade de gerenciar o acesso aos recursos. Esses recursos podem ser parte do diretório, como no caso de permissões para gerenciar objetos por meio de funções no diretório, ou recursos que são externos ao diretório, como aplicativos SaaS, serviços do Azure e sites do SharePoint ou recursos locais. Um grupo pode ser atribuído a um recurso pelo proprietário do recurso e, dessa forma, os membros desse grupo recebem o acesso ao recurso. A associação do grupo pode então ser gerenciada pelo proprietário do grupo. O proprietário do recurso delega efetivamente ao proprietário do grupo a permissão para atribuir usuários aos seus recursos.


##Como criar e gerenciar um grupo de segurança

**Para criar um grupo no portal do Azure**

1. No portal do Azure, clique em **Active Directory** e no nome do diretório de sua organização.
2. Clique na guia **Grupos**.
3. Na página Grupos, clique em **Adicionar grupo**.
4. Na janela **Adicionar grupo**, especifique o nome e a descrição de um grupo.
5. Essa tarefa pode ser concluída usando o portal de conta do Office 365, portal de conta do Windows Intune ou o portal do Azure, dependendo de quais serviços sua organização se inscreveu. Para obter mais informações sobre como usar portais para gerenciar o Active Directory do Azure, consulte [Administrando seu diretório Azure AD](active-directory-administer).

## Como atribuir ou remover usuários em um grupo de segurança

**Para adicionar um membro a um grupo no portal do Azure**

1. No portal do Azure, clique em **Active Directory** e no nome do diretório de sua organização.
2. Clique na guia **Grupos**.
3. Na página **Grupos**, clique no nome do grupo que você deseja adicionar a membros. Por padrão, é exibida a guia **Membros** do grupo selecionado.
4. Na página do grupo, clique em **Adicionar Membros**.
5. Na página **Adicionar Membros**, clique no nome do usuário ou em um grupo que você deseja adicionar como um membro desse grupo e verifique se esse nome é adicionado ao painel selecionado.


**Para remover um membro de um grupo no portal do Azure**

1. No portal do Azure, clique em **Active Directory** e no nome do diretório de sua organização.
2. Clique na guia **Grupos**.
3. Na página Grupos, clique no nome do grupo do qual você deseja remover membros.
4. Na página desse grupo, clique na guia **Membros**.
5. Na página desse grupo, clique no nome do membro que você deseja remover deste grupo e, em seguida, clique em **Remover**.
6. Confirme que você deseja remover esse membro do grupo clicando em **Sim** como a resposta à pergunta de confirmação de ação.


## Como usar uma regra para gerenciar dinamicamente os membros de um grupo de segurança

**Para habilitar a associação dinâmica de um determinado grupo, execute as seguintes etapas:**

1. No portal do Azure, na guia **Grupos**, selecione o grupo que deseja editar e, na guia **Configurar** esse grupo, defina a opção **Habilitar Associações Dinâmicas** como **Sim**.
2. Agora você pode configurar uma única regra simples para o grupo que controlará a associação dinâmica como para esse grupo de funções. Verifique se o botão de opção **Adicionar usuários onde** está marcado e, em seguida, selecione uma propriedade de usuário no menu suspenso (por exemplo, departamento, jobTitle, etc.)
3. Em seguida, selecione uma condição (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) e finalmente especificar um valor para a propriedade do usuário selecionado.

Por exemplo, se um grupo é atribuído a um aplicativo de SaaS (para mais informações, consulte Atribuir acesso de um grupo a um aplicativo SaaS no AD do Azure) e você habilita associações dinâmicas a este grupo, definindo uma regra na qual adicionar usuários em que é definido como o jobTitle that Equals(-eq)Sales Rep, todos os usuários no diretório do AD do Azure, cujos cargos são definidos como representante de vendas, terão acesso a esse aplicativo SaaS.

## Informações adicionais

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->
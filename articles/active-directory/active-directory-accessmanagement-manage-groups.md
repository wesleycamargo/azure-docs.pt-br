<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


#Gerenciamento de grupos de segurança no Active Directory do Azure


##Como criar e gerenciar um grupo de segurança

**Para criar um grupo do Portal de gerenciamento do Azure**

1. No Portal de Gerenciamento, clique em Active Directory e no nome do diretório da sua organização.
2. Clique na guia **Grupos**.
3. Na página Grupos, clique em**Adicionar grupo**.
4. Na janela **Adicionar grupo**, especifique o nome e a descrição de um grupo.
5. Essa tarefa pode ser concluída usando o portal de conta do Office 365, portal de conta do Windows Intune ou o portal de gerenciamento do Azure, dependendo de quais serviços sua organização se inscreveu. Para obter mais informações sobre como usar portais para gerenciar o Active Directory do Azure, consulte Administrando seu diretório Azure AD.

## Como atribuir ou remover usuários em um grupo de segurança

**Para adicionar um membro a um grupo do Portal de gerenciamento do Azure**

1. No Portal de Gerenciamento, clique em Active Directory e no nome do diretório da sua organização.
2. Clique na guia **Grupos**.
3. Na página **Grupos**, clique no nome do grupo que você deseja adicionar a membros. Por padrão, é exibida a guia **Membros** do grupo selecionado.
4. Na página do grupo, clique em **Adicionar membros**.
5. Na página **Adicionar membros**, clique no nome do usuário ou em um grupo que você deseja adicionar como um membro desse grupo e verifique se esse nome é adicionado ao painel selecionado.


**Para remover um membro de um grupo do Portal de gerenciamento do Azure**

1. No Portal de Gerenciamento, clique em Active Directory e no nome do diretório da sua organização.
2. Clique na guia **Grupos**.
3. Na página Grupos, clique no nome do grupo do qual você deseja remover membros.
4. Na página desse grupo, clique na guia **Membros**.
5. Na página desse grupo, clique no nome do membro que você deseja remover deste grupo e, em seguida, clique em**Remover**.
6. Confirme que você deseja remover esse membro do grupo clicando**Sim** como a resposta à pergunta de verificação de ação.


##Como usar uma regra para gerenciar dinamicamente os membros de um grupo de segurança
**Para habilitar a associação dinâmica de um determinado grupo, execute as seguintes etapas:**

1. No Portal de gerenciamento do Azure, sob a guia **Grupos**, selecione o grupo que você deseja editar, e, em seguida, na guia **Configurar** esse grupo, defina a chave **Habilitar Associações Dinâmicas** para **Sim**.
2. Agora você pode configurar uma única regra simples para o grupo que controlará a associação dinâmica como para esse grupo de funções. Verifique se o botão de opção **Adicionar usuários onde** está marcado e, em seguida, selecione uma propriedade de usuário no menu suspenso (por exemplo, departamento, jobTitle, etc.) 
3. Em seguida, selecione uma condição (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) e finalmente especificar um valor para a propriedade do usuário selecionado.
4. Por exemplo, se um grupo é atribuído a um aplicativo de SaaS (para mais informações, consulte Atribuir acesso de um grupo a um aplicativo SaaS no AD do Azure) e você habilita associações dinâmicas a este grupo, definindo uma regra na qual adicionar usuários em que é definido como o jobTitle that Equals(-eq)Sales Rep, todos os usuários no diretório do AD do Azure, cujos cargos são definidos como representante de vendas, terão acesso a esse aplicativo SaaS.

Estes são alguns tópicos que fornecem informações adicionais sobre o Active Directory do Azure

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->
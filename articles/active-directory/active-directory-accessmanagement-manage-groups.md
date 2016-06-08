<properties

	pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage groups to manage Azure users using Azure Active Directory."
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
	ms.date="05/26/2016"
	ms.author="curtand"/>


# Gerenciamento de grupos no Azure Active Directory

Um dos recursos de gerenciamento de usuários do Azure AD (Azure Active Directory) é a capacidade de criar grupos de usuários. Você pode então usar um grupo para atribuir licenças a uma classe de usuários. Você também pode usar grupos para atribuir a permissão de acesso a

- Recursos como objetos no diretório
- Recursos externos para o diretório, como aplicativos SaaS, serviços do Azure, sites do SharePoint ou recursos locais.

Além disso, um proprietário do recurso pode atribuir acesso a um recurso a um grupo do AD do Azure. Isso concede acesso ao recurso aos membros desse grupo. Em seguida, o proprietário do grupo gerencia a associação ao grupo. Efetivamente, o proprietário do recurso delega ao proprietário do grupo a permissão de atribuir usuários aos seus recursos.

## Como faço para criar um grupo?

Essa tarefa pode ser concluída usando o portal de conta do Office 365, portal de conta do Windows Intune ou o portal clássico do Azure, dependendo dos serviços que sua organização assinou. Para saber mais sobre como usar portais que não são do Azure para gerenciar o Azure Active Directory, confira [Administrar seu diretório do Azure AD](active-directory-administer.md).

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e selecione o nome do diretório de sua organização.

2. Selecione a guia **Grupos**.

3. Selecione **Adicionar Grupo**.

4. Na janela **Adicionar grupo**, especifique o nome e a descrição de um grupo.


## Como faço para adicionar ou remover usuários individuais em um grupo de segurança?

**Para adicionar um usuário individual a um grupo**

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e selecione o nome do diretório de sua organização.

2. Selecione a guia **Grupos**.

3. Abra o grupo ao qual você deseja adicionar membros. Por padrão, é exibida a guia **Membros** do grupo selecionado.

4. Selecione **Adicionar Membros**.

5. Na página **Adicionar Membros**, selecione o nome do usuário ou um grupo que você deseja adicionar como um membro desse grupo e verifique se o nome é adicionado ao painel **Selecionado**.


**Para remover um usuário individual de um grupo**

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e selecione o nome do diretório de sua organização.

2. Selecione a guia **Grupos**.

3. Abra o grupo do qual deseja remover membros.

4. Selecione a guia **Membros**, selecione o nome do membro que você deseja remover desse grupo e clique em **Remover**.

6. No prompt, confirme que você deseja remover esse membro do grupo.


## Como posso gerenciar a associação de um grupo dinamicamente?

No AD do Azure, você pode configurar com facilidade uma regra simples (uma regra que faça apenas uma comparação única) para determinar quais usuários devem ser membros do grupo. Por exemplo, se um grupo for atribuído a um aplicativo SaaS, e se você configurar uma regra para adicionar usuários com o cargo "Representante de vendas", todos os usuários no diretório AD do Azure com esse cargo terão acesso a esse aplicativo SaaS.

> [AZURE.NOTE] Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office 365. No momento, as associações de grupo aninhadas não têm suporte para atribuição com base em grupo de aplicativos.
>
> As associações dinâmicas de grupos exigem que uma licença do Azure AD Premium seja atribuída a
>
> - O administrador que gerencia a regra em um grupo
> - Todos os usuários selecionados pela regra para serem membros do grupo

**Para habilitar a associação dinâmica a um grupo**

1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e selecione o nome do diretório de sua organização.

2. Selecione a guia **Grupos** e abra o grupo que você deseja editar.

3. Selecione a guia **Configurar** e defina **Habilitar Associações Dinâmicas** como **Sim**.

4. Configure uma única regra simples para o grupo que controlará o funcionamento da associação dinâmica para esse grupo. Verifique se a opção **Adicionar usuários em que** está marcada e selecione uma propriedade de usuário na lista (por exemplo, departamento, jobTitle, etc.)

5. Em seguida, selecione uma condição (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) e finalmente especificar um valor para a propriedade do usuário selecionado.

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para a associação dinâmica de grupo, confira [Uso de atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Informações adicionais

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0601_2016-->
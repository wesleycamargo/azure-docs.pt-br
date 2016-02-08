<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Introdução | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Serviços de Domínio do Azure AD *(Visualização)* - Introdução

Este artigo percorre as etapas de configuração necessárias para habilitar os Serviços de Domínio do Azure AD para seu locatário do Azure AD.

## Etapa 1: Criar o grupo “AAD DC Administrators”
A primeira etapa é criar um grupo administrativo no seu locatário do Active Directory do Azure. Este grupo administrativo especial é chamado **AAD DC Administrators**. Os membros desse grupo terão privilégios administrativos nos computadores que estão associados pelo domínio dos Serviços de Domínio do Azure AD que você vai configurar. Depois do ingresso no domínio, este grupo será adicionado ao grupo “Administradores” nos computadores associados pelo domínio. Além disso, os membros desse grupo também poderão usar a área de trabalho remota para se conectar remotamente a computadores associados pelo domínio.

> [AZURE.NOTE] Você não poderá exercer privilégios de Administrador corporativo ou Administrador do domínio no domínio criado usando os Serviços de Domínio do Azure AD. Como este é um domínio gerenciado, esses privilégios são reservados pelo serviço e não serão disponibilizados aos usuários dentro do locatário. No entanto, você poderá usar o grupo de administrador especial criado nesta etapa de configuração para executar algumas operações privilegiadas, como adicionar computadores ao domínio, pertencer ao grupo de Administradores nos computadores associados pelo domínio, configurar a Política de grupo etc.

Nessa etapa de configuração, você cria o grupo e adiciona um ou mais usuários em seu locatário ao grupo. Execute as seguintes etapas para criar o grupo administrativo para os Serviços de Domínio do Azure AD:

1. Navegue no **portal de gerenciamento do Azure** (ou seja, [https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. No painel esquerdo, selecione o nó **Active Directory**.
3. Selecione o locatário do Azure AD (diretório) para o qual você deseja habilitar os Serviços de Domínio do Azure AD. Observe que você só pode criar um domínio para cada diretório do Azure AD.

    ![Selecionar um diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **Grupos**.
5. Clique em **Adicionar Grupo** no painel de tarefas na parte inferior da página para adicionar um grupo ao diretório.
6. Crie um grupo chamado **AAD DC Administrators**.

    > [AZURE.WARNING] Você deve criar um grupo com esse nome exato para permitir acesso nos Serviços de Domínio do Azure AD.

	![Criar grupo de administradores](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Você pode adicionar uma descrição para este grupo para permitir que outros usuários no seu locatário do Azure AD entendam que o grupo será usado para conceder privilégios administrativos nos Serviços de Domínio do Azure AD.
8. Após o grupo se criado, clique no nome dele para ver suas propriedades. Clique no botão **Adicionar membros** no painel inferior para adicionar usuários como membros deste grupo.
9. Na caixa de diálogo **Adicionar membros**, selecione os usuários que devem ser membros desse grupo e marque a caixa de seleção quando terminar.

    ![Adicionar usuários ao grupo de administradores](./media/active-directory-domain-services-getting-started/add-group-members.png)

---
[**Próxima etapa - Criar ou selecionar uma rede virtual do Azure.**](active-directory-ds-getting-started-vnet.md)

<!---HONumber=AcomDC_0128_2016-->
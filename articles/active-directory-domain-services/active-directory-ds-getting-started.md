<properties
    pageTitle="Serviços de Domínio do Azure AD: criar o grupo AAD DC Administrators | Microsoft Azure"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>


# <a name="get-started-with-azure-ad-domain-services"></a>Introdução aos Azure AD Domain Services

Este artigo percorre as tarefas de configuração necessárias para habilitar os Serviços de Domínio do Azure AD para seu locatário do Azure AD.

## <a name="task-1:-create-the-'aad-dc-administrators'-group"></a>Tarefa 1: Criar o grupo “AAD DC Administrators”
A primeira etapa é criar um grupo administrativo no seu locatário do Azure Active Directory. Este grupo administrativo especial é chamado **AAD DC Administrators**. Os membros desse grupo recebem privilégios administrativos nos computadores que ingressaram no domínio gerenciado dos Azure AD Domain Services. Em computadores que ingressaram no domínio, esse grupo é adicionado ao grupo "Administradores". Além disso, os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente a computadores ingressados no domínio.  

> [AZURE.NOTE] Você não tem privilégios de Administrador do Domínio ou Administrador Corporativo no domínio gerenciado usando os Serviços de Domínio do Azure AD. Em domínios gerenciados, esses privilégios são reservados pelo serviço e não serão disponibilizados aos usuários dentro do locatário. No entanto, você poderá usar o grupo de administradores especiais criado nessa tarefa de configuração para executar algumas operações privilegiadas. Essas operações incluem adicionar computadores ao domínio, pertencer ao grupo de Administradores em computadores que ingressaram no domínio, configurar a Política de Grupo, etc.

Nessa tarefa de configuração, você cria o grupo administrativo e adicionará um ou mais usuários em seu diretório ao grupo. Execute as seguintes etapas para criar o grupo administrativo para os Azure AD Domain Services:

1. Navegue até o **portal clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. No painel esquerdo, selecione o nó **Active Directory** .

3. Selecione o locatário do AD do Azure (diretório) para o qual você deseja habilitar os Serviços de Domínio do AD do Azure. Você só pode criar um domínio para cada diretório do Azure AD.

    ![Selecionar um diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **Grupos** .

5. Para adicionar um grupo ao seu locatário do Azure AD, clique em **Adicionar Grupo** no painel de tarefas na parte inferior da página.

    ![Botão Adicionar grupo](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Crie um grupo chamado **AAD DC Administrators**. Defina **GROUP TYPE** como **Segurança**.

    > [AZURE.WARNING] Para habilitar o acesso no domínio gerenciado pelo Azure AD Domain Services, crie um grupo com esse mesmo nome.

    ![Criar grupo de administradores](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Adicione uma descrição a este grupo para que outras pessoas entendam que este grupo será usado para conceder privilégios administrativos nos Azure AD Domain Services.

8. Após a criação do grupo, clique no nome do grupo para ver as propriedades desse grupo. Para adicionar usuários como membros desse grupo, clique no botão **Adicionar membros** no painel inferior.

    ![Botão Adicionar membros do grupo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Na caixa de diálogo **Adicionar membros** , selecione os usuários que devem ser membros desse grupo e marque a caixa de seleção quando terminar.

    ![Adicionar usuários ao grupo de administradores](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2:-create-or-select-an-azure-virtual-network"></a>Etapa 2: Criar ou selecionar uma rede virtual do Azure
A próxima tarefa de configuração é [criar ou selecionar uma rede virtual do Azure](active-directory-ds-getting-started-vnet.md).



<!--HONumber=Oct16_HO2-->



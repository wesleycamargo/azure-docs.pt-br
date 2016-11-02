<properties
    pageTitle="Serviços de Domínio do Azure Active Directory: guia de administração | Microsoft Azure"
    description="Criar uma UO (Unidade Organizacional) em um domínio gerenciado dos Serviços de Domínio do Azure AD"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="create-an-organizational-unit-(ou)-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma Unidade Organizacional (UO) em um domínio gerenciado dos Serviços de Domínio do Azure AD
Domínios gerenciados dos Serviços de Domínio do Azure AD incluem dois contêineres internos chamados 'Computadores do AADDC' e 'Usuários do AADDC', respectivamente. O contêiner 'Computadores do AADDC' tem objetos de computador para todos os computadores ingressados no domínio gerenciado. O contêiner 'Usuários do AADDC' inclui usuários e grupos no locatário do Azure AD. Ocasionalmente, pode ser necessário criar contas de serviço no domínio gerenciado para implantar cargas de trabalho. Para essa finalidade, você pode criar uma UO (unidade organizacional) personalizada no domínio gerenciado e criar contas de serviço dentro dessa UO. Este artigo mostra como criar uma UO em seu domínio gerenciado.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração do AD em uma máquina virtual ingressada no domínio para administração remota
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados remotamente usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Os administradores de locatários não têm privilégios para conectar-se a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Para administrar o domínio gerenciado, instale o recurso de ferramentas de administração do AD em uma máquina virtual ingressada no domínio gerenciado. Consulte o artigo intitulado [administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma Unidade Organizacional no domínio gerenciado
Agora que as Ferramentas Administrativas do AD estão instaladas na máquina virtual ingressada no domínio, podemos usar essas ferramentas para criar uma unidade organizacional no domínio gerenciado. Execute as seguintes etapas:

> [AZURE.NOTE] Somente os membros do grupo 'Administradores de DC do AAD' têm os privilégios necessários para criar uma nova UO. Certifique-se de executar as etapas a seguir como um usuário pertencente a esse grupo.

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas Administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique em **Centro Administrativo do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para exibir o domínio, clique no nome de domínio no painel à esquerda (por exemplo, 'contoso100.com').

    ![ADAC - exibir domínio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. No lado direito do painel **Tarefas**, clique em **Novo** no nó de nome de domínio. Neste exemplo, vamos clicar em **Novo** no nó 'contoso100(local)' no lado direito do painel **Tarefas**.

    ![ADAC - nova UO](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Você deve ver a opção de criar uma unidade organizacional. Clique em **Unidade Organizacional** para iniciar o diálogo **Criar Unidade Organizacional**.

6. Na caixa de diálogo **Criar Unidade Organizacional**, especifique um **Nome** para a nova UO. Forneça uma descrição curta para a UO. Você também pode definir o campo **Gerenciado Por** da UO. Para criar a UO personalizada, clique em **OK**.

    ![ADAC - criar caixa de diálogo da UO](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. A UO recém-criada agora deve aparecer no ADAC (Centro Administrativo do AD).

    ![ADAC - UO criada](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissions/security-for-newly-created-ous"></a>Permissões/segurança para UOs recém-criadas
Por padrão, são concedidos ao usuário (membro do grupo 'Administradores de DC do AAD') que criou a UO personalizada privilégios administrativos (controle total) sobre a UO. O usuário pode, em seguida, continuar e conceder privilégios a outros usuários ou ao grupo 'Administradores do AAD DC', conforme desejado. Como visto na captura de tela a seguir, o controle total sobre a nova unidade organizacional 'MyCustomOU', criada pelo usuário 'bob@domainservicespreview.onmicrosoft.com', é concedido a esse usuário.

 ![ADAC - segurança da nova UO](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notas sobre como administrar UOs personalizadas
Agora que você criou uma UO personalizada, pode criar usuários, grupos, computadores e contas de serviço nessa UO. Você não pode mover usuários ou grupos da UO 'Usuários de DC do AAD' para as UOs personalizadas.

> [AZURE.WARNING] As contas de usuário, de grupos, as contas de serviço e de objetos de computador que você criar em OUs personalizadas não ficam disponíveis no seu locatário do Azure AD. Em outras palavras, esses objetos não aparecem usando a API do Azure AD Graph ou na interface do usuário do Azure AD. Esses objetos só estarão disponíveis no seu domínio gerenciado dos Azure AD Domain Services.


## <a name="related-content"></a>Conteúdo relacionado

- [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Centro Administrativo do Active Directory: introdução](https://technet.microsoft.com/library/dd560651.aspx)

- [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)



<!--HONumber=Oct16_HO2-->



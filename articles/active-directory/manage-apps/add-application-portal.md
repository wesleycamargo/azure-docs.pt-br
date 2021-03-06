---
title: Adicionar um aplicativo a seu locatário do Azure Active Directory | Microsoft Docs
description: Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD (Azure Active Directory).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/24/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: af30568d3ab35b35d2a0e0bef00921debb1ea451
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917797"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Início Rápido: Adicionar um aplicativo a seu locatário do Azure Active Directory

O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que sua organização usa provavelmente estão na galeria. Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD (Azure Active Directory).

Depois que um aplicativo é adicionado ao locatário do Azure AD, você pode:

- Gerenciar o acesso de usuário ao aplicativo com uma política de acesso condicional.
- Configurar usuários para logon único no aplicativo com suas contas do Azure AD.

## <a name="before-you-begin"></a>Antes de começar

Para adicionar um aplicativo ao locatário, você precisará de:

- Uma assinatura do Azure AD
- Uma assinatura habilitada com logon único para seu aplicativo

Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure AD, administrador do aplicativo de nuvem ou administrador do aplicativo.

Para testar as etapas deste tutorial, recomendamos o uso de um ambiente que não seja de produção. Se não tiver um ambiente do Azure AD que não seja de produção, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Adicionar um aplicativo ao locatário do Azure AD

Para adicionar um aplicativo de galeria ao locatário do Azure AD:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

2. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](media/add-application-portal/open-enterprise-apps.png)

3. O painel **Todos os aplicativos** é aberto para mostrar uma amostra aleatória dos aplicativos em seu locatário do Azure AD. Selecione **Novo aplicativo** na parte superior do painel **Todos os aplicativos**.

    ![Novo aplicativo](media/add-application-portal/new-application.png)

4. No painel **Categorias**, você verá ícones na área **Aplicativos em destaque** que são uma amostra aleatória de aplicativos da galeria.  Para ver mais aplicativos, selecione **Mostrar mais**. No entanto, não recomendamos pesquisar dessa maneira, pois há milhares de aplicativos na galeria.

    ![Pesquisar por nome ou categoria](media/add-application-portal/categories.png)

5. Para pesquisar um aplicativo, em **Adicionar da galeria**, insira o nome do aplicativo que você deseja adicionar. Selecione o aplicativo nos resultados e selecione **Adicionar**. O exemplo a seguir mostra o formulário **Adicionar aplicativo** que aparece depois da pesquisa de github.com.

    ![Adicionar um aplicativo](media/add-application-portal/add-an-application.png)

6. No formulário específico do aplicativo, você pode alterar as informações de propriedade. Por exemplo, você pode editar o nome do aplicativo para atender às necessidades de sua organização. Este exemplo usa o nome **GitHub-test**.

7. Quando você terminar de fazer alterações nas propriedades, selecione **Adicionar**.

8. Uma página de introdução é exibida com as opções para configurar o aplicativo para a organização.

Você terminou de adicionar seu aplicativo. Fique à vontade para fazer um intervalo. As seções a seguir mostram como alterar o logotipo e editar outras propriedades do aplicativo.

## <a name="find-your-azure-ad-tenant-application"></a>Localizar seu aplicativo de locatário do Azure AD

Vamos supor que você teve que sair e agora você está retornando para continuar a configurar o aplicativo. A primeira coisa a fazer é localizar o aplicativo.

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione **Azure Active Directory**.

2. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.

3. No menu suspenso **Tipo de Aplicativo**, selecione **Todos os Aplicativos** e, sem seguida, **Aplicar**. Para saber mais sobre as opções de exibição, consulte [Exibir aplicativos de locatário](view-applications-portal.md).

4. Agora você pode ver uma lista de todos os aplicativos em seu locatário do Azure AD. A lista é uma amostra aleatória. Para ver mais aplicativos, selecione **Mostrar mais** uma ou mais vezes.

5. Para localizar rapidamente um aplicativo no locatário, insira o nome dele na caixa de pesquisa e selecione **Aplicar**. Este exemplo localiza o aplicativo de teste do GitHub adicionado anteriormente.

    ![Pesquisar um aplicativo](media/add-application-portal/find-application.png)


## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

Agora que você localizou o aplicativo, pode abri-lo e configurar suas propriedades.

Para editar as propriedades do aplicativo:

1. Selecione o aplicativo para abri-lo.
2. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-application-portal/edit-properties.png)

3. Reserve um tempo para entender as opções de entrada. As opções determinam como os usuários que foram atribuídos ou não ao aplicativo podem entrar nele. Além disso, as opções também determinam se um usuário pode ver o aplicativo no painel de acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - A **atribuição de usuário obrigatória** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para usuário** determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no inicializador do O365.

4. Use as tabelas a seguir para ajudar você a escolher as melhores opções para suas necessidades.

   - Comportamento de usuários **atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência do usuário atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários atribuídos podem entrar? | Os usuários atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | Sim | Sim  |
       | Sim | Sim | não  | Sim | não   |
       | Sim | não  | Sim | Sim | Sim  |
       | Sim | não  | não  | Sim | não   |
       | não  | Sim | Sim | não  | não   |
       | não  | Sim | não  | não  | não   |
       | não  | não  | Sim | não  | não   |
       | não  | não  | não  | não  | não   |

   - Comportamento de usuários **não atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência de usuário não atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários não atribuídos podem entrar? | Os usuários não atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | não  | não   |
       | Sim | Sim | não  | não  | não   |
       | Sim | não  | Sim | Sim | não   |
       | Sim | não  | não  | Sim | não   |
       | não  | Sim | Sim | não  | não   |
       | não  | Sim | não  | não  | não   |
       | não  | não  | Sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O usuário pode ver o aplicativo no painel de acesso e no iniciador de aplicativos do Office 365?

## <a name="use-a-custom-logo"></a>Usar um logotipo personalizado

Para usar um logotipo personalizado:

1. Crie um logotipo de 215 x 215 pixels e salve-o no formato PNG.
2. Como você já localizou seu aplicativo, selecione-o.
2. No painel esquerdo, selecione **Propriedades**.
4. Carregue o logotipo.
5. Quando terminar, selecione **Salvar**.

    ![Alterar o logotipo](media/add-application-portal/change-logo.png)


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a adicionar um aplicativo de galeria ao locatário do Azure AD. Você aprendeu a editar as propriedades de um aplicativo.

Agora, você está pronto para configurar o aplicativo para logon único.

> [!div class="nextstepaction"]
> [Configurar o logon único](configure-single-sign-on-portal.md)



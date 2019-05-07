---
title: Criar um registro de aplicativo do Azure AD e o usuário para o Azure Red Hat OpenShift | Microsoft Docs
description: Saiba como criar uma entidade de serviço, gerar uma URL de retorno de chamada de autenticação e o segredo do cliente e criar um novo usuário do Active Directory para testar aplicativos em seu cluster do Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078514"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Criar um registro de aplicativo do Azure AD e o usuário para o Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift precisa de permissões para executar tarefas em nome do seu cluster. Se sua organização ainda não tiver um registro de aplicativo do Azure Active Directory (Azure AD) para usar como a entidade de serviço, siga estas instruções para criar um.

Este tópico conclui com instruções sobre como criar um novo usuário do AD do Azure que você precisará acessar a aplicativos em execução no cluster do Azure Red Hat OpenShift.

Se você ainda não criou um locatário do AD do Azure, siga as instruções em [criar um locatário do AD do Azure para Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

## <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

Um aplicativo que deseja usar os recursos do Azure AD deve primeiro ser registrado em um locatário do Azure AD. Esse processo de registro envolve fornecer detalhes do Azure AD sobre seu aplicativo, como a URL onde o aplicativo está localizado, a URL para enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo e assim por diante.

1. No [portal do Azure](https://portal.azure.com), certifique-se de que seu locatário aparece em seu nome de usuário no canto superior direito do portal:

    ![Captura de tela do portal de locatário listado no canto superior direito][tenantcallout] se o locatário errado for exibido, clique em seu nome de usuário no canto superior direito, clique **mudar diretório**e selecione o locatário correto do **todos os Diretórios** lista.

2. Abra o [folha registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e clique em **novo registro de aplicativo**.
3. No **criar** painel, insira um nome amigável para seu objeto de aplicativo.
4. Certifique-se de que **tipo de aplicativo** é definido como *aplicativo Web/API*.
5. Criar uma **URL de logon** usando o seguinte padrão:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . em que `<cluster-name>` é o nome pretendido do seu cluster do Azure Red Hat OpenShift e `<azure-region>` é o [região do Azure que hospeda o cluster do Azure Red Hat OpenShift](supported-resources.md#azure-regions). Por exemplo, se o nome do cluster deve ser `contoso`, e você estará criando-o na `eastus` região, o nome de domínio totalmente qualificado (FQDN) que você vai inserir para o **URL de logon** seria `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > O nome do cluster deve estar todo em letras maiusculas, e a URL de FQDN deve ser exclusiva.
    > Verifique se que você escolher um nome distinto suficientemente para seu cluster.

    Anote o nome do seu cluster e URL de logon, você precisará deles posteriormente ao acessar aplicativos em execução no seu cluster. Vamos nos referir ao nome do cluster conforme `CLUSTER_NAME`e esta URL de logon como `FQDN` na [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

6. Certifique-se de sua **URL de logon** valor valida com uma marca de seleção verde. (Pressione a tecla Tab para o foco dos *URL de logon* campo e execute a verificação de validação.)
7. Clique o **criar** botão para criar o objeto de aplicativo do Azure AD.
8. Sobre o **aplicativo registrado** página for exibida, anote o **ID do aplicativo**. Vamos nos referir a esse valor como `APPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

    ![Captura de tela da caixa de texto de ID de aplicativo][appidimage]
    
Para obter mais informações sobre objetos de aplicativo do Azure, consulte [aplicativo e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre como criar um novo aplicativo do Azure AD, consulte [registrar um aplicativo com o ponto de extremidade do Active Directory do Azure v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Agora você está pronto para gerar um segredo do cliente para autenticar seu aplicativo ao Azure Active Directory.

1. Dos **aplicativo registrado** , clique em **configurações** para abrir as configurações para seu aplicativo registrado.
2. Sobre o **as configurações** painel que aparece, clique em **chaves**.  O **chaves** painel será exibido.
![Captura de tela da página Criar chaves no portal][createkeyimage]
3. Forneça uma chave **descrição**.
4. Definir um valor para **Expires**, por exemplo *em 2 anos*.
5. Clique em **salvar** e o valor da chave será exibido.
6. Copie o valor da chave. Vamos nos referir a esse valor como `SECRET` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

### <a name="create-a-reply-url"></a>Criar uma URL de resposta

O Azure AD usa o objeto de aplicativo *URL de resposta* para especificar o retorno de chamada a ser usado ao autorizar o aplicativo. No caso de uma API da web ou aplicativo web, a URL de resposta é o local em que o Azure AD enviará a resposta de autenticação, incluindo um token se a autenticação foi bem-sucedida.

Até mesmo a incompatibilidade de menor (à direita de barra "/" ausentes, diferenciam maiusculas de minúsculas) fará com que a falha da operação de emissão de token e você não poderá entrar.

1. Volte para o **as configurações** painel (você pode clicar em **configurações** na trilha na parte superior do portal) e clique em **URLs de resposta** à direita.  O **URLs de resposta** painel será exibido.
2. A URL de resposta primeiro na lista será o `FQDN` o valor da etapa 6 no [criar um novo registro de aplicativo](#create-a-new-app-registration). Editá-lo e acrescente `/oauth2callback/Azure%20AD`.  Por exemplo, a URL de resposta deve se parecer algo como `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Clique em **salvar** para salvar a URL de resposta.

## <a name="create-a-new-active-directory-user"></a>Criar um novo usuário do Active Directory

Crie um novo usuário no Active Directory para usar para entrar no aplicativo em execução no cluster do Azure Red Hat OpenShift.

1. Vá para o [usuários – todos os usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) folha.
2. Clique em **+ Novo Usuário**. O **usuário** painel será exibido.
3. Insira um **nome** que você deseja para este usuário.
4. Criar uma **nome de usuário** com base no nome do locatário criada com `.onmicrosoft.com` acrescentado ao final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote esse nome de usuário. Você precisará dela para entrar para usar o aplicativo em seu cluster.
5. Clique em **função de diretório** e selecione **Administrador Global** e, em seguida, clique em **Okey** na parte inferior do painel.
6. No meio do **usuário** painel, clique em **Mostrar senha** e registre a senha temporária. Depois de entrar pela primeira vez, você será solicitado para redefini-lo.
7. Na parte inferior do painel, clique em **criar** para criar o usuário.

## <a name="resources"></a>Recursos

* [Aplicativos e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Início Rápido: Registrar um aplicativo com o ponto de extremidade do Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Próximas etapas

Se você cumpriu todos os [pré-requisitos do Azure Red Hat OpenShift](howto-setup-environment.md), você estará pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

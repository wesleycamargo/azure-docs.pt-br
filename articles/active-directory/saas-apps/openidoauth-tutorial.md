---
title: Etapas para configurar um aplicativo OpenID/OAuth da galeria do Aplicativo Azure AD | Microsoft Docs
description: Etapas para configurar um aplicativo OpenID/OAuth da galeria do Aplicativo Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 176af41197810059a17daf5ab09d29e0169e9640
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225000"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Etapas para configurar um aplicativo OpenID/OAuth da galeria do Aplicativo Azure AD

## <a name="process-of-open-id-application-addition-from-gallery"></a>Processo de adição do aplicativo Open ID da galeria

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](./media/openidoauth-tutorial/tutorial_general_03.png)

4. Na caixa de pesquisa, digite o **Nome do aplicativo**, selecione o **aplicativo desejado** no painel de resultados e inscreva-se no aplicativo.

    ![Adicionando aplicativo](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Para os aplicativos Open ID Connect e OAuth, o botão Adicionar está desabilitado por padrão. Aqui o administrador de locatário deve clicar no botão **inscrever-se** e fornecer o consentimento ao aplicativo. Com isso, o aplicativo será adicionado ao locatário do cliente, sem necessidade de adicionar explicitamente e realizar as configurações.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

5. Ao clicar no link inscrever-se, você será redirecionado à página do Azure AD para inserir as credenciais de logon.

6. Após a autenticação bem-sucedida, o usuário deve aceitar o consentimento na página de consentimento e, depois disso, a home page do aplicativo será exibida.

    > [!NOTE]
    > Os clientes têm permissão para adicionar somente uma instância do aplicativo. Se já tiver adicionado um e tiver tentado fornecer o consentimento novamente, ele não será adicionado outra vez no locatário. Portanto, logicamente, eles podem usar apenas uma instância de aplicativo no locatário.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando o OpenID Connect

O fluxo de credenciais mais básico contém as seguintes etapas: cada um delas é descrita em detalhes abaixo.

![Fluxo de autenticação usando o OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Aplicativos multilocatários**: um aplicativo multilocatário é destinado para uso em muitas organizações, não apenas em uma. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor de software independente (ISV). Aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados, o que requer o consentimento do usuário ou administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo tiver sido registrado no diretório e receber acesso à API do Graph ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscreve para usar o aplicativo, é apresentada uma caixa de diálogo que exibe as permissões exigidas pelo aplicativo. O usuário ou administrador pode então dar consentimento para o aplicativo, que fornece ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo em seu diretório.

    > [!NOTE]
    > Se você estiver disponibilizando seu aplicativo a usuários em vários diretórios, será necessário um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único só precisa procurar em seu próprio diretório por um usuário, enquanto que um aplicativo multilocatário precisa identificar um usuário específico em todos os diretórios no Azure AD. Para realizar essa tarefa, o Azure AD fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico de locatário. Esse ponto de extremidade é [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) para todos os diretórios no Azure AD, enquanto um ponto de extremidade específico do locatário pode ser [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). O ponto de extremidade comum deve ser considerado com especial importância ao desenvolver seu aplicativo, pois você precisará da lógica necessária para lidar com vários locatários durante o login, logout e validação de token.

Por padrão, a equipe do Azure AD promove o aplicativo multilocatário, uma vez que ele pode ser facilmente acessado entre várias organizações e é fácil de usar após aceitar o consentimento.

## <a name="what-is-consent-framework"></a>O que é a estrutura de consentimento?

A estrutura de consentimento do Azure AD facilita o desenvolvimento de aplicativos da Web multilocatários e clientes nativos. Esses aplicativos permitem a entrada de contas de usuário de um locatário do Azure AD diferente daquele no qual o aplicativo está registrado. Também poderão precisar acessar APIs da Web, como a API do Microsoft Graph (para acessar o Azure Active Directory, o Intune e serviços no Office 365) e outras APIs de serviços da Microsoft, além das suas próprias APIs da Web. A estrutura se baseia em um usuário ou administrador que dá autorização a um aplicativo que solicita seu registro no diretório, o que pode envolver acesso aos dados do diretório. Depois que o consentimento for dado, o aplicativo cliente poderá chamar a API do Microsoft Graph em nome do usuário e usar as informações conforme necessário.

A [API do Graph da Microsoft](https://graph.microsoft.io/) fornece acesso aos dados do Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do OneDrive, blocos de anotações do OneNote, tarefas do Planner, pastas de trabalho do Excel etc.), bem como usuários e grupos do Azure AD e outros objetos de dados de outros serviços do Microsoft Cloud.

As etapas a seguir mostram como a experiência de consentimento funciona para o desenvolvedor e o usuário do aplicativo.

1. Suponha que você tenha um aplicativo cliente Web que precise solicitar permissões específicas para acessar uma recurso/API. O portal do Azure é usado para declarar as solicitações de permissão em tempo de configuração. Como outras definições de configuração, elas se tornam parte do registro do Azure AD do aplicativo:

    ![API do Graph](./media/openidoauth-tutorial/graphapi.png)

2. Pense na possibilidade de que as permissões do aplicativo tenham sido atualizadas, de que o aplicativo esteja em execução e de que um usuário esteja prestes a usá-lo pela primeira vez. Primeiro, o aplicativo precisa obter um código de autorização do ponto de extremidade /authorize do Azure AD. O código de autorização então pode ser usado para adquirir um novo acesso e token de atualização.

3. Se o usuário ainda não tiver sido autenticado, o ponto de extremidade /authorize do Azure AD solicitará o logon.

    ![Autenticação](./media/openidoauth-tutorial/authentication.png)

4. Depois que o usuário tiver se conectado, o AD do Azure determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo. Se o consentimento ainda não tiver sido dado, o Azure AD solicitará o consentimento ao usuário e exibirá as permissões necessárias de que ele precisa para funcionar. O conjunto de permissões exibido na caixa de diálogo de consentimento corresponde àqueles selecionados nas Permissões Delegadas no portal do Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Algumas permissões podem ser consentidas por um usuário normal, enquanto outras exigem o consentimento de um administrador de locatários.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Qual é a diferença entre o consentimento do administrador e o consentimento do usuário?

Como administrador, você pode também concorda com permissões do aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impede que a caixa de diálogo de consentimento seja exibida para todos os usuários do locatário e pode ser feito no portal do Azure por usuários com a função de administrador. Na página Configurações para seu aplicativo, clique em Permissões Necessárias e clique no botão Conceder Permissões.

![Conceder permissão](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A concessão explícita de consentimento usando o botão Conceder Permissões é necessária no momento para SPAs (aplicativos de página única) que usam o ADAL.js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.

As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários. Se o aplicativo solicitar uma permissão somente do aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida informando que o usuário não pode fornecer o consentimento.

Se o aplicativo usar permissões que exigem o consentimento do administrador, você precisará ter um gesto como um botão ou link, em que o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização do OAuth2/OpenID Connect normal, que também inclui o parâmetro de cadeia de caracteres de consulta prompt=admin_consent. Depois que o administrador fornecer seu consentimento e a entidade de serviço for criada no locatário do cliente, as próximas solicitações de conexão não precisarão do parâmetro prompt=admin_consent. Uma vez que o administrador tiver decidido que as permissões solicitadas forem aceitáveis, não será solicitado o consentimento de nenhum outro usuário no locatário daquele ponto em diante. Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos. Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser usado no locatário. Se desejar testar seu aplicativo com o consentimento do usuário final desabilitado, localize a opção de configuração no [portal do Azure](https://portal.azure.com/), na seção [Configurações de Usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) em **Aplicativos empresariais**

O parâmetro prompt=admin_consent também pode ser usado por aplicativos que solicitam permissões que não necessitam do consentimento do administrador. Um exemplo de como isso seria usado é quando o aplicativo exige uma experiência na qual o administrador de locatários “se inscreve” uma vez e não é solicitado o consentimento de nenhum outro usuário desse momento em diante.

Se um aplicativo exigir o consentimento do administrador e um administrador se conectar sem o envio do parâmetro prompt=admin_consent, o administrador fornecerá consentimento ao aplicativo somente para a conta de usuário dele. Os usuários normais ainda não poderão entrar ou dar consentimento ao aplicativo. Esse recurso é útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.
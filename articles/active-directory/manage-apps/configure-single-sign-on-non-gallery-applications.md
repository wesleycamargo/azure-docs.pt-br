---
title: Logon único – aplicativos não da galeria – Azure Active Directory | Microsoft Docs
description: Configurar o SSO (logon único) para aplicativos não da galeria no Azure AD (Azure Active Directory)
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: celested
ms.reviewer: asmalser,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: eefde6173156ea86f210ecc196c848fd97aaa0c8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435774"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Configurar o logon único para aplicativos não da galeria no Azure Active Directory

Este artigo é sobre um recurso que permite aos administradores configurar o logon único para aplicativos que não estejam presentes na galeria de aplicativos do Active Directory do Azure *sem escrever código*. Se você estiver procurando orientações para desenvolvedores sobre como integrar aplicativos personalizados com o AD do Azure por meio de código, veja [Cenários de autenticação do AD do Azure](../develop/authentication-scenarios.md).

A galeria de aplicativos do Active Directory do Azure fornece uma lista de aplicativos que reconhecidamente dão suporte a um formulário de logon único com o Active Directory do Azure, conforme descrito [neste artigo](what-is-single-sign-on.md). Como especialista em TI ou integrador de sistema em sua organização, quando você tiver localizado o aplicativo que deseja conectar, poderá começar seguindo as instruções apresentadas no Portal do Azure para habilitar o logon único.

Essas funcionalidades também estão disponíveis, de acordo com seu contrato de licença. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/active-directory/). 

- Integração de autoatendimento de qualquer aplicativo com suporte a provedores de identidade SAML 2.0 (iniciado por SP ou IdP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](what-is-single-sign-on.md#password-based-sso)
- Conexão de autoatendimento de aplicativos que usam o protocolo SCIM para provisionamento de usuários ([descrito aqui](use-scim-to-provision-users-and-groups.md))
- Capacidade de adicionar links aos aplicativos no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sso)

Isso pode incluir não apenas aplicativos SaaS usados por você que ainda não foram integrados à galeria de aplicativos do AD do Azure, mas também aplicativos Web de terceiros que sua organização implantou em servidores sob seu controle, seja na nuvem ou locais.

Esses recursos, também conhecidos como *modelos de integração de aplicativos*, fornecem pontos de conexão baseados em padrões para aplicativos que dão suporte à autenticação baseada em formulários, a SCIM ou a SAML e que incluem opções e configurações flexíveis para compatibilidade com um grande número de aplicativos. 

## <a name="adding-an-unlisted-application"></a>Adicionando um aplicativo não listado
Para se conectar a um aplicativo usando um modelo de integração do aplicativo, entrar no portal do Azure usando sua conta de administrador do Active Directory do Azure. Navegue até a seção **Ativar Diretório> Aplicativos corporativos> Novo aplicativo> Aplicativo sem galeria**, selecione **Adicionar** e, em seguida, **Adicionar um aplicativo da galeria**.

  ![Adicionar aplicativo](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

Na galeria do aplicativo, você pode adicionar um aplicativo não listado, selecionando o bloco **Aplicativo inexistente na galeria** que será mostrado nos resultados da pesquisa se o aplicativo desejado não for localizado. Depois de inserir um nome para seu aplicativo, você pode configurar as opções de logon único e comportamento. 

**Dicas rápida**:  Como uma prática recomendada, use a função de pesquisa para verificar se o aplicativo já existe na galeria de aplicativos. Se o aplicativo for localizado e a descrição mencionar "logon único", o aplicativo já terá suporte para logon único federado.

  ![Search](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Adicionar um aplicativo dessa maneira fornece uma experiência semelhante à disponível para aplicativos pré-integrados. Para começar, selecione **Configurar Logon Único** ou clique em **Logon único** no menu de navegação à esquerda do aplicativo. A próxima tela apresenta as opções para configurar o logon único. As opções são descritas nas próximas seções deste artigo.
  
![Opções de configuração](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Logon único baseado em SAML
Selecione esta opção para configurar a autenticação baseada em SAML no aplicativo. Isso requer que o aplicativo ofereça suporte SAML 2.0. Isso requer que o aplicativo ofereça suporte a SAML 2.0, e você deve coletar informações sobre como usar os recursos SAML do aplicativo antes de continuar. Conclua as seguintes seções para configurar o logon único entre o aplicativo e o AD do Azure.

### <a name="enter-basic-saml-configuration"></a>Configuração Básica de SAML

Para configurar o AD do Azure, insira a configuração básica do SAML. Pode inserir os valores manualmente ou carregar um arquivo de metadados para extrair o valor dos campos.

  ![Domínio e URLs Litware](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **URL de logon (somente iniciado por SP)** – onde o usuário vai para entrar nesse aplicativo. Se o aplicativo estiver configurado para executar logon único iniciado pelo provedor de serviços, quando um usuário navegar para essa URL, o provedor de serviços fará o redirecionamento necessário para o Azure Active Directory autenticar e conectar o usuário. Se esse campo estiver preenchido, o Azure AD usará essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se esse campo for omitido, o Azure AD executará em vez disso, o provedor de identidade-iniciado pelo logon quando o aplicativo é iniciado a partir do Office 365, o painel de acesso do AD do Azure, ou o AD do Azure única URL de entrada (pode ser copiado da guia Painel).
- **Identificador** - deve identificar exclusivamente o aplicativo para o qual o logon único está sendo configurado. Você pode encontrar esse valor como o elemento Emissor no AuthRequest (solicitação SAML) enviado pelo aplicativo. Esse valor também aparece como a **ID da entidade** em todos os metadados SAML fornecidos pelo aplicativo. Verifique a documentação SAML do aplicativo para obter detalhes sobre o que é a ID da Entidade ou o valor Audiência. 

    Veja a seguir um exemplo de como o Identificador ou Emissor aparece na solicitação SAML enviada pelo aplicativo ao Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **URL de resposta** – a URL de resposta é onde o aplicativo espera receber o token SAML. Ela também é chamada de URL de ACS (Serviço do Consumidor de Declaração). Verifique a documentação SAML do aplicativo para obter detalhes sobre o que é a URL de resposta de token SAML ou a URL de ACS. 

    Para configurar vários replyURLs, você pode usar o seguinte script do PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Para obter mais informações, consulte [ solicitações de autenticação e respostas de SAML 2.0 que o Azure AD (Azure Active Directory) suporta ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Revise ou personalize as reivindicações emitidas no token SAML

Quando um usuário é autenticado no aplicativo, o AD do Azure emite um token SAML para o aplicativo que contém informações (ou declarações) sobre o usuário que o identifica com exclusividade. Por padrão, isso inclui o nome de usuário, o endereço de email, o nome e sobrenome do usuário. 

Você pode exibir ou editar as declarações enviadas no token SAML para o aplicativo na guia **Atributos** .

  ![Atributos](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Há dois motivos pelos quais você pode precisar editar as reivindicações emitidas no token SAML:

- O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.
- O aplicativo foi implantado de uma forma que exige que a declaração NameIdentifier seja algo diferente do nome de usuário (também conhecido como nome UPN) armazenado no Azure Active Directory. 

Para obter mais informações, consulte [ Personalizando declarações emitidas no token SAML para aplicativos corporativos ](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Revisar dados de expiração de certificado, status e notificação por email

Quando você cria um aplicativo Galeria ou Não-Galeria, o Azure AD cria um certificado específico do aplicativo com uma data de expiração de 3 anos a partir da data de criação. Você precisa deste certificado para configurar a confiança entre o Azure AD e o aplicativo. Para obter detalhes sobre o formato de certificado, consulte a documentação do aplicativo SAML. 

No Azure AD, você pode baixar o certificado no formato Base64 ou Raw. Além disso, você pode obter o certificado ao fazer o download do arquivo XML de metadados do aplicativo ou usando a URL de metadados de Federação do aplicativo.

  ![Certificado](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Verifique se que o certificado tem:

- A data de validade desejada. Você pode configurar a data de expiração de no máximo 3 anos.
- Um status ativo. Se o status estiver inativo, altere o status para ativo. Para alterar o status, verifique **Ativar** e, em seguida, salvar a configuração. 
- O email de notificação correto. Quando o certificado active estiver próximo a data de validade, o AD do Azure enviará uma notificação para o endereço de e-mail configurado neste campo.  

[Gerenciar certificados para logon único federado no diretório Azure Active](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Configurar aplicativo de destino

Para configurar o aplicativo para logon único, localize a documentação do aplicativo. Para localizar a documentação, role até o final da página de configuração de logon baseado em SAML e, em seguida, clique em **configurar \<nome do aplicativo >**. 

Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do aplicativo SAML. O URL de serviço de logon e logoff é resolvido para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitações SAML para sua instância do Azure AD. O ID da Entidade SAML é o valor que aparece como o Emissor no token SAML que é emitido para o aplicativo.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Atribuir usuários e grupos ao seu aplicativo SAML

Quando o aplicativo estiver configurado para usar o AD do Azure como um provedor de identidade baseado em SAML, estará quase pronto para teste. Como controle de segurança, o Azure AD não emitirá um token permitindo que um usuário faça logon no aplicativo, a menos que o Azure AD conceda acesso ao usuário. Os usuários podem receber acesso diretamente ou por meio de uma associação ao grupo. 

Para atribuir um usuário ou grupo ao seu aplicativo, clique no botão **Atribuir Usuários** . Selecione o usuário ou grupo que você deseja atribuir e selecione o botão **Atribuir** .

  ![Atribuir usuários](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

A atribuição de um usuário permitirá que o Azure AD emita um token para o usuário. Isso também faz com que um bloco para este aplicativo apareça no Painel de Acesso do usuário. Um bloco do aplicativo também aparece no iniciador do aplicativo do Office 365 se o usuário estiver usando o Office 365. 

> [!NOTE] 
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo. 


### <a name="test-the-saml-application"></a>Teste o aplicativo SAML

Antes de testar o aplicativo SAML, você deve configurar o aplicativo com o Azure AD e atribuir usuários ou grupos ao aplicativo. Para testar aplicativo SAML, consulte [ Como depurar o logon único com base em SAML para aplicativos no Active Directory do Azure](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Logon único com senha

Selecione esta opção para configurar o [logon único baseado em senha](what-is-single-sign-on.md) para um aplicativo Web que possua uma página de entrada em HTML. O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários onde vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

Depois de selecionar **Próximo**, você deverá inserir a URL da página de entrada baseada na Web do aplicativo. Observe que deve ser a página que inclui os campos de entrada de nome de usuário e senha. Depois de inserida, o AD do Azure iniciará um processo para analisar a página de entrada em busca de entradas de nome de usuário e de senha. Se o processo não for bem-sucedido, ele o orientará por um processo alternativo de instalação de uma extensão de navegador (requer o Internet Explorer, o Chrome ou o Firefox) que permite que você capture os campos manualmente.

Depois que a página for capturada, os usuários e grupos podem ser atribuídos e as políticas de credencial podem ser definidas como [aplicativos de SSO de senha](what-is-single-sign-on.md)normais.

> [!NOTE] 
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo. 
>

## <a name="existing-single-sign-on"></a>Logon único existente
Selecione esta opção para adicionar um link para um aplicativo no Painel de Acesso do AD do Azure ou no portal do Office 365 da sua organização. Você pode usar isso para adicionar links para aplicativos Web personalizado que atualmente usam os Serviços de Federação do Active Directory (AD FS) (ou outro serviço de federação) em vez do AD do Azure para autenticação. Ou você pode adicionar links profundos para páginas específicas do SharePoint ou outras páginas da Web que você queira que apareçam somente nos Painéis de Acesso do usuário. 

Depois de selecionar **Próximo**, você deverá inserir a URL do aplicativo a ser vinculado. Depois de concluído, os usuários e os grupos poderão ser atribuídos ao aplicativo, o que faz com que o aplicativo seja exibido no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso do Azure AD](end-user-experiences.md) desses usuários.

> [!NOTE] 
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo. 
>

## <a name="related-articles"></a>Artigos relacionados
- [Como personalizar declarações emitidas no token SAML para aplicativos pré-integrados](../develop/active-directory-saml-claims-customization.md)
- [Solução de problemas de logon único baseado em SAML](../develop/howto-v1-debug-saml-sso-issues.md)


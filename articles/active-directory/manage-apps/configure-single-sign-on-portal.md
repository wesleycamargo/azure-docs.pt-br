---
title: Configurar logon único – Azure Active Directory | Microsoft Docs
description: Este tutorial usa o portal do Azure para configurar logon único baseado em SAML para um aplicativo com Azure AD (Azure Active Directory).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d96799e69e2fdef3a4ffd1a436727e6a58da79
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565981"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Tutorial: Configurar logon único baseado em SAML para um aplicativo com Azure Active Directory

Este tutorial usa o [portal do Azure](https://portal.azure.com) para configurar logon único baseado em SAML para um aplicativo com Azure AD (Azure Active Directory). Use este tutorial quando um [tutorial específicos do aplicativo](../saas-apps/tutorial-list.md) não estiver disponível. 

Este tutorial usa o portal do Azure para:

> [!div class="checklist"]
> * Selecione o modo logon único baseado em SAML
> * Configurar URLs e domínio específicos do aplicativo
> * Configurar atributos de usuário
> * Criar um certificado de autenticação SAML
> * Atribuir usuários ao aplicativo
> * Configurar o aplicativo para logon único com base em SAML
> * Testar as configurações de SAML

## <a name="before-you-begin"></a>Antes de começar

1. Caso o aplicativo não tenha sido adicionado ao seu locatário do Azure AD, confira [Início Rápido: Adicionar um aplicativo ao locatário do Azure AD](add-application-portal.md).

2. Solicite ao fornecedor do aplicativo as informações descritas em [Configurar opções básicas do SAML](#configure-basic-saml-options).

3. Use um ambiente de não produção para testar as etapas deste tutorial. Se não tiver um ambiente do Azure AD que não seja de produção, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

4. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Selecione um modo de logon único

Depois de adicionar um aplicativo ao seu locatário do Azure AD, você estará pronto para configurar o logon único para o aplicativo.

Para abrir as configurações de logon único:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**. 

2. Em **Gerenciar** no painel de navegação do **Azure Active Directory** exibido, selecione **Aplicativos empresariais**. Uma amostra aleatória dos aplicativos em seu locatário do Azure AD é exibida. 

3. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**.

4. Insira o nome do aplicativo para o qual você deseja configurar o logon único. Por exemplo, você pode inserir **GitHub-test** para configurar o aplicativo adicionado no Início Rápido [Adicionar um aplicativo](add-application-portal.md).  

     ![Captura de tela que mostra a barra de pesquisa do aplicativo.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Escolha o aplicativo para o qual deseja configurar o logon único.

6. Na seção **Gerenciar**, selecione **Logon único**. 

7. Selecione **SAML** para configurar o logon único. A página **Configurar logon único com SAML – Visualização** será exibida.

## <a name="configure-basic-saml-options"></a>Configurar opções básicas do SAML

Para configurar o domínio e as URLs:

1. Entre em contato com o fornecedor do aplicativo para obter informações corretas sobre as seguintes configurações:

    | Parâmetro de configuração | Iniciado por SP | iniciado por idP | DESCRIÇÃO |
    |:--|:--|:--|:--|
    | Identificador (ID da Entidade) | Obrigatório para alguns aplicativos | Obrigatório para alguns aplicativos | Identifica exclusivamente o aplicativo para o qual o logon único está sendo configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. O aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo.|
    | URL de resposta | Opcional | Obrigatório | Especifica onde o aplicativo espera receber o token SAML. A URL de resposta também é chamada de URL do ACS (Serviço do Consumidor de Declaração). |
    | URL de logon | Obrigatório | Não especifique | Quando um usuário abre essa URL, o provedor de serviço redireciona para o Azure AD a fim de autenticar e conectar o usuário. O Azure AD usa a URL para iniciar o aplicativo no Office 365 ou no painel de acesso do Azure AD. Quando ela estiver em branco, o Azure AD dependerá do provedor de identidade para iniciar o logon único quando um usuário iniciar o aplicativo.|
    | Estado de Retransmissão | Opcional | Opcional | Especifica para onde o aplicativo deve redirecionar o usuário depois que a autenticação é concluída. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, pergunte ao fornecedor do aplicativo.
    | URL de logoff | Opcional | Opcional | Usada para enviar as respostas de Logoff do SAML novamente ao aplicativo.


2. Para editar as opções de configuração básica do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Configuração Básica do SAML**.

     ![Configurar certificados](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Nos campos apropriados da página, insira as informações fornecidas pelo fornecedor do aplicativo na etapa 1.

4. Na parte superior da página, selecione **Salvar**.

## <a name="configure-user-attributes-and-claims"></a>Configurar atributos e declarações de usuário 

Você pode controlar quais informações o Azure AD envia para o aplicativo no token SAML quando um usuário se conecta. Controle essas informações por meio da configuração de atributos de usuário. Por exemplo, você pode configurar o Azure AD para enviar o nome do usuário, o email e a ID do funcionário para o aplicativo quando um usuário se conectar. 

Esses atributos podem ser obrigatórios ou opcionais para que o trabalho de logon único funcione corretamente. Para obter mais informações, confira o [tutorial específico do aplicativo](../saas-apps/tutorial-list.md) ou pergunte ao fornecedor do aplicativo.

1. Para editar os atributos e as declarações de usuário, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Atributos e Declarações de Usuário**.

   O **Valor do Identificador de Nome** é definido com o valor padrão de *user.principalname*. O identificador de usuário identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o endereço de email é tanto o nome de usuário quanto o identificador exclusivo, defina o valor como *user.mail*.

2. Para modificar o **Valor do Identificador de Nome**, selecione o ícone **Editar** (um lápis) para o campo **Valor do Identificador de Nome**. Faça as alterações apropriadas na origem e no formato do identificador, conforme necessário. Salve as alterações quando terminar. Para obter mais informações sobre como personalizar declarações, confira o artigo de instruções [Personalizar as declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md).

3. Para adicionar uma declaração, selecione **Adicionar nova declaração** na parte superior da página. Insira o **Nome** e selecione a fonte apropriada. Se você selecionar a origem **Atributo**, precisará escolher o **Atributo de origem** que deseja usar. Se você selecionar a origem **Tradução**, precisará escolher a **Transformação** e o **Parâmetro 1** que deseja usar.

4. Clique em **Salvar**. A nova declaração será exibida na tabela.
 
## <a name="generate-a-saml-signing-certificate"></a>Gerar um certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. 

1. Para gerar um novo certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **Certificado de Autenticação SAML**.

2. Na seção **Certificado de Autenticação SAML**, selecione **Novo Certificado**.

3. Na nova linha de certificado exibida, defina a **Data de Validade**. Para obter mais informações sobre as opções de configuração disponíveis, confira o artigo [Opções avançadas de autenticação de certificado](certificate-signing-options.md).

4. Selecione **Salvar** na parte superior da seção **Certificado de Autenticação SAML**. 

## <a name="assign-users-to-the-application"></a>Atribuir usuários ao aplicativo

É uma boa ideia testar o logon único com vários usuários ou grupos antes de distribuir o aplicativo para sua organização.

> [!NOTE]
>
> Estas etapas levarão você para a seção de configuração **Usuários e grupos** no portal. Quando terminar, você precisará navegar novamente para a seção **Logon único** para concluir o tutorial.

Para atribuir um usuário ou um grupo ao aplicativo:

1. Abra o aplicativo no portal se já não estiver aberto.
2. No painel de navegação à esquerda do aplicativo, selecione **Usuários e grupos**.
3. Selecione **Adicionar usuário**.
4. Na seção **Adicionar Atribuição**, selecione **Usuários e grupos**.
5. Para encontrar um usuário específico, digite o nome de usuário na caixa **Selecionar um membro ou convidar um usuário externo**. Em seguida, selecione o logotipo ou a foto do perfil do usuário e, em seguida, escolha **Selecionar**. 
6. Na seção **Adicionar Atribuição**, selecione **Atribuir**. Quando concluído, os usuários selecionados serão exibidos na lista **Usuários e grupos**.

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurar o aplicativo para usar o Azure AD

Você está quase lá.  Como etapa final, você precisará configurar o aplicativo para usar o Azure AD como um provedor de identidade SAML. 

1. Role a página para baixo até a seção **Configurar o <applicationName>**. Para este tutorial, esta seção é chamada **Configurar GitHub-test**. 
2. Copie o valor de cada linha nesta seção. Em seguida, cole cada valor na linha apropriada na seção **Configuração Básica do SAML**. Por exemplo, copie o valor da **URL de Logon** da seção **Configurar o GitHub-test** e cole-o no campo **URL de Logon** da seção **Configuração Básica do SAML** e assim por diante.
3. Depois de colar todos os valores nos campos apropriados, selecione **Salvar**.

## <a name="test-single-sign-on"></a>Testar logon único

Você está pronto para testar as configurações.  

1. Abra as configurações de logon único do aplicativo. 
2. Role a página até a seção **Validar o logon único com o <applicationName>**. Para este tutorial, esta seção é chamada **Configurar GitHub-test**.
3. Selecione **Testar**. As opções de teste são exibidas.
4. Selecione **Entrar como o usuário atual**. Esse teste permite que você veja primeiro se o logon único funciona para você, o administrador.

Se houver um erro, uma mensagem de erro será exibida. Conclua as seguintes etapas:

1. Copie e cole as informações específicas na caixa **Como é o erro?**.

    ![Obter diretrizes de resolução](media/configure-single-sign-on-portal/error-guidance.png)

2. Selecione **Obter diretrizes de resolução**. A causa raiz e as orientações para resolução aparecem.  Neste exemplo, o usuário não estava atribuído ao aplicativo.

3. Leia as diretrizes de resolução e, em seguida, se possível, corrija o problema.

4. Execute o teste novamente até que ele seja concluído com êxito.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você definiu as configurações de logon único para um aplicativo. Depois de concluir a configuração, você atribuiu um usuário ao aplicativo e configurou o aplicativo para usar o logon único baseado em SAML. Quando todo esse trabalho foi concluído, você verificou se o logon do SAML estava funcionando corretamente.

Você fez essas coisas:
> [!div class="checklist"]
> * Selecionou SAML como modo de logon único
> * Entrou em contato com o fornecedor do aplicativo para configurar URLs e domínio
> * Configurou atributos de usuário
> * Criou um certificado de assinatura de SAML
> * Atribuiu usuários ou grupos ao aplicativo manualmente
> * Configurado o aplicativo para usar o Azure AD como um provedor de identidade SAML
> * Testou o logon único baseado em SAML

Para distribuir o aplicativo para mais usuários em sua organização, use o provisionamento automático de usuário.

> [!div class="nextstepaction"]
> [Saiba como atribuir usuários com o provisionamento automático](configure-automatic-user-provisioning-portal.md)



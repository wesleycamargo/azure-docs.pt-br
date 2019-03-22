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
ms.date: 12/06/2018
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037f5b554889d89fc0b50983d3d85d38f4345311
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571398"
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

2. Peça a seu fornecedor do aplicativo as informações descritas em [Configurar domínio e URLS](#configure-domain-and-urls).

3. Para testar as etapas deste tutorial, recomendamos o uso de um ambiente que não seja de produção. Se não tiver um ambiente do Azure AD que não seja de produção, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

4. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Selecione um modo de logon único

Depois que um aplicativo é adicionado ao locatário do Azure AD, você estará pronto para configurar o logon único para o aplicativo.

Para abrir as configurações de logon único:

1. No [portal do Azure](https://portal.azure.com), no painel navegação à esquerda, clique em **Azure Active Directory**. 

2. Na folha do **Azure Active Directory**, clique em **Aplicativos empresariais**. A folha **Todos os aplicativos** é aberta para mostrar uma amostra aleatória dos aplicativos em seu locatário do Azure AD. 

3. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos**e clique em **Aplicar**.

4. Insira o nome do aplicativo para o qual você deseja configurar o logon único. Escolha seu próprio aplicativo ou insira **GitHub-test** para configurar o aplicativo que você adicionou no início rápido [adicionar aplicativo](add-application-portal.md).

5. Clique em **Logon único**. Em **Modo de Logon Único**, **Logon único baseado em SAML** aparece como a opção padrão. 

    ![Opções de configuração](media/configure-single-sign-on-portal/config-options.png)

6. Clique em **Salvar** na parte superior da folha. 

## <a name="configure-domain-and-urls"></a>Configurar domínio e URLs

Para configurar o domínio e as URLs:

1. Entre em contato com o fornecedor do aplicativo para obter informações corretas sobre as seguintes configurações:

    | Parâmetro de configuração | Iniciado por SP | iniciado por idP | DESCRIÇÃO |
    |:--|:--|:--|:--|
    | URL de logon | Obrigatório | Não especifique | Quando um usuário abre essa URL, o provedor de serviço redireciona para o Azure AD a fim de autenticar e conectar o usuário. O Azure AD usa a URL para iniciar o aplicativo no Office 365 ou no painel de acesso do Azure AD. Quando estiver em branco, o Azure AD dependerá do provedor de identidade para iniciar o logon único quando um usuário iniciar o aplicativo.|
    | Identificador (ID da Entidade) | Obrigatório para alguns aplicativos | Obrigatório para alguns aplicativos | Identifica exclusivamente o aplicativo para o qual o logon único está sendo configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. O aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo.|
    | URL de resposta | Opcional | Obrigatório | Especifica onde o aplicativo espera receber o token SAML. A URL de resposta também é chamada de URL do ACS (Serviço do Consumidor de Declaração). |
    | Estado de Retransmissão | Opcional | Opcional | Especifica para onde o aplicativo deve redirecionar o usuário depois que a autenticação é concluída. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, pergunte ao fornecedor do aplicativo.

2. Insira as informações. Para ver todas as configurações, clique em **Mostrar configurações avançadas de URL**.

    ![Opções de configuração](media/configure-single-sign-on-portal/config-urls.png)

3. Na parte superior da folha, clique em **Salvar**.

4. Há um botão **Testar Configurações de SAML** nesta seção. Execute este teste posteriormente no tutorial da seção [Testar logon único](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Configurar atributos de usuário

Atributos de usuário permitem que você controle quais informações o Azure AD envia para o aplicativo no token SAML sempre que um usuário se inscreve. Por exemplo, o Azure AD poderia enviar o nome, o email e a ID de funcionário do usuário para o aplicativo. 

Esses atributos podem ser obrigatórios ou opcionais para que o trabalho de logon único funcione corretamente. Para obter mais informações, confira o [tutorial específico do aplicativo](../saas-apps/tutorial-list.md) ou pergunte ao fornecedor do aplicativo.

1. Para exibir todas as opções, clique em **Exibir e editar todos os outros atributos de usuário**.

    ![Configurar atributos de usuário](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Insira **identificador de usuário**.

    O identificador de usuário identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o endereço de email é tanto o nome de usuário quanto o identificador exclusivo, defina o valor como *user.mail*.

3. Para obter mais atributos do token SAML, clique em **Exibir e editar todos os outros atributos de usuário**.

4. Para adicionar um atributo aos **Atributos do Token SAML**, clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** no menu.

5. Clique em **Salvar**. Você verá o novo atributo na tabela.
 
## <a name="create-a-saml-signing-certificate"></a>Criar um certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. 

1. Para ver todas as opções, clique em **Mostrar opções de assinatura de certificado avançadas**.

    ![Configurar certificados](media/configure-single-sign-on-portal/config-certificate.png)

2. Para configurar um certificado, clique em **Criar novo certificado**.

3. Na folha **Criar Novo Certificado**, defina a **data do término** e clique em **Salvar**.

4. Clique em **Ativar novo certificado**.

5. Para obter mais informações, confira [Opções de assinatura de certificado avançadas](certificate-signing-options.md).

6. Para manter as alterações feitas até o momento, não se esqueça de clicar em **Salvar** na parte superior da folha **Logon único**. 

## <a name="assign-users-to-the-application"></a>Atribuir usuários ao aplicativo

A Microsoft recomenda testar o logon único com vários usuários ou grupos antes de distribuir o aplicativo em sua organização.

Para atribuir um usuário ou um grupo ao aplicativo:

1. Abra o aplicativo no portal se já não estiver aberto.
2. Na folha do aplicativo à esquerda, clique em **Usuários e grupos**.
3. Clique em **Adicionar usuário**.
4. Na folha **Adicionar Atribuição**, clique em **Usuários e grupos**.
5. Para localizar um usuário específico, digite o nome de usuário na caixa **Selecionar**, clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário e clique em **Selecionar**. 
6. Localize seu nome de usuário atual e selecione-o. Opcionalmente, você pode selecionar mais usuários.
7. Na folha **Adicionar Atribuição**, clique em **Atribuir**. Quando concluído, os usuários selecionados aparecerão na lista **Usuários e grupos**.

## <a name="configure-the-application-to-use-azure-ad"></a>Configurar o aplicativo para usar o Azure AD

Você está quase lá.  Como etapa final, você precisa configurar o aplicativo para usar o Azure AD como um provedor de identidade SAML. 

1. Role para baixo até o final da folha **Logon único** do aplicativo. 

    ![Configurar o aplicativo](media/configure-single-sign-on-portal/configure-app.png)

2. Clique em **Configurar aplicativo** no portal e siga as instruções.
3. Crie manualmente as contas de usuário no aplicativo para testar o logon único. Crie as contas de usuário que você atribuiu ao aplicativo na [seção anterior](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Testar logon único

Você está pronto para testar suas configurações.  

1. Abra as configurações de logon único do aplicativo. 
2. Role até a seção **Configurar domínio e URLs**.
2. Clique em **Testar as configurações de SAML**. As opções de teste são exibidas.

    ![Testar opções de logon único](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Clique em **Entrar como o usuário atual**. Esse teste permite que você veja primeiro se o logon único funciona para você, o administrador.
4. Se houver um erro, uma mensagem de erro será exibida. Copie e cole as informações específicas na caixa **Como é o erro?**.

    ![Obter diretrizes de resolução](media/configure-single-sign-on-portal/error-guidance.png)

5. Clique em **Obter diretrizes de resolução**. A causa raiz e as orientações para resolução aparecem.  Neste exemplo, o usuário não estava atribuído ao aplicativo.

    ![Corrigir erros](media/configure-single-sign-on-portal/fix-error.png)

6. Leia as orientações de resolução e, se for o caso, clique em **Corrigir**.

7. Execute o teste novamente até que ele seja concluído com êxito.



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

Para distribuir o aplicativo para mais usuários em sua organização, recomendamos usar o provisionamento automático de usuário.

> [!div class="nextstepaction"]
> [Saiba como atribuir usuários com o provisionamento automático](configure-automatic-user-provisioning-portal.md)



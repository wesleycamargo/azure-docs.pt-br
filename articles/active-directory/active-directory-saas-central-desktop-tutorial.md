---
title: "Tutorial: Integração do Azure Active Directory ao Central Desktop | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e a Área de Trabalho Central."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Active Directory do Azure ao Central Desktop

Neste tutorial, você aprenderá a integrar a Área de Trabalho Central ao Azure AD (Azure Active Directory).

A integração da Área de Trabalho Central ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso à Área de Trabalho Central.
- Você pode permitir que os usuários façam logon automaticamente na Área de Trabalho Central com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD à Área de Trabalho Central, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura da Área de Trabalho Central habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se você ainda não tem um ambiente de avaliação do Azure AD, [obtenha uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar a Área de Trabalho Central da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-central-desktop-from-the-gallery"></a>Adicionar a Área de Trabalho Central da galeria
Para configurar a integração da Área de Trabalho Central ao Azure AD, você precisa adicionar a Área de Trabalho Central da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Área de Trabalho Central da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Área de Trabalho Central**. Selecione **Área de Trabalho Central** do painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Área de Trabalho Central na lista de resultados](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a área de Trabalho Central, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário na Área de Trabalho Central é equivalente a um usuário do Azure AD. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do AD do Azure e um usuário relacionado na Área de Trabalho Central.

Na Área de Trabalho Central, dê ao **Username** o mesmo valor que o **nome de usuário** no Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários.

Para configurar e testar o logon único do Azure AD com a Área de Trabalho Central, você precisa concluir os blocos de construção a seguir:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste da Área de Trabalho Central ](#create-a-central-desktop-test-user) para ter um equivalente de Brenda Fernandes na Área de Trabalho Central que esteja vinculado à representação de usuário do Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo da Área de Trabalho Central.

**Para configurar o logon único do Azure AD com a Área de Trabalho Central, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos da **Área de Trabalho Central**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Na seção **URLs e domínio de Área de Trabalho Central**, execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da Área de Trabalho Central](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Na caixa **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<companyname>.centraldesktop.com`

    b. Na caixa **Identificador**, digite uma URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente da Área de Trabalho Central](https://imeetcentral.com/contact-us) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, selecione **Certificados**. Em seguida, salve o arquivo de certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Selecione o botão **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração da Área de Trabalho Central**, selecione **Configurar Área de Trabalho Central** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![Configuração da Área de Trabalho Central](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Faça logon no locatário da sua **Área de Trabalho Central**.

8. Vá para **Configurações**. Selecione **Avançado** e, em seguida, selecione **Logon Único**.

    ![Configuração – Avançada](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Configuração – Avançada")

9. Na página **Configurações de Logon Único** , execute as seguintes etapas:

    ![Configurações de logon único](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Configurações de Logon Único")
    
    a. Selecione **Habilitar Logon Único do SAML v2**.
    
    b. Na caixa **URL SSO**, cole o valor da **ID da Entidade SAML** que você copiou do Portal do Azure.
    
    c. Na caixa **URL de logon SSO**, cole o valor da **URL de Serviço de Logon Único SAML** que você copiou do Portal do Azure.
    
    d. Na caixa **URL de Logoff SSO**, cole o valor da **URL de Logout** que você copiou do Portal do Azure.

10. Na seção **Método de Verificação de Assinatura de Mensagem**, execute as seguintes etapas:

    ![Método de verificação de assinatura de mensagem](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "Método de verificação de assinatura de mensagem") a. Selecione **Certificado**.
    
    b. Na lista **Certificado de SSO**, selecione **RSH SHA256**.
    
    c. Abra seu certificado baixado no Bloco de Notas. Em seguida, copie o conteúdo do certificado e cole-o no campo **Certificado SSO**.
        
    d. Selecione **Exibir um link para a sua página de logon do SAMLv2**.
    
    e. Selecione **Atualização**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com) enquanto você estiver configurando o aplicativo. Depois de adicionar este aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida por meio da seção **Configuração** na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Para exibir uma lista de usuários, vá para **Usuários e grupos**. Selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-central-desktop-test-user"></a>Criar um usuário de teste da Área de Trabalho Central

Para que usuários do Azure AD consigam entrar, eles devem ser provisionados no aplicativo da Área de Trabalho Central. Esta seção descreve como criar contas de usuário do Azure AD na Área de Trabalho Central.

> [!NOTE]
> Para provisionar contas de usuário do Azure AD, você pode usar qualquer outra ferramenta de criação de conta de usuário da Área de Trabalho Central ou APIs fornecidas pela Área de Trabalho Central.

**Para provisionar contas de usuário no Central Desktop:**

1. Entre no locatário da sua Área de Trabalho Central.

2. Vá para **Pessoas** > **Membros Internos**.

3. Selecione **Adicionar Membros Internos**.

    ![Pessoas](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Pessoas")
    
4. Na caixa **Endereço de Email de Novos Membros**, digite uma conta do Azure AD que você deseja provisionar e, em seguida, selecione **Próximo**.

    ![Endereços de Email de Novos Membros](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "Endereços de Email de Novos Membros")

5. Selecione **Adicionar Membro(s) Interno(s)**.

    ![Adicionar membro interno](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Adicionar membro interno")
   
   >[!NOTE]
   >Os usuários que você adicionar recebem um email que inclui um link de confirmação para ativar suas contas.
   
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário Brenda Fernandes use o logon único do Azure, concedendo acesso à Área de Trabalho Central.

![Atribuir a função de usuário][200] 

**Para atribuir o usuário Brenda Fernandes à Área de Trabalho Central, execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Vá para a exibição de diretório e, em seguida, vá para **Aplicativos empresariais**.

2. Selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Área de Trabalho Central**.

    ![O link da Área de Trabalho Central na lista de aplicativos](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de **Usuários**.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, teste a configuração de logon único do seu Azure AD usando o painel de acesso.

Quando você seleciona o bloco Área de Trabalho Central no painel de acesso, você entra automaticamente em seu aplicativo da Área de Trabalho Central.
Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png


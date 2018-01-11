---
title: "Tutorial: integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory com o Atlassian Cloud

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure AD (Azure Active Directory).

A integração do Atlassian Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Atlassian Cloud.
- Você pode habilitar os usuários a fazer logon automaticamente no Atlassian Cloud (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Atlassian Cloud, você precisa dos seguintes itens:

- Uma assinatura do Azure AD.
- Para habilitar o logon único Security Assertion Markup Language (SAML) para produtos Atlassian Cloud, você precisará configurar o Identity Manager. Saiba mais sobre o [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito no tutorial consiste em dois blocos de construção principais:

* Adição do Atlassian Cloud da galeria
* Configurar e testar o logon único do AD do Azure

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud por meio da galeria
Para configurar a integração do Atlassian Cloud ao Azure AD, adicione o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados, fazendo o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o botão **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais][2]
    
3. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **Atlassian Cloud**, na lista de resultados, selecione **Atlassian Cloud**e, em seguida, selecione **Adicionar**.

    ![Atlassian Cloud na lista de resultados](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Atlassian Cloud, com base em um usuário de teste chamado *Britta Simon*.

Para que o logon único funcione, o Azure AD precisa identificar o usuário do Atlassian Cloud e sua contraparte do Azure AD. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para estabelecer a relação de vinculação, atribuir como o *nome de usuário* do Atlassian Cloud o mesmo valor que é atribuído ao *nome de usuário* do Azure AD.

Para configurar e testar o logon único do Azure AD com o Atlassian Cloud, você precisa concluir os blocos de construção nas seguintes seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Atlassian Cloud.

Para configurar o logon único do Azure AD com o Atlassian Cloud, faça o seguinte:

1. No portal do Azure, na página de integração de aplicativo do **Atlassian Cloud**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na janela **Logon único**, na caixa **Modo Logon único**, selecione **Logon baseado em SAML**.
 
    ![Janela de Logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Para configurar o aplicativo no modo iniciado pelo IDP, em **URLs e Domínio do Atlassian Cloud**, faça o seguinte:

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Na caixa **Identificador**, digite **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. Na caixa **URL de Resposta**, digite **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. Na caixa de **Estado de Retransmissão**, digite uma URL com o seguinte padrão: **`https://<instancename>.atlassian.net`**.

4. Para configurar o aplicativo no modo iniciado pelo SP, selecione o **Mostrar configurações de URL avançadas** e, em seguida, na caixa **URL de logon**, digite uma URL com a seguinte sintaxe: **`https://<instancename>.atlassian.net`**.

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com o Identificador, a URL de Resposta e a URL de Logon reais. Você pode obter os valores reais na tela Configuração do SAML do Atlassian Cloud. Explicamos os valores posteriormente no tutorial.

5. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Seu aplicativo Atlassian Cloud espera encontrar as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de Atributos de Token SAML. 

    Por padrão, o valor do **identificador de usuário** é mapeado para user.userprincipalname. Altere este valor para mapear para user.mail. Você também pode escolher qualquer outro valor apropriado de acordo com a configuração de sua organização, mas, na maioria dos casos, o email deve funcionar.

    ![O link de download do Certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Selecione **Salvar**.

    ![O botão Salvar de Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Para abrir a janela **Configurar o logon**, na seção **Configuração do Atlassian Cloud**, selecione **Configurar Atlassian Cloud**. 

9. Na seção **Referência Rápida**, copie a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML**. 

    ![Configuração do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. Para configurar o SSO para seu aplicativo, faça logon no portal do Atlassian usando com as credenciais de administrador.

11. Vá para **Administração de Site da Atlassian** > **Organizações e Segurança**. Se você ainda não fez isso, crie e atribua um nome para sua organização e, em seguida, no painel esquerdo, selecione **Domínios**.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. Selecione o modo em que você deseja verificar seu domínio: **DNS** ou **HTTPS**.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. Para verificação de DNS, na janela **Domínios**, selecione a guia **DNS** e, em seguida, faça o seguinte:

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Para copiar o valor para seu registro de texto (registro TXT), selecione **Cópia**.

    b. Para adicionar um registro, vá para a página de configurações no seu DNS.

    c. Selecione a opção de adicionar um novo registro e cole o valor que você copiou na janela **Domínios** para o campo **Valor**. Seu registro DNS também pode referir-se a ela como **Resposta** ou **Descrição**.

    d. O registro DNS também pode incluir os seguintes campos:
    
    * Na caixa **Tipo de registro**, digite **TXT**.
    * Na caixa **Alias/Host/nome**, deixe o valor padrão (@ ou em branco).
    * Na caixa **Tempo de vida (TTL)**, digite **86400**.
    
    e.  Salve o registro.

14. Retorne para a janela **Domínios** na administração da organização e, em seguida, selecione **Verificar domínio**. Na caixa **Domínio**, digite seu nome de domínio e, em seguida, selecione **Verificar domínio**.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > Como pode levar até 72h para que as alterações ao registro TXT entrem em vigor, você não saberá imediatamente se sua verificação de domínio foi bem-sucedida. Para exibir o status de verificação, verifique a janela **Domínios** logo depois de concluir este procedimento. O status atualizado será exibido como *Verificado*, conforme mostrado na imagem a seguir:
    > 
    > ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. Para verificação de HTTPS, na janela **Domínios**, selecione a guia **HTTPS** e, em seguida, faça o seguinte:

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. Para fazer o download do arquivo HTML, selecione **Fazer o download do arquivo**.

    b. Carregue o arquivo HTML no diretório raiz do seu domínio.

16. Retorne para a página **Domínios** na administração da organização e selecione **Verificar domínio**. Na janela **Verificar domínio**, na caixa **Domínio**, digite seu **nome de domínio** e, em seguida, selecione **Verificar domínio**.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. Se o processo de verificação puder localizar o arquivo que você carregou no diretório raiz, o status do domínio será atualizado para *Verificado*, conforme mostrado aqui:

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Para obter mais informações, consulte [Verificação de domínio Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

18. No painel à esquerda, selecione **Logon único SAML**. Se você ainda não fez isso, assine o Identity Manager da Atlassian.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. Na janela de **Adicionar configuração SAML**, faça o seguinte:

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Na caixa **ID da Entidade do Provedor de Identidade**, cole o ID da entidade SAML que você copiou do portal do Azure.

    b. Na caixa **URL de SSO do Provedor de Identidade**, cole a URL de serviço de logon único SAML que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure em arquivo .txt, copie o valor (sem as linhas *Begin Certificate* e *End Certificate*) e cole-os na caixa **Certificado X509 público**.
    
    d. Selecione **Salvar configuração**.
     
20. Para garantir que você configurou as URLs corretas, atualize as configurações do Azure AD fazendo o seguinte:
  
    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Na janela de SAML, copie a **ID de Entidade do SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **Identificador**.
    
    b. Na janela de SAML, copie a **URL do Serviço do Consumidor de Declaração de SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **URL de Resposta**.  
        A URL de logon é a URL do locatário do seu Atlassian Cloud. 

    > [!NOTE]
    > Se você for um cliente existente, depois de atualizar os valores da **ID de identidade SP** e **URL do Serviço de Consumidor de Declaração de SP** no portal do Azure, selecione **Sim, atualize a configuração**. Se você for um novo cliente, você pode ignorar esta etapa. 
    
21. No portal do Azure, selecione **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar esse aplicativo a partir da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida na seção **Configuração**, na parte inferior da janela. Para saber mais, confira a [documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

   ![Criar um usuário de teste do Azure AD][100]

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, selecione **Usuários e grupos** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Na janela **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo. 

    ![Criar um usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Para enviar um convite por email para o usuário, selecione **Convidar usuários**.  
    Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema. 

>[!NOTE] 
>Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao Atlassian Cloud. Para fazer isso, faça o seguinte:

![Atribuir a função de usuário][200] 

1. No portal do Azure, abra a exibição de **Aplicativos**, acesse a exibição de diretório e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de **Aplicativos**, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

6. Na janela **Usuários e grupos**, selecione **Selecionar**.

7. Na janela **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco **Atlassian Cloud** no Painel de Acesso, você deve entrar automaticamente no aplicativo Atlassian Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png


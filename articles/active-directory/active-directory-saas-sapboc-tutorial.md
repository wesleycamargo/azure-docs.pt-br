---
title: "Tutorial: integração do Azure Active Directory ao SAP Business Object Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a09e965b6fc9b89023c09092860fcf79773a4518
ms.contentlocale: pt-br
ms.lasthandoff: 08/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: integração do Azure Active Directory ao SAP Business Object Cloud

Neste tutorial, você aprenderá a integrar o SAP Business Object Cloud ao Azure AD (Azure Active Directory).

A integração do SAP Business Object Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao SAP Business Object Cloud
- Você pode habilitar os usuários a fazer logon automaticamente no SAP Business Object Cloud (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business Object Cloud, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do SAP Business Object Cloud habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP Business Object Cloud da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Adicionar o SAP Business Object Cloud da galeria
Para configurar a integração do SAP Business Object Cloud ao Azure AD, você precisará adicionar o SAP Business Object Cloud da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Business Object Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **SAP Business Object Cloud**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. No painel de resultados, selecione **SAP Business Object Cloud** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Business Object Cloud, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP Business Object Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP Business Object Cloud.

No SAP Business Object Cloud, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SAP Business Object Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do SAP Business Object Cloud](#creating-an-sap-business-object-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Business Object Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo SAP Business Object Cloud.

**Para configurar o logon único do Azure AD com o SAP Business Object Cloud, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **SAP Business Object Cloud**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Na seção **Domínio e URLs do SAP Business Object Cloud**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do Cliente SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html) para obter a URL de logon e obter o identificador ao baixar os metadados do SAP Business Object Cloud do console do administrador que é explicado posteriormente no tutorial. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Em outra janela do navegador da Web, faça logon em seu site de empresa do SAP Business Object Cloud como um administrador.

7. Vá para **Menu > Sistema > Administração**
    
    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. Na guia **Segurança**, clique em **[Editar – ícone Caneta]**.
    
    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. Selecione **SSO (Logon Único) do SAML** como método de autenticação.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Clique em **Baixar** para baixar os metadados do provedor de serviços. Recupere o valor **entityID** do arquivo e cole-o na caixa de texto **Identificador** na seção **URLs e Domínio do SAP Business Object Cloud Domain** no portal do Azure.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Na etapa **Carregar Metadados do Provedor de Identidade**, clique no botão **Carregar...** para carregar o arquivo de metadados que você baixou do portal do Azure. 

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Selecione o **Atributo de Usuário** apropriado que você deseja usar para sua implementação da lista para mapear para o provedor de identidade. Use a opção "Mapeamento de SAML Personalizado" para inserir um atributo personalizado na página de usuários ou selecione "Email" ou "USER ID" como o atributo de usuário. Por exemplo, **Email** é selecionado conforme mapeamos a declaração do identificador de usuário com o atributo userprincipalname na seção **"Atributos do Usuário"** no portal do Azure, que fornece o email de usuário único, que é enviado para o aplicativo SAP Business Object Cloud em cada resposta de SAML bem-sucedida.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Digite seu email na caixa de texto **Credencial de Logon (Email)** e clique no botão **Verificar Conta** para permitir que o sistema adicione suas credenciais de logon para sua conta.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Clique no ícone **Salvar**

    ![Salvar](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>Criar um usuário de teste do SAP Business Object Cloud

Para permitir que os usuários do Azure AD façam logon no SAP Business Object Cloud, eles devem ser provisionados no SAP Business Object Cloud. No caso do SAP Business Object Cloud, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do SAP Business Object Cloud como administrador.

2. Vá para **Menu > Segurança > Usuários**

    ![Adicionar Funcionário](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Na página **Usuários**, clique em **"+"** para adicionar novos detalhes do usuário e executar as seguintes etapas:

    ![Convidar Pessoas](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. Na caixa de texto **ID DE USUÁRIO**, digite a ID de usuário, por exemplo, Brenda.

    b. Na caixa de texto **NOME**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **SOBRENOME**, digite o sobrenome do usuário, como Fernandes.

    d. Na caixa de texto **NOME DE EXIBIÇÃO**, digite o nome completo do usuário, como Brenda Fernandes.

    e. Na caixa de texto **EMAIL**, digite o endereço de email do usuário, como brittasimon@contoso.com.

    f. Selecione a função apropriada para o usuário.

      ![Função](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. Clique no ícone **Salvar**. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao SAP Business Object Cloud.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SAP Business Object Cloud, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP Business Object Cloud**.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP Business Object Cloud no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo SAP Business Object Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png



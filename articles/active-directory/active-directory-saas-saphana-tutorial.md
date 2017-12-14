---
title: "Tutorial: integração do Azure Active Directory com o SAP HANA | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 855525e2c1d3c33cc7134bbc1cd9b53ca59e1a70
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: integração do Azure Active Directory com o SAP HANA

Neste tutorial, você aprenderá a integrar o SAP HANA ao Azure AD (Azure Active Directory).

Quando você integra SAP HANA com Azure Active Directory, você obtém os seguintes benefícios:

- No Azure Active Directory, é possível controlar quem tem acesso ao SAP HANA.
- Você pode permitir que os usuários façam logon automaticamente no SAP HANA com as suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP HANA, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do SAP HANA com SSO (logon único) habilitado
- Uma instância do HANA em execução em qualquer IaaS público, localmente, em VMs do Azure ou em grandes instâncias SAP no Azure
- A Interface da Web de Administração do XSA, bem como o HANA Studio instalado na instância do HANA

> [!NOTE]
> Não recomendamos o uso de um ambiente de produção do SAP HANA para testar as etapas neste tutorial. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Obtenha uma [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/) do Azure Active Directory se você ainda não tiver um ambiente de avaliação do Azure Active Directory.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP HANA da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="add-sap-hana-from-the-gallery"></a>Adicionar o SAP HANA da galeria
Para configurar a integração do SAP HANA ao Azure Active Directory, adicione o SAP HANA da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP HANA da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP HANA**. Em seguida, selecione **SAP HANA** a partir do painel de resultados. Por fim, selecione o botão **Adicionar** para adicionar o aplicativo. 

    ![O novo aplicativo](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o SAP HANA, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do SAP HANA é equivalente a um usuário do Azure Active Directory. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure Active Directory e o usuário relacionado no SAP HANA.

No SAP HANA, dê ao valor **Username** o mesmo valor do **nome de usuário** no Azure Active Directory. Essa etapa estabelece o vínculo entre os dois usuários.

Para configurar e testar o logon único do Azure Active Directory com o SAP HANA, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configuring-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do SAP HANA](#creating-a-sap-hana-test-user) para ter um equivalente de Britta Simon no SAP HANA vinculado à representação do usuário no Azure Active Directory.
4. [Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#testing-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo SAP HANA.

**Para configurar o logon único do Azure Active Directory com o SAP HANA, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SAP HANA**, selecione **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, sob **Logon baseado em SAML**, selecione o **Modo**.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Na seção **Domínio e URLs do SAP HANA**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Na caixa **Identificador**, digite as seguintes informações: `HA100` 

    b. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SAP HANA](https://cloudplatform.sap.com/contact.html) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, selecione **XML de Metadados**. Em seguida, salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se o certificado não estiver ativo, ative-o clicando na caixa de seleção **Tornar o novo certificado ativo** no Azure Active Directory. 

5. O aplicativo SAP HANA espera que as declarações SAML estejam em um formato específico. As capturas de tela a seguir mostram um exemplo desse formato. 

    Aqui, nós mapeamos o **Identificador de Usuário** com a função **ExtractMailPrefix()** função de **user.mail**. Isso dá o valor de prefixo do e-mail do usuário, que é a ID de usuário único. Essa ID de usuário é enviada para o aplicativo SAP HANA em cada resposta bem-sucedida.

    ![Configurar o logon único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Na seção **Atributos do usuário** da caixa de diálogo do **Logon único**, execute as seguintes etapas:

    a. Na lista suspensa **Identificador de Usuário**, selecione **ExtractMailPrefix**.
    
    b. Na lista suspensa **Email**, selecione **user.mail**.

7. Selecione o botão **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar o logon único no lado do SAP HANA, faça logon no seu **Console Web XSA do HANA** navegando até o respectivo end-point de HTTPS.

    > [!NOTE]
    > Na configuração padrão, a URL redireciona a solicitação para uma tela de logon, o que exige as credenciais de um usuário de banco de dados SAP HANA autenticado. O usuário que faz logon deve ter permissões para executar tarefas de administração do SAML.

9. Na Interface da Web XSA, acesse **Provedor de identidade do SAML**. A partir daí, selecione o botão  **+**  na parte inferior da tela para exibir o painel **Adicionar informações do provedor de identidade**. Em seguida, execute as etapas a seguir:

    ![Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. No painel **Adicionar informações do provedor de identidade**, cole o conteúdo do XML de metadados (que você fez o download do Portal do Azure) na caixa de **Metadados**.

    ![Configurações para Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML for válido, o processo de análise extrai as informações necessárias para os campos **Assunto, ID de entidade e emissor** na área da tela de **Dados gerais**. Ele também extrai as informações necessárias para os campos de URL na área da tela de **Destino**, por exemplo, os campos de  **URL de Base e URL de Logon único (*)**.

    ![Configurações para Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Na caixa **Nome** da área **Dados Gerais** da tela, insira um nome para o novo provedor de identidade SSO de SAML.

    > [!NOTE]
    > O nome do IDP do SAML é obrigatório e deve ser único. Ele aparece na lista de IDPs do SAML disponíveis que é exibida quando você seleciona o SAML como o método de autenticação para aplicativos do SAP HANA XS. Por exemplo, você pode fazer isso na área da tela **Autenticação** da ferramenta de administração do artefato XS.

10. Selecione **Salvar** para salvar os detalhes do provedor de identidade SAML e adicione o novo IdP de SAML à lista de IdPs de SAML conhecidos.

    ![Botão Salvar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. No Studio HANA, dentro das propriedades do sistema do guia **Configuração**, filtre as configurações por **saml**. Em seguida, ajuste **assertion_timeout** de **10 segundos** para **120 segundos**.

    ![configuração assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo! Depois de adicionar esse aplicativo a partir da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração**, na parte inferior. Saiba mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do Azure Active Directory][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Para exibir uma lista de usuários, vá para **Usuários e grupos**. Selecione **Todos os usuários**.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o **endereço de email** de Brenda Fernandes.

    c. Selecione **Mostrar Senha** e anote o valor da senha.

    d. Selecione **Criar**.
 
### <a name="create-a-sap-hana-test-user"></a>Criar um usuário de teste SAP HANA

Para permitir que os usuários do Azure Active Directory entrem no SAP HANA, você deverá provisioná-los no SAP HANA.
O SAP HANA dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Se você precisar criar um usuário manualmente, execute as seguintes etapas:

>[!NOTE]
>Você pode alterar a autenticação externa usada pelo usuário. Eles poderão autenticar com um sistema externo, como o Kerberos. Para obter informações detalhadas sobre identidades externas, contate seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e habilite o usuário do BD para SSO do SAML.

    ![Criar usuário](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Selecione a caixa de seleção invisível à esquerda do **SAML** e selecione o link **Configurar**.

3. Selecione **Adicionar** para adicionar o IDP do SAML.  Selecione o IDP do SAML apropriado e, em seguida, selecione **OK**.

4. Adicione a **Identidade externa** (nesse caso, BrittaSimon) ou escolha **Qualquer**. Depois, selecione **OK**.

    >[!Note]
    >Se a caixa de seleção **Qualquer** não estiver marcada, o nome de usuário no HANA deverá corresponder exatamente ao nome do usuário no nome UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com se torna BrittaSimon em HANA.)

5. Para fins de teste, atribua todas as funções **XS** ao usuário.

    ![Atribuição de funções](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Você deve conceder as permissões apropriadas apenas para seus casos de uso.

6. Salve o usuário.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP HANA.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SAP HANA, execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Vá para a exibição de diretório, e acesse **Aplicativos empresariais**. Selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP HANA**.

    ![Atribuir usuário](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Selecione o botão **Adicionar**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de **Usuários**.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grupos**.

7. Selecione o botão **Atribuir** na caixa de diálogo **Adicionar atribuição**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar SAP HANA no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SAP HANA.
Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png


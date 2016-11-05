---
title: 'Tutorial: Integração do Azure Active Directory ao Hightail | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hightail.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jeedes

---
# Tutorial: Integração do Azure Active Directory ao Hightail
O objetivo desse tutorial é mostrar como integrar o Hightail ao Azure AD (Azure Active Directory).

A integração do Hightail ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Hightail
* Você pode habilitar os usuários a entrar automaticamente no Hightail (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD ao Hightail, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Hightail

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Hightail a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## Adição do Hightail a partir da galeria
Para configurar a integração do Hightail ao Azure AD, você precisará adicionar o Hightail da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Hightail da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Hightail**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)
7. No painel de resultados, selecione **Hightail** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

## Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Hightail baseado em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Hightail é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hightail.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Hightail.

Para configurar e testar o logon único do Azure AD com o Hightail, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Hightail](#creating-a-hightail-test-user)** - para ter um equivalente de Brenda Fernandes no Hightail que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Hightail.

O aplicativo Hightail espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png)

**Para configurar o logon único do Azure AD com o Hightail, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **Hightail**, no menu superior, clique em **Atributos**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png)
2. Na caixa de diálogo **Atributos de token SAML**, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
   
   | Nome do atributo | Valor do atributo |
   | --- | --- |
   | Nome |user.givenname |
   | Sobrenome |user.surname |
   | Email |user.mail |
   | UserIdentity |user.mail |
   
    a. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png)

    b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.

    d. Clique em **Concluído**.




1. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)
2. Na página **Como você deseja que os usuários façam logon no Hightail**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png)
3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo em **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png)

    a. Na caixa de texto **URL de resposta**, digite a URL no seguinte padrão: **"https://www.hightail.com/samlLogin?phi\_action=app/samlLogin&subAction=handleSamlResponse"**

    b. Clique em **Próximo**.

1. Para configurar o aplicativo em **modo iniciado pelo SP**, na página da caixa de diálogo **Definir Configurações de Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png)
   
    a. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para entrar em seu aplicativo Hightail usando o seguinte padrão: **https://www.hightail.com/loginSSO**. Essa é a página de Logon comum para todos os clientes que querem usar SSO.
   
    b. Clique em **Próximo**.
2. Na página **Configurar logon único no Hightail**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo de certificado codificado em base 64 no computador.

    b. Clique em **Próximo**.

    > [AZURE.NOTE] Antes de configurar o logon único no aplicativo Hightail, coloque seu domínio de email na lista branca com a equipe do Hightail para que todos os usuários que usam esse domínio possam aproveitar a funcionalidade de Logon Único.

1. Para configurar o SSO para o aplicativo, você precisa entrar no locatário Hightail como administrador.
   
    a. No menu na parte superior, clique na guia **Conta** e selecione **Configurar SAML**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png)

    b. Marque a caixa de seleção **Habilitar Autenticação SAML**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png)

    c. Abra o certificado codificado em base 64 no Bloco de Notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado de Assinatura de Token SAML**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png)


    d. Copie a URL de Logon Remoto do Azure AD para **Autoridade SAML (Provedor de Identidade)** no Hightail.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Para configurar o aplicativo em **modo iniciado pelo IDP**, selecione **"Logon iniciado pelo IdP (Provedor de Identidade)"**. Se usar o **modo iniciado pelo SP**, selecione **"Logon iniciado pelo SP (Provedor de Serviços)"**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Copie a URL do consumidor SAML para sua instância e cole-a na caixa de texto **URL de resposta**, conforme mostrado na etapa 4.

    g. Clique em **Salvar**.



1. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)
5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png)
   
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   
    b. Na caixa de texto **Nome do Usuário**, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criação de um usuário de teste do Hightail
O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Hightail.

Não há itens de ação para você nesta seção. O Hightail dá suporte ao provisionamento de usuários just-in-time com base nas declarações personalizadas. Se você tiver configurado as declarações personalizadas, como mostrado na seção **[Configurando o Logon Único do Azure AD](#configuring-azure-ad-single-sign-on)** acima, um usuário será criado automaticamente no aplicativo, caso ainda não exista.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Hightail em [support@hightail.com](mailto:support@hightail.com).
> 
> 

### Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo-lhe acesso ao Hightail.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Hightail, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Hightail**.
   
    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

![Atribuir usuário][205]

### Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hightail no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Hightail.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->
<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Salesforce | Microsoft Azure"
    description="Saiba como usar o Salesforce com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>


#<a name="tutorial:-how-to-integrate-salesforce-with-azure-active-directory"></a>Tutorial: Como integrar o Salesforce ao Active Directory do Azure

Este tutorial mostrará como conectar o ambiente Salesforce ao Active Directory do Azure. Você aprenderá como configurar o logon único para Salesforce, como habilitar o provisionamento automatizado de usuários e como atribuir usuários para que tenham acesso ao Salesforce.

##<a name="prerequisites"></a>Pré-requisitos

1. Para acessar o Azure Active Directory usando o [portal clássico do Azure](https://manage.windowsazure.com), você precisará ter uma assinatura válida do Azure.

2. Você deve ter um locatário válido em [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Se você estiver usando uma conta Salesforce.com de **avaliação** , não será possível configurar o provisionamento automatizado de usuários. Contas de avaliação não têm o acesso à API necessária habilitado até que sejam compradas.
> 
> Você pode contornar essa limitação usando uma [conta de desenvolvedor gratuita](https://developer.salesforce.com/signup) para concluir este tutorial.

Se você estiver usando um ambiente de área restrita do Salesforce, consulte o [Tutorial de integração de área restrita do Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Tutoriais em vídeo

Você pode acompanhar este tutorial usando os vídeos abaixo.

**Tutorial em vídeo parte 1: Como Habilitar o Logon Único**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Tutorial em vídeo parte 2: Como Automatizar o Provisionamento de Usuários**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1:-add-salesforce-to-your-directory"></a>Etapa 1: Adicionar o Salesforce ao seu diretório

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Selecione Active Directory no painel de navegação esquerdo.][0]

2. Na lista **Diretório** , selecione o diretório ao qual você deseja adicionar o Salesforce.

3. Clique em **Aplicativos** no menu superior.

    ![Clique em Aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

    ![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Clique em Adicionar um aplicativo da galeria.][3]

6. Na **caixa de pesquisa**, digite **Salesforce**. Em seguida, selecione **Salesforce** nos resultados e clique em **Conclui** para adicionar o aplicativo.

    ![Adicione o Salesforce.][4]

7. Agora, você verá a página Início Rápido para o Salesforce:

    ![Página de Início Rápido do Salesforce no AD do Azure][5]

##<a name="step-2:-enable-single-sign-on"></a>Parte 2: Habilitar logon único

1. Antes de configurar o logon único, é preciso configurar e implantar um domínio personalizado para o ambiente do Salesforce. Para obter instruções sobre como fazer isso, consulte [Configurar um Nome de Domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Na página Início Rápido do Salesforce no AD do Azure, clique no botão **Configurar logon único** .

    ![Botão de configuração de logon único][6]

3. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria que os usuários fizessem logon no Salesforce?" Selecione **Logon Único do Azure AD** e, em seguida, clique em **Avançar**.

    ![Selecione Logon Único do AD do Azure][7]

    > [AZURE.NOTE] Para saber mais sobre as diferentes opções de logon único, [clique aqui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Na página **Definir Configurações de Aplicativo**, preencha a **URL de Logon** digitando a URL do domínio do Salesforce usando o seguinte formato:
 - Conta empresarial: `https://<domain>.my.salesforce.com`
 - Conta de desenvolvedor: `https://<domain>-dev-ed.my.salesforce.com` 

    ![Digite a URL de Logon][8]

5. Na página **Configurar logon único no Salesforce**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado em seu computador.

    ![Baixar certificado][9]

6. Abra uma nova guia no navegador e faça logon em sua conta de administrador do Salesforce.

7. No painel de navegação **Administrador**, clique em **Controles de Segurança** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Clique em Configurações de Logon Único, em Controles de Segurança][10]

8. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Clique no botão Editar][11]

    > [AZURE.NOTE] Se não for possível habilitar as configurações de logon único para a conta do Salesforce, talvez seja necessário entrar em contato com o suporte do Salesforce para que o recurso seja habilitado.

9. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Selecione SAML Habilitado][12]

10. Para definir as configurações de logon único do SAML, clique em **Novo**.

    ![Selecione SAML Habilitado][13]

11. Na página **Edição de Configuração de Logon Único do SAML** , realize as seguintes configurações:

    ![Captura de tela das configurações que você deve fazer][14]

 - Para o campo **Nome** , digite um nome amigável para essa configuração. Fornecer um valor para **Nome** preenche automaticamente a caixa de texto **Nome da API**.

 - No Azure AD, copie o valor **URL do Emissor** e, em seguida, cole-o no campo **Emissor** no Salesforce.

 - Na **caixa de texto Id da Entidade**, digite seu nome de domínio do Salesforce usando o seguinte padrão:
     - Conta empresarial: `https://<domain>.my.salesforce.com`
     - Conta de desenvolvedor: `https://<domain>-dev-ed.my.salesforce.com`

 - Clique em **Procurar** ou **Escolher Arquivo** para abrir a caixa de diálogo **Escolher Arquivo para Carregar**, selecione seu certificado do Salesforce e, em seguida, clique em **Abrir** para carregar o certificado.

 - Para **Tipo de Identidade do SAML**, selecione **A declaração contém o nome de usuário salesforce.com do Usuário**.

 - Para **Local de Identidade do SAML**, selecione **A identidade está no elemento NameIdentifier da instrução de assunto**

 - No Azure AD, copie o valor da **URL de Logon Remoto** e, em seguida, cole-o no campo **URL de Logon do Provedor de Identidade** no Salesforce.

 - Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **Redirecionamento HTTP**.

 - Finalmente, clique em **Salvar** para aplicar as configurações de logon único do SAML.

12. No painel de navegação à esquerda no Salesforce, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e, em seguida, clique em **Meu Domínio**.

    ![Clique em Meu Domínio][15]

13. Role para baixo até a seção **Configuração de Autenticação** e, em seguida, clique no botão **Editar**.

    ![Clique no botão Editar][16]

14. Na seção **Serviço de Autenticação**, selecione o nome amigável da sua configuração de SSO de SAML e, em seguida, clique em **Salvar**.

    ![Selecione a configuração de SSO][17]

    > [AZURE.NOTE] Se mais de um serviço de autenticação estiver selecionado, quando os usuários tentarem iniciar o logon único em seu ambiente do Salesforce, serão solicitados a selecionar o serviço de autenticação com o qual desejam entrar. Se você não quiser que isso aconteça, deverá **deixar todos os outros serviços de autenticação desmarcados**.

15. No AD do Azure, marque a caixa de seleção de confirmação de configuração de logon único para habilitar o certificado que você carregou para o Salesforce. Em seguida, clique em **Próximo**.

    ![Marque a caixa de seleção de confirmação][18]

16. Na página final da caixa de diálogo, digite um endereço de email se você quiser receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único. 

    ![Digite seu endereço de email.][19]

17. Clique em **Concluir** para fechar a caixa de diálogo. Para testar sua configuração, consulte a seção intitulada [Atribuir Usuários ao Salesforce](#step-4-assign-users-to-salesforce).

##<a name="step-3:-enable-automated-user-provisioning"></a>Etapa 3: Habilitar o provisionamento automatizado de usuários

1. Na página Início Rápido do AD do Azure para o Salesforce, clique no botão **Configurar provisionamento do usuário** .

    ![Clique no botão Configurar Provisionamento do Usuário][20]

2. Na caixa de diálogo **Configurar provisionamento do usuário** , digite o nome de usuário e a senha de administrador do Salesforce.

    ![Digite o nome de usuário ou a senha de administrador][21]

    > [AZURE.NOTE] Se você estiver configurando um ambiente de produção, a prática recomendada é criar uma nova conta de administrador no Salesforce especificamente para esta etapa. Essa conta deve ter o perfil do **Administrador de Sistema** atribuído a ela no Salesforce.

3. Para obter o token de segurança do Salesforce, abra uma nova guia e entre na mesma conta de administrador do Salesforce. No canto superior direito da página, clique em seu nome e, em seguida, clique em **Minhas Configurações**.

    ![Clique em seu nome, depois clique em Minhas Configurações][22]

4. No painel de navegação esquerdo, clique em **Pessoal** para expandir a seção correspondente e, em seguida, clique em **Redefinir Meu Token de Segurança**.

    ![Clique em seu nome, depois clique em Minhas Configurações][23]

5. Na página **Redefinir Meu Token de Segurança**, clique no botão **Redefinir Token de Segurança**.

    ![Leia os avisos.][24]

6. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce.com que contenha o novo token de segurança.

7. Copie o token, vá até a janela do AD do Azure e cole-o no campo **Token de Segurança do Usuário** . Em seguida, clique em **Próximo**.

    ![Cole o token de segurança][25]

8. Na página de confirmação, é possível optar por receber notificações de email quando ocorrerem falhas de provisionamento. Clique em **Concluir** para fechar a caixa de diálogo.

    ![Digite seu endereço de email para receber notificações][26]

##<a name="step-4:-assign-users-to-salesforce"></a>Etapa 4: Atribuir usuários ao Salesforce

1. Para testar sua configuração, comece criando uma nova conta de teste no diretório.

2. Na página Início Rápido do Salesforce, clique no botão **Atribuir Usuários** .

    ![Clique em Atribuir Usuários][27]

3. Selecione seu usuário de teste e clique no botão **Atribuir** na parte inferior da tela:

 - Se você ainda não tiver habilitado o provisionamento automatizado de usuários, verá o seguinte aviso para confirmar:

        ![Confirm the assignment.][28]

 - Se você tiver habilitado o provisionamento automatizado de usuários, verá um aviso para definir o tipo de perfil do Salesforce que o usuário deve ter. Usuários recentemente provisionados devem aparecer no ambiente do Salesforce após alguns minutos.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Se você estiver provisionando para um ambiente de **desenvolvedor** do Salesforce, terá um número bem limitado de licenças disponíveis para cada perfil.  Portanto, é melhor provisionar usuários para o perfil **Usuário Chatter Free**, que tem 4.999 licenças disponíveis.

4. Para testar suas configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), depois entre na conta de teste e clique em **Salesforce**.

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png



<!--HONumber=Oct16_HO2-->



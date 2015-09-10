<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao ServiceNow | Microsoft Azure" description="Aprenda a usar o ServiceNow com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o ServiceNow
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=521880).
  
O objetivo deste tutorial é mostrar a integração do Azure ao ServiceNow.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no ServiceNow
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao ServiceNow poderão fazer logon único no aplicativo em seu site de empresa ServiceNow (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o ServiceNow
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Cenário")
##Habilitando a integração de aplicativos para o ServiceNow
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ServiceNow.

###Para habilitar a integração de aplicativos com o ServiceNow, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na caixa **pesquisar**, digite **ServiceNow**.

    ![Galeria de aplicativos](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ServiceNow** e clique em **Concluir** para adicionar o aplicativo.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ServiceNow com sua conta do AD do Azure usando federação baseada em protocolo SAML.

Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Dropbox for Business. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no ServiceNow**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do ServiceNow**, digite sua URL usando o seguinte padrão "*https://<InstanceName>.servicenow.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no ServiceNow**, clique em **Baixar certificado**, salve o arquivo de certificado em seu computador e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar logon único")

5.  Em seu locatário ServiceNow, na barra de navegação à esquerda, clique em **Propriedades** para abrir a página **Propriedades do Logon único do SAML 2.0**.


6. Na página **Propriedades do Logon único do SAML 2.0**, execute as seguintes etapas:

     6.1. Para **Ativar autenticação externa**, selecione **Sim**.


     6.2. Na caixa de texto **A URL do Provedor de Identidade que emitirá o token de segurança do SAML2 com informações do usuário**, digite **https://sts.windows.net/<GUID do locatário>/**.


     6.3. Na caixa de texto **A base URL para o serviço AuthnRequest do Provedor de Identidade**, digite **https://login.windows.net/<GUID do locatário>/saml2**.


     6.4. Na caixa de texto **A base URL para o serviço SingleLogoutRequest do Provedor de Identidade**, digite **https://login.windows.net/<GUID do locatário>/saml2**.


     6.5. Na caixa de texto **A ligação de protocolo para serviço SingleLogoutRequest do provedor de identidade**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

     6.6. Para **Entrar em LogoutRequest**, selecione **Sim**.

     6.7. Na caixa de texto **Quando o logon único do SAML 2.0 falhar porque a sessão não foi autenticada ou por ser o primeiro logon, redirecione para essa URL**, digite **https://login.windows.net/<GUID do locatário>/saml2**.

  

7. Na seção **Propriedades do Provedor de Serviço (Service-Now)**, execute as seguintes etapas:

     7.1. Na caixa de texto **A URL para a home page de instância do Service-now**, digite a URL para a home page de instância do ServiceNow. A URL da página inicial da instância do ServiceNow é uma concatenação do seu **URL de locatário do ServiceNow** e **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** <br><br>   ![Home page de instância do Service-now](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Home page de instância do Service-now")


     7.2. Na caixa de texto **A identificação da entidade ou o emissor**, digite a URL do seu locatário.

     7.3. Na caixa de texto **URI do público que aceita o token SAML2**, digite a URL do seu locatário.

     7.4. Na caixa de texto **O campo da tabela de usuário para corresponder ao elemento NameID do assunto na SAMLResponse**, digite **email**.

     7.5. Na caixa de texto **A política NameID para usar para retornar ao Assunto do NameID na SAMLResponse**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

     7.6 Deixe desmarcada **Criar uma solicitação de AuthnContextClass na instrução AuthnRequest**.

     7.7 Na caixa de texto **O método AuthnContextClassRef que será incluído em nosso SAML 2.0 AuthnRequest para o Provedor de Identidade**, digite **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.



8. Na seção **Configurações Avançadas**, execute as seguintes etapas:

     8.1. Na caixa de texto **O número de segundos antes da restrição "notBefore", ou após a restrição "notOnOrAfter" para considerar ainda válido**, digite **60**.


9. Para salvar a configuração, clique em **Salvar**.

10. Na barra de navegação à esquerda, clique em **Certificado** para abrir a página **Certificado**.



11. Para carregar seu certificado, na página de certificados, execute as seguintes etapas:

     11.1. Clique em **Novo**.

     11.2. Na caixa de texto **Nome**, digite **SAML 2.0**.

     11.3. Selecione **Ativo**.

     11.4. Para **Formato**, selecione **PEM**.

     11.5. Crie um arquivo codificado em base-64 a partir do certificado baixado.  > [AZURE.NOTE]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

     11.6. Em **Bloco de notas**, abra o arquivo codificado em Base 64 e, em seguida, copie o conteúdo desse arquivo para a área de transferência.

     11.7. Na caixa de texto **Certificado PEM**, cole o conteúdo da área de transferência.

     11.8. Clique em **Enviar**.



12. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em Concluir para fechar a caixa de diálogo Configurar Logon Único. <br><br> ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Configurar logon único")




##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no ServiceNow.


### Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar provisionamento do usuário**. <br><br> ![Provisionamento do usuário](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisionamento do usuário")


2. Na página **Inserir suas credenciais do ServiceNow para habilitar o provisionamento automático de usuários**, forneça as seguintes configurações: Configurar Provisionamento do Usuário

     2.1. Na caixa de texto **Nome de Instância do ServiceNow**, digite o nome da instância do ServiceNow.

     2.2. Na caixa de texto **Nome de Usuário Administrador do ServiceNow**, digite o nome da conta de administrador do ServiceNow.

     2.3. Na caixa de texto **Senha de Administrador do ServiceNow**, digite a senha para essa conta.

     2.4. Clique em **validar** para verificar a sua configuração.

     2.5. Clique no botão **Avançar** para abrir a página **Próximas etapas**.

     2.6. Se você deseja provisionar esse aplicativo a todos os usuários, selecione “**Provisionar automaticamente todas as contas de usuário no diretório para este aplicativo**”. <br><br> ![Próximas etapas](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Próximas etapas")

     2.7. Na página **Próximas etapas**, clique em **Concluir** para salvar sua configuração.











##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao ServiceNow, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ServiceNow**, clique **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!-----HONumber=August15_HO8-->
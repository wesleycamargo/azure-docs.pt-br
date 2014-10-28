<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Integração do AD do Azure com o Google Apps

O objetivo deste tutorial é mostrar a integração do Azure com o Google Apps. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste no Google Apps

Se você não tiver um locatário válido no Google Apps, você poderá, por exemplo, inscrever-se para obter uma conta de avaliação no Google Apps para o site da empresa.

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

Habilitando a integração de aplicativos com o Google Apps
Configurando o logon único
Habilitando o acesso à API do Google Apps
 Adicionar domínios personalizados
Configurando o provisionamento de usuários

# Habilitando a integração de aplicativos com o Google Apps

O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Google Apps.

## Para habilitar a integração de aplicativos com o Google Apps, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.
2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
4.  Clique em **Adicionar** na parte inferior para abrir a caixa de diálogo **Adicionar Aplicativo**.
5.  Na caixa de diálogo **Integrar um aplicativo com o AD do Azure**, clique em **Gerenciar o acesso a um aplicativo**.
6.  Na página **Selecionar um aplicativo** para gerenciar, selecione **Google Apps** na lista de aplicativos.
7.  Clique no botão **Concluir** para adicionar o aplicativo e fechar a caixa de diálogo.

# Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Google Apps com sua conta do AD do Azure usando federação baseada em protocolo SAML.

## Para configurar o logon único federado, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, selecione **Active Directory** no painel de navegação à esquerda para abrir a página da caixa de diálogo Active Directory.
2.  Na lista Diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
3.  Selecione **APLICATIVOS** no menu de nível superior.
4.  Na lista de aplicativos, selecione **Google Apps** para abrir a caixa de diálogo de configuração do Google Apps.
5.  Para abrir a caixa de diálogo **CONFIGURAR LOGON ÚNICO**, clique em **Configurar logon único.**

    ![Google\_Tutorial\_01][Google\_Tutorial\_01]

6.  Na página da caixa de diálogo Selecionar o modo de logon único para este aplicativo, selecione Usuários são autenticados com sua conta no AD do Azure como MODO e clique no botão Avançar.

    ![Google\_Tutorial\_02][Google\_Tutorial\_02]

7.  Na página da caixa de diálogo **Configurar URL do Aplicativo**, na caixa de texto **URL DO LOCATÁRIO DO GOOGLE APPS**, digite a URL do locatário do Google Apps e, em seguida, clique no botão **Avançar**.

    ![Google\_Tutorial\_03][Google\_Tutorial\_03]

8.  Na página da caixa de diálogo **Configurar logon único no Google Apps**, execute as seguintes etapas e, em seguida, clique no botão **Concluir**.

    -   Clique em **Baixar certificado** e salve o certificado como **c:\\googleapps.cer**.
    -   Abra a página de logon do Google Apps e, em seguida, efetue logon

    ![Google\_Tutorial\_04][Google\_Tutorial\_04]

    -   No **Console do administrador**, clique em Segurança

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    Se o ícone de Segurança não estiver visível, você deve clicar em Mais controles na parte inferior da página.

9.  Na página **Segurança**, clique em configurações **Avançadas**.

    ![Google\_Tutorial\_06][Google\_Tutorial\_06]

10. Na seção configurações **Avançadas** da página, selecione **Configurar logon único**.

    ![Google\_Tutorial\_07][Google\_Tutorial\_07]

11. Na página Configurar logon único, execute as seguintes etapas:

    ![Google\_Tutorial\_08][Google\_Tutorial\_08]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Na página da caixa de diálogo **Configurar logon único no Google Apps** no Portal do AD do Azure, clique no botão Concluir.

Agora, você pode ir para o [Painel de Acesso][Painel de Acesso] e testar o logon único no Google Apps.

# Habilitando o acesso à API do Google Apps

Ao integrar o Active Directory do Azure com o Google Apps para provisionamento de usuários, você deve habilitar o acesso à API para seu locatário no Google Apps.

## Para habilitar o acesso à API do Google Apps

1.  Efetue logon em seu locatário do Google Apps.
2.  No **Console do administrador**, clique em **Segurança**.

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    Se o ícone de Segurança não estiver visível, clique em Mais controles na parte inferior do Console de Administração.

3.  Na página Segurança, clique em **Referência à API** para abrir a página da caixa de diálogo de configuração relacionada.
4.  Selecione **Habilitar o acesso à API**.

    ![Google\_Tutorial\_09][Google\_Tutorial\_09]

    </p>

# Adicionando domínios personalizados

A configuração do provisionamento de usuários com o Google Apps requer que o domínio do AD do Azure e o domínio do Google Apps tenham o mesmo FQDN (nome de domínio totalmente qualificado). No entanto, quando você estiver, por exemplo, usando locatários de avaliação para testar o cenário deste tutorial, os FQDNs de seus locatários normalmente não coincidem. Para resolver esse problema, você pode configurar domínios personalizados no AD do Azure e no Google Apps.
A configuração de um domínio personalizado requer o acesso ao arquivo de zona DNS do seu domínio público.

![Google\_Tutorial\_10][Google\_Tutorial\_10]

## Para adicionar um domínio personalizado ao AD do Azure, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, selecione **Active Directory** no painel de navegação à esquerda para abrir a página da caixa de diálogo **Active Directory**.
2.  Na lista Diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
3.  Selecione **DOMÍNIOS** no menu de nível superior.
4.  Para abrir a caixa de texto **ADICIONAR O NOME DO DOMÍNIO**, digite seu nome de domínio e, em seguida, clique em **Adicionar**.
5.  Clique em **Avançar** para abrir a página da caixa de diálogo **Verificar seu nome de domínio**.

    ![Google\_Tutorial\_11][Google\_Tutorial\_11]

6.  Selecione um **TIPO DE REGISTRO**e, em seguida, registre o registro selecionado em seu arquivo de zona DNS.

    ![Google\_Tutorial\_12][Google\_Tutorial\_12]

7.  Usando o **comando nslookup**, você deve verificar se o registro DNS foi registrado com êxito.

    ![Google\_Tutorial\_13][Google\_Tutorial\_13]

## Para adicionar um domínio personalizado no Google Apps, execute as seguintes etapas:

1.  Efetue logon em seu locatário do Google Apps.
2.  No **Console do administrador**, clique em **Segurança**.

    ![Google\_Tutorial\_14][Google\_Tutorial\_14]

3.  Clique em **Adicionar um domínio personalizado**.

    ![Google\_Tutorial\_15][Google\_Tutorial\_15]

4.  Clique em **Usar um domínio que você já possui**e, em seguida, clique em **Continuar**.

    ![Google\_Tutorial\_16][Google\_Tutorial\_16]

5.  Digite o nome do seu domínio personalizado e, em seguida, clique em **Continuar**.

    ![Google\_Tutorial\_17][Google\_Tutorial\_17]

6.  Conclua as etapas para verificar a propriedade do domínio.

    Se já tiver configurado o logon único federado, você poderá atualizar a URL de locatário do Google Apps em sua configuração de logon único federado.

# Configurando o provisionamento de usuários

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Google Apps.

## Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, selecione **Active Directory** no painel de navegação à esquerda para abrir a página da caixa de diálogo **Active Directory**.
2.  Na lista Diretório, selecione o diretório a ser aberto para sua página de configuração do diretório.
3.  Selecione **APLICATIVOS** no menu de nível superior.
    Na lista de aplicativos, selecione **Google Apps** para abrir a caixa de diálogo de configuração do **Google Apps**.
4.  Para abrir a caixa de diálogo **CONFIGURAR SINCRONIZAÇÃO DA CONTA**, clique em **Configurar sincronização da conta**.
5.  Na página da caixa de diálogo **CONFIGURAR SINCRONIZAÇÃO DA CONTA**, forneça o nome do domínio do Google Apps, o nome do usuário do Google Apps e a senha do Google Apps e clique no botão **Avançar**.

    ![Google\_Tutorial\_18][Google\_Tutorial\_18]

6.  Na página da caixa de diálogo **Confirmação**, clique no botão **Concluir** para fechar a caixa de diálogo **CONFIGURAR SINCRONIZAÇÃO DA CONTA**.

Agora, você pode criar uma conta de teste, aguardar 10 minutos e verificar se a conta foi sincronizada no Google Apps.

Consulte também
[Práticas recomendadas para gerenciar as melhorias de acesso ao aplicativo para acesso ao aplicativo do Active Directory do Azure]
[Práticas recomendadas para gerenciar as melhorias de acesso ao aplicativo para acesso ao aplicativo do Active Directory do Azure]

  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [Painel de Acesso]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [Práticas recomendadas para gerenciar as melhorias de acesso ao aplicativo para acesso ao aplicativo do Active Directory do Azure]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx

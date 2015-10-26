<properties
   pageTitle="Tutorial: Integração do Google Apps ao Active Directory do Azure | Microsoft Azure"
   description="Saiba como usar o Google Apps com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#Tutorial: Como integrar o Google Apps ao Active Directory do Azure

Este tutorial mostrará como conectar seu ambiente do Google Apps ao Active Directory do Azure (AD do Azure). Você aprenderá como configurar o logon único para o Google Apps, como habilitar o provisionamento automatizado de usuários e como atribuir usuários para que tenham acesso ao Google Apps.

##Pré-requisitos

1. Para acessar o Active Directory do Azure por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), primeiro é preciso ter uma assinatura válida do Azure.

2. É preciso ter um locatário válido para o [Google Apps for Work](https://www.google.com/work/apps/) ou o [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Você pode usar uma conta de avaliação gratuita para qualquer serviço.

##Etapa 1: Adicionar Google Apps ao seu diretório

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), no painel navegação à esquerda, clique em **Active Directory**.

	![Selecione Active Directory no painel de navegação esquerdo.][0]

2. Na lista **Diretório**, selecione o diretório ao qual você deseja adicionar o Google Apps.

3. Clique em **Aplicativos** no menu superior.

	![Clique em Aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

	![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo a partir da galeria**.

	![Clique em Adicionar um aplicativo da galeria.][3]

6. Na **caixa de pesquisa**, digite **Google Apps**. Em seguida, selecione **Google Apps** nos resultados e clique em **Concluir** para adicionar o aplicativo.

	![Adicione o Google Apps.][4]

7. Agora, você verá a página Início Rápido para o Google Apps:

	![Página de Início Rápido do Google Apps no AD do Azure][5]

##Parte 2: Habilitar logon único

1. No AD do Azure, na página Início Rápido para o Google Apps, clique no botão **Configurar logon único**.

	![Botão de configuração de logon único][6]

2. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria que os usuários fizessem logon no Google Apps?" Selecione **Logon Único do AD do Azure** e, em seguida, clique em **Avançar**.

	![Selecione Logon Único do AD do Azure][7]

	> [AZURE.NOTE]Para saber mais sobre as diferentes opções de logon único, [clique aqui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Definir Configurações de Aplicativo**, para o campo **URL de Logon**, digite a URL do locatário do Google Apps usando o seguinte formato: `https://mail.google.com/a/<yourdomain>`

	![Digite a URL do seu locatário][8]

4. Na página **Configurar logon único no Google Apps**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado em seu computador.

	![Baixe o certificado.][9]

5. Abra uma nova guia no navegador e entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador.

6. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

	![Clique em Segurança.][10]

7. Na página **Segurança**, clique em **Configurar logon único (SSO).**

	![Clique em SSO.][11]

8. Realize as seguintes alterações de configuração:

	![Configure o SSO][12]

	- Selecione **Configurar SSO com o provedor de identidade de terceiros**.

	- No AD do Azure, copie a **URL de serviço do logon único** e cole-a no campo **URL da página de entrada** no Google Apps.

	- No AD do Azure, copie a **URL de serviço da saída única** e cole-a no campo **URL da página de saída** no Google Apps.

	- No AD do Azure, copie a **URL de alteração de senha** e cole-a no campo **URL de alteração de senha** no Google Apps.

	- No Google Apps, para o **Certificado de verificação**, carregue o certificado que você baixou na etapa 4.

	- Clique em **Salvar Alterações**.

9. No AD do Azure, marque a caixa de seleção de confirmação de configuração de logon único para habilitar o certificado que você carregou para o Google Apps. Em seguida, clique em **Próximo**.

	![Marque a caixa de seleção de confirmação][13]

10. Na página final da caixa de diálogo, digite um endereço de email se você quiser receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único.

	![Digite seu endereço de email.][14]

11. Clique em **Concluir** para fechar a caixa de diálogo. Para testar sua configuração, consulte a seção abaixo intitulada [Atribuir Usuários ao Google Apps](#step-4-assign-users-to-google-apps).

##Etapa 3: Habilitar o provisionamento automatizado de usuários

> [AZURE.NOTE]Outra opção viável para automatizar o provisionamento de usuários para o Google Apps é usar a [Ferramenta de Sincronização do Google Apps (GADS)](https://support.google.com/a/answer/106368?hl=en), que provisiona suas identidades do Active Directory local para o Google Apps. Por outro lado, a solução neste tutorial provisiona seus usuários do Active Directory do Azure (nuvem) e grupos habilitados para email para o Google Apps.

1. Entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador e clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

	![Clique em Segurança.][10]

2. Na página **Segurança**, clique em **Referência de API**.

	![Clique em Referência de API.][15]

3. Selecione **Habilitar o acesso à API**.

	![Clique em Referência de API.][16]

	> [AZURE.IMPORTANT]Para cada usuário que você pretende provisionar para o Google Apps, seu nome de usuário no Active Directory do Azure *deve* estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários que parecem bob@contoso.onmicrosoft.com não serão aceitos pelo Google Apps, enquanto bob@contoso.com serão aceitos. É possível alterar o domínio de um usuário existente editando as propriedades dele no AD do Azure. Instruções sobre como configurar um domínio personalizado para o Active Directory do Azure e para o Google Apps estão incluídas abaixo.

4. Se você ainda não adicionou um nome de domínio personalizado ao Active Directory do Azure, siga as etapas abaixo:

	- No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), no painel navegação à esquerda, clique em **Active Directory**. Na lista de diretórios, selecione o diretório. 

	- Clique em **Domínios** no menu de nível superior e, em seguida, clique em **Adicionar um domínio personalizado**.

		![Adicionar um domínio personalizado][17]

	- Digite seu nome de domínio no campo **Nome de domínio**. Ele deve ser o mesmo nome de domínio que você pretende usar para o Google Apps. Quando estiver pronto, clique no botão **Adicionar**.

		![Digite o nome de domínio.][18]

	- Clique em **Avançar** para ir até a página de verificação. Para verificar se você possui esse domínio, é preciso editar os registros DNS do domínio de acordo com os valores fornecidos nesta página. É possível optar por verificar usando **Registros MX** ou **Registros TXT**, dependendo do que você selecionar na opção **Tipo de Registro**. Para obter instruções mais abrangentes sobre como verificar o nome de domínio com o AD do Azure, consulte [Adicionar seu próprio nome de domínio ao AD do Azure](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Verifique o nome de domínio][19]

	- Repita as etapas acima para todos os domínios que você pretende adicionar ao diretório.

5. Agora que você verificou todos os seus domínios com o AD do Azure, deve agora verificá-los novamente com o Google Apps. Para cada domínio que já não estiver registrado com o Google Apps, realize as etapas a seguir:

	- No [Console de Admin do Google Apps](http://admin.google.com/), clique em **Domínios**.

		![Clique em Domínios][20]

	- Clique em **Adicionar um domínio ou um alias de domínio**.

		![Adicione um novo domínio][21]

	- Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.

		![Digite o nome de domínio][22]

	- Clique em **Continuar e verificar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google Apps, consulte [Verificar sua propriedade do site com o Google Apps](https://support.google.com/webmasters/answer/35179).

	- Repita as etapas acima para domínios adicionais que você pretende adicionar ao Google Apps.

	> [AZURE.WARNING]Se você alterar o domínio primário para seu locatário do Google Apps e já tiver configurado logon único com o AD do Azure, precisará repetir a etapa 3 em [Etapa 2: Habilitar Logon Único](#step-two-enable-single-sign-on).

6. No [Console de Admin do Google Apps](http://admin.google.com/), clique em **Funções Admin**.

	![Clique em Google Apps][26]

7. Determine a conta de administrador que você gostaria de usar para gerenciar o provisionamento de usuários. Para a **função admin** dessa conta, edite os **Privilégios** para essa função. Verifique se todos os **Privilégios de API de Admin** dela estão habilitados para que essa conta possa ser usada para provisionamento.

	![Clique em Google Apps][27]

	> [AZURE.NOTE]Se você estiver configurando um ambiente de produção, a prática recomendada é criar uma nova conta de administrador no Google Apps especificamente para esta etapa. Essa conta deve ter uma função admin associada a ela que tenha os privilégios de API necessários.

8. No Active Directory do Azure, clique em **Aplicativos** no menu de nível superior e, em seguida, clique em **Google Apps**.

	![Clique em Google Apps][23]

9. Na página Início Rápido para o Google Apps, clique em **Configurar provisionamento do usuário**.

	![Configure o provisionamento do usuário][24]

10. Na caixa de diálogo que é aberta, clique em **Habilitar o provisionamento do usuário** para autenticar na Conta de Administrador do Google Apps que você deseja usar para gerenciar o provisionamento.

	![Habilite o provisionamento][25]

11. Confirme que você deseja dar permissão ao Active Directory do Azure para fazer alterações em seu locatário do Google Apps.

	![Confirme permissões.][28]

12. Clique em **Concluir** para fechar a caixa de diálogo.

##Etapa 4: Atribuir usuários ao Google Apps

1. Para testar sua configuração, comece a criar uma nova conta de teste no diretório.

2. Na página Início Rápido do Google Apps, clique no botão **Atribuir Usuários**.

	![Clique em Atribuir Usuários][29]

3. Selecione seu usuário de teste e clique no botão **Atribuir** na parte inferior da tela:

 - Se você ainda não tiver habilitado o provisionamento automatizado de usuários, verá o seguinte aviso para confirmar:

		![Confirm the assignment.][30]

 - Se você tiver habilitado o provisionamento automatizado de usuários, verá um aviso para definir o tipo de função que o usuário deve ter no Google Apps. Usuários recentemente provisionados devem aparecer no ambiente do Google Apps após alguns minutos.

4. Para testar suas configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), depois entre na conta de teste e clique em **Google Apps**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=Oct15_HO3-->
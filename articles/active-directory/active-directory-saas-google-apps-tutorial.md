<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Google Apps | Microsoft Azure"
    description="Saiba como usar o Google Apps com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
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

#Tutorial: Como integrar o Google Apps ao Active Directory do Azure

Este tutorial mostrará como conectar seu ambiente do Google Apps ao Active Directory do Azure (AD do Azure). Você aprenderá como configurar o logon único para o Google Apps, como habilitar o provisionamento automatizado de usuários e como atribuir usuários para que tenham acesso ao Google Apps.

##Pré-requisitos

1. Para acessar o Azure Active Directory usando o [portal clássico do Azure](https://manage.windowsazure.com), você precisará ter uma assinatura válida do Azure.

2. É preciso ter um locatário válido para o [Google Apps for Work](https://www.google.com/work/apps/) ou o [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Você pode usar uma conta de avaliação gratuita para qualquer serviço.

##Tutorial em vídeo

Como habilitar o logon único no Google Apps em dois minutos:

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##Perguntas frequentes

1. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**

	R: Sim, os usuários poderão entrar em seus dispositivos Chromebook usando suas credenciais do AD do Azure. Confira este [artigo de suporte do Google Apps](https://support.google.com/chrome/a/answer/6060880) para saber mais sobre o motivo de os usuários receberem uma solicitação por credenciais duas vezes.

2. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do AD do Azure para entrar em qualquer produto do Google, como o Google Classroom, o GMail, o Google Drive, o YouTube etc?**

	R: Sim, dependendo de [quais aplicativos do Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você optar por habilitar ou desabilitar para sua organização.

3. **P: Posso habilitar o logon único apenas para um subconjunto de meus usuários do Google Apps?**

	R: Não, ativar o logon único exigirá imediatamente que todos os usuários do Google Apps se autentiquem com as credenciais do AD do Azure. Como o Google Apps não oferece suporte a vários provedores de identidade, o provedor de identidade para seu ambiente do Google Apps pode ser o AD do Azure ou o Google, mas não ambos ao mesmo tempo.

4. **P: Se um usuário se conectar por meio do Windows, ele será autenticado automaticamente no Google Apps sem que uma senha seja solicitada?**

	R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](active-directory-aadconnect-user-signin.md). Obviamente, as duas opções exigem que você siga o tutorial a seguir para habilitar o logon único entre o AD do Azure e o Google Apps.

##Etapa 1: adicionar Google Apps ao seu diretório

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

	![Selecione Active Directory no painel de navegação esquerdo.][0]

2. Na lista **Diretório**, selecione o diretório ao qual você deseja adicionar o Google Apps.

3. Clique em **Aplicativos** no menu superior.

	![Clique em Aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

	![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Clique em Adicionar um aplicativo da galeria.][3]

6. Na **caixa de pesquisa**, digite **Google Apps**. Em seguida, selecione **Google Apps** nos resultados e clique em **Concluir** para adicionar o aplicativo.

	![Adicione o Google Apps.][4]

7. Agora, você verá a página Início Rápido para o Google Apps:

	![Página de Início Rápido do Google Apps no AD do Azure][5]

##Parte 2: Habilitar logon único

1. No AD do Azure, na página Início Rápido para o Google Apps, clique no botão **Configurar logon único**.

	![Botão de configuração de logon único][6]

2. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria que os usuários fizessem logon no Google Apps?" Selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Selecione Logon Único do AD do Azure][7]

	> [AZURE.NOTE] Para saber mais sobre as diferentes opções de logon único, [clique aqui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Definir Configurações de Aplicativo**, para o campo **URL de Logon**, digite a URL do locatário do Google Apps usando o seguinte formato: `https://mail.google.com/a/<yourdomain>`

	![Digite a URL do seu locatário][8]

4. Na página **Configurar logon único automaticamente**, digite o domínio de seu locatário do Google Apps. Em seguida, pressione o botão **Configurar**.

	![Digite seu nome de domínio e pressione Configurar.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

	> [AZURE.NOTE] Se você preferir configurar manualmente o logon único, consulte [Etapa opcional: configurar logon único manualmente](#optional-step-manually-configure-single-sign-on)

5. Entre em sua conta de administrador do Google Apps. Em seguida, clique em **Permitir** para permitir que o Azure Active Directory faça alterações de configuração em sua assinatura do Google Apps.

	![Digite seu nome de domínio e pressione Configurar.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. Aguarde alguns segundos enquanto o Active Directory do Azure configura seu locatário do Google Apps. Quando for concluído, clique em **Avançar**.

10. Na página final da caixa de diálogo, digite um endereço de email se você quiser receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único.

	![Digite seu endereço de email.][14]

11. Clique em **Concluir** para fechar a caixa de diálogo. Para testar sua configuração, consulte a seção abaixo intitulada [Atribuir Usuários ao Google Apps](#step-4-assign-users-to-google-apps).

##Etapa opcional: configurar logon único manualmente

Se você preferir configurar manualmente o logon único, conclua as seguintes etapas:

1. No AD do Azure, na página Início Rápido para o Google Apps, clique no botão **Configurar logon único**.

	![Botão de configuração de logon único][6]

2. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria que os usuários fizessem logon no Google Apps?" Selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Selecione Logon Único do AD do Azure][7]

	> [AZURE.NOTE] Para saber mais sobre as diferentes opções de logon único, [clique aqui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Definir Configurações de Aplicativo**, para o campo **URL de Logon**, digite a URL do locatário do Google Apps usando o seguinte formato: `https://mail.google.com/a/<yourdomain>`

	![Digite a URL do seu locatário][8]

4. Na página **Configurar logon único automaticamente**, marque a caixa de seleção **Configurar logon único manualmente para esse aplicativo**. Em seguida, clique em **Próximo**.

	![Clique em configuração manual.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

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

> [AZURE.NOTE] Outra opção viável para automatizar o provisionamento de usuários para o Google Apps é usar a [Ferramenta de Sincronização do Google Apps (GADS)](https://support.google.com/a/answer/106368?hl=en), que provisiona suas identidades do Active Directory local para o Google Apps. Por outro lado, a solução neste tutorial provisiona seus usuários do Active Directory do Azure (nuvem) e grupos habilitados para email para o Google Apps.

1. Entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador e clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

	![Clique em Segurança.][10]

2. Na página **Segurança**, clique em **Referência de API**.

	![Clique em Referência de API.][15]

3. Selecione **Habilitar o acesso à API**.

	![Clique em Referência de API.][16]

	> [AZURE.IMPORTANT] Para cada usuário que você pretende provisionar para o Google Apps, seu nome de usuário no Active Directory do Azure *deve* estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários que parecem bob@contoso.onmicrosoft.com não serão aceitos pelo Google Apps, enquanto bob@contoso.com serão aceitos. É possível alterar o domínio de um usuário existente editando as propriedades dele no AD do Azure. Instruções sobre como configurar um domínio personalizado para o Active Directory do Azure e para o Google Apps estão incluídas abaixo.

4. Se você ainda não adicionou um nome de domínio personalizado ao Active Directory do Azure, siga as etapas abaixo:

	- No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**. Na lista de diretórios, selecione o diretório.

	- Clique em **Domínios** no menu de nível superior e, em seguida, clique em **Adicionar um domínio personalizado**.

		![Adicionar um domínio personalizado][17]

	- Digite seu nome de domínio no campo **Nome de domínio**. Ele deve ser o mesmo nome de domínio que você pretende usar para o Google Apps. Quando estiver pronto, clique no botão **Adicionar**.

		![Digite o nome de domínio.][18]

	- Clique em **Avançar** para ir até a página de verificação. Para verificar se você possui esse domínio, é preciso editar os registros DNS do domínio de acordo com os valores fornecidos nesta página. É possível optar por verificar usando **Registros MX** ou **Registros TXT**, dependendo do que você selecionar na opção **Tipo de Registro**. Para obter instruções mais abrangentes sobre como verificar o nome de domínio com o AD do Azure, consulte [Adicionar seu próprio nome de domínio ao AD do Azure](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Verifique o seu nome de domínio.][19]

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

	> [AZURE.WARNING] Se você alterar o domínio primário para seu locatário do Google Apps e já tiver configurado logon único com o AD do Azure, precisará repetir a etapa 3 em [Etapa 2: habilitar Logon Único](#step-two-enable-single-sign-on).

6. No [Console de Admin do Google Apps](http://admin.google.com/), clique em **Funções Admin**.

	![Clique em Google Apps][26]

7. Determine a conta de administrador que você gostaria de usar para gerenciar o provisionamento de usuários. Para a **função de administrador** dessa conta, edite os **Privilégios** para essa função. Verifique se todos os **Privilégios de API de Admin** dela estão habilitados para que essa conta possa ser usada para provisionamento.

	![Clique em Google Apps][27]

	> [AZURE.NOTE] Se você estiver configurando um ambiente de produção, a prática recomendada é criar uma nova conta de administrador no Google Apps especificamente para esta etapa. Essa conta deve ter uma função admin associada a ela que tenha os privilégios de API necessários.

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

4. Para testar suas configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), entre na conta de teste e clique em **Google Apps**.

## Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

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

<!---HONumber=AcomDC_0928_2016-->
<properties
   pageTitle="Tutorial: Integração do NetSuite ao Active Directory do Azure | Microsoft Azure"
   description="Saiba como usar o NetSuite com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
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

#Tutorial: Como integrar o NetSuite ao Active Directory do Azure

Este tutorial mostrará como conectar o ambiente NetSuite ao Active Directory do Azure (AD do Azure). Você aprenderá como configurar o logon único para NetSuite, como habilitar o provisionamento automatizado de usuários e como atribuir usuários para que tenham acesso ao NetSuite.

##Pré-requisitos

1. Para acessar o Active Directory do Azure por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), primeiro é preciso ter uma assinatura válida do Azure.

2. É preciso ter acesso de administrador para uma assinatura do [NetSuite](http://www.netsuite.com/portal/home.shtml). Você pode usar uma conta de avaliação gratuita para qualquer serviço.

##Etapa 1: Adicionar o NetSuite ao seu diretório

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), no painel navegação à esquerda, clique em **Active Directory**.

	![Selecione Active Directory no painel de navegação esquerdo.][0]

2. Na lista **Diretório**, selecione o diretório ao qual você deseja adicionar o NetSuite.

3. Clique em **Aplicativos** no menu superior.

	![Clique em Aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

	![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo a partir da galeria**.

	![Clique em Adicionar um aplicativo da galeria.][3]

6. Na **caixa de pesquisa**, digite **NetSuite**. Em seguida, selecione **NetSuite** nos resultados e clique em **Concluir** para adicionar o aplicativo.

	![Adicione o NetSuite.][4]

7. Agora, você verá a página Início Rápido para o NetSuite:

	![Página de Início Rápido do NetSuite no AD do Azure][5]

##Parte 2: Habilitar logon único

1. No AD do Azure, na página Início Rápido para o NetSuite, clique no botão **Configurar logon único**.

	![Botão de configuração de logon único][6]

2. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria que os usuários fizessem logon no NetSuite?" Selecione **Logon Único do AD do Azure** e, em seguida, clique em **Avançar**.

	![Selecione Logon Único do AD do Azure][7]

	> [AZURE.NOTE]Para saber mais sobre as diferentes opções de logon único, [clique aqui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Definir Configurações de Aplicativo**, para o campo **URL de Logon**, digite a URL do locatário do NetSuite usando um dos seguintes formatos:
	- `https://<tenant-name>.netsuite.com`
	- `https://<tenant-name>.na1.netsuite.com`
	- `https://<tenant-name>.na2.netsuite.com`
	- `https://<tenant-name>.sandbox.netsuite.com`
	- `https://<tenant-name>.na1.sandbox.netsuite.com`
	- `https://<tenant-name>.na2.sandbox.netsuite.com`

	![Digite a URL do seu locatário][8]

4. Na página **Configurar logon único no NetSuite**, clique em **Baixar metadados** e, em seguida, salve o arquivo de certificado em seu computador.

	![Baixe os metadados.][9]

5. Abra uma nova guia no navegador e entre no site Netsuite de sua empresa como administrador.

6. Na barra de ferramentas na parte superior da página, clique em **Instalação** e depois em **Gerenciador de Instalação**.

	![Vá para Gerenciador de Instalação][10]

7. Na lista **Tarefas de Instalação**, selecione**Integração**.

	![Vá para Integração][11]

8. Na seção **Gerenciar Autenticação**, clique em **Logon Único do SAML**.

	![Vá para Logon Único do SAML][12]

9. Na página **Instalação do SAML**, realize as seguintes etapas:

	- No Active Directory do Azure, copie o valor da **URL de Logon Remoto** e cole-o no campo **Página de Logon do Provedor de Identidade** no NetSuite.

		![Página de Instalação do SAML.][13]

	- No NetSuite, selecione **Método de Autenticação Primária**.

	- Para o campo **Metadados do Provedor de Identidade SAMLV2**, selecione **Carregar Arquivo de Metadados IDP**. Em seguida, clique em **Procurar** para carregar o arquivo de metadados que você baixou na etapa 4.

		![Carregue os metadados][16]

	- Clique em **Enviar**.

9. No AD do Azure, marque a caixa de seleção de confirmação de configuração de logon único para habilitar o certificado que você carregou para o NetSuite. Em seguida, clique em **Próximo**.

	![Marque a caixa de seleção de confirmação][14]

10. Na página final da caixa de diálogo, digite um endereço de email se você quiser receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único.

	![Digite seu endereço de email.][15]

11. Clique em **Concluir** para fechar a caixa de diálogo. Em seguida, clique em **Atributos** na parte superior da página.

	![Clique em Atributos.][17]

12. Clique em **Adicionar Atributo de Usuário**.

	![Clique em Adicionar Atributo de Usuário.][18]

13. Para o campo **Nome do Atributo**, digite `account`. Para o campo **Valor do Atributo**, digite a ID de conta do NetSuite. Instruções sobre como localizar a ID de conta estão abaixo:

	![Adicione a ID de conta do NetSuite.][19]

	- No NetSuite, clique em **Instalação** no menu de navegação superior.
	- Em seguida, clique na seção **Tarefas de Instalação** no menu de navegação à esquerda, selecione a seção **Integração** e, em seguida, clique em **Preferências de Serviços da Web**.
	- Copie a ID de conta do NetSuite e cole-a no campo **Valor do Atributo** no AD do Azure.

		![Obtenha a ID de conta][20]

14. No AD do Azure, clique em **Concluir** para concluir a adição do atributo do SAML. Em seguida, clique em **Aplicar Alterações** no menu inferior.

	![Salve suas alterações.][21]

15. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

	- No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.

	![Vá para Gerenciador de Instalação][10]

	- No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Funções**.

	![Vá para Gerenciar Funções][22]

	- Clique em **Nova Função**.

	- Digite um **Nome** para a nova função e marque a caixa de seleção **Apenas Logon Único**.

	![Dê um nome à nova função.][23]

	- Clique em **Salvar**.

	- No menu na parte superior, clique em **Permissões**. Em seguida, clique em **Instalação**.

	![Vá para Permissões][24]

	- Selecione **Instalar o Logon Único do SAM** e, em seguida, clique em **Adicionar**.

	- Clique em **Salvar**.

	- No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.

	![Vá para Gerenciador de Instalação][10]

	- No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Usuários**.

	![Vá para Gerenciar Usuários][25]

	- Selecione um usuário de teste. Em seguida, clique em **Editar**.

	![Vá para Gerenciar Usuários][26]

	- Na caixa de diálogo Funções, selecione a função que você criou e clique em **Adicionar**.

	![Vá para Gerenciar Usuários][27]

	- Clique em **Salvar**.

19. Para testar sua configuração, consulte a seção intitulada [Atribuir Usuários ao NetSuite](#step-4-assign-users-to-netsuite).

##Etapa 3: Habilitar o provisionamento automatizado de usuários

1. No Active Directory do Azure, na página Início Rápido para o NetSuite, clique em **Configurar provisionamento do usuário**.

	![Configure o provisionamento do usuário][28]

2. Na caixa de diálogo que é aberta, digite as credenciais de administrador para o NetSuite e clique em **Avançar**.

	![Digite as credenciais de administrador do NetSuite.][29]

3. Na página de confirmação, digite seu endereço de email para receber notificações de falhas de provisionamento.

	![Confirme.][30]

4. Clique em **Concluir** para fechar a caixa de diálogo.

##Etapa 4: Atribuir usuários ao NetSuite

1. Para testar sua configuração, comece a criar uma nova conta de teste no diretório.

2. Na página Início Rápido do NetSuite, clique no botão **Atribuir Usuários**.

	![Clique em Atribuir Usuários][31]

3. Selecione seu usuário de teste e clique no botão **Atribuir** na parte inferior da tela:

 - Se você ainda não tiver habilitado o provisionamento automatizado de usuários, verá o seguinte aviso para confirmar:

		![Confirm the assignment.][32]

 - Se você tiver habilitado o provisionamento automatizado de usuários, verá um aviso para definir o tipo de função que o usuário deve ter no NetSuite. Usuários recentemente provisionados devem aparecer no ambiente do NetSuite após alguns minutos.

4. Para testar suas configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), entre na conta de teste e clique em **NetSuite**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=August15_HO6-->
<properties 
	pageTitle="Autenticar com APIs REST do Mobile Engagement - configuração manual"
	description="Descreve como configurar manualmente a autenticação para APIs REST do Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/11/2016"
	ms.author="piyushjo"/>

# Autenticar com APIs REST do Mobile Engagement - configuração manual

Esta é uma documentação de apêndice de [Autenticar com APIs REST do Mobile Engagement](mobile-engagement-api-authentication.md). Leia esse artigo primeiro para obter o contexto. Ele descreve uma maneira alternativa de fazer a configuração única da autenticação para APIs REST do Mobile Engagement usando o portal do Azure.

>[AZURE.NOTE] As instruções a seguir se baseiam nesse [Guia do Active Directory](../resource-group-create-service-principal-portal/) e são personalizadas com o necessário para autenticação de APIs do Mobile Engagement. Portanto, consulte-o para entender as etapas abaixo em detalhes.

1. Faça logon na sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).

2. Selecione **Active Directory** no painel à esquerda.

     ![selecionar Active Directory][1]

3. Escolha o **Active Directory Padrão** em seu portal do Azure.

     ![escolher o diretório][2]

	>[AZURE.IMPORTANT] Essa abordagem funciona somente quando você está trabalhando no Active Directory padrão de sua conta, e não funcionará se você estiver fazendo isso em um Active Directory que você criou em sua conta.

4. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.

     ![exibir aplicativos][3]

5. Clique em **ADICIONAR**.

     ![adicionar aplicativo][4]

6. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.

     ![novo aplicativo][5]

6. Preencha o nome do aplicativo e selecione o tipo de aplicativo como **APLICATIVO WEB E/OU API WEB** e clique no botão Avançar.

     ![nomear aplicativo][6]

7. Você pode fornecer qualquer URL fictícia para **URL de LOGON** e **URI da ID do APLICATIVO**. Elas não são usadas em nosso cenário e as próprias URLs não são validadas.

     ![propriedades do aplicativo][7]

8. Ao final deste artigo, você terá um aplicativo AAD com o nome fornecido anteriormente, como o seguinte. Este é o seu **NOME\_APLICATIVO\_AD**, anote-o.

     ![nome do aplicativo][8]

9. Clique no nome do aplicativo e clique em **Configurar**.

     ![configurar aplicativo][9]

10. Anote a ID do cliente que será usada como **ID\_CLIENTE** para as chamadas de sua API.

     ![configurar aplicativo][10]

11. Role a tela para baixo até a seção **Chaves** e adicione uma chave com duração de dois anos (validade) de preferência e clique em **Salvar**.

     ![configurar aplicativo][11]


12. Copie imediatamente o valor mostrado para a chave, pois ele será exibido apenas agora e não será armazenado, portanto, não será exibido novamente. Se você perdê-lo, terá que gerar uma nova chave. Esse será o **SEGREDO\_DO\_CLIENTE** para suas chamadas à API.

     ![configurar aplicativo][12]

	>[AZURE.IMPORTANT] Essa chave expirará ao final da duração especificada, portanto lembre-se de renová-la quando chegar a hora, caso contrário a autenticação da API não funcionará mais. Você também pode excluir e recriar essa chave se achar que ela foi comprometida.
 
13. Clique no botão **EXIBIR PONTOS DE EXTREMIDADE**, o que abrirá a caixa de diálogo **Pontos de Extremidade do Aplicativo**.

	![][13]

14. Na caixa de diálogo Pontos de Extremidade do Aplicativo, copie o **PONTO DE EXTREMIDADE DO TOKEN OAUTH 2.0**.

	![][14]

15. Esse ponto de extremidade terá o seguinte formato, em que o GUID na URL é seu **ID\_DE\_LOCATÁRIO**, então anote-o:

		https://login.microsoftonline.com/<GUID>/oauth2/token

16. Agora, configuraremos as permissões nesse aplicativo. Para isso, você precisará abrir o [portal do Azure](https://portal.azure.com).

17. Clique em **Grupos de Recursos** e localize o grupo de recursos **Mobile Engagement**.

	![][15]

18. Clique no grupo de recursos **Mobile Engagement** e navegue até a folha **Configurações**.

	![][16]

19. Clique em **Usuários** na folha Configurações e clique em **Adicionar** para adicionar um usuário.

	![][17]

20. Clique em **Selecionar uma função**

	![][18]

21. Clique em **Proprietário**

	![][19]

22. Procure o nome de seu aplicativo **NOME\_APLICATIVO\_AD** na caixa Pesquisar. Você não verá ele por padrão aqui. Depois de encontrá-lo, selecione-o e clique em **Selecionar** na parte inferior da folha.

	![][20]

23. Na folha **Adicionar Acesso**, ele aparecerá como **1 usuário, 0 grupos**. Clique em **OK** nessa folha para confirmar a alteração.

	![][21]

Agora você concluiu a configuração necessária do AAD e está pronto para chamar as APIs.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png

<!---HONumber=AcomDC_0316_2016-->
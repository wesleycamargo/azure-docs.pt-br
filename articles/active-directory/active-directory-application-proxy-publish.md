<properties
	pageTitle="Publicar aplicativos com o Proxy de Aplicativo do Azure AD | Microsoft Azure"
	description="Publique aplicativos locais na nuvem com o Proxy de Aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="kgremban"/>


# Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure


Após habilitar o Proxy de Aplicativo do Microsoft Azure AD (Active Directory), você pode publicar aplicativos locais para que os usuários remotos possam acessá-los fora da rede privada.

Este artigo explica as etapas para publicar aplicativos que estão em execução em sua rede local e fornecer acesso remoto seguro de fora de sua rede. Se você não tiver configurado o Proxy de Aplicativo ou nem instalado conectores, siga as etapas em [Habilitar o Proxy de Aplicativo no portal do Azure](active-directory-application-proxy-enable.md) antes de continuar aqui.

Se esta for a primeira vez que você usa o Proxy de aplicativo do Azure AD, recomendamos que teste o Conector publicando um site da rede privada antes de publicar aplicativos.

> [AZURE.NOTE] O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

## Publicar um aplicativo usando o assistente

1. Entre como administrador no [portal clássico do Azure](https://manage.windowsazure.com/).
2. Vá para o Active Directory e selecione o diretório no qual você habilitou o Proxy de Aplicativo.

	![Active Directory - ícone](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Clique na guia **Aplicativos** e no botão **Adicionar** na parte inferior da tela

	![Adicionar aplicativo](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Selecione **Publicar um aplicativo que estará acessível fora de sua rede**.

	![Publicar um aplicativo que poderá ser acessado de fora de sua rede](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Forneça as seguintes informações sobre o aplicativo:

	- **Nome**: o nome amigável para o aplicativo. Ele deve ser exclusivo dentro no diretório.
	- **URL interna**: o endereço que o Conector de Proxy de Aplicativo usa para acessar o aplicativo dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor e dar a cada um deles seu próprio nome e suas regras de acesso.
	- **Método de pré-autenticação**: a maneira como o Proxy de Aplicativo verificará os usuários antes de lhes dar acesso ao aplicativo. Escolha uma das opções no menu suspenso.

		- Azure Active Directory: o Proxy de Aplicativo redirecionará os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo.
		- Passagem: os usuários não precisam ser autenticados para acessar o aplicativo.

	![Propriedades do aplicativo](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

6. Para concluir o assistente, clique na marca de seleção na parte inferior da tela. O aplicativo agora está definido no AD do Azure.


## Atribuir usuários e grupos ao aplicativo

Para que os usuários acessem o aplicativo publicado, você precisa atribuí-los individualmente ou em grupos. Para aplicativos que exigem pré-autenticação, isso concede permissões para usar o aplicativo. Para aplicativos que não exigem pré-autenticação, os usuários não precisam de permissões, mas ainda precisam ser atribuídos ao aplicativo para que ele seja exibido na lista de aplicativos.

1. Após concluir o assistente Adicionar Aplicativo, você verá a página de Início Rápido para o aplicativo. Para gerenciar quem tem acesso ao aplicativo, selecione **Usuários e grupos**.

	![Início rápido do Proxy de Aplicativo para atribuir usuários - captura de tela](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Pesquise grupos específicos no diretório ou mostre todos os usuários. Clique na marca de seleção para exibir os resultados.

  	![Pesquisar grupos ou usuários - captura de tela](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selecione cada usuário ou grupo que você deseja atribuir a esse aplicativo e clique em **Atribuir**. Você será solicitado a confirmar essa ação.

> [AZURE.NOTE] Para aplicativos de autenticação integrada do Windows, você pode atribuir apenas usuários e grupos que são sincronizados do Active Directory local. Usuários que entram com uma conta da Microsoft e convidados não podem ser atribuídos para aplicativos publicados com o Proxy de Aplicativo do Azure Active Directory. Verifique se os usuários entram com credenciais que fazem parte do mesmo domínio que o aplicativo que você está publicando.


## Configuração avançada

Você pode modificar aplicativos publicados ou configurar opções avançadas na página Configurar. Nessa página, você pode personalizar o aplicativo alterando o nome ou carregando um logotipo. Também pode gerenciar regras de acesso, como o método de pré-autenticação ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Depois de publicar aplicativos usando o Proxy de Aplicativo do Active Directory do Azure, eles aparecerão na lista de aplicativos do AD do Azure e será possível gerenciá-los de lá.

Se você desabilitar os serviços do Proxy de Aplicativo depois de publicar aplicativos, os aplicativos não serão excluídos, mas deixarão de ficar acessíveis de fora da rede privada.

Para exibir um aplicativo e verificar se ele está acessível, clique duas vezes no nome do aplicativo. Se o serviço do Proxy de Aplicativo estiver desabilitado e o aplicativo não estiver disponível, uma mensagem de aviso será exibida na parte superior da tela.

Para excluir um aplicativo, selecione-o na lista e clique em **Excluir**.

## Próximas etapas

- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0608_2016-->
<properties
	pageTitle="Criar um aplicativo Web do Java no Serviço de Aplicativo do Azure"
	description="Este tutorial mostra como implantar um aplicativo Web do Java no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="06/03/2015"
	ms.author="robmcm"/>

# Criar um aplicativo Web do Java no Serviço de Aplicativo do Azure

Este tutorial mostra como criar um aplicativo Web no Microsoft Azure em Java, usando o Azure Marketplace ou então a interface de usuário de configuração dos [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

Se você não quiser usar nenhuma dessas técnicas, por exemplo, se você desejar personalizar seu contêiner de aplicativos, consulte [Carregar um aplicativo Web do Java personalizado no Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tem uma conta, você pode <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se em uma avaliação gratuita</a>.

Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um aplicativo Web do Java usando o Azure Marketplace

Estas informações mostram como usar o Azure Marketplace para selecionar um contêiner de aplicativos Java, Apache Tomcat ou Jetty para seu aplicativo Web.

Veja a seguir como seria a aparência de um aplicativo Web criado usando o Tomcat por meio do Azure Marketplace:

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

Veja a seguir como seria a aparência de um aplicativo Web criado usando o Jetty no Azure Marketplace:

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. Faça logon no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
2. Clique no ícone **Novo**, localizado na parte inferior esquerda da página.
3. Clique na folha **Web + Móvel**.
4. Clique em **Azure Marketplace** na parte inferior da folha **Web + Móvel**.
5. Clique em **Web**.
6. A parte superior da página **Web** contém uma caixa de texto de pesquisa. Nessa caixa de texto, digite o servidor de aplicativos Java desejado, como **Apache Tomcat** ou **Jetty**.
4. Clique no servidor de aplicativos Java desejado.
5. Clique em **Criar**.
6. Especifique o nome da URL.
6. Selecione uma região. Por exemplo, **West US**.
7. Clique em **Criar**.

Em poucos instantes, o aplicativo Web será criado. Para exibir o aplicativo Web, no Portal de Gerenciamento do Azure, na folha **Aplicativos Web**, clique em seu aplicativo Web criado recentemente e, em seguida, clique na URL para o aplicativo Web.

Agora que você criou o aplicativo Web com um contêiner de aplicativos, consulte a seção **Próximas etapas** para obter informações sobre como carregar seu aplicativo no aplicativo Web.

## Criar um aplicativo Web em Java usando a interface do usuário de configuração do Azure

Estas informações mostram como usar a interface do usuário de configuração do Azure para selecionar um contêiner de aplicativos Java, Apache Tomcat ou Jetty, no seu aplicativo Web.

1. Faça logon no Portal de Gerenciamento do Microsoft Azure.
2. Clique no ícone **Novo**, localizado na parte inferior esquerda da página.
3. Clique na folha **Web + Móvel**.
4. Clique em **Azure Marketplace** na parte inferior da folha **Web + Móvel**.
5. Clique em **Web**.
6. Clique em **Aplicativo Web**.
7. Clique em **Criar**.
8. Especifique o nome da URL.
9. Selecione uma região. Por exemplo, **West US**.
10. Clique em **Criar**.
11. Quando o aplicativo Web tiver sido criado, clique em **Todas as configurações**.
12. Clique em **Configurações do aplicativo**.
13. Clique na versão desejada do Java.
14. As opções para o contêiner da web são exibidas, por exemplo, Tomcat e Jetty. Selecione o **Contêiner da Web** desejado.
15. Clique em **Salvar**.

Em poucos instantes, o aplicativo Web se tornará baseado em Java. Para confirmar que ele é baseado em Java, clique na URL para o aplicativo Web. Observe que a página fornecerá texto informando que o novo aplicativo Web é um aplicativo Web baseado em Java.

Agora que você criou o aplicativo Web com um contêiner de aplicativos, consulte a seção **Próximas etapas** para obter informações sobre como carregar seu aplicativo no aplicativo Web.

## Próximas etapas

Neste ponto, você tem um servidor de aplicativos Java executando como seu aplicativo Web em Java no Azure. Para adicionar em seu próprio aplicativo ou página da Web, consulte [Adicionar um aplicativo ou página da Web ao seu aplicativo Web em Java](web-sites-java-add-app.md).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->
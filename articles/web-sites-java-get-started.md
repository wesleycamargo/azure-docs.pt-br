<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Introdução ao Azure" pageTitle="Introdução aos Sites do Microsoft Azure usando Java" metaKeywords="" description="Este tutorial mostra como implantar um site Java no Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Introdução ao Azure e ao Java" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

# Introdução aos sites do Azure e ao Java

Este tutorial mostra como criar um site no Microsoft Azure usando Java, usando a galeria de aplicativos do Azure ou a interface do usuário de configuração de site do Azure. 

Se você não quiser usar nenhuma dessas técnicas, por exemplo, se você desejar personalizar seu contêiner de aplicativos, consulte [Carregar um site Java personalizado no Azure](../web-sites-java-custom-upload)

> [WACOM.NOTE] Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>.

# Criar um site Java usando a galeria de aplicativos do Azure

Estas informações mostram como usar a galeria de aplicativos do Azure para selecionar um contêiner de aplicativos Java, Apache Tomcat ou Jetty, no seu site.

Veja a seguir como um site criado usando o Tomcat da galeria de aplicativos será exibido:

![Site usando o Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

Veja a seguir como um site criado usando o Jetty da galeria de aplicativos será exibido:

![Site usando o Jetty](./media/web-sites-java-get-started/jetty.png)

1. Faça logon no Portal de Gerenciamento do Microsoft Azure.
2. Clique em **Novo**, clique em **Computação**, clique em **Site** e, em seguida, clique em **Da galeria**.
3. Na lista de aplicativos, selecione um dos servidores de aplicativos Java, como o **Apache Tomcat** ou o **Jetty**.
4. Clique em **Avançar**.
5. Especifique o nome da URL.
6. Selecione uma região. Por exemplo, **West US**.
7. Clique em **Concluído**.

Em poucos instantes, o site será criado. Para exibir o site, no Portal de Gerenciamento do Azure, na exibição **Sites**, aguarde até que o status seja mostrado como **Executando** e, em seguida, clique na URL do site.

Agora que você criou o site com um contêiner de aplicativos, consulte a seção **Próximas etapas** para obter informações sobre como carregar seu aplicativo no site.

# Criar um site Java usando a interface do usuário de configuração do Azure

Estas informações mostram como usar a interface do usuário de configuração do Azure para selecionar um contêiner de aplicativos Java, Apache Tomcat ou Jetty, no seu site.

1. Faça logon no Portal de Gerenciamento do Microsoft Azure.
2. Clique em **Novo**, clique em **Computação**, clique em **Site** e, em seguida, clique em **Criação Rápida**.
3. Especifique o nome da URL.
4. Selecione uma região. Por exemplo, **West US**.
5. Clique em **Concluído**. Em poucos instantes, o site será criado. Para exibir o site, no Portal de Gerenciamento do Azure, na exibição **Sites**, aguarde até que o status seja mostrado como **Executando** e, em seguida, clique na URL do site.
6. Ainda no Portal de Gerenciamento do Azure, na exibição **Sites**, clique no nome do site para abrir o painel.
7. Clique em **Configurar**.
8. Na seção **Geral**, habilite o **Java** clicando na versão disponível.
9. As opções para o contêiner da web são exibidas, por exemplo, Tomcat e Jetty. Selecione o contêiner da web que você deseja usar. 
10. Clique em **Salvar**. 

Em poucos instantes, o site se tornará baseado em Java. Para confirmar que o site está baseado em Java, no Portal de Gerenciamento do Azure, na exibição **Sites**, aguarde até que o status seja mostrado como **Executando** e, em seguida, clique na URL do site. Observe que a página fornecerá texto informando que o novo site é um site baseado em Java.

Agora que você criou o site com um contêiner de aplicativos, consulte a seção **Próximas etapas** para obter informações sobre como carregar seu aplicativo no site.

# Próximas etapas

Neste ponto, você tem um servidor de aplicativos Java executando como seu site Java no Azure. Para adicionar em seu próprio aplicativo ou página da web, consulte [Adicionar um aplicativo ou página da web ao seu site Java](../web-sites-java-add-app).


---
title: "Criar um aplicativo Web Java no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Este tutorial mostra como implantar um aplicativo Web do Java no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d6e73cc3-8b71-4742-a197-3edeabc6a289
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 3451e6d13119bacc66e9ccd861862edea5a5b4fe


---
# <a name="create-a-java-web-app-in-azure-app-service"></a>Criar um aplicativo Web do Java no Serviço de Aplicativo do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar um [Aplicativo Web Java] no Serviço de Aplicativo do Azure usando o [Portal do Azure]. O Portal do Azure é uma interface da Web que você pode usar para gerenciar recursos do Azure.

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante do Visual Studio] ou [inscrever-se em uma avaliação gratuita].
> 
> Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo]. Lá, você poderá criar imediatamente um aplicativo Web de curta duração inicial no Serviço de Aplicativo, sem exigência de cartão de crédito e sem compromissos.
> 
> 

## <a name="java-application-options"></a>Opções de aplicativos Java
Há várias maneiras de configurar um aplicativo Java em um aplicativo Web do Serviço de Aplicativo. 

1. Crie um aplicativo e defina as **Configurações do aplicativo**.
   
    O Serviço de aplicativo oferece várias versões do Tomcat e do Jetty, com a configuração padrão. Se o aplicativo que será hospedado funcionará com uma das versões internas, este método de configuração de um contêiner da Web será o mais fácil e será adequado quando você quiser apenas carregar um arquivo war para um contêiner da Web. Para este método, crie um aplicativo no Portal do Azure e acesse a folha **Configurações do aplicativo** para escolher a versão do Java com o contêiner Web Java desejado. Quando você usa este método, o Java e o contêiner da Web são executados em Arquivos de Programas. Os outros métodos colocam o contêiner da Web e, potencialmente, o JVM em seu espaço em disco. Quando você usa esse modelo, não tem acesso para editar arquivos nesta parte do sistema de arquivos. Isso significa que você não pode fazer coisas como configurar o arquivo*server.xml* ou colocar os arquivos da biblioteca na pasta */lib*. Para saber mais, consulte a seção [Criar e configurar um aplicativo Web Java](#portal) mais adiante neste tutorial.
2. Use um modelo do Azure Marketplace.
   
    O Azure Marketplace inclui modelos que criam e configuram automaticamente aplicativos Web Java com contêineres da Web Tomcat ou Jetty. Os contêineres da Web criados pelos modelos são configuráveis. Para saber mais, consulte a seção [Usar um modelo Java do Azure Marketplace](#marketplace) deste tutorial.
3. Criar um aplicativo e copiar e editar manualmente os arquivos de configuração 
   
    Talvez você queira hospedar um aplicativo Java personalizado que não será implantado em qualquer um dos recipientes da Web fornecidos pelo Serviço de Aplicativo. Por exemplo:
   
   * Seu aplicativo Java requer uma versão do Tomcat ou do Jetty que não tem suporte direto do Serviço de Aplicativo ou que não é fornecido na galeria.
   * Seu aplicativo Java recebe solicitações HTTP e não é implantado como um WAR em um contêiner da Web já existente.
   * Você deseja configurar o contêiner da Web do zero por conta própria. 
   * Você deseja usar uma versão do Java que não tem suporte no Serviço de Aplicativo e deseja carregá-la por conta própria.
     
     Para casos como esse, crie um aplicativo usando o Portal do Azure e forneça manualmente os arquivos de tempo de execução apropriados. Nesse caso, os arquivos serão contados em relação às suas cotas de espaço de armazenamento para seu plano do Serviço de Aplicativo. Para saber mais, consulte [Carregar um aplicativo Web Java personalizado no Azure].

## <a name="a-nameportala-create-and-configure-a-java-web-app"></a><a name="portal"></a> Criar e configurar um aplicativo Web Java
Esta seção mostra como criar um aplicativo Web e como configurá-lo para Java usando a folha **Configurações do aplicativo** do portal.

1. Entre no [Portal do Azure].
2. Clique em **Novo > Web + Celular > Aplicativo Web**.
   
    ![Novo aplicativo Web][newwebapp]
3. Digite um nome para o aplicativo Web na caixa **aplicativo Web** .
   
    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo Web será {nome}.azurewebsites.net. Se o nome inserido não for exclusivo, um ponto de exclamação vermelho aparecerá na caixa de texto.
4. Selecione um **Grupo de Recursos** ou crie um novo.
   
    Para saber mais sobre os grupos de recursos, confira [Visão geral do Resource Manager].
5. Selecione um **Plano/Local do Serviço de Aplicativo** ou crie um novo.
   
    Para saber mais sobre os planos do Serviço de Aplicativo, confira [Visão geral dos planos do Serviço de Aplicativo do Azure].
6. Clique em **Criar**.
   
    ![Criar um aplicativo Web][newwebapp2]
7. Quando o aplicativo Web tiver sido criado, clique em **Aplicativos Web > {seu aplicativo Web}**.
   
    ![Selecionar aplicativo Web][selectwebapp]
8. Na folha **Aplicativo Web**, clique em **Configurações**.
9. Clique em **Configurações do aplicativo**.
10. Clique na **Versão Java**desejada. 
11. Clique na **Versão secundária Java**desejada. Se você selecionar **Mais recente**, seu aplicativo usará a versão secundária mais recente disponível no Serviço de Aplicativo para a versão principal do Java. O item **Mais recente** é exclusivo para aplicativos Java criados com base nas **Configurações do aplicativo**. Se criar o aplicativo Java por meio da galeria, você precisará gerenciar as alterações em seu próprio contêiner e no JVM. 
12. Escolha o **Contêiner da Web**desejado. Se você selecionar um nome de contêiner que comece com **Newest**, seu aplicativo será mantido na versão mais recente da versão principal do contêiner da Web disponível no Serviço de Aplicativo. 
    
    ![Versões de contêiner da Web][versions]
13. Clique em **Salvar**.
    
    Em poucos instantes, seu aplicativo Web estará baseado em Java e configurado para usar o contêiner da Web que você selecionou.
14. Clique em **Aplicativos Web > {seu novo aplicativo Web}**.
15. Clique na **URL** para navegar até o novo site.
    
    A página da Web confirma que você criou um aplicativo Web baseado em Java.

## <a name="a-namemarketplacea-use-a-java-template-from-the-azure-marketplace"></a><a name="marketplace"></a> Usar um modelo Java do Azure Marketplace
Esta seção mostra como usar o Azure Marketplace para criar um aplicativo Web Java. O mesmo fluxo geral também pode ser usado para criar um aplicativo de API ou um aplicativo móvel baseado em Java. 

1. Entre no [Portal do Azure]
2. Clique em **Novo > Marketplace**.
   
    ![Novo Marketplace][newmarketplace]
3. Clique em **Web + móvel**.
   
    Talvez seja necessário rolar para a esquerda para ver a folha** Marketplace**, onde você pode selecionar **Web + Celular**.
4. Na caixa de texto de pesquisa, insira o nome de um servidor de aplicativos Java, como o **Apache Tomcat** ou o **Jetty** e pressione Enter.
5. Nos resultados da pesquisa, clique no servidor de aplicativos Java.
   
    ![Jetty móvel da Web][webmobilejetty]
6. Na primeira folha do **Apache Tomcat** ou do **Jetty**, clique em **Criar**.
   
    ![Folha do Portal Jetty][jettyblade]
7. Na próxima folha do **Apache Tomcat** ou do **Jetty**, insira um nome para o aplicativo Web na caixa **Aplicativo Web**.
   
    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo Web será {nome}.azurewebsites.net. Se o nome inserido não for exclusivo, um ponto de exclamação vermelho aparecerá na caixa de texto.
8. Selecione um **Grupo de Recursos** ou crie um novo.
   
    Para saber mais sobre os grupos de recursos, confira [Visão geral do Resource Manager].
9. Selecione um **Plano/Local do Serviço de Aplicativo** ou crie um novo.
   
    Para saber mais sobre os planos do Serviço de Aplicativo, confira [Visão geral dos planos do Serviço de Aplicativo do Azure].
10. Clique em **Criar**.
    
    ![Criar Portal Jetty][jettyportalcreate2]
    
    Em pouco tempo, normalmente menos de um minuto, o Azure termina de criar o novo aplicativo Web.
11. Clique em **Aplicativos Web > {seu novo aplicativo Web}**.
12. Clique na **URL** para navegar até o novo site.
    
    ![URL do Jetty][jettyurl]
    
    O Tomcat vem com um conjunto padrão de páginas e, portanto, se você tiver escolhido o Tomcat, verá uma página semelhante ao exemplo a seguir.
    
    ![Aplicativo Web usando o Apache Tomcat][tomcat]
    
    Se você tiver escolhido o Jetty, verá uma página semelhante ao exemplo a seguir. O Jetty não tem um conjunto de páginas padrão e, portanto, o mesmo JSP usado para um site Java vazio será reutilizado aqui.
    
    ![Aplicativo Web usando o Jetty][jetty]

Agora que você criou o aplicativo Web com um contêiner de aplicativos, confira a seção [Próximas etapas](#next-steps) para obter informações sobre como carregar seu aplicativo no aplicativo Web.

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um servidor de aplicativos Java em execução em seu aplicativo Web no Serviço de Aplicativo do Azure. Para adicionar seu próprio código ao aplicativo Web, consulte [Adicionar um aplicativo ou uma página da Web ao seu aplicativo Web Java].

Para saber mais sobre o desenvolvimento de aplicativos Java no Azure, consulte o [Centro de Desenvolvedores do Java].

<!-- URL List -->

[Adicionar um aplicativo ou uma página da Web ao seu aplicativo Web Java]: ./web-sites-java-add-app.md
[Visão geral dos planos do Serviço de Aplicativo do Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal do Azure]: https://portal.azure.com/
[ativar os benefícios de assinante do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[inscrever-se em uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Experimentar o Serviço de Aplicativo]: https://azure.microsoft.com/try/app-service/
[Aplicativo Web Java]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de Desenvolvedores do Java]: /develop/java/
[Visão geral do Resource Manager]: ../azure-resource-manager/resource-group-overview.md
[Carregar um aplicativo Web Java personalizado no Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=Jan17_HO3-->



<properties 
	pageTitle="Criar uma campanha de marketing digital em Aplicativos Web do Serviço de Aplicativo do Azure" 
	description="Este guia fornece uma visão geral técnica de como usar Aplicativos Web do Serviço de Aplicativo do Azure para criar campanhas de marketing digital. Isso inclui implantação, integração de mídia social, estratégias de dimensionamento e monitoramento." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/29/2015" 
	ms.author="cephalin"/>

# Criar uma campanha de marketing digital em Aplicativos Web do Serviço de Aplicativo do Azure
Os Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) são uma ótima opção para campanhas de marketing digital. As campanhas de marketing digital geralmente têm curta duração e servem para orientar metas de marketing de curto prazo. Há dois cenários principais a serem considerados. No primeiro cenário, uma empresa de marketing de terceiros cria e gerencia a campanha para os clientes durante o período da promoção. Um segundo cenário envolve a empresa de marketing criando e, em seguida, transferindo a propriedade dos recursos digitais da campanha de marketing para os clientes. Em seguida, o cliente executa e gerencia a campanha de marketing digital por conta própria. É uma boa opção para ambos os cenários.

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

A seguir há um exemplo de uma campanha de marketing digital global multicanais usando Aplicativos Web do Serviço de Aplicativo. Ele demonstra o que você pode fazer simplesmente reunindo Aplicativos Web do Serviço de Aplicativo a outros serviços, com investimento técnico mínimo. **Clique em um elemento na topografia para ler mais sobre ele.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas à execução de uma campainha de marketing digital em Aplicativos Web do Serviço de Aplicativo do Azure. No entanto, há outras soluções comuns que você pode implementar em Aplicativos Web do Serviço de Aplicativo. Para examinar essas soluções, consulte também as outras guias em [Presença Global da Web](web-sites-global-web-presence-solution-overview.md) e [Aplicativos de Negócios](web-sites-business-application-solution-overview.md).

## Criar do zero ou incluir ativos existentes

Crie novos aplicativos Web rapidamente por meio de um CMS popular na galeria ou inclua seus ativos da Web existentes nos Aplicativos Web do Serviço de Aplicativo dentre diversas linguagens e estruturas.

O Azure Marketplace oferece modelos populares de CMS (sistemas de gerenciamento de conteúdo de site), como Orchard, Umbraco, Drupal e [WordPress]. Você pode criar um aplicativo Web usando seu tipo de CMS favorito. Você pode escolher dentre vários back-ends de banco de dados para atender às suas necessidades, incluindo o [Banco de Dados do SQL Azure] e [MySQL].

Seus ativos da Web existentes podem ser executados em Aplicativos Web, independentemente de utilizarem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para os Aplicativos Web usando suas ferramentas de [FTP] familiares. Se costuma cria campanhas de marketing digitais com frequência, é possível que você tenha ativos da Web existentes em um sistema de gerenciamento de controle do código-fonte. Você pode implantar Aplicativos Web diretamente de opções populares de controle do código-fonte, como o [Visual Studio], [Visual Studio Online] e [Git] - local, GitHub, BitBucket, DropBox, Mercurial, etc.

## Permanecer ágil

Permaneça ágil publicando de forma contínua diretamente de seu controle de código-fonte existente e execute testes A/B nos Aplicativos Web do Serviço de Aplicativo.

Durante o planejamento, a prototipagem e o desenvolvimento inicial de um aplicativo Web, você e seu computador podem examinar versões de trabalho reais do aplicativo de campanha antes que ele entre no ar [implantando em um slot de preparo] do aplicativo Web. Integrando o controle de código-fonte a Aplicativos Web do Serviço de Aplicativos, você pode [publicar continuamente] em um slot de preparo e alternando-o para a produção sem tempo de inatividade, quando estiver pronto.

Além disso, ao planejar alterações em aplicativo Web ativo, você pode facilmente [executar testes A/B] nas atualizações propostas usando o recurso Testar em Produção e analisar o comportamento dos usuários reais para ajudá-lo a tomar decisões embasadas sobre o design do aplicativo.


## Usar recursos sociais

Sua campanha de marketing digital nos Aplicativos Web do Serviço de Aplicativo pode se integrar à mídia social por meio da autenticação com provedores populares, como Facebook e Twitter. Para obter um exemplo dessa abordagem com um aplicativo ASP.NET, consulte [Criar um aplicativo MVC ASP.NET com autenticação e Banco de Dados SQL e implantar no Serviço de Aplicativo do Azure].

Além disso, cada site de mídia social geralmente fornece informações sobre outras maneiras de se integrar a ele do .NET e muitas outras estruturas.

## Usar mídia avançada e acessar todos os dispositivos

Enriqueça sua campanha de marketing digital com outros serviços do Azure, como:

-  Carregar e transmitir vídeos globalmente com os [Serviços de Mídia do Azure]
-  Enviar emails aos usuários com o [serviço SendGrid no Azure Marketplace]
-  Estabelecer presença em dispositivos Windows, iOS e Android com [Serviços Móveis]
-  Enviar notificações por push a milhões de dispositivos com o [Hub de Notificação]

## Ter alcance global

Tenha alcance global oferecendo sites regionais com o Gerenciador de Tráfego do Azure e fornecendo conteúdo de forma extremamente rápida com a CDN do Azure.

Para atender a clientes globais em suas respectivas regiões, use o [Gerenciador de Tráfego do Azure] para rotear os visitantes do site para um site regional que ofereça o melhor desempenho. Como alternativa, você pode distribuir a carga do site de forma uniforme entre várias cópias de seu aplicativo Web hospedadas em diversas regiões.

Forneça seu conteúdo estático de forma extremamente rápida aos usuários globalmente [integrando seu aplicativo Web à CDN do Azure]. A CDN do Azure armazena em cache o conteúdo estático no [nó CDN] mais próximo ao usuário, o que minimiza a latência e as conexões a seu aplicativo Web.

## Otimizar

Otimize seu aplicativo Web dimensionando automaticamente com o recurso de Autoescala, armazenando em cache com o Cache Redis do Azure, executando tarefas em segundo plano com Trabalhos Web e mantendo alta disponibilidade com o Gerenciador de Tráfego do Azure.

A capacidade dos Aplicativos Web do Serviço Aplicativos de [escalar verticalmente e horizontalmente] é perfeita para cargas de trabalho imprevisíveis, o que é o caso das campanhas de marketing digital. Escale horizontalmente seu aplicativo Web de forma manual por meio do [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), de forma programática por meio da [API de Gerenciamento de Serviços] ou dos [scripts do PowerShell] ou de forma automática por meio do recurso de Autoescala. Na camada **Padrão**, o recurso de Autoescala o habilita a escalar horizontalmente um aplicativo Web de forma automática com base na utilização da CPU. Esse recurso ajuda a maximizar a agilidade e minimizar os custos ao mesmo tempo, escalando horizontalmente o aplicativo Web apenas quando necessário com base na atividade do usuário. Para obter as práticas recomendadas, confira o artigo de [Troy Hunt]\: [Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure].

Torne seu aplicativo Web mais ágil com o [Cache Redis do Azure]. Use-o para armazenar em cache os dados de bancos de dados de back-end e outros itens, como o [estado da sessão ASP.NET] e o [cache de saída].

Mantenha a alta disponibilidade de seu aplicativo Web usando o [Gerenciador de Tráfego do Azure]. Usando o método de **Failover**, o Gerenciador de Tráfego roteia automaticamente o tráfego para um site secundário, se houver um problema no site primário.

## Monitorar e analisar

Mantenha-se atualizado sobre o desempenho de seu aplicativo Web com o Azure ou com ferramentas de terceiros. Receba alertas sobre eventos críticos do aplicativo Web. Obtenha facilmente percepções sobre o usuário com o Application Insights ou com a análise de log da Web do HDInsight.

Obtenha uma [visão rápida] das métricas de desempenho atuais do aplicativo Web e das cotas de recursos na folha do aplicativo Web no [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para ter uma visão de 360° de seu aplicativo em termos de disponibilidade, desempenho e uso, use o [Azure Application Insights] para obter informações rápidas e eficazes sobre solução de problemas, diagnósticos e uso. Ou use uma ferramenta de terceiros, como o [New Relic], para fornecer dados de monitoramento avançado sobre seus aplicativos Web.

Na camada **Padrão**, monitore a capacidade de resposta do aplicativo e receba notificações por email sempre que seu aplicativo Web não responder. Para obter mais informações, consulte [Como: receber notificações de alerta e gerenciar regras de alerta no Azure].

## Mais Recursos

- [Documentação de Aplicativos Web do Serviço de Aplicativo](/services/app-service/web/)
- [Mapa de aprendizagem para Aplicativos Web do Serviço de Aplicativo do Azure](websites-learning-map.md)
- [Blog do Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Banco de Dados do SQL Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[implantando em um slot de preparo]: web-sites-staged-publishing.md
[publicar continuamente]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[executar testes A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Criar um aplicativo MVC ASP.NET com autenticação e Banco de Dados SQL e implantar no Serviço de Aplicativo do Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Serviços de Mídia do Azure]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[serviço SendGrid no Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Serviços Móveis]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Hub de Notificação]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Gerenciador de Tráfego do Azure]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrando seu aplicativo Web à CDN do Azure]: cdn-websites-with-cdn.md
[nó CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalar verticalmente e horizontalmente]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[API de Gerenciamento de Serviços]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[scripts do PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis do Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado da sessão ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache de saída]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[visão rápida]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[Como: receber notificações de alerta e gerenciar regras de alerta no Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=Nov15_HO2-->
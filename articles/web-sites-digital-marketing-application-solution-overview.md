<properties 
	pageTitle="Criar uma campanha de marketing digital nos sites do Azure" 
	description="Este guia fornece uma visão geral técnica de como usar sites do Azure para criar campanhas de marketing digital. Isso inclui implantação, integração de mídia social, estratégias de dimensionamento e monitoramento." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Campanhas de Marketing digital em Websites do Azure
Os [Websites do Azure] são uma ótima opção para campanhas de marketing digital. As campanhas de marketing digital são geralmente de curta duração e se destinam a direcionar os objetivos de marketing de curto prazo. Há dois cenários principais a serem considerados. No primeiro cenário, uma empresa de marketing de terceiros cria e gerencia a campanha para os clientes durante o período da promoção. Um segundo cenário envolve a empresa de marketing criando e, em seguida, transferindo a propriedade dos recursos digitais da campanha de marketing para os clientes. Em seguida, o cliente executa e gerencia a campanha de marketing digital por conta própria. É uma boa combinação para os dois cenários. 

> [AZURE.NOTE] Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

Abaixo está um exemplo de uma campanha de marketing digital de vários canais global usando Websites do Azure. Ele demonstra o que você pode fazer simplesmente ao compor Websites do Azure junto com outros serviços com investimentos técnicos mínimos. **Clique em um elemento na topografia para ler mais sobre ele.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Este guia apresenta algumas das áreas e tarefas mais comuns que são alinhadas com a execução de uma campanha de marketing digital em Websites do Azure. No entanto, há outras soluções comuns que você pode implementar nos Websites do Azure. Para revisar essas soluções, consulte as outras guias em <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">presença Global na Web Global</a> e <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">Aplicativos de negócios</a>.

### Crie do zero ou coloque ativos existentes

Crie rapidamente novos sites a partir de um CMS popular na galeria ou traga os ativos da web existentes para Websites do Azure a partir de uma variedade de linguagens e estruturas.

A galeria do Azure fornece modelos a partir dos sistemas de gerenciamento de conteúdo (CMS) do site popular, como [Orchard], [Umbraco], [Drupal] e [WordPress]. Você pode criar um site usando o sabor CMS favorito. Você tem vários back-ends do banco de dados para atender às suas necessidades, incluindo [Banco de Dados SQL Azure] e [MySQL].

Os ativos da web existentes podem executar em Websites do Azure, independentemente de serem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para Websites do Azure usando as ferramentas [FTP] familiares. Se você costuma cria campanhas de marketing digital, é possível que você tenha ativos da web existentes em um sistema de gerenciamento de controle de origem. Você pode implantar Websites do Azure diretamente a partir de opções de controle de origem popular, como [Visual Studio], [Visual Studio Online] e [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Manter-se ágil

Se mantenha ágil continuamente publicando diretamente do seu controle de origem existente e execute testes A/B em Websites do Azure. 

Durante o planejamento, criação de protótipos e desenvolvimento inicial de um site, você e seu cliente podem examinar versões de trabalho reais do site da campanha antes de entrarem no ar ao [implantando em um slot de preparo] do seu Website do Azure. Ao integrar o controle de origem com Websites do Azure, você pode [publicar continuamente] para um slot de preparo e trocá-lo na produção sem tempo de inatividade quando estiver pronto. 

Além disso, ao planejar alterações em um site da Web online, você pode facilmente [executar testes A/B] nas atualizações propostas usando o recurso de teste na produção e analisar o comportamento do usuário real para ajudá-lo a tomar decisões informadas sobre o design do site.


### Socialize

Sua campanha de marketing digital em Websites do Azure pode integrar a mídia social autenticando com fornecedores populares como o Facebook e Twitter. Para obter um exemplo dessa abordagem com um aplicativo ASP.NET, consulte [Implantar um aplicativo ASP.NET MVC seguro com Associação, OAuth e banco de dados SQL em um Site do Azure]. 

Além disso, cada site de mídia social normalmente fornece informações sobre outras maneiras de integrar com o .NET e muitas outras estruturas.

### Usar mídia avançada e acessar todos os dispositivos

Aprimore sua campanha de marketing digital com outros serviços do Azure, tais como:

-  Carregar e transmitir vídeos globalmente com os [Serviços de mídia do Azure]
-  Enviar emails para usuários com o [Serviço do SendGrid no Azure Marketplace]
-  Estabelecer presença nos dispositivos Windows, iOS e Android com [serviços móveis]
-  Enviar notificação por push para milhões de dispositivos com [Hub de notificação]

### Globalize

Globalize ao servir sites regionais com o Azure Traffic Manager e fornecer conteúdo extremamente rápido com CDN do Azure.

Para atender clientes globais em suas respectivas regiões, use o [Gerenciador de Tráfego do Azure] para encaminhar visitantes do site para um site regional que fornece o melhor desempenho. Como alternativa, você pode difundir a carga do site uniformemente em várias cópias do seu site hospedado em várias regiões.

Forneça seu conteúdo estático extremamente rápido aos usuários globalmente ao [integrando seu site com o CDN do Azure]. O CDN do Azure armazena em cache o conteúdo estático no [nó do CDN] mais próximo para o usuário, o que minimiza a latência e conexões ao seu site.

### Otimizar

Otimize seu site, dimensionando automaticamente com o dimensionamento automático, armazenamento em cache com Cache Redis do Azure, executando tarefas em segundo plano com o WebJobs e mantendo a alta disponibilidade com o Azure Traffic Manager.

A capacidade de Websites do Azure para [aumentar e dimensionar] é perfeita para cargas de trabalho imprevisíveis, que é o caso com campanhas de marketing digital. Dimensione seu site manualmente através do [Portal de gerenciamento do Azure], de forma programática por meio de [API de gerenciamento de serviço] ou [script do PowerShell], ou automaticamente por meio do recurso de dimensionamento automático. No plano de hospedagem **padrão**, o dimensionamento automático permite expandir um site automaticamente com base na utilização da CPU. Esse recurso ajuda a maximizar a agilidade e minimizar os custos ao mesmo tempo que expande o site da Web apenas quando necessário com base na atividade do usuário. Para práticas recomendadas, consulte [10 coisas que eu aprendi sobre como dimensionar rapidamente sites com o Azure] do [Troy Hunt].

Torne seu site mais responsivo com o [Cache Redis do Azure]. Use-o em dados em cache a partir dos bancos de dados de back-end e outras coisas, como o [estado da sessão ASP.NET] e [cache de saída].

Mantenha a alta disponibilidade do seu site usando [Gerenciador de Tráfego do Azure]. Usando o método **Failover**, o Traffic Manager automaticamente roteia o tráfego para um site secundário, se houver um problema no site primário.

### Monitorar e analisar

Mantenha-se atualizado sobre o desempenho do seu site com o Azure ou ferramentas de terceiros. Receba alertas sobre eventos críticos do site. Obtenha informações do usuário facilmente com o Application Insight ou análise de log da web do HDInsight. 

Obtenha uma [visão rápida] das métricas de desempenho atual do site e recursos de cotas no painel do Websites do Azure. Para uma exibição de 360° do seu aplicativo sobre disponibilidade, desempenho e uso, use o [Azure Application Insights] para fornecer solução de problemas, diagnósticos e informações de uso rápido e eficaz. Ou use uma ferramenta de terceiros como [Nova Relíquia] para fornecer dados de monitoramento avançados para seus sites.

No plano de hospedagem **padrão**, monitore as notificações de email de recepção da capacidade de resposta do site sempre que seu site ficar sem resposta. Para obter mais informações, consulte [Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure].

## Mais recursos

- [Documentação dos Websites do Azure](/pt-br/documentation/services/websites/)
- [Mapa de aprendizado para Websites do Azure](websites-learning-map.md)
- [Blog de Web do Azure](/blog/topics/web/)


[Websites do Azure]:/pt-br/services/websites/

[Orchard]:/pt-br/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/pt-br/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/pt-br/documentation/articles/web-sites-php-migrate-drupal/
[wordpress]:/pt-br/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/pt-br/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Banco de Dados SQL Azure]:/pt-br/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/pt-br/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/pt-br/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/pt-br/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/pt-br/documentation/articles/web-sites-publish-source-control/

[implantando em um slot de preparo]:/pt-br/documentation/articles/web-sites-staged-publishing/ 
[publicar continuamente]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[executar testes A/B]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Implantar um aplicativo ASP.NET MVC seguro com Associação, OAuth e banco de dados SQL em um Site do Azure]:/pt-br/develop/net/tutorials/web-site-with-sql-database/

[Serviços de mídia do Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Serviço do SendGrid no Azure Marketplace]:/pt-br/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Serviços Móveis]:/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Hub de Notificação]:/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Gerenciador de Tráfego do Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrando seu site com o CDN do Azure]:/pt-br/documentation/articles/cdn-websites-with-cdn/ 
[Nó do CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[dimensionar]:/pt-br/manage/services/web-sites/how-to-scale-websites/
[Portal de Gerenciamento do Azure]:http://manage.windowsazure.com/
[API de gerenciamento de serviço]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Script do PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 coisas que eu aprendi sobre como dimensionar rapidamente sites com o Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis do Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[Estado da sessão ASP.NET]:https://msdn.microsoft.com/pt-br/library/azure/dn690522.aspx
[cache de saída]:https://msdn.microsoft.com/pt-br/library/azure/dn798898.aspx

[visão rápida]:/pt-br/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[Nova Relíquia]:/pt-br/develop/net/how-to-guides/new-relic/
[Como: receber notificações de alerta e gerenciar regras de alerta no Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  


<!--HONumber=42-->

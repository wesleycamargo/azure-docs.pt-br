<properties 
	pageTitle="Criar uma presença global na Web em Aplicativos Web do Serviço de Aplicativo do Azure" 
	description="Este guia fornece uma visão geral técnica de como hospedar o site de sua organização (.COM) em Aplicativos Web do Serviço de Aplicativo do Azure. Isso inclui implantação, domínios personalizados, SSL e monitoramento." 
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
	ms.date="07/06/2015" 
	ms.author="cephalin"/>


# Criar uma presença global na Web em Aplicativos Web do Serviço de Aplicativo do Azure

Os Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) têm todos os recursos de que você precisa para estabelecer uma presença global na Web para seu site .COM. Independentemente do porte de sua organização, você precisa de uma plataforma robusta, segura e escalonável para impulsionar seus negócios, o reconhecimento de sua marca e suas comunicações com os clientes. Os Aplicativos Web do Serviço de Aplicativo podem ajudar a manter sua identidade e marca corporativa com a continuidade dos negócios garantida pela Microsoft.

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

A seguir há um exemplo de um site .COM em execução nos Aplicativos Web do Serviço de Aplicativo. Ele demonstra o que você pode fazer simplesmente reunindo Aplicativos Web a outros serviços, com investimento técnico mínimo. **Clique em um elemento na topografia para ler mais sobre ele.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas com a execução de um site .COM voltado ao público em Aplicativos Web do Serviço de Aplicativo do Azure. No entanto, há outras soluções comuns que você pode implementar em Aplicativos Web do Serviço de Aplicativo do Azure. Para examinar essas soluções, consulte também os outros guias em [Campanhas de Marketing Digital](web-sites-digital-marketing-application-solution-overview.md) e [Aplicativos de negócios](web-sites-business-application-solution-overview.md).

## Criar do zero ou incluir ativos existentes

Crie novos sites rapidamente por meio de um CMS popular na galeria ou inclua seus ativos da Web existentes nos Aplicativos Web do Serviço de Aplicativo dentre diversas linguagens e estruturas.

O Azure Marketplace fornece modelos de CMS (sistemas de gerenciamento de conteúdo de site) populares, como [Orchard], [Umbraco], [Drupal] e [WordPress]. Você pode criar um aplicativo Web usando seu tipo de CMS favorito. Você pode escolher dentre vários back-ends de banco de dados para atender às suas necessidades, incluindo o [Banco de Dados do SQL Azure] e [MySQL].

Seus ativos da Web existentes podem ser executados em aplicativos Web do Serviço de Aplicativo, independentemente de utilizarem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para os Aplicativos Web usando suas ferramentas de [FTP] familiares ou seu sistema de gerenciamento de controle do código-fonte. Os Aplicativos Web oferecem suporte à publicação direta de opções de controle de código-fonte populares, como [Visual Studio], [Visual Studio Online] e [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

## Publicar de forma confiável

Publica seu site com confiabilidade publicando de forma contínua diretamente de seu sistema de controle de código-fonte existente e testando o conteúdo dinamicamente.

Durante o planejamento, a prototipagem e o desenvolvimento inicial de um site, você pode examinar versões de trabalho reais do site antes que ele entre no ar [implantando em um slot de preparo do site] em Aplicativos Web do Serviço de Aplicativo. Integrando o controle de código-fonte a Aplicativos Web, você pode [publicar continuamente] em um slot de preparo e alternando-o para a produção sem tempo de inatividade, quando estiver pronto para fazê-lo. Se algo der errado no site de produção, você também poderá alterná-lo para uma versão anterior do site imediatamente.

Além disso, ao planejar alterações em um site da Web ativo, você pode facilmente [executar testes A/B] nas atualizações propostas usando o recurso Testar em Produção e analisar o comportamento dos usuários reais para ajudá-lo a tomar decisões embasadas sobre o design do site.

## Marca e proteção

Use o domínio dos Aplicativos Web do Serviço de Aplicativo gratuitamente ou mapeie para seu nome de domínio registrado e proteja sua marca com seu certificado SSL assinado por uma autoridade de certificação.

O domínio ***.azurewebsites.net** é gratuito quando você executa seu site nos Aplicativos Web. Ou então, você pode mapear seu site para um [domínio personalizado] (por exemplo, contoso.com), que você obteve em qualquer registro de DNS, como GoDaddy.

Se coleta informações do usuário, realiza comércio eletrônico ou gerencia outros dados confidenciais, você pode proteger a reputação de sua marca e seus clientes com o [HTTPS]. O nome de domínio ***.azurewebsites.net** já vem com um certificado SSL e, se usar seu domínio personalizado, você poderá incluir seu certificado SSL para ele nos Aplicativos Web. Há uma cobrança mensal (rateada por hora) associada a cada certificado SSL. Para obter mais informações, confira [Detalhes de preços do Serviço de Aplicativos].

## Ter alcance global

Tenha alcance global oferecendo sites regionais com o Gerenciador de Tráfego do Azure e fornecendo conteúdo de forma extremamente rápida com a CDN do Azure.

Para atender a clientes globais em suas respectivas regiões, use o [Gerenciador de Tráfego do Azure] para rotear os visitantes do site para um site regional que ofereça o melhor desempenho. Como alternativa, você pode distribuir a carga do site de forma uniforme entre várias cópias de seu site hospedadas em diversas regiões.

Forneça seu conteúdo estático de forma extremamente rápida aos usuários globalmente [integrando seu aplicativo Web à CDN do Azure]. A CDN do Azure armazena em cache o conteúdo estático no [nó CDN] mais próximo ao usuário, o que minimiza a latência e as conexões a seu site.

## Otimizar

Otimize seu site .COM dimensionando automaticamente com o recurso de Autoescala, armazenando em cache com o Cache Redis do Azure, executando tarefas em segundo plano com Trabalhos Web e mantendo alta disponibilidade com o Gerenciador de Tráfego do Azure.

A capacidade dos Aplicativo Web do Serviço Aplicativos de [escalar verticalmente e horizontalmente] atende às necessidades de seu site .COM, independentemente do porte de sua carga de trabalho. Escale horizontalmente seu site de forma manual por meio do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), de forma programática por meio da [API de Gerenciamento de Serviços] ou dos [scripts do PowerShell] ou de forma automática por meio do recurso de Autoescala. No plano de hospedagem **Padrão**, o recurso de Autoescala o habilita a escalar horizontalmente um site de forma automática com base na utilização da CPU. Para obter as práticas recomendadas, confira o artigo de [Troy Hunt]: [Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure].

Torne seu site mais ágil com o [Cache Redis do Azure]. Use-o para armazenar em cache os dados de bancos de dados de back-end e outros itens, como o [estado da sessão ASP.NET] e o [cache de saída].

Mantenha a alta disponibilidade de seu site usando o [Gerenciador de Tráfego do Azure]. Usando o método de **Failover**, o Gerenciador de Tráfego roteia automaticamente o tráfego para um site secundário, se houver um problema no site primário.

## Monitorar e analisar

Mantenha-se atualizado sobre o desempenho de seu site com o Azure ou com ferramentas de terceiros. Receba alertas sobre eventos críticos do site. Obtenha facilmente percepções sobre o usuário com o Application Insights ou com a análise de log da Web do HDInsight.

Obtenha uma [visão rápida] das métricas de desempenho atuais do site e das cotas de recursos na folha do aplicativo Web no [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para ter uma visão de 360° de seu aplicativo em termos de disponibilidade, desempenho e uso, use o [Azure Application Insights] para obter informações rápidas e eficazes sobre solução de problemas, diagnósticos e uso. Ou então, use uma ferramenta de terceiros, como o [New Relic], para fornecer dados de monitoramento avançado sobre seus sites.

No plano de hospedagem **Padrão**, monitore a capacidade de resposta do site e receba notificações por email sempre que seu site não responder. Para obter mais informações, consulte [Como: receber notificações de alerta e gerenciar regras de alerta no Azure].

## Usar mídia avançada e acessar todos os dispositivos

Torne seu site .COM atraente com recursos de mídia avançada, como:

-  Carregar e transmitir vídeos globalmente com os [Serviços de Mídia do Azure]
-  Enviar emails aos usuários com o [serviço SendGrid no Azure Marketplace]

## Mais Recursos

- [Documentação de Aplicativos Web do Serviço de Aplicativo](/services/app-service/web/)
- [Mapa de aprendizagem para Aplicativos Web do Serviço de Aplicativo do Azure](websites-learning-map.md)
- [Blog do Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Banco de Dados do SQL Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[implantando em um slot de preparo do site]: web-sites-staged-publishing.md
[publicar continuamente]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[executar testes A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[domínio personalizado]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[Detalhes de preços do Serviço de Aplicativos]: /pricing/details/app-service/#ssl-connections

[Gerenciador de Tráfego do Azure]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrando seu aplicativo Web à CDN do Azure]: cdn-websites-with-cdn.md
[nó CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalar verticalmente e horizontalmente]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[API de Gerenciamento de Serviços]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[scripts do PowerShell]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis do Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado da sessão ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache de saída]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[visão rápida]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Como: receber notificações de alerta e gerenciar regras de alerta no Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Serviços de Mídia do Azure]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[serviço SendGrid no Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=July15_HO3-->
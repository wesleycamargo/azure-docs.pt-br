<properties 
	pageTitle="Crie uma presença Global na Web em Sites Azure" 
	description="Este guia fornece uma visão geral técnica de como hospedar o site da sua organização (.COM) em sites do Azure. Isso inclui implantação, domínios personalizados, SSL e monitoramento." 
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


# Crie uma presença Global na Web em Sites Azure

Os [Websites do Azure] têm todos os recursos que você precisa para estabelecer uma presença global na Web para seu site .COM. Independentemente do tamanho da sua organização, você precisa de uma plataforma robusta, segura e escalonável para impulsionar seus negócios, o reconhecimento de sua marca e suas comunicações com o cliente. Os sites do Azure podem ajudar a manter sua marca corporativa e a identidade com a Microsoft reserva continuidade de negócios.

> [AZURE.NOTE] Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

Abaixo está um exemplo de um site .COM em execução em Websites do Azure. Ele demonstra o que você pode fazer simplesmente ao compor Websites do Azure junto com outros serviços com investimentos técnicos mínimos. **Clique em um elemento na topografia para ler mais sobre ele.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Este guia apresenta algumas das áreas mais comuns e tarefas que estão alinhadas com a execução de um site. voltado para o público em Websites do Azure. No entanto, há outras soluções comuns que você pode implementar nos Websites do Azure. Para revisar essas soluções, consulte as outras guias em <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campanhas de Marketing Digital</a> e <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">Aplicativos de negócios</a>.

### Crie do zero ou coloque ativos existentes

Crie rapidamente novos sites a partir de um CMS popular na galeria ou traga os ativos da web existentes para Websites do Azure a partir de uma variedade de linguagens e estruturas.

A galeria do Azure fornece modelos a partir dos sistemas de gerenciamento de conteúdo (CMS) do site popular, como [Orchard], [Umbraco], [Drupal] e [WordPress]. Você pode criar um site usando o sabor CMS favorito. Você tem vários back-ends do banco de dados para atender às suas necessidades, incluindo [Banco de Dados SQL Azure] e [MySQL].

Os ativos da web existentes podem executar em Websites do Azure, independentemente de serem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para Websites do Azure usando as ferramentas [FTP] familiares ou o sistema de gerenciamento de controle de origem. Os Websites do Azure oferecem suporte de publicação direta a partir das opções de controle de origem popular, como [Visual Studio], [Visual Studio Online] e [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Publicação confiável

Publique o seu site confiávelao publicar continuamente diretamente do seu sistema de controle de origem existente e testando online seu conteúdo. 

Durante o planejamento, criação de protótipos e desenvolvimento inicial de um site, você pode examinar as versões de trabalho reais do site antes de entrar no ar ao [implantar em um slot de preparo] do seu Website do Azure. Ao integrar o controle de origem com Websites do Azure, você pode [publicar continuamente] para um slot de preparo e trocar na produção sem tempo de inatividade, quando você estiver pronto para fazer isso. Se algo der errado no site de produção, você também pode trocá-lo para uma versão anterior do seu site imediatamente. 

Além disso, ao planejar alterações em um site da Web online, você pode facilmente [executar testes A/B] nas atualizações propostas usando o recurso de teste na produção e analisar o comportamento do usuário real para ajudá-lo a tomar decisões informadas sobre o design do site.

### Marca e proteção

Use o domínio de sites gratuitamente ou mapeie para seu nome de domínio registrado e proteja sua marca com o certificado SSL assinado por autoridade de certificação.

O domínio **\*.azurewebsites.net** é complementar ao executar seu site em Websites do Azure. Ou, você pode mapear seu site para um [domínio personalizado] (por exemplo, contoso.com), que você obteve em qualquer registro DNS, como GoDaddy.

Se você coleta informações do usuário, realiza comércio eletrônico ou gerencia quaisquer outros dados confidenciais, você pode proteger a reputação de sua marca e seus clientes com [HTTPS]. O nome do domínio **\*.azurewebsites.net** já vem com um certificado SSL e se você usar seu domínio personalizado, você pode colocar seu certificado SSL para ele para os Websites do Azure. Há uma cobrança mensal (rateada por hora) associada a cada certificado SSL. Para obter mais informações, consulte [Detalhes de preços de sites].

### Globalize

Globalize ao servir sites regionais com o Azure Traffic Manager e fornecer conteúdo extremamente rápido com CDN do Azure.

Para atender clientes globais em suas respectivas regiões, use o [Gerenciador de Tráfego do Azure] para encaminhar visitantes do site para um site regional que fornece o melhor desempenho. Como alternativa, você pode difundir a carga do site uniformemente em várias cópias do seu site hospedado em várias regiões.

Forneça seu conteúdo estático extremamente rápido aos usuários globalmente ao [integrando seu site com o CDN do Azure]. O CDN do Azure armazena em cache o conteúdo estático no [nó do CDN] mais próximo para o usuário, o que minimiza a latência e conexões ao seu site.

### Otimizar

Otimize seu site .COM, dimensionando automaticamente com o dimensionamento automático, armazenamento em cache com Cache Redis do Azure, executando tarefas em segundo plano com o WebJobs e mantendo a alta disponibilidade com o Azure Traffic Manager.

A capacidade dos Websites do Azure para [dimensionar] atende à necessidade de seu site .COM, independentemente do tamanho da carga de trabalho. Dimensione seu site manualmente através do [Portal de gerenciamento do Azure], de forma programática por meio de [API de gerenciamento de serviço] ou [script do PowerShell], ou automaticamente por meio do recurso de dimensionamento automático. No plano de hospedagem **padrão**, o dimensionamento automático permite expandir um site automaticamente com base na utilização da CPU. Para práticas recomendadas, consulte [10 coisas que eu aprendi sobre como dimensionar rapidamente sites com o Azure] do [Troy Hunt].

Torne seu site mais responsivo com o [Cache Redis do Azure]. Use-o em dados em cache a partir dos bancos de dados de back-end e outras coisas, como o [estado da sessão ASP.NET] e [cache de saída].

Mantenha a alta disponibilidade do seu site usando [Azure Traffic Manager]. Usando o método **Failover**, o Traffic Manager automaticamente roteia o tráfego para um site secundário, se houver um problema no site primário.

### Monitorar e analisar

Mantenha-se atualizado sobre o desempenho do seu site com o Azure ou ferramentas de terceiros. Receba alertas sobre eventos críticos do site. Obtenha informações do usuário facilmente com o Application Insight ou análise de log da web do HDInsight. 

Obtenha uma [visão rápida] das métricas de desempenho atual do site e recursos de cotas no painel do Websites do Azure. Para uma exibição de 360° do seu aplicativo sobre disponibilidade, desempenho e uso, use o [Azure Application Insights] para fornecer solução de problemas, diagnósticos e informações de uso rápido e eficaz. Ou use uma ferramenta de terceiros como [Nova Relíquia] para fornecer dados de monitoramento avançados para seus sites.

No plano de hospedagem **padrão**, monitore as notificações de email de recepção da capacidade de resposta do site sempre que seu site ficar sem resposta. Para obter mais informações, consulte [Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure].

### Usar mídia avançada e acessar todos os dispositivos

Torne seu site .COM atraente com mídia avançada, como:

-  Carregar e transmitir vídeos globalmente com os [Serviços de mídia do Azure]
-  Enviar emails para usuários com o [Serviço do SendGrid no Azure Marketplace]

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

[domínios personalizados]:/pt-br/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
[Detalhes do preço dos sites]:/pt-br/pricing/details/web-sites/#service-ssl

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

[Serviços de mídia do Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Serviço do SendGrid no Azure Marketplace]:/pt-br/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->

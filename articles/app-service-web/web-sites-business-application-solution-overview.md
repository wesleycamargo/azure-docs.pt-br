<properties 
	pageTitle="Criar um aplicativo Web de linha de negócios no Serviço de Aplicativo do Azure" 
	description="Este guia fornece uma visão geral técnica de como usar Aplicativos Web do Serviço de Aplicativo do Azure para criar aplicativos de linha de negócios de intranet. Isso inclui estratégias de autenticação, retransmissão do barramento de serviço e monitoramento." 
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



# Criar um aplicativo Web de linha de negócios no Serviço de Aplicativo do Azure

[Os Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) são uma ótima opção para aplicativos de linha de negócios. Esses são aplicativos de intranet que devem ser protegidos para uso comercial interno. Geralmente, eles exigem autenticação, normalmente em relação a um diretório corporativo, e algum acesso ou integração a dados e serviços locais.

Há grandes benefícios ao mover aplicativos de linha de negócios para Aplicativos Web do Serviço de Aplicativo, como:

-  escalar verticalmente e horizontalmente com cargas de trabalho dinâmicas, como um aplicativo que lida com revisões de desempenho anuais. Durante o período de revisão, o tráfego terá picos de altos níveis para uma grande empresa. O Azure fornece opções de escala que permitem que a empresa seja escalada horizontalmente para lidar com a carga durante o período de análise de tráfego intenso, poupando dinheiro através da escala de volta para o restante do ano. 
-  concentrar-se mais no desenvolvimento de aplicativos e menos na aquisição de infraestrutura e gerenciamento
-  maior suporte para funcionários e parceiros para usar o aplicativo em qualquer lugar. Os usuários não precisam estar conectados à rede corporativa para poder usar o aplicativo, e o grupo de TI evita soluções complexas de proxy reverso. Há várias opções de autenticação para certificar-se de que o acesso aos aplicativos da empresa estão protegidos.

A seguir há um exemplo de um aplicativo de linha de negócios em execução nos Aplicativos Web do Serviço de Aplicativo. Ele demonstra o que você pode fazer simplesmente reunindo Aplicativos Web a outros serviços, com investimento técnico mínimo. **Clique em um elemento na topografia para ler mais sobre ele.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas aos aplicativos de linha de negócios. No entanto, há outros recursos dos Aplicativos Web do Serviço de Aplicativo do Azure que você pode usar em sua implementação específica. Para rever esses recursos, consulte também as outras guias no [Presença Global da Web](web-sites-global-web-presence-solution-overview.md) e [Campanhas de Marketing Digital](web-sites-digital-marketing-application-solution-overview.md).

## Incluir ativos existentes

Inclua seus ativos da Web existentes nos Aplicativos Web do Serviço de Aplicativo com uma variedade de linguagens e estruturas.

Seus ativos da Web existentes podem ser executados em aplicativos Web do Serviço de Aplicativo, independentemente de utilizarem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para os Aplicativos Web usando suas ferramentas de [FTP] familiares ou seu sistema de gerenciamento de controle do código-fonte. Os Aplicativos Web oferecem suporte à publicação direta de opções de controle de código-fonte populares, como [Visual Studio], [Visual Studio Online] e [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

## Proteger seus ativos

Proteja os ativos com criptografia, autentique usuários corporativos no local ou remotamente e autorize seu uso dos ativos.

Proteja os ativos internos contra interceptadores com o [HTTPS]. O nome de domínio ***.azurewebsites.net** já vem com um certificado SSL e, se usar seu domínio personalizado, você poderá incluir seu certificado SSL para ele nos Aplicativos Web do Serviço de Aplicativo. Há uma cobrança mensal (rateada por hora) associada a cada certificado SSL. Para obter mais informações, confira [Detalhes de preços do Serviço de Aplicativos].

[Autentique usuários] em relação ao diretório corporativo. Os aplicativos Web do Serviço de Aplicativo podem autenticar usuários com provedores de identidade locais, como AD FS (Serviços de Federação do Active Directory), ou com um locatário do Active Directory do Azure que foi sincronizado com sua implantação corporativa do Active Directory. Os usuários podem acessar as propriedades da Web em aplicativos Web por meio de logon único quando estão no local e quando estão em campo. Serviços existentes, como o Office 365 ou o Microsoft Intune, já usam o Active Directory do Azure. Com o recurso de [Autenticação Fácil], é muito fácil ativar a autenticação com o mesmo locatário do Active Directory do Azure para seu aplicativo Web.

[Autorize usuários] para seu uso de propriedades da Web. Com código adicional mínimo, você pode incluir o mesmo padrão de codificação local do ASP.NET em aplicativos Web do Serviço de Aplicativo usando a decoração `[Authorize]`, por exemplo. Você mantém a mesma flexibilidade para controle de acesso refinado que os aplicativos que você mantém no local.

## Conectar-se a recursos locais ##

Conecte-se a seus dados de aplicativos Web ou recursos, se eles estiverem na nuvem para desempenho ou no local para conformidade. Para saber mais sobre como manter os dados no Azure, confira [Central de Confiabilidade do Azure].

Você pode escolher dentre vários back-ends de banco de dados no Azure para atender às necessidades de seu aplicativo Web, incluindo o [Banco de Dados SQL do Azure] e [MySQL]. Manter seus dados com segurança no Azure aproxima os dados de seu aplicativo Web em termos geográficos e otimiza o desempenho.

No entanto, sua empresa pode necessitar que seus dados sejam mantidos no local. Os Aplicativos Web do Serviço de Aplicativo permitem configurar facilmente uma [conexão híbrida] para seu recurso local, como um back-end de banco de dados. Para permitir o gerenciamento unificado de suas conexões locais, integre vários aplicativos Web com uma [Rede Virtual do Azure] que tenha uma VPN site a site. Você pode então acessar recursos locais como se seus aplicativos Web estivessem no local.

## Otimizar

Otimize seu aplicativo de linha de negócios dimensionando automaticamente com o recurso de Autoescala, armazenando em cache com o Cache Redis do Azure, executando tarefas em segundo plano com Trabalhos Web e mantendo alta disponibilidade com o Gerenciador de Tráfego do Azure.

A capacidade dos Aplicativos Web do Serviço Aplicativos de [escalar verticalmente e horizontalmente] atende às necessidades de seu aplicativo de linha de negócios, independentemente do porte de sua carga de trabalho. Expanda seu aplicativo Web de forma manual por meio do [Portal de Gerenciamento do Azure], de forma programática por meio da [API de Gerenciamento de Serviços] ou dos [scripts do PowerShell] ou de forma automática por meio do recurso de Autoescala. Na camada **Padrão**, o recurso de Autoescala o habilita a escalar horizontalmente um aplicativo Web de forma automática com base na utilização da CPU. Para obter as práticas recomendadas, confira o artigo de [Troy Hunt]: [Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure].

Torne seu aplicativo Web mais ágil com o [Cache Redis do Azure]. Use-o para armazenar em cache os dados de bancos de dados de back-end e outros itens, como o [estado da sessão ASP.NET] e o [cache de saída].

Mantenha a alta disponibilidade de seu aplicativo Web usando o [Gerenciador de Tráfego do Azure]. Usando o método de **Failover**, o Gerenciador de Tráfego roteia automaticamente o tráfego para um site secundário, se houver um problema no site primário.

## Monitorar e analisar

Mantenha-se atualizado sobre o desempenho de seu aplicativo Web com o Azure ou com ferramentas de terceiros. Receba alertas sobre eventos críticos do aplicativo Web. Obtenha facilmente percepções sobre o usuário com o Application Insights ou com a análise de log da Web do HDInsight.

Obtenha uma [visão rápida] das métricas de desempenho atuais do aplicativo Web e das cotas de recursos na folha do aplicativo Web no [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para ter uma visão de 360° de seu aplicativo em termos de disponibilidade, desempenho e uso, use o [Azure Application Insights] para obter informações rápidas e eficazes sobre solução de problemas, diagnósticos e uso. Ou use uma ferramenta de terceiros, como o [New Relic], para fornecer dados de monitoramento avançado sobre seus aplicativos Web.

Na camada **Padrão**, monitore a capacidade de resposta do aplicativo e receba notificações por email sempre que seu aplicativo não responder. Para obter mais informações, consulte [Como: receber notificações de alerta e gerenciar regras de alerta no Azure].

## Mais Recursos

- [Documentação de Aplicativos Web do Serviço de Aplicativo](/services/app-service/web/)
- [Mapa de aprendizagem para Aplicativos Web do Serviço de Aplicativo do Azure](websites-learning-map.md)
- [Blog do Azure](/blog/topics/web/)

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[HTTPS]: web-sites-configure-ssl-certificate.md
[Detalhes de preços do Serviço de Aplicativos]: /pricing/details/app-service/#ssl-connections
[Autentique usuários]: web-sites-authentication-authorization.md
[Autenticação Fácil]: /blog/2014/11/13/azure-websites-authentication-authorization/
[Autorize usuários]: web-sites-authentication-authorization.md

[Central de Confiabilidade do Azure]: /support/trust-center/
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Banco de Dados SQL do Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[conexão híbrida]: web-sites-hybrid-connection-get-started.md
[Rede Virtual do Azure]: web-sites-integrate-with-vnet.md

[escalar verticalmente e horizontalmente]: web-sites-scale.md
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
[API de Gerenciamento de Serviços]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[scripts do PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[Dez coisas que aprendi sobre como expandir com rapidez os aplicativos Web com o Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis do Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado da sessão ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache de saída]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[visão rápida]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Como: receber notificações de alerta e gerenciar regras de alerta no Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 

<!---HONumber=July15_HO4-->
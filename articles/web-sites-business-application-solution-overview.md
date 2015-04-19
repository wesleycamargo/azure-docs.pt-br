<properties 
	pageTitle="Criar um aplicativo de linha de negócios em Sites do Azure" 
	description="Este guia fornece uma visão geral técnica de como usar sites do Azure para criar aplicativos de linha de negócios da intranet. Isso inclui estratégias de autenticação, retransmissão do barramento de serviço e monitoramento." 
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



# Criar um aplicativo de linha de negócios em Sites do Azure

Os [Websites do Azure] são uma ótima opção para aplicativos de linha de negócios. Esses aplicativos são aplicativos de intranet que devem ser protegidos para uso comercial interno. Geralmente, eles exigem autenticação, normalmente em um diretório corporativo e algum acesso ou integração com dados no local e serviços. 

Há benefícios principais da mudança de aplicativos de linha de negócios para Websites do Azure, tais como:

-  aumentar e diminuir cargas de trabalho dinâmicas, como um aplicativo que lida com revisões de desempenho anual. Mas durante o período de revisão, o tráfego terá picos de altos níveis para uma grande empresa. O Azure fornece opções de escala que permitem que a empresa seja escalada horizontalmente para lidar com a carga durante o período de análise de tráfego intenso, poupando dinheiro através da escala de volta para o restante do ano. 
-  se concentra mais no desenvolvimento de aplicativos e menos na aquisição de infraestrutura e gerenciamento
-  maior suporte para funcionários e parceiros para usar o aplicativo de qualquer lugar. Os usuários não precisam estar conectados à rede corporativa para poder usar o aplicativo, e o grupo de TI evita soluções complexas de proxy reverso. Há várias opções de autenticação para certificar-se de que o acesso aos aplicativos da empresa estão protegidos.

Abaixo está um exemplo de um aplicativo de linha de negócios em execução em Websites do Azure. Ele demonstra o que você pode fazer simplesmente ao compor Websites do Azure junto com outros serviços com investimentos técnicos mínimos. **Clique em um elemento na topografia para ler mais sobre ele.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>Observação</strong>
<p>Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas com aplicativos de linha de negócios. No entanto, há outros recursos de sites do Azure que você pode usar em sua implementação específica. Para rever esses recursos, consulte também as outras guias no <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">Presença Global da Web</a> e <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campanhas de Marketing Digital</a>.</p>
</div>

### Colocar ativos existentes

Coloque os ativos da web existentes para Websites do Azurea partir de uma variedade de linguagens e estruturas.

Os ativos da web existentes podem executar em Websites do Azure, independentemente de serem .NET, PHP, Java, Node.js ou Python. Você pode movê-los para Websites do Azure usando as ferramentas [FTP] familiares ou o sistema de gerenciamento de controle de origem. Os Websites do Azure oferecem suporte de publicação direta a partir das opções de controle de origem popular, como [Visual Studio], [Visual Studio Online] e [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Proteger os ativos

Protege ativos, criptografia, autentica usuários corporativos se eles estão no local ou remoto e autoriza o uso de ativos. 

Protege ativos internos contra esses interceptadores com [HTTPS]. O nome do domínio **\*.azurewebsites.net** já vem com um certificado SSL e se você usar seu domínio personalizado, você pode colocar seu certificado SSL para ele para os Websites do Azure. Há uma cobrança mensal (rateada por hora) associada a cada certificado SSL. Para obter mais informações, consulte [Detalhes do preço dos sites].

[Autenticar usuários] no diretório corporativo. Os Websites do Azure podem autenticar usuários com provedores de identidade local, como os serviços de Federação do Active Directory (AD FS), ou com um locatário do Active Directory do Azure que foi sincronizado com a implantação corporativa do Active Directory. Os usuários podem acessar as propriedades da web nos Websites do Azure por meio de logon único quando estiverem no local e quando estiverem no campo. Os serviços existentes, como o Office 365 ou Windows Intune, já usam o Active Directory do Azure. Por meio de [Autenticação simples], ativar a autenticação com o mesmo locatário do Active Directory do Azure para seu site é muito fácil. 

[Autorizar usuários] para seu uso das propriedades da web. Com o mínimo de código adicional, você pode colocar o mesmo padrão de codificação do ASP.NET local para Websites do Azure usando a decoração `[Autorizar]`, por exemplo. Manter a mesma flexibilidade para controle de acesso refinado que os aplicativos que você mantém no local.

### Conectar-se a recursos locais ###

Conecte-se aos dados do site ou recursos, se ele estiver na nuvem para desempenho ou locais para fins de conformidade. Para obter mais informações sobre como manter os dados no Azure, consulte [Central de confiabilidade do Azure]. 

Você tem vários back-ends de banco de dados no Azure para atender às necessidades do seu site, incluindo [Banco de Dados SQL Azure] e [MySQL]. Mantendo seus dados com segurança no Azure faz com que os dados se aproximem de seu site geograficamente e otimiza o desempenho.

No entanto, sua empresa pode exigir que seus dados sejam mantidos no local. Os Websites Azure permitem que você configure facilmente uma [conexão híbrida] para o recurso local como um back-end do banco de dados. Se você quiser gerenciamento unificado de suas conexões locais, integre vários Websites do Azure com uma [Rede Virtual do Azure] que tem uma VPN site a site. Você pode então acessar recursos locais como se seus Websites do Azure estivessem no local. [Enterprise Pizza - Conectando Sites da Web no local usando o barramento de serviço][enterprisepizza]

### Otimizar

Otimize seu aplicativo de linha de negócio, dimensionando automaticamente com o dimensionamento automático, armazenamento em cache com Cache Redis do Azure, executando tarefas em segundo plano com o WebJobs e mantendo a alta disponibilidade com o Azure Traffic Manager.

A capacidade dos Websites do Azure para [dimensionar] atende à necessidade do seu aplicativo de linha de negócios, independentemente do tamanho da carga de trabalho. Dimensione seu site manualmente através do [Portal de gerenciamento do Azure], de forma programática por meio de [API de gerenciamento de serviço] ou [script do PowerShell], ou automaticamente por meio do recurso de dimensionamento automático. No plano de hospedagem **padrão**, o dimensionamento automático permite expandir um site automaticamente com base na utilização da CPU. Para práticas recomendadas, consulte [10 coisas que eu aprendi sobre como dimensionar rapidamente sites com o Azure] do [Troy Hunt].

Torne seu site mais responsivo com o [Cache Redis do Azure]. Use-o em dados em cache a partir dos bancos de dados de back-end e outras coisas, como o [estado da sessão ASP.NET] e [cache de saída].

Mantenha a alta disponibilidade do seu site usando [Azure Traffic Manager]. Usando o método **Failover**, o Traffic Manager automaticamente roteia o tráfego para um site secundário, se houver um problema no site primário.

### Monitorar e analisar

Mantenha-se atualizado sobre o desempenho do seu site com o Azure ou ferramentas de terceiros. Receba alertas sobre eventos críticos do site. Obtenha informações do usuário facilmente com o Application Insight ou análise de log da web do HDInsight. 

Obtenha uma [visão rápida] das métricas de desempenho atual do site e recursos de cotas no painel do Websites do Azure. Para uma exibição de 360° do seu aplicativo sobre disponibilidade, desempenho e uso, use o [Azure Application Insights] para fornecer solução de problemas, diagnósticos e informações de uso rápido e eficaz. Ou use uma ferramenta de terceiros como [Nova Relíquia] para fornecer dados de monitoramento avançados para seus sites.

No plano de hospedagem **padrão**, monitore as notificações de email de recepção da capacidade de resposta do site sempre que seu site ficar sem resposta. Para obter mais informações, consulte [Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure].

## Mais recursos

- [Documentação dos Websites do Azure](/pt-br/documentation/services/websites/)
- [Mapa de aprendizado para Websites do Azure](websites-learning-map.md)
- [Blog de Web do Azure](/blog/topics/web/)



[Websites do Azure]:/pt-br/services/websites/

[FTP]:/pt-br/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/pt-br/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/pt-br/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/pt-br/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
[Detalhes do preço dos sites]:/pt-br/pricing/details/web-sites/#service-ssl
[Autenticar usuários]:/pt-br/documentation/articles/web-sites-authentication-authorization/
[Autenticação simples]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorizar usuários]:/pt-br/documentation/articles/web-sites-authentication-authorization/

[Central de confiabilidade do Azure]:/pt-br/support/trust-center/
[MySQL]:/pt-br/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Banco de Dados SQL Azure]:/pt-br/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[Conexão híbrida]:/pt-br/documentation/articles/web-sites-hybrid-connection-get-started/
[Rede Virtual do Azure]:/pt-br/documentation/articles/web-sites-integrate-with-vnet/

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



<!--HONumber=42-->

<properties urlDisplayName="Resources" pageTitle="Criar uma campanha de marketing digital nos sites do Azure" metaKeywords="" description="Este guia fornece uma visão geral técnica de como usar sites do Azure para criar campanhas de marketing digital. Isso inclui implantação, integração de mídia social, estratégias de dimensionamento e monitoramento." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />

# Criar uma campanha de marketing digital nos sites do Azure
Este guia fornece uma visão geral técnica de como usar sites do Azure para criar campanhas de marketing digital. Uma campanha de marketing digital geralmente é uma entidade de curta duração que serve para orientar uma meta de marketing de curto prazo. Há dois cenários principais a serem considerados. No primeiro cenário, uma empresa de marketing de terceiros cria e gerencia a campanha para os clientes durante o período da promoção. Um segundo cenário envolve a empresa de marketing criando e, em seguida, transferindo a propriedade dos recursos digitais da campanha de marketing para os clientes. Em seguida, o cliente executa e gerencia a campanha de marketing digital por conta própria. 

[Sites da Web do Azure][websitesoverview] é ideal para os dois cenários. Ele fornece a criação rápida, dá suporte a vários idiomas e estruturas, pode ser dimensionado para atender à demanda dos usuários e acomoda muitos sistemas de controle do código-fonte e implantação.Usando o Azure, você também tem acesso a outros serviços do Azure, como os serviços de mídia, o que pode melhorar uma campanha de marketing.

> [WACOM.NOTE]
> Se quiser conhecer os Sites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, onde é possível criar imediatamente um site ASP.NET de vida curta nos Sites do Azure gratuitamente. Nenhum cartão de crédito necessário, nenhum compromisso.

Embora seja possível usar os [serviços de nuvem do Azure][csoverview] ou as [máquinas virtuais do Azure][vmoverview] para hospedar sites, não é a opção ideal para esse cenário, a menos que haja um recurso necessário não fornecer Sites do Azure. Para entender as opções, consulte [Sites do Azure, serviços de nuvem e MVs: Quando usar qual opção?][chooseservice].

As seguintes áreas são abordadas neste guia:

- [Implantar Sites existentes](#deployexisting)
- [Integração com a mídia Social](#socialmedia)
- [Escala com a demanda do usuário](#scale)
- [Integração com outros serviços](#integrate)
- [Monitorar a campanha](#monitor)

> [WACOM.NOTE]
> Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas com o desenvolvimento do site .COM voltado para o público. No entanto, há outros recursos de sites do Azure que você pode usar em sua implementação específica. Para analisar essas capacidades, também consulte outros guias em <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/global-web-presence-solution-overview/">Presença Global na Web</a> e <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/business-application-solution-overview">Aplicativos de Negócio</a>.

##<a name="deployexisting"></a>Implantar sites existentes
No cenário de Presença Global na Web, discutimos várias opções para criar e implantar um novo site.  Se você é novo em sites do Azure, é uma boa ideia [revisar essas informações][scenarioglobalweb].Se você costuma cria campanhas de marketing digitais, é possível que você tenha ativos da web existentes que você personalizou para promoções diferentes.Nesta seção, veremos mais detalhadamente as opções para a implantação de vários tipos de sites do controle do código-fonte.

Se você estiver reutilizando ativos da web para várias finalidades, você deve considerar fortemente um sistema de gerenciamento de controle do código-fonte, se você ainda não usa um. Isso permite que você armazene modelos de soluções da web comuns que podem ser ramificados e personalizados para clientes específicos. Os sites fornecem a opção para sincronizar com vários repositórios de código-fonte diferentes. Na guia **Painel de controle**, selecione o link **Configurar a implantação do controle do código-fonte**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Isso abre uma caixa de diálogo com várias opções de controle do código-fonte. Estas incluem sistemas de controle do código-fonte com todos os recursos, tais como TFS, bem como soluções de implantação simples, como o Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Você pode usar várias técnicas de controle do código-fonte para gerenciar o novo projeto que está baseado em uma linha de base existente. Por exemplo, você pode copiar um repositório de linha de base que foi salva anteriormente para começar a trabalhar no novo projeto, ou você pode criar uma nova ramificação para acompanhar as personalizações para o projeto atual. Para um bom exemplo do uso de ramificações no gerenciamento de implantações distintas do mesmo repositório de controle do código-fonte, consulte [Vários ambientes com sites do Azure][gitstaging].Esta postagem demonstra como usar a ramificação git para gerenciar ambientes de estágios e de produção.

Depois que você conectar o seu Site ao controle do código-fonte, você pode configurar e controlar as implantações do portal. Para obter mais informações sobre como usar o controle do código-fonte com Sites, consulte [Publicação do controle do código-fonte para Sites do Azure][publishingwithgit].

Ao usar os ativos da web existentes, também é importante ter flexibilidade para hospedar muitos tipos diferentes de sites. Na guia **Configurar**, você pode selecionar os suportes .NET e PHP para seu site. 

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Além dessas opções de configuração, os sites automaticamente dão suporte ao Python 2.7 e Node.js. A versão padrão do Node.js é 0.10.5.

Uma vantagem adicional dos Sites do Azure é a velocidade da implantação de sites faseados na Web. Durante o planejamento, criação de protótipos e desenvolvimento inicial de um site, a agência e seus clientes podem examinar versões de trabalho reais do site da campanha antes de entrarem no ar. Depois que o site está pronto para produção, a agência pode gerenciar a implantação de produção do cliente ou fornecer os ativos da web para o cliente implantar e gerenciar.

##<a name="socialmedia"></a>Integração com a mídia Social
A maioria das campanhas de marketing digital fazem uso de sites de mídia social, como Facebook ou Twitter. Um ponto de integração é usar as identidades de mídia social para autenticação. Para obter um exemplo dessa abordagem com um aplicativo ASP.NET, consulte [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL para um Site do Azure][deploysecurewebsite].

No entanto, várias campanhas de marketing digital vão além da autenticação e usam a integração de mídia social como uma parte fundamental da estratégia. Os sites de mídia social normalmente têm uma seção de desenvolvedores que explica diferentes maneiras para que os aplicativos sejam integrados com seus serviços. Os serviços que fornecem uma API REST podem ser usados em praticamente qualquer estrutura da web. No entanto, geralmente há informações específicas para a linguagem de sua preferência. Por exemplo, o Twitter fornece [uma lista de bibliotecas disponíveis que dão à API do Twitter][twitter], inclusive para o .NET, Node.js e PHP.Este é um exemplo e você deve procurar por diretrizes de desenvolvedores semelhantes diretamente de cada site de mídia social que você escolheu como destino.

Para desenvolvedores ASP.NET visando o Facebook, o Visual Studio fornece um modelo para um aplicativo de Facebook MVC 4.  

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Para uma explicação rápida utilizando esse modelo com sites, consulte [Criando um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC e hospedando-os gratuitamente em sites do Azure][fbtutorial]. Para um tutorial mais detalhado e o aplicativo de exemplo, consulte [Aplicativo de aniversário do Facebook ASP.NET MVC][fbbirthdayapp] e [O novo modelo de aplicativo do Facebook e a biblioteca do ASP.NET MVC][fbvstemplate].

Se você for um desenvolvedor ASP.NET, é importante perceber que a integração com a mídia social não é limitada por modelos fornecidos pelo Visual Studio. Um modelo apenas ajuda a tornar o processo mais simples. Mas, conforme mencionado anteriormente, cada site de mídia social normalmente fornece informações sobre outras maneiras de se conectar por meio do .NET e de muitos outros idiomas e estruturas.

##<a name="scale"></a>Escala com a demanda do usuário
A computação em nuvem é útil para cargas de trabalho imprevisíveis. Campanhas de marketing digital se enquadram nesta categoria. É difícil prever a popularidade de um site de marketing de duração relativamente curta, pois depende muito da captura de interesse do usuário e as interações de mídia social relacionadas que direcionam mais tráfego para o site. O Azure fornece várias opções para dimensionar os sites e serviços de nuvem.

- Dimensionamento manual através do [Portal de Gerenciamento Azure][managementportal].
- Dimensionamento de programação através do [API do serviço de gerenciamento][servicemanagementapi] ou do [Script PowerShell][powershell].
- Dimensionamento automático através do recurso de escala automática (visualização).

No portal de gerenciamento, abra a guia **Escala** do seu site. Há várias opções para dimensionamento.A primeira opção determina o modo do site, que é definido como **Livre, ****Compartilhado** ou **Padrão**. 

![DigitalMarketingScale][DigitalMarketingScale]

Opções e recursos de escalabilidade aumentam com cada nível.  Por exemplo, os sites de modo **Livre** não podem ser dimensionados para várias instâncias, mas sites **Compartilhados** podem ser dimensionados para 6 instâncias.  Você também tem a opção de escalar verticalmente, selecionando o modo **Padrão**.Este modo executa seu site em máquinas virtuais dedicadas e oferece opções de dimensionamento da máquina para pequenas, médias e grandes.Depois de decidir o tamanho da máquina virtual, você também tem a opção de ampliar para várias instâncias.No modo **Padrão**, você pode escalar horizontalmente para 10 instâncias. Uma lista completa das diferenças entre cada modo pode ser encontrada nas [diretrizes de preços para Sites da Web][pricing].

Quando você seleciona o modo **Padrão**, você tem a opção adicional de habilitar o recurso de escala automática (visualização).   Isso permite que você dimensione com base na CPU.A porcentagem da **CPU de destino** é uma variedade de utilização do processador que o Azure destina para instâncias do seu site.Se a CPU média for menor que esse destino, o Azure reduz a contagem da instância, pois a mesma carga em menos instâncias resultará em maior utilização nas instâncias restantes.No entanto, ele não pode reduzir a contagem de instâncias abaixo do valor mínimo da **Contagem de instâncias**. Da mesma forma, se a CPU média for maior do que a **CPU de destino**, o Azure aumenta o número de instâncias.A mesma carga espalhada em computadores adicionais tem o efeito de reduzir a utilização da CPU em cada computador.O número de instâncias adicionado é limitado pelo valor máximo da **Contagem de instâncias**.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

O dimensionamento automático torna o uso dos recursos mais eficiente. Por exemplo, é possível que a campanha de marketing digital seja mais ativa durante as noites e fins de semana. Isto também trata efetivamente um cenário onde a popularidade de uma campanha aumenta de forma inesperada. O dimensionamento automático ajuda a segurar o aumento da carga de forma eficiente e, em seguida, diminui as instâncias (e custo), quando a carga diminui. 

Para obter mais informações sobre os sites de dimensionamento, consulte [Como dimensionar sites][scalewebsite]. Este assunto também está intimamente ligado com o monitoramento.Para obter mais informações, consulte a seção deste guia no [monitoramento da sua campanha](#monitor).

> [WACOM.NOTE]
> Para aplicativos da web que optam por usar serviços de nuvem e funções Web, há uma opção adicional para dimensionar baseada no tamanho de itens em uma fila. Em um serviço de nuvem, as funções que processam filas de backend são um padrão de arquitetura comum. Para mais informações sobre ajuste de escala de serviço de nuvem, consulte <a href="http://www.windowsazure.com/pt-br/manage/services/cloud-services/how-to-scale-a-cloud-service/">Como dimensionar um serviço de nuvem</a>.

##<a name="integrate"></a>Integração com outros serviços
Um site de marketing digital geralmente incorporará mídia avançada, tal como straming de vídeo. Hospedar esses sites no Azure fornece uma estreita integração aos serviços Azure relacionados. Por exemplo, você pode usar os serviços de mídia do Azure para codificar e transmitir vídeo para o seu site. Para obter mais informações sobre os serviços de mídia, consulte [Introdução aos cenários e conceitos de serviços de mídia do Azure][mediaservices].

Outros serviços Azure podem ser usados para criar um aplicativo mais robusto.  Por exemplo, os Sites podem usar o cache distribuído fornecido pelo novo [Serviço de cache do Azure (visualização)][caching]. Ou você pode usar os serviços de armazenamento do Azure para armazenar recursos e dados de aplicativos.Por exemplo, elementos gráficos, vídeos e outros arquivos grandes podem ser permanentemente armazenados em bolhas.Os serviços de banco de dados, como MySQL e banco de dados SQL do Azure, também estão disponíveis para atender aos requisitos dos dados relacionais.

##<a name="monitor"></a>Monitorar a campanha
A guia **Monitorar** contém métricas que podem ajudá-lo a avaliar o sucesso e o desempenho do seu site de marketing digital.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

Por exemplo, você pode examinar padrões de uso e níveis, observando métricas como **Tempo da CPU, ****Solicitações** e **Dados**. Todas essas métricas aumentarão conforme a campanha de marketing ficar mais popular. Informações sobre o uso podem ser usadas para decidir quando escalar horizontalmente ou verticalmente.Para obter mais informações, consulte [Como monitorar sites (a página pode estar em inglês)][monitoring].

Para os modos **Livre** e **Compartilhado**, você também deve estar ciente das cotas de recursos.  Na guia **Painel**, o portal mostra as estatísticas de uso atual das várias cotas e quando essas cotas serão redefinidas.Essas estatísticas de uso variam de acordo com o modo selecionado.A captura de tela a seguir mostra as estatísticas exibidas para o modo **Livre**. No modo **Compartilhado**, você não tem uma cota para os **Dados**. No modo **Padrão**, ele exibe apenas o **Armazenamento do sistema de arquivos** e **Tamanho**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Se você achar que você está chegando perto do esgotamento dessas cotas, considere mover de **Livre** para **Compartilhado** ou de **Compartilhado** para **Padrão**. No modo **Padrão**, você dedicou recursos em uma ou mais máquinas virtuais pequenas, médias ou grandes. 

Além de usar o Portal de Gerenciamento para essa informação, existem diversas ferramentas de terceiros que fornecem dados de monitoramento avançados para seus sites. Por exemplo, você pode instalar um complemento de Nova Relíquia do armazenamento do Azure. Para uma explicação sobre como usar a Nova Relíquia para monitoramento, consulte [Gerenciamento de desempenho de aplicativo da Nova Relíquia no Azure][newrelic]. 

Por fim, sites do modo padrão podem habilitar o monitoramento e alerta de pontos de extremidade. Um monitor de ponto de extremidade regularmente tenta acessar seu site e, em seguida, emite relatórios sobre o tempo de resposta. Os alertas fornecem notificações por email se o tempo de resposta excede um limite especificado. Para obter mais informações, consulte a seção de monitoramento do cenário [Presença Global na Web][scenarioglobalweb] e o tópico [Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure][receivealerts].

##<a name="summary"></a>Resumo
Os sites do Azure são uma boa opção para conteúdo da web reutilizável que é personalizado para campanhas de marketing individual. Os sites dão suporte várias linguagens populares, estruturas e sistemas de controle do código-fonte, facilitando a migração para esses ativos e fluxos de trabalho para a nuvem. O modelo de aplicativo para Facebook ASP.NET torna mais fácil criar aplicativos do Facebook, mas você pode usar praticamente qualquer integração de mídia social de terceiros que dá suporte a interfaces da web. Os serviços de mídia do Azure e outros serviços relacionados no Azure fornecem ferramentas adicionais para construir um site de campanha bem projetado. E as várias opções de dimensionamento automático e manual são úteis para lidar com a demanda do usuário, que pode ser difícil de prever. Para mais informações, consulte os seguintes artigos técnicos.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Área</th>
   <th align="left" valign="top">Recursos</th>
</tr>
<tr>
   <td valign="middle"><strong>Plano</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/choose-web-app-service">Sites do Azure, serviços de nuvem e máquinas virtuais: Quando usar qual opção?</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Criar</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-create-websites/">Como criar e implantar um site</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Implantar</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-deploy/">Como implantar um site do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/common-tasks/publishing-with-git/">Publicando do Controle do código-fonte para sites do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Mídia social</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/web-site-with-sql-database/">Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Criar um aplicativo para Facebook usando Modelos do Facebook MVC ASP.NET</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Modelo de aplicativo para Facebook e biblioteca para MVC ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Escala</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-scale-websites/">Como dimensionar sites</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Mídia avançada</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223282.aspx">Introdução aos conceitos e cenários dos Serviços de Mídia do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoramento</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/">Como monitorar Sites</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure</a></td>
</tr>
</table>

  [websitesoverview]:/pt-br/documentation/services/web-sites/
  [csoverview]:/pt-br/documentation/services/cloud-services/
  [vmoverview]:/pt-br/documentation/services/virtual-machines/
  [chooseservice]:/pt-br/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/pt-br/manage/services/web-sites/global-web-presence-solution-overview/
  
  
  [publishingwithgit]:/pt-br/develop/net/common-tasks/publishing-with-git/
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [deploysecurewebsite]:/pt-br/develop/net/tutorials/web-site-with-sql-database/
  [twitter]:https://dev.twitter.com/docs/twitter-libraries#dotnet
  [fbtutorial]:http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [fbbirthdayapp]:http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [fbvstemplate]:http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [managementportal]:http://manage.windowsazure.com/
  [servicemanagementapi]:http://msdn.microsoft.com/pt-br/library/windowsazure/ee460799.aspx
  [powershell]:http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841.aspx
  [pricing]:https://www.windowsazure.com/pt-br/pricing/details/web-sites/
  [scalewebsite]:/pt-br/manage/services/web-sites/how-to-scale-websites/
  
  [mediaservices]:http://msdn.microsoft.com/pt-br/library/windowsazure/dn223282.aspx
  [caching]:http://msdn.microsoft.com/pt-br/library/windowsazure/dn386094.aspx
  [monitoring]:/pt-br/manage/services/web-sites/how-to-monitor-websites/
  [newrelic]:/pt-br/develop/net/how-to-guides/new-relic/
  [receivealerts]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  
  
  
   [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  
  
  
  
  

<!--HONumber=35.1-->

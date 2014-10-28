<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Criar uma campanha de marketing digital nos sites do Azure

Este guia fornece uma visão geral técnica de como usar sites do Azure para criar campanhas de marketing digital. Uma campanha de marketing digital geralmente é uma entidade de curta duração que serve para orientar uma meta de marketing de curto prazo. Há dois cenários principais a serem considerados. No primeiro cenário, uma empresa de marketing de terceiros cria e gerencia a campanha para os clientes durante o período da promoção. Um segundo cenário envolve a empresa de marketing criando e, em seguida, transferindo a propriedade dos recursos digitais da campanha de marketing para os clientes. Em seguida, o cliente executa e gerencia a campanha de marketing digital por conta própria.

[Sites da Web do Azure][Sites da Web do Azure] é ideal para os dois cenários. Ele fornece a criação rápida, oferece suporte a vários idiomas e estruturas, pode ser dimensionado para atender à demanda dos usuários e acomoda muitos sistemas de controle de origem e implantação. Usando o Azure, você também tem acesso a outros serviços do Azure, como os serviços de mídia, o que pode melhorar uma campanha de marketing.

Embora seja possível usar os [serviços de nuvem do Azure][serviços de nuvem do Azure] ou as [máquinas virtuais do Azure][máquinas virtuais do Azure] para hospedar sites, não é a opção ideal para esse cenário, a menos que haja um recurso necessário não fornecer Sites do Azure. Para entender as opções, consulte [Sites do Azure, serviços de nuvem e MVs:Quando usar qual opção?][Sites do Azure, serviços de nuvem e MVs:Quando usar qual opção?].

As seguintes áreas são abordadas neste guia:

-   [Implantar Sites existentes][Implantar Sites existentes]
-   [Integração com a mídia Social][Integração com a mídia Social]
-   [Escala com a demanda do usuário][Escala com a demanda do usuário]
-   [Integração com outros serviços][Integração com outros serviços]
-   [Monitorar a campanha][Monitorar a campanha]

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Este guia apresenta algumas das &aacute;reas e tarefas mais comuns que est&atilde;o alinhadas com o desenvolvimento do site .COM voltado para o p&uacute;blico. No entanto, h&aacute; outros recursos de sites do Azure que voc&ecirc; pode usar em sua implementa&ccedil;&atilde;o espec&iacute;fica. Para rever esses recursos, consulte tamb&eacute;m as outras guias em <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/global-web-presence-solution-overview/">Presen&ccedil;a Global da Web</a> e <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/business-application-solution-overview">Aplicativos de Neg&oacute;cios</a>.</p>
</div>

## <a name="deployexisting"></a>Implantar sites existentes

No cenário de Presença Global na Web, discutimos várias opções para criar e implantar um novo site. Se você é novo em sites do Azure, é uma boa ideia [revisar essas informações][revisar essas informações]. Se você costuma cria campanhas de marketing digitais, é possível que você tenha ativos da web existentes que você personalizou para promoções diferentes. Nesta seção, veremos mais detalhadamente as opções para a implantação de vários tipos de sites do controle de origem.

Se você estiver reutilizando ativos da web para várias finalidades, você deve considerar fortemente um sistema de gerenciamento de controle de origem, se você ainda não usa um. Isso permite que você armazene modelos de soluções da web comuns que podem ser ramificados e personalizados para clientes específicos. Os sites fornecem a opção para sincronizar com vários repositórios de código fonte diferentes. Na guia **Painel de controle**, selecione o link **Configurar a implantação do controle de origem**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Isso abre uma caixa de diálogo com várias opções de controle de código fonte. Estas incluem sistemas de controle de origem com todos os recursos, tais como TFS, bem como soluções de implantação simples, como o Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Você pode usar várias técnicas de controle de origem para gerenciar o novo projeto que está baseado em uma linha de base existente. Por exemplo, você pode copiar um repositório de linha de base que foi salva anteriormente para começar a trabalhar no novo projeto, ou você pode criar uma nova ramificação para acompanhar as personalizações para o projeto atual. Para um bom exemplo do uso de ramificações no gerenciamento de implantações distintas do mesmo repositório de controle de origem, consulte [Vários ambientes com sites do Azure][Vários ambientes com sites do Azure]. Esta postagem demonstra como usar a ramificação git para gerenciar ambientes de estágios e de produção.

Depois que você conectar o seu Site ao controle de origem, você pode configurar e controlar as implantações do portal. Para obter mais informações sobre como usar o controle de origem com Sites, consulte [Publicação do controle de origem para Sites do Azure][Publicação do controle de origem para Sites do Azure].

Ao usar os ativos da web existentes, também é importante ter flexibilidade para hospedar muitos tipos diferentes de sites. Na guia **Configurar**, você pode selecionar os suportes .NET e PHP para seu site.

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Além dessas opções de configuração, os sites automaticamente oferecem suporte ao Python 2.7 e Node.js. A versão padrão do Node.js é 0.10.5.

Uma vantagem adicional dos Sites do Azure é a velocidade da implantação de sites faseados na Web. Durante o planejamento, criação de protótipos e desenvolvimento inicial de um site, a agência e seus clientes podem examinar versões de trabalho reais do site da campanha antes de entrarem no ar. Depois que o site está pronto para produção, a agência pode gerenciar a implantação de produção do cliente ou fornecer os ativos da web para o cliente implantar e gerenciar.

## <a name="socialmedia"></a>Integração com a mídia Social

A maioria das campanhas de marketing digital fazem uso de sites de mídia social, como Facebook ou Twitter. Um ponto de integração é usar as identidades de mídia social para autenticação. Para obter um exemplo dessa abordagem com um aplicativo ASP.NET, consulte [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL para um Site do Azure][Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL para um Site do Azure].

No entanto, várias campanhas de marketing digital vão além da autenticação e usam a integração de mídia social como uma parte fundamental da estratégia. Os sites de mídia social normalmente têm uma seção de desenvolvedores que explica diferentes maneiras para que os aplicativos sejam integrados com seus serviços. Os serviços que fornecem uma API REST podem ser usados em praticamente qualquer estrutura da web. No entanto, geralmente há informações específicas para a linguagem de sua preferência. Por exemplo, o Twitter fornece [uma lista de bibliotecas disponíveis que suportam a API do Twitter][uma lista de bibliotecas disponíveis que suportam a API do Twitter], inclusive para o .NET, Node.js e PHP. Este é um exemplo e você deve procurar por diretrizes de desenvolvedores semelhantes diretamente de cada site de mídia social que você escolheu como destino.

Para desenvolvedores ASP.NET visando o Facebook, o Visual Studio fornece um modelo para um aplicativo de Facebook MVC 4.

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Para uma explicação rápida utilizando esse modelo com sites, consulte [Criando um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC e hospedando-os gratuitamente em sites do Azure][Criando um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC e hospedando-os gratuitamente em sites do Azure]. Para um tutorial mais detalhado e o aplicativo de exemplo, consulte [Aplicativo de aniversário do Facebook ASP.NET MVC][Aplicativo de aniversário do Facebook ASP.NET MVC] e [O novo modelo de aplicativo do Facebook e a biblioteca do ASP.NET MVC][O novo modelo de aplicativo do Facebook e a biblioteca do ASP.NET MVC].

Se você for um desenvolvedor ASP.NET, é importante perceber que a integração com a mídia social não é limitada por modelos fornecidos pelo Visual Studio. Um modelo apenas ajuda a tornar o processo mais simples. Mas, conforme mencionado anteriormente, cada site de mídia social normalmente fornece informações sobre outras maneiras de se conectar a partir do .NET e de muitos outros idiomas e estruturas.

## <a name="scale"></a>Escala com a demanda do usuário

A computação em nuvem é útil para cargas de trabalho imprevisíveis. Campanhas de marketing digital se enquadram nesta categoria. É difícil prever a popularidade de um site de marketing de duração relativamente curta, pois depende muito da captura de interesse do usuário e as interações de mídia social relacionadas que direcionam mais tráfego para o site. O Azure fornece várias opções para dimensionar os sites e serviços na nuvem.

-   Dimensionamento manual através do [Portal de Gerenciamento Azure][Portal de Gerenciamento Azure].
-   Dimensionamento de programação através do [API do serviço de gerenciamento][API do serviço de gerenciamento] ou do [Script PowerShell][Script PowerShell].
-   Dimensionamento automático através do recurso de escala automática (visualização).

No portal de gerenciamento, abra a guia **Escala** do seu site. Há várias opções para dimensionamento. A primeira opção determina o modo do site, que é definido como **Livre**, **Compartilhado** ou **Padrão**.

![DigitalMarketingScale][DigitalMarketingScale]

Opções e recursos de escalabilidade aumentam com cada nível. Por exemplo, os sites de modo **Livre** não podem ser dimensionados para várias instâncias, mas sites **Compartilhados** podem ser dimensionados para 6 instâncias. Você também tem a opção de ampliar, selecionando o modo **Padrão**. Este modo executa seu site em máquinas virtuais dedicadas e oferece opções de dimensionamento da máquina para pequenas, médias e grandes. Depois de decidir o tamanho da máquina virtual, você também tem a opção de ampliar para várias instâncias. No modo **Padrão**, você pode dimensionar para 10 instâncias. Uma lista completa das diferenças entre cada modo pode ser encontrada nas [diretrizes de preços para Sites da Web][diretrizes de preços para Sites da Web].

Quando você seleciona o modo **Padrão**, você tem a opção adicional de habilitar o recurso de escala automática (visualização). Isso permite que você dimensione com base na CPU. A porcentagem da **CPU de destino** é uma variedade de utilização do processador que o Azure destina para instâncias do seu site. Se a CPU média for menor que esse destino, o Azure reduz a contagem da instância, pois a mesma carga em menos instâncias resultará em maior utilização nas instâncias restantes. No entanto, ele não pode reduzir a contagem de instâncias abaixo do valor mínimo da **Contagem de instâncias**. Da mesma forma, se a CPU média for maior do que a **CPU de destino**, o Azure aumenta o número de instâncias. A mesma carga espalhada em máquinas adicionais tem o efeito de reduzir a utilização da CPU em cada máquina. O número de instâncias adicionado é limitado pelo valor máximo da **Contagem de instâncias**.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

O dimensionamento automático torna o uso dos recursos mais eficiente. Por exemplo, é possível que a campanha de marketing digital seja mais ativa durante as noites e fins de semana. Isto também trata efetivamente um cenário onde a popularidade de uma campanha aumenta de forma inesperada. O dimensionamento automático ajuda a segurar o aumento da carga de forma eficiente e, em seguida, diminui as instâncias (e custo), quando a carga diminui.

Para obter mais informações sobre os sites de dimensionamento, consulte [Como dimensionar sites][Como dimensionar sites]. Este assunto também está intimamente ligado com o monitoramento. Para obter mais informações, consulte a seção deste guia no [monitoramento da sua campanha][Monitorar a campanha].

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Para aplicativos da web que optam por usar servi&ccedil;os de nuvem e fun&ccedil;&otilde;es Web, h&aacute; uma op&ccedil;&atilde;o adicional para dimensionar baseada no comprimento de itens em uma fila. Em um servi&ccedil;o de nuvem, as fun&ccedil;&otilde;es que processam filas de backend s&atilde;o um padr&atilde;o de arquitetura comum. Para obter mais informa&ccedil;&otilde;es sobre o dimensionamento de servi&ccedil;os de nuvem, consulte <a href="http://www.windowsazure.com/pt-br/manage/services/cloud-services/how-to-scale-a-cloud-service/">Como dimensionar um servi&ccedil;o de nuvem</a>.</p>
</div>

## <a name="integrate"></a>Integração com outros serviços

Um site de marketing digital geralmente incorporará mídia avançada, tal como straming de vídeo. Hospedar esses sites no Azure fornece uma estreita integração aos serviços Azure relacionados. Por exemplo, você pode usar os serviços de mídia do Azure para codificar e transmitir vídeo para o seu site. Para obter mais informações sobre os serviços de mídia, consulte [Introdução aos cenários e conceitos de serviços de mídia do Azure][Introdução aos cenários e conceitos de serviços de mídia do Azure].

Outros serviços Azure podem ser usados para criar um aplicativo mais robusto. Por exemplo, os Sites podem usar o cache distribuído fornecido pelo novo [Serviço de cache do Azure (visualização)][Serviço de cache do Azure (visualização)]. Ou você pode usar os serviços de armazenamento do Azure para armazenar recursos e dados de aplicativos. Por exemplo, elementos gráficos, vídeos e outros arquivos grandes podem ser permanentemente armazenados em bolhas. Os serviços de banco de dados, como MySQL e banco de dados SQL do Azure, também estão disponíveis para atender aos requisitos dos dados relacionais.

## <a name="monitor"></a>Monitorar a campanha

A guia **Monitorar** contém métricas que podem ajudá-lo a avaliar o sucesso e o desempenho do seu site de marketing digital.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

Por exemplo, você pode examinar padrões de uso e níveis, observando métricas como **Tempo da CPU**, **Solicitações** e **Dados**. Todas essas métricas aumentarão conforme a campanha de marketing ficar mais popular. Informações sobre o uso podem ser usadas para decidir quando expandir para fora ou para cima. Para obter mais informações, consulte [Como monitorar sites (a página pode estar em inglês)][Como monitorar sites (a página pode estar em inglês)].

Para os modos **Livre** e **Compartilhado**, você também deve estar ciente das cotas de recursos. Na guia **Painel**, o portal mostra as estatísticas de uso atual das várias cotas e quando essas cotas serão redefinidas. Essas estatísticas de uso variam de acordo com o modo selecionado. A captura de tela a seguir mostra as estatísticas exibidas para o modo **Livre**. No modo **Compartilhado**, você não tem uma cota para os **Dados**. No modo **Padrão**, ele exibe apenas o **Armazenamento do sistema de arquivos** e **Tamanho**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Se você achar que você está chegando perto do esgotamento dessas cotas, considere mover de **Livre** para **Compartilhado** ou de **Compartilhado** para **Padrão**. No modo **Padrão**, você dedicou recursos em uma ou mais máquinas virtuais pequenas, médias ou grandes.

Além de usar o Portal de Gerenciamento para essa informação, existem diversas ferramentas de terceiros que fornecem dados de monitoramento avançados para seus sites. Por exemplo, você pode instalar um complemento de Nova Relíquia do armazenamento do Azure. Para uma explicação sobre como usar a Nova Relíquia para monitoramento, consulte [Gerenciamento de desempenho de aplicativo da Nova Relíquia no Azure][Gerenciamento de desempenho de aplicativo da Nova Relíquia no Azure].

Por fim, sites do modo padrão podem habilitar o monitoramento e alerta de pontos de extremidade. Um monitor de ponto de extremidade regularmente tenta acessar seu site e, em seguida, emite relatórios sobre o tempo de resposta. Os alertas fornecem notificações por e-mail se o tempo de resposta excede um limite especificado. Para obter mais informações, consulte a seção de monitoramento do cenário [Presença Global na Web][revisar essas informações] e o tópico [Como receber notificações de alerta e gerenciar regras de alerta no Azure][Como receber notificações de alerta e gerenciar regras de alerta no Azure].

## <a name="summary"></a>Resumo

Os sites do Azure são uma boa opção para conteúdo da web reutilizável que é personalizado para campanhas de marketing individual. Os sites suportam várias linguagens populares, estruturas e sistemas de controle de origem, facilitando a migração para esses ativos e fluxos de trabalho para a nuvem. O modelo de aplicativo para Facebook ASP.NET torna mais fácil criar aplicativos do Facebook, mas você pode usar praticamente qualquer integração de mídia social de terceiros que suporta interfaces da web. Os serviços de mídia do Azure e outros serviços relacionados no Azure fornecem ferramentas adicionais para construir um site de campanha bem projetado. E as várias opções de dimensionamento automático e manual são úteis para lidar com a demanda do usuário, que pode ser difícil de prever. Para mais informações, consulte os seguintes artigos técnicos.

| Área               | Recursos                                                                                                                                                                                             |
|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Plano**          | - [Sites do Azure, serviços de nuvem e máquinas virtuais: Quando usar qual opção?][Sites do Azure, serviços de nuvem e máquinas virtuais: Quando usar qual opção?]                                                                                                                 |
| **Criar**          | - [Como criar e implantar um site][Como criar e implantar um site]                                                                                                                                                                 |
| **Implantar**      | - [Como implantar um site do Azure][Como implantar um site do Azure]                                                                                                                                                                
                      - [Publicando do Controle do código-fonte para sites do Azure.][Publicando do Controle do código-fonte para sites do Azure.]                                                                                                                                     |
| **Mídia social**   | - [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL][Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL]                                                                                                    
                      - [Criar um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC][Criando um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC e hospedando-os gratuitamente em sites do Azure]  
                      - [Biblioteca e modelo de aplicativo do Facebook ASP.NET MVC][O novo modelo de aplicativo do Facebook e a biblioteca do ASP.NET MVC]                                                                  |
| **Escala**         | - [Como dimensionar sites][1]                                                                                                                                                                        |
| **Mídia avançada** | - [Introdução aos conceitos e cenários dos Serviços de Mídia do Azure][Introdução aos cenários e conceitos de serviços de mídia do Azure]                                                            |
| **Monitoramento**  | - [Como monitorar Sites][Como monitorar Sites]                                                                                                                                                                           
                      - [como: receber notificações de alerta e gerenciar regras de alerta no Azure][Como receber notificações de alerta e gerenciar regras de alerta no Azure]                                             |

  [Sites da Web do Azure]: /pt-br/documentation/services/web-sites/
  [serviços de nuvem do Azure]: /pt-br/documentation/services/cloud-services/
  [máquinas virtuais do Azure]: /pt-br/documentation/services/virtual-machines/
  [Sites do Azure, serviços de nuvem e MVs:Quando usar qual opção?]: /pt-br/manage/services/web-sites/choose-web-app-service
  [Implantar Sites existentes]: #deployexisting
  [Integração com a mídia Social]: #socialmedia
  [Escala com a demanda do usuário]: #scale
  [Integração com outros serviços]: #integrate
  [Monitorar a campanha]: #monitor
  [Presença Global da Web]: http://www.windowsazure.com/pt-br/manage/services/web-sites/global-web-presence-solution-overview/
  [Aplicativos de Negócios]: http://www.windowsazure.com/pt-br/manage/services/web-sites/business-application-solution-overview
  [revisar essas informações]: /pt-br/manage/services/web-sites/global-web-presence-solution-overview/
  [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [Vários ambientes com sites do Azure]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [Publicação do controle de origem para Sites do Azure]: /pt-br/develop/net/common-tasks/publishing-with-git/
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL para um Site do Azure]: /pt-br/develop/net/tutorials/web-site-with-sql-database/
  [uma lista de bibliotecas disponíveis que suportam a API do Twitter]: https://dev.twitter.com/docs/twitter-libraries#dotnet
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [Criando um aplicativo do Facebook usando modelos do Facebook ASP.NET MVC e hospedando-os gratuitamente em sites do Azure]: http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [Aplicativo de aniversário do Facebook ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [O novo modelo de aplicativo do Facebook e a biblioteca do ASP.NET MVC]: http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [Portal de Gerenciamento Azure]: http://manage.windowsazure.com/
  [API do serviço de gerenciamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee460799.aspx
  [Script PowerShell]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841.aspx
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [diretrizes de preços para Sites da Web]: https://www.windowsazure.com/pt-br/pricing/details/web-sites/
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [Como dimensionar sites]: /pt-br/manage/services/web-sites/how-to-scale-websites/
  [Como dimensionar um serviço de nuvem]: http://www.windowsazure.com/pt-br/manage/services/cloud-services/how-to-scale-a-cloud-service/
  [Introdução aos cenários e conceitos de serviços de mídia do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223282.aspx
  [Serviço de cache do Azure (visualização)]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn386094.aspx
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [Como monitorar sites (a página pode estar em inglês)]: /pt-br/manage/services/web-sites/how-to-monitor-websites/
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  [Gerenciamento de desempenho de aplicativo da Nova Relíquia no Azure]: /pt-br/develop/net/how-to-guides/new-relic/
  [Como receber notificações de alerta e gerenciar regras de alerta no Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  [Sites do Azure, serviços de nuvem e máquinas virtuais: Quando usar qual opção?]: http://www.windowsazure.com/pt-br/manage/services/web-sites/choose-web-app-service
  [Como criar e implantar um site]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-create-websites/
  [Como implantar um site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-deploy/
  [Publicando do Controle do código-fonte para sites do Azure.]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/publishing-with-git/
  [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL]: http://www.windowsazure.com/pt-br/develop/net/tutorials/web-site-with-sql-database/
  [1]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-scale-websites/
  [Como monitorar Sites]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/

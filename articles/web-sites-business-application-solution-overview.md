<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Criar um aplicativo de linha de negócios em Sites do Azure" metaKeywords="Web Sites" description="Este guia fornece uma visão geral técnica de como usar sites do Azure para criar aplicativos de linha de negócios da intranet. Isso inclui estratégias de autenticação, retransmissão do barramento de serviço e monitoramento." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />



# Criar um aplicativo de linha de negócios em Sites do Azure

Este guia fornece uma visão geral técnica de como usar sites do Azure para criar aplicativos de linha de negócios. Para os fins deste documento, presume-se que esses aplicativos são aplicativos de intranet que devem ser protegidos para uso comercial interno. Há duas características distintas de aplicativos de negócios. Esses aplicativos requerem autenticação, normalmente em um diretório corporativo. E eles normalmente exigem algum acesso ou integração com serviços e dados locais. Este guia concentra-se na criação de aplicativos de negócios em [Sites do Azure][websitesoverview]. No entanto, há situações onde os [Serviços de nuvem do Azure][csoverview] ou as [Máquinas virtuais do Azure][vmoverview] seriam a melhor opção para suas necessidades. É importante analisar as diferenças entre essas opções no tópico [Sites do Azure, serviços de nuvem e MVs: Quando usar qual opção?][chooseservice]. 

As seguintes áreas são abordadas neste guia:

- [Considere os benefícios](#benefits)
- [Escolha uma estratégia de autenticação](#authentication)
- [Crie um site do Azure que dê suporte à autenticação](#createintranetsite)
- [Use o Barramento de Serviço para integrar recursos locais](#servicebusrelay)
- [Monitore seu aplicativo](#monitor)

<div class="dev-callout">
<strong>Observação</strong>
<p>Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas com o desenvolvimento do site .COM voltado para o público. No entanto, há outros recursos de sites do Azure que você pode usar em sua implementação específica. Para analisar essas capacidades, também consulte outros guias em <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/global-web-presence-solution-overview/">Presença Global na Web</a> e <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campanhas de Marketing Digital</a>.</p>
</div>

##<a name="benefits"></a>Considere os benefícios
Como os aplicativos de linha de negócios tipicamente segmentam os usuários corporativos, você deve considerar os motivos para usar a nuvem versus a infraestrutura e os recursos corporativos locais. Em primeiro lugar, há alguns dos benefícios típicos de migração para a nuvem, como a capacidade de escalar e reduzir verticalmente com cargas de trabalho dinâmicas. Por exemplo, considere um aplicativo que lida com as revisões de desempenho anual. Para a maioria do ano, esse tipo de aplicativo usaria muito pouco tráfego. Mas durante o período de revisão, o tráfego será picos de altos níveis para uma grande empresa. O Azure fornece opções de escala que permitem que a empresa seja escalada horizontalmente para lidar com a carga durante o período de análise de tráfego intenso, poupando dinheiro através da escala de volta para o restante do ano. Outro benefício da nuvem é a capacidade de se concentrar mais no desenvolvimento de aplicativos e menos na aquisição de infraestrutura e gerenciamento.

Além dessas vantagens padrão, colocar um aplicativo de negócios na nuvem dá maior suporte para funcionários e parceiros usarem o aplicativo de qualquer lugar. Os usuários não precisam estar conectados à rede corporativa para poder usar o aplicativo, e o grupo de TI evita soluções complexas de proxy reverso. Há várias opções de autenticação para certificar-se de que o acesso aos aplicativos da empresa estão protegidos. Essas opções são discutidas nas seções a seguir deste guia.

##<a name="authentication"></a>Escolha uma estratégia de autenticação
Para o cenário de aplicativo de negócios, sua estratégia de autenticação é uma das decisões mais importantes. Há várias opções:

- Use o [Serviço Active Directory do Azure][adoverview]. Você pode usá-lo como um diretório independente, ou você pode sincronizá-lo com um Active Directory local. Os aplicativos então interagem com o Active Directory do Azure para autenticar usuários.Para obter uma visão geral dessa abordagem, consulte [Usando o Active Directory do Azure][adusing].
- Use máquinas virtuais do Azure e rede virtual para instalar o Active Directory. Isso dá a você a opção de estender uma instalação do Active Directory local para a nuvem. Você também pode optar por usar Serviços de Federação do Active Directory (ADFS) para agrupar os pedidos de identidade para o local AD. Em seguida, a autenticação para o aplicativo do Azure passa pelo ADFS para o Active Directory local. Para obter mais informações sobre essa abordagem, consulte [Executando o Active Directory do Windows Server em MVs][adwithvm] e [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure][addeployguidelines]. 
- Use um serviço intermediário, como o [Serviço de Controle de Acesso do Azure][acs2] (ACS), para usar vários serviços de identidade para autenticar usuários. Isso proporciona uma abstração para autenticar através do Active Directory ou através de um provedor de identidade diferente.Para obter mais informações, consulte [Usando o Controle de Acesso do Active Directory do Azure][acsusing].

Para esse cenário de aplicativo de negócios, o primeiro cenário usando o Active Directory do Azure fornece o caminho mais rápido para implementar uma estratégia de autenticação para o seu aplicativo. O restante deste guia se concentra no Active Directory do Azure. No entanto, dependendo dos seus requisitos de negócios, você pode achar que uma das duas soluções é a melhor opção. Por exemplo, se você não tem permissão para sincronizar informações de identidade para a nuvem, a solução ADFS pode ser a melhor opção. Ou se você tem de dar suporte a outros provedores de identidade, como Facebook, uma solução ACS se encaixaria melhor.

Antes de começar a configurar um novo Active Directory do Azure, você deve observar que os serviços existentes, como o Office 365 ou o Windows Intune, já usam o Active Directory do Azure. Nesse caso, você pode associar sua assinatura existente com a sua assinatura do Azure. Para obter mais detalhes, consulte [O que é um locatário AD Azure?][adtenant].

Se você ainda não tem um serviço que já usa o Active Directory do Azure, você pode criar um novo diretório no Portal de Gerenciamento. Use a seção do **Active Directory** do Portal de Gerenciamento para criar um novo diretório.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

Uma vez criado, você tem a opção de criar e gerenciar usuários, aplicativos integrados e domínios.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Para uma explicação completa dessas etapas, consulte [Adicionando registro único para seu aplicativo Web usando o AD do Azure][adsso]. Se você estiver usando este novo diretório como um recurso autônomo, a próxima etapa será desenvolver aplicativos que se integram com o diretório. No entanto, se você tiver identidades do Active Directory local, você geralmente desejará sincronizar com o novo Active Directory do Azure.Para obter mais informações sobre como fazer isso, consulte a [Integração de diretórios][dirintegration].

Depois que o diretório é criado e preenchido, você deve criar aplicativos da web que exigem autenticação e então integrá-los com o diretório. Essas etapas são cobertas nas duas seções seguintes.

##<a name="createintranetsite"></a>Crie um site do Azure que dê suporte à autenticação
No cenário de Presença Global na Web, discutimos várias opções para criar e implantar um novo site.  Se você é novo em sites do Azure, é uma boa ideia [revisar essas informações][scenarioglobalweb].Um aplicativo do ASP.NET no Visual Studio é uma opção comum para um aplicativo Web de intranet que usa autenticação Windows.Um dos motivos para isso é a integração e o suporte para esse cenário fornecido pelo ASP.NET e Visual Studio.

Por exemplo, ao criar um projeto ASP.NET MVC 4 no Visual Studio, você tem a opção de criar um **Aplicativo de Intranet** na caixa de diálogo de criação de projeto.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

Isso altera as configurações do projeto para dar suporte à autenticação do Windows. Especificamente, o arquivo web.config tem o atributo do **modo** do elemento de **autenticação** definido como **Windows**.Se você criar um projeto diferente do ASP.NET, como um projeto de formulários da Web, ou se você estiver trabalhando com um projeto existente, você deve fazer essa alteração manualmente.

Para um projeto do MVC, você também deve alterar dois valores na janela de propriedades do projeto. Defina a **Autenticação Windows** para **Habilitada** e a **Autenticação anônima** para **Desabilitada**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Para autenticar com o Active Directory do Azure, você deve registrar este aplicativo com o diretório e, em seguida, você deve modificar essa configuração de aplicativo para se conectar. Isso pode ser feito de duas formas no Visual Studio:

- [Ferramenta de acesso e identidade para o Visual Studio](#identityandaccessforvs)
- [Ferramentas do Microsoft ASP.NET para Active Directory do Azure](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Ferramenta de acesso e identidade para o Visual Studio:
Um método é usar a [Ferramenta de Identidade e Acesso][identityandaccess] (que você pode baixar e instalar).  Esta ferramenta integra-se com o Visual Studio no menu de contexto do projeto. As seguintes instruções e capturas de tela são do Visual Studio 2012.Clique com o botão direito no projeto e selecione **Identidade e Acesso**.Há três coisas que devem ser configuradas.Na guia **Provedores**, você deve fornecer o **caminho para o documento de metadados STS** e o **URI de ID do Aplicativo** (para obter esses valores, consulte a seção em [Registrando aplicativos no Active Directory do Azure](#registerwaadapp)).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

A alteração da configuração final que deve ser feita é na guia **Configuração** da caixa de diálogo **Acesso e identidade**. Você deve selecionar a caixa de seleção **Habilitar cookies de farm da web**. Para uma explicação detalhada dessas etapas, consulte [Adicionando registro único para seu aplicativo Web usando o AD do Azure][adsso].

####<a name="registerwaadapp"></a>Registrar aplicativos no Active Directory do Azure:
Para preencher a guia **Provedores**, você precisa registrar seu aplicativo com o Active Directory do Azure.  No Portal de Gerenciamento do Azure, na seção do **Active Directory**, selecione o diretório e, em seguida, vá para a guia **Aplicativos**. Isso fornece uma opção para adicionar o Site do Azure pela URL.Observe que quando você seguir estas etapas, você inicialmente define a URL para o endereço de localhost fornecido para depuração local no Visual Studio.Posteriormente você poderá alterar isso para a URL atual do seu site da web quando você implantar.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

Depois de adicionado, o portal fornece o caminho do documento de metadados STS (a **URL do documento de metadados de Federação**) e o **URI da ID do Aplicativo**. Esses valores são usados na guia **Provedores** da caixa de diálogo **Acesso e identidade** no Visual Studio. 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Ferramentas do Microsoft ASP.NET para Active Directory do Azure:
Um método alternativo para conseguir as etapas anteriores é usar as [Ferramentas do Microsoft ASP.NET para Active Directory do Azure][aspnettools]. Para usá-la, clique no item **Habilitar autenticação do Azure** no menu **Projeto** no Visual Studio.Isso abre uma caixa de diálogo que solicita o endereço do seu domínio do Active Directory do Azure (não a URL do aplicativo).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Se você for o administrador do domínio do Active Directory, selecione a caixa de seleção **Configurar este aplicativo no AD do Azure**. Isso fará o trabalho de registrar o aplicativo com o Active Directory. Se você não for o administrador, desmarque essa caixa e forneça as informações exibidas para um administrador. Esse administrador pode usar o Portal de Gerenciamento para criar um aplicativo integrado usando as etapas anteriores na ferramenta de Identidade e Acesso.Para obter as etapas detalhadas sobre como usar as ferramentas do ASP.NET para o Active Directory do Azure, consulte [Autenticação do Azure][azureauthtutorial].

Ao gerenciar seu aplicativo da linha de negócios, você tem a capacidade de usar qualquer um dos sistemas de controle do código-fonte com suporte para a implantação.  No entanto, devido ao alto nível de integração do Visual Studio neste cenário, é mais provável que o Team Foundation Service (TFS) seja seu sistema de controle do código-fonte de preferência. Nesse caso, você deve verificar se os Sites do Azure fornecem integração com o TFS. No portal de gerenciamento, abre a guia **Painel** do seu site. Em seguida, selecione **Configurar a implantação do controle do código-fonte**.Siga as instruções para usar o TFS. 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>Use o Barramento de Serviço para integrar recursos locais
Muitos aplicativos de linha de negócios devem-se integrar com os dados e serviços locais. Há várias razões de por que certos tipos de dados não podem ser movidos para a nuvem. Essas razões podem ser práticas ou normativas. Se você estiver nas fases de planejamento de decisão sobre quais dados hospedar no Azure e quais dados devem permanecer locais, é importante examinar os recursos na [Central de confiabilidade do Azure][trustcenter].Aplicativos híbridos da web executados no Azure e recursos de acesso que devem permanecer locais.

Ao usar máquinas virtuais ou serviços de nuvem, você pode usar uma rede virtual para conectar aplicativos no Azure com uma rede corporativa.  No entanto, Sites da Web não dão suporte a redes virtuais, portanto, a melhor maneira de realizar esse tipo de integração com Sites da Web é através do uso do [Serviço de Retransmissão do Barramento de Serviço do Azure][sbrelay]. O serviço de retransmissão do Barramento de Serviço permite que os aplicativos na nuvem conectem-se com segurança aos serviços WCF em execução em uma rede corporativa.O Barramento de Serviço permite a comunicação sem abrir portas do firewall. 

No diagrama abaixo, o aplicativo em nuvem e o serviço WCF local se comunicam com o Barramento de Serviço por meio de um namespace criado anteriormente. O serviço WCF local tem acesso aos serviços e dados internos que não podem ser movidos para a nuvem. O serviço WCF registra um ponto de extremidade no namespace. O site da web em execução no Azure também se conecta a esse ponto de extremidade no Barramento de Serviço. Eles só precisam de ser capazes de fazer solicitações HTTP públicas de saída para concluírem esta etapa.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

O Barramento de Serviço se conecta ao aplicativo em nuvem para o serviço WCF local. Isso fornece uma arquitetura básica para a criação de aplicativos híbridos que usam serviços e recursos no Azure e locais. Para obter mais informações, consulte [Como usar o serviço de retransmissão do Service Bus][sbrelayhowto] e o tutorial [Tutorial de sistema de mensagens de retransmissão do Barramento de Serviço][sbrelaytutorial]. Para um exemplo que demonstra essa técnica, consulte [Pizza Enterprise - Conectando Sites da Web no local usando o Barramento de Serviço][enterprisepizza].

##<a name="monitor"></a>Monitore seu aplicativo
Os aplicativos de negócios se beneficiam dos recursos do Site padrão, como dimensionamento e monitoramento. Para um aplicativo de negócios que passa por carga variável durante determinados dias ou horas, o recurso de escala automática (visualização) pode ajudar o site a sair e voltar para uso eficiente de recursos. As opções de monitoramento incluem monitoramento do ponto de extremidade e da cota. Todos esses cenários foram abordados em mais detalhes nos cenários [Presença Global na Web][scenarioglobalweb] e [campanha de Marketing Digital][scenariodigitalmarketing].

As necessidades de monitoramento podem variar entre os diferentes aplicativos de linha de negócios que possuem diferentes níveis de importância para os negócios. Para os aplicativos de missão mais críticos, considere a possibilidade de investir em uma solução de monitoração de terceiros, como a [Nova Relíquia][newrelic].

Os aplicativos de linha de negócios normalmente são gerenciados pela equipe de TI. Em caso de comportamento ou erros inesperados, os profissionais de TI podem habilitar logs mais detalhados e, em seguida, analisar os dados resultantes para determinar os problemas. No Portal de Gerenciamento, vá para a guia **Configurar** e reveja as opções nas seções **diagnóstico de aplicativos** e **diagnósticos do site**. 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Use os vários logs de aplicativo e site para solucionar problemas com o site.   Observe que algumas das opções especificam o **Sistema de arquivos**, que coloca os arquivos de registro no sistema de arquivos para o site.Eles podem ser acessados por meio do FTP, PowerShell do Azure ou as ferramentas de linha de comando do Azure.Outras opções especificam o **Armazenamento**.Isto envia as informações para a conta de armazenamento do Azure que você especificar.Para **Registro de servidor Web**, você também tem a opção de especificar uma cota de disco para o sistema de arquivos ou uma política de retenção para a opção de armazenamento.Isso evita que você armazene por tempo indeterminado aumentando a quantidade de dados de registro armazenados.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Para mais informações sobre essas configurações de log, consulte [Como: Configurar diagnósticos e baixar logs para um site][configurediagnostics].

Além de exibir os registros brutos por meio de utilitários FTP ou armazenamento, como o Azure Storage Explorer, você também pode visualizar informações de registro no Visual Studio. Para obter informações detalhadas sobre como usar esses registros em cenários de solução de problemas, consulte [Solucionando problemas de sites do Azure no Visual Studio][troubleshootwebsites].

##<a name="summary"></a>Resumo
O Azure permite que você para hospede aplicativos de intranet seguras na nuvem. O Active Directory do Azure fornece a capacidade de autenticar usuários, para que somente os membros da sua organização possam acessar os aplicativos. O serviço de retransmissão do Barramento de Serviço fornece um mecanismo para aplicativos da web para comunicar-se com dados e serviços locais. Essa abordagem de aplicativo híbrido torna mais fácil publicar um aplicativo de negócios para a nuvem sem migrar todos os dados e serviços. Uma vez implantados, os aplicativos de negócios beneficiam da escala padrão e das capacidades de monitoramento fornecidas pelos sites do Azure. Para mais informações, consulte os seguintes artigos técnicos.

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
   <td valign="middle"><strong>Criar e implantar</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/">Implantando um aplicativo Web ASP.NET em um site do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/web-site-with-sql-database/">Implantar um aplicativo MVC ASP.NET seguro no Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Autenticação</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/manage/windows/fundamentals/identity/">Entenda as opções de identidade do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/documentation/services/active-directory/">Serviço do Active Directory do Azure</a><br/>- <a href="http://technet.microsoft.com/pt-br/library/jj573650.aspx">O que é um locatário AD Azure?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Adicionando logon ao seu aplicativo Web usando o AD do Azure</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Tutorial de autenticação do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Retransmissão do Barramento de Serviço</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/how-to-guides/service-bus-relay/">Como usar o serviço de Retransmissão do Barramento de Serviço</a><br/>- <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/ee706736.aspx">Tutorial de sistema de mensagens de retransmissão do Barramento de Serviço</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoramento</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/">Como monitorar Sites</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Como: Receber notificações de alerta e Gerenciar regras de alerta no Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Como: Configurar o diagnóstico e baixar logs para um site</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Solucionando problemas de Sites do Azure no Visual Studio</a></td>
</tr>
</table>

  [websitesoverview]:/pt-br/documentation/services/web-sites/
  [csoverview]:/pt-br/documentation/services/cloud-services/
  [vmoverview]:/pt-br/documentation/services/virtual-machines/
  [chooseservice]:/pt-br/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/pt-br/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/pt-br/documentation/services/active-directory/
  [adusing]:/pt-br/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/pt-br/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/pt-br/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/pt-br/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/pt-br/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/pt-br/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/pt-br/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/pt-br/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/pt-br/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/pt-br/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




<!--HONumber=35.1-->

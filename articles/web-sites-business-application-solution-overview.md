<properties linkid="websites-business-application" urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Create a Line-of-Business Application on Azure Websites" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Criar um aplicativo de linha de negócios em Sites do Azure

Este guia fornece uma visão geral técnica de como usar sites do Azure para criar aplicativos de linha de negócios. Para os fins deste documento, presume-se que esses aplicativos são aplicativos de intranet que devem ser protegidos para uso comercial interno. Há duas características distintas de aplicativos de negócios. Esses aplicativos requerem autenticação, normalmente em um diretório corporativo. E eles normalmente exigem algum acesso ou integração com serviços e dados no local. Este guia concentra-se na criação de aplicativos de negócios em [Sites do Azure][Sites do Azure]. No entanto, há situações onde os [Serviços de nuvem do Azure][Serviços de nuvem do Azure] ou as [Máquinas virtuais do Azure][Máquinas virtuais do Azure] seriam a melhor opção para suas necessidades. É importante analisar as diferenças entre essas opções no tópico [Sites do Azure, serviços de nuvem e MVs:Quando usar qual opção?][Sites do Azure, serviços de nuvem e MVs:Quando usar qual opção?].

As seguintes áreas são abordadas neste guia:

-   [Considere os benefícios][Considere os benefícios]
-   [Escolha uma estratégia de autenticação][Escolha uma estratégia de autenticação]
-   [Crie um site do Azure que oferece suporte à autenticação][Crie um site do Azure que oferece suporte à autenticação]
-   [Use o Service Bus para integrar recursos no local][Use o Service Bus para integrar recursos no local]
-   [Monitore seu aplicativo][Monitore seu aplicativo]

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Este guia apresenta algumas das &aacute;reas e tarefas mais comuns que est&atilde;o alinhadas com o desenvolvimento do site .COM voltado para o p&uacute;blico. No entanto, h&aacute; outros recursos de sites do Azure que voc&ecirc; pode usar em sua implementa&ccedil;&atilde;o espec&iacute;fica. Para rever esses recursos, consulte tamb&eacute;m as outras guias no <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/global-web-presence-solution-overview/">Presen&ccedil;a Global da Web</a> e <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campanhas de Marketing Digital</a>.</p>
</div>

## <a name="benefits"></a>Considere os benefícios

Como os aplicativos de linha de negócios tipicamente segmentam os usuários corporativos, você deve considerar os motivos para usar a nuvem versus a infraestrutura e os recursos corporativos no local. Em primeiro lugar, há alguns dos benefícios típicos de migração para a nuvem, como a capacidade de expansão para cima e para baixo com cargas de trabalho dinâmicas. Por exemplo, considere um aplicativo que lida com as revisões de desempenho anual. Para a maioria do ano, esse tipo de aplicativo usaria muito pouco tráfego. Mas durante o período de revisão, o tráfego será picos de altos níveis para uma grande empresa. O Azure fornece opções de escala que permitem que a empresa seja dimensionada para lidar com a carga durante o período de análise de tráfego intenso, poupando dinheiro através da escala de volta para o restante do ano. Outro benefício da nuvem é a capacidade de se concentrar mais no desenvolvimento de aplicativos e menos na aquisição de infraestrutura e gerenciamento.

Além dessas vantagens padrão, colocar um aplicativo de negócios na nuvem oferece maior suporte para funcionários e parceiros usarem o aplicativo a partir de qualquer lugar. Os usuários não precisam estar conectados à rede corporativa para poder usar o aplicativo, e o grupo de TI evita soluções complexas de proxy reverso. Há várias opções de autenticação para certificar-se de que o acesso aos aplicativos da empresa estão protegidos. Essas opções são discutidas nas seções a seguir deste guia.

## <a name="authentication"></a>Escolha uma estratégia de autenticação

Para o cenário de aplicativo de negócios, sua estratégia de autenticação é uma das decisões mais importantes. Há várias opções:

-   Use o [Serviço Active Directory do Azure][Serviço Active Directory do Azure]. Você pode usá-lo como um diretório independente, ou você pode sincronizá-lo com um Active Directory no local. Os aplicativos então interagem com o Active Directory do Azure para autenticar usuários. Para obter uma visão geral dessa abordagem, consulte [Usando o Active Directory do Azure][Usando o Active Directory do Azure].
-   Use máquinas virtuais do Azure e rede virtual para instalar o Active Directory. Isso dá a você a opção de estender uma instalação do Active Directory no local para a nuvem. Você também pode optar por usar Serviços da Federação do Active Directory (ADFS) para agrupar os pedidos de identidade para o local AD. Em seguida, a autenticação para o aplicativo do Azure passa pelo ADFS para o Active Directory no local. Para obter mais informações sobre essa abordagem, consulte [Executando o Active Directory do Windows Server em MVs][Executando o Active Directory do Windows Server em MVs] e [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure][Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure].
-   Use um serviço intermediário, como o [Access Control Service do Azure][Access Control Service do Azure] (ACS), para usar vários serviços de identidade para autenticar usuários. Isso proporciona uma abstração para autenticar através do Active Directory ou através de um provedor de identidade diferente. Para obter mais informações, consulte [Usando o Access Control do Active Directory do Azure][Usando o Access Control do Active Directory do Azure].

Para esse cenário de aplicativo de negócios, o primeiro cenário usando o Active Directory do Azure fornece o caminho mais rápido para implementar uma estratégia de autenticação para o seu aplicativo. O restante deste guia se concentra no Active Directory do Azure. No entanto, dependendo dos seus requisitos de negócios, você pode achar que uma das duas soluções é a melhor opção. Por exemplo, se você não tem permissão para sincronizar informações de identidade para a nuvem, a solução ADFS pode ser a melhor opção. Ou se você tem de suportar outros provedores de identidade, como Facebook, uma solução ACS se encaixaria melhor.

Antes de começar a configurar um novo Active Directory do Azure, você deve observar que os serviços existentes, como o Office 365 ou o Windows Intune, já usam o Active Directory do Azure. Nesse caso, você pode associar sua assinatura existente com a sua assinatura do Azure. Para obter mais detalhes, consulte [O que é um locatário AD Azure?][O que é um locatário AD Azure?]

Se você ainda não tem um serviço que já usa o Active Directory do Azure, você pode criar um novo diretório no Portal de Gerenciamento. Use a seção do **Active Directory** do Portal de Gerenciamento para criar um novo diretório.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

Uma vez criado, você tem a opção de criar e gerenciar usuários, aplicativos integrados e domínios.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Para uma explicação completa dessas etapas, consulte [Adicionando registro único para seu aplicativo da Web usando o AD do Azure][Adicionando registro único para seu aplicativo da Web usando o AD do Azure]. Se você estiver usando este novo diretório como um recurso autônomo, a próxima etapa será desenvolver aplicativos que se integram com o diretório. No entanto, se você tiver identidades do Active Directory no local, você geralmente desejará sincronizar com o novo Active Directory do Azure. Para obter mais informações sobre como fazer isso, consulte a [Integração de diretórios][Integração de diretórios].

Depois que o diretório é criado e preenchido, você deve criar aplicativos da web que exigem autenticação e então integrá-los com o diretório. Essas etapas são cobertas nas duas seções seguintes.

## <a name="createintranetsite"></a>Crie um site do Azure que oferece suporte à autenticação

No cenário de Presença Global na Web, discutimos várias opções para criar e implantar um novo site. Se você é novo em sites do Azure, é uma boa ideia [revisar essas informações][revisar essas informações]. Um aplicativo do ASP.NET no Visual Studio é uma opção comum para um aplicativo da web de intranet que usa autenticação Windows. Um dos motivos para isso é a integração e o suporte para esse cenário fornecido pelo ASP.NET e Visual Studio.

Por exemplo, ao criar um projeto ASP.NET MVC 4 no Visual Studio, você tem a opção de criar um **Aplicativo de Intranet** na caixa de diálogo de criação de projeto.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

Isso altera as configurações do projeto para oferecer suporte à autenticação do Windows. Especificamente, o arquivo web.config tem o atributo do **modo** do elemento de **autenticação** definido como **Windows**. Se você criar um projeto diferente do ASP.NET, como um projeto de formulários da Web, ou se você estiver trabalhando com um projeto existente, você deve fazer essa alteração manualmente.

Para um projeto do MVC, você também deve alterar dois valores na janela de propriedades do projeto. Defina a **Autenticação Windows** para **Habilitada** e a **Autenticação anônima** para **Desativada**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Para autenticar com o Active Directory do Azure, você deve registrar este aplicativo com o diretório e, em seguida, você deve modificar essa configuração de aplicativo para se conectar. Isso pode ser feito de duas formas no Visual Studio:

-   [Ferramenta de acesso e identidade para o Visual Studio][Ferramenta de acesso e identidade para o Visual Studio]
-   [Ferramentas do Microsoft ASP.NET para Active Directory do Azure][Ferramentas do Microsoft ASP.NET para Active Directory do Azure]

### <a name="identityandaccessforvs"></a>Ferramenta de acesso e identidade para o Visual Studio:

Um método é usar a [Ferramenta de Identidade e Acesso][Ferramenta de Identidade e Acesso] (que você pode baixar e instalar). Esta ferramenta integra-se com o Visual Studio no menu de contexto do projeto. As seguintes instruções e capturas de tela são do Visual Studio 2012. Clique com botão direito no projeto e selecione **Acesso e identidade**. Há três coisas que devem ser configuradas. Na guia **Provedores**, você deve fornecer o **caminho para o documento de metadados STS** e o **URI de ID do Aplicativo** (para obter esses valores, consulte a seção em [Registrando aplicativos no Active Directory do Azure][Registrando aplicativos no Active Directory do Azure]).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

A alteração da configuração final que deve ser feita é na guia **Configuração** da caixa de diálogo **Acesso e identidade**. Você deve selecionar a caixa de seleção **Ativar cookies de farm da web**. Para uma explicação completa dessas etapas, consulte [Adicionando registro único para seu aplicativo da Web usando o AD do Azure][Adicionando registro único para seu aplicativo da Web usando o AD do Azure].

#### <a name="registerwaadapp"></a>Registrar aplicativos no Active Directory do Azure:

Para preencher a guia **Provedores**, você precisa registrar seu aplicativo com o Active Directory do Azure. No Portal de Gerenciamento do Azure, na seção do **Active Directory**, selecione o diretório e, em seguida, vá para a guia **Aplicativos**. Isso fornece uma opção para adicionar o Site do Azure pela URL. Observe que quando você seguir estas etapas, você inicialmente define a URL para o endereço de host local fornecido para depuração local no Visual Studio. Posteriormente você poderá alterar isso para a URL atual do seu site da web quando você implantar.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

Depois de adicionado, o portal fornece o caminho do documento de metadados STS (a **URL do documento de metadados de Federação**) e o **URI de ID do APP**. Esses valores são usados na guia **Provedores** da caixa de diálogo **Acesso e identidade** no Visual Studio.

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

### <a name="aspnettoolsforwaad"></a>Ferramentas do Microsoft ASP.NET para Active Directory do Azure:

Um método alternativo para conseguir as etapas anteriores é usar as [Ferramentas do Microsoft ASP.NET para Active Directory do Azure][1]. Para usá-la, clique no item **Ativar autenticação do Azure** no menu **Projeto** no Visual Studio. Isso abre uma caixa de diálogo que solicita o endereço do seu domínio do Active Directory do Azure (não a URL do aplicativo).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Se você for o administrador do domínio do Active Directory, selecione a caixa de seleção **Configurar este aplicativo no AD do Azure**. Isso fará o trabalho de registrar o aplicativo com o Active Directory. Se você não for o administrador, desmarque essa caixa e forneça as informações exibidas para um administrador. Esse administrador pode usar o Portal de Gerenciamento para criar um aplicativo integrado usando as etapas anteriores na ferramenta de Identidade e Acesso. Para obter as etapas detalhadas sobre como usar as ferramentas do ASP.NET para o Active Directory do Azure, consulte [Autenticação do Azure][Autenticação do Azure].

Ao gerenciar seu aplicativo da linha de negócios, você tem a capacidade de usar qualquer um dos sistemas de controle de código fonte com suporte para a implantação. No entanto, devido ao alto nível de integração do Visual Studio neste cenário, é mais provável que o Team Foundation Service (TFS) seja seu sistema de controle de origem de preferência. Nesse caso, você deve verificar se os Sites do Azure fornecem integração com o TFS. No portal de gerenciamento, abre a guia **Painel** do seu site. Em seguida, selecione **Configurar a implantação do controle de origem**. Siga as instruções para usar o TFS.

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

## <a name="servicebusrelay"></a>Use o Service Bus para integrar recursos no local

Muitos aplicativos de linha de negócios devem-se integrar com os dados e serviços locais. Há várias razões de por que certos tipos de dados não podem ser movidos para a nuvem. Essas razões podem ser práticas ou normativas. Se você estiver nas fases de planejamento de decisão sobre quais dados hospedar no Azure e quais dados devem permanecer no local, é importante examinar os recursos na [Central de confiabilidade do Azure][Central de confiabilidade do Azure]. Aplicativos híbridos da web executados no Azure e recursos de acesso que devem permanecer no local.

Ao usar máquinas virtuais ou serviços na nuvem, você pode usar uma rede virtual para conectar aplicativos no Azure com uma rede corporativa. No entanto, Sites da Web não suportam redes virtuais, portanto, a melhor maneira de realizar esse tipo de integração com Sites da Web é através do uso do [Serviço de Retransmissão do Service Bus do Azure][Serviço de Retransmissão do Service Bus do Azure]. O serviço de retransmissão do Service Bus permite que os aplicativos na nuvem conectem-se com segurança aos serviços WCF em execução em uma rede corporativa. O Service Bus permite a comunicação sem abrir portas do firewall.

No diagrama abaixo, o aplicativo em nuvem e o serviço WCF no local se comunicam com o Service Bus por meio de um espaço de nome criado anteriormente. O serviço WCF no local tem acesso aos serviços e dados internos que não podem ser movidos para a nuvem. O serviço WCF registra um ponto de extremidade no namespace. O site da web em execução no Azure também se conecta a esse ponto de extremidade no Service Bus. Eles só precisam de ser capazes de fazer solicitações HTTP públicas de saída para concluírem esta etapa.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

O Service Bus se conecta ao aplicativo em nuvem para o serviço WCF no local. Isso fornece uma arquitetura básica para a criação de aplicativos híbridos que usam serviços e recursos no Azure e no local. Para obter mais informações, consulte [Como usar o serviço de retransmissão do Service Bus][Como usar o serviço de retransmissão do Service Bus] e o tutorial [Tutorial de mensagens retransmitido do Service Bus][Tutorial de mensagens retransmitido do Service Bus]. Para um exemplo que demonstra essa técnica, consulte [Pizza Enterprise - Conectando Sites da Web no local usando o Service Bus][Pizza Enterprise - Conectando Sites da Web no local usando o Service Bus].

## <a name="monitor"></a>Monitore seu aplicativo

Os aplicativos de negócios se beneficiam dos recursos do Site padrão, como dimensionamento e monitoramento. Para um aplicativo de negócios que passa por carga variável durante determinados dias ou horas, o recurso de escala automática (visualização) pode ajudar o site a sair e voltar para uso eficiente de recursos. As opções de monitoramento incluem monitoramento do ponto de extremidade e da cota. Todos esses cenários foram abordados em mais detalhes nos cenários [Presença Global na Web][revisar essas informações] e [campanha de Marketing Digital][campanha de Marketing Digital].

As necessidades de monitoramento podem variar entre os diferentes aplicativos de linha de negócios que possuem diferentes níveis de importância para os negócios. Para os aplicativos de missão mais críticos, considere a possibilidade de investir em uma solução de monitoração de terceiros, como a [Nova Relíquia][Nova Relíquia].

Os aplicativos de linha de negócios normalmente são gerenciados pela equipe de TI. Em caso de comportamento ou erros inesperados, os profissionais de TI podem ativar logs mais detalhados e, em seguida, analisar os dados resultantes para determinar os problemas. No Portal de Gerenciamento, vá para a guia **Configurar** e reveja as opções nas seções **diagnósticos dos aplicativos** e **diagnósticos do site**.

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Use os vários logs de aplicativo e site para solucionar problemas com o site. Observe que algumas das opções especificam o **Sistema de arquivos**, que coloca os arquivos de registro no sistema de arquivos para o site. Eles podem ser acessados por meio do FTP, PowerShell do Azure ou as ferramentas de linha de comando do Azure. Outras opções especificam o **Armazenamento**. Isto envia as informações para a conta de armazenamento do Azure que você especificar. Para **Registro de servidor da Web**, você também tem a opção de especificar uma cota de disco para o sistema de arquivos ou uma política de retenção para a opção de armazenamento. Isso evita que você armazene por tempo indeterminado aumentando a quantidade de dados de registro armazenados.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Para obter mais informações sobre essas configurações de log, consulte [Como configurar diagnósticos e fazer download de logs para um site da web][Como configurar diagnósticos e fazer download de logs para um site da web].

Além de exibir os registros brutos por meio de utilitários FTP ou armazenamento, como o Azure Storage Explorer, você também pode visualizar informações de registro no Visual Studio. Para obter informações detalhadas sobre como usar esses registros em cenários de solução de problemas, consulte [Solucionando problemas de sites do Azure no Visual Studio][Solucionando problemas de sites do Azure no Visual Studio].

## <a name="summary"></a>Resumo

O Azure permite que você para hospede aplicativos de intranet seguras na nuvem. O Active Directory do Azure fornece a capacidade de autenticar usuários, para que somente os membros da sua organização possam acessar os aplicativos. O serviço de retransmissão do Service Bus fornece um mecanismo para aplicativos da web para comunicar-se com dados e serviços locais. Essa abordagem de aplicativo híbrido torna mais fácil publicar um aplicativo de negócios para a nuvem sem migrar todos os dados e serviços. Uma vez implantados, os aplicativos de negócios beneficiam da escala padrão e das capacidades de monitoramento fornecidas pelos sites do Azure. Para mais informações, consulte os seguintes artigos técnicos.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">&Aacute;rea</th>
   <th align="left" valign="top">Recursos</th>
</tr>
<tr>
   <td valign="middle"><strong>Plano</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/choose-web-app-service">Sites do Azure, servi&ccedil;os de nuvem e m&aacute;quinas virtuais: Quando usar qual op&ccedil;&atilde;o?</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Criar e implantar</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/">Implantando um aplicativo da Web do ASP.NET a um site do Azure</a><br/>-<a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/web-site-with-sql-database/">Implantar um aplicativo seguro ASP.NET MVC para o Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Autentica&ccedil;&atilde;o</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/manage/windows/fundamentals/identity/">Compreender as op&ccedil;&otilde;es de identidade do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/documentation/services/active-directory/">Servi&ccedil;o Active Directory do Azure</a><br/>- <a href="http://technet.microsoft.com/pt-br/library/jj573650.aspx">O que &eacute; um locat&aacute;rio AD Azure?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Adicionar registro &uacute;nico para seu aplicativo da web usando o AD do Azure</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Tutorial de autentica&ccedil;&atilde;o do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Retransmiss&atilde;o do Service Bus</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/how-to-guides/service-bus-relay/">Como usar o servi&ccedil;o de retransmiss&atilde;o do Service Bus</a><br/>- <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/ee706736.aspx">Tutorial de mensagens de retransmiss&atilde;o do Service Bus</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoramento</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/">Como monitorar sites da web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Como receber notifica&ccedil;&otilde;es de alerta e gerenciar regras de alerta no Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Como configurar diagn&oacute;sticos e fazer download de logs para um site da web</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Solucionando problemas dos sites da web do Azure no Visual Studio</a></td>
</tr>
</table>


  [Sites do Azure]: /pt-br/documentation/services/web-sites/
  [Serviços de nuvem do Azure]: /pt-br/documentation/services/cloud-services/
  [Máquinas virtuais do Azure]: /pt-br/documentation/services/virtual-machines/
  [Considere os benefícios]: #benefits
  [Escolha uma estratégia de autenticação]: #authentication
  [Crie um site do Azure que oferece suporte à autenticação]: #createintranetsite
  [Use o Service Bus para integrar recursos no local]: #servicebusrelay
  [Monitore seu aplicativo]: #monitor
  [Serviço Active Directory do Azure]: /pt-br/documentation/services/active-directory/
  [Usando o Active Directory do Azure]: /pt-br/manage/windows/fundamentals/identity/#ad
  [Executando o Active Directory do Windows Server em MVs]: /pt-br/manage/windows/fundamentals/identity/#adinvm
  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx
  [Access Control Service do Azure]: http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [Usando o Access Control do Active Directory do Azure]: /pt-br/manage/windows/fundamentals/identity/#ac
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [Adicionando registro único para seu aplicativo da Web usando o AD do Azure]: http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [Integração de diretórios]: http://technet.microsoft.com/pt-br/library/jj573653.aspx
  [revisar essas informações]: /pt-br/manage/services/web-sites/global-web-presence-solution-overview/
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [Ferramenta de acesso e identidade para o Visual Studio]: #identityandaccessforvs
  [Ferramentas do Microsoft ASP.NET para Active Directory do Azure]: #aspnettoolsforwaad
  [Ferramenta de Identidade e Acesso]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Registrando aplicativos no Active Directory do Azure]: #registerwaadapp
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [1]: http://go.microsoft.com/fwlink/?LinkID=282306
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [Autenticação do Azure]: http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [Central de confiabilidade do Azure]: /pt-br/support/trust-center/
  [Serviço de Retransmissão do Service Bus do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj860549.aspx
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [Como usar o serviço de retransmissão do Service Bus]: /pt-br/develop/net/how-to-guides/service-bus-relay/
  [Tutorial de mensagens retransmitido do Service Bus]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee706736.aspx
  [Pizza Enterprise - Conectando Sites da Web no local usando o Service Bus]: http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [campanha de Marketing Digital]: /pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [Nova Relíquia]: http://newrelic.com/azure
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  [Como configurar diagnósticos e fazer download de logs para um site da web]: /pt-br/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [Solucionando problemas de sites do Azure no Visual Studio]: /pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/

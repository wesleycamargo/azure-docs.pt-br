<properties linkid="websites-global-web-presence" urlDisplayName="Criar uma presença Global da Web em Sites do Azure" pageTitle="Criar uma presença Global da Web em Sites do Azure" metaKeywords="" description="Este guia fornece uma visão geral técnica de como hospedar o site da sua organização (.COM) nos sites do Azure. Isso inclui implantação, domínios personalizados, SSL e monitoramento." metaCanonical="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Crie uma presença Global na Web em Sites Azure" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />.





# Crie uma presença Global na Web em Sites Azure

Este guia fornece uma visão geral técnica de como hospedar o site da sua organização (.COM) no Azure. Este cenário também é conhecido como uma presença global na web. Este guia se concentra no uso de [Sites do Azure][websitesoverview], pois os Sites da Web são a maneira mais rápida e simples para criar, migrar, dimensionar e gerenciar um aplicativo da web no Azure. No entanto, alguns requisitos do aplicativo prestam-se melhor para [serviços de nuvem do Azure][csoverview] ou [Máquinas virtuais do Azure][vmoverview] executando o IIS. Eles também são excelentes opções para hospedar aplicativos da web. Se você estiver na fase de planejamento inicial, revise o documento [Sites do Azure, serviços de nuvem e MVs: quando usar o quê?][chooseservice] Na ausência de um requisito para usar os serviços de nuvem ou as máquinas virtuais, é recomendável usar os Sites da Web para hospedar sua presença global na web. O restante deste documento fornece orientação para o uso de Sites da Web com esse cenário. 

As seguintes áreas são abordadas neste guia:

- [Criar um site do Azure](#createwebsite)
- [Implantar o Site da Web](#deploywebsite)
- [Adicionar um domínio personalizado](#customdomain)
- [Proteger o Site da Web com SSL](#ssl)
- [Monitorar o Site](#monitor)

<div class="dev-callout">
<strong>Observação</strong>
<p>Este guia apresenta algumas das áreas e tarefas mais comuns que estão alinhadas com o desenvolvimento do site .COM voltado para o público. No entanto, há outros recursos dos sites Azure que você pode usar em sua implementação específica. Para rever esses recursos, consulte também os outros guias em <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campanhas de Marketing Digital</a> e <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/business-application-solution-overview">Aplicativos de negócios</a>.</p>
</div>

##<a name="createwebsite"></a>Criar um Site do Azure
Usando o Portal de Gerenciamento do Azure, você pode criar um novo Site do Azure de várias maneiras. Quando você clicar no botão **Novo** na parte inferior do portal, você verá o seguinte diálogo:

![GlobalWebCreate][GlobalWebCreate]

Há três opções para a criação de um novo Site: **Criação rápida**, **Criação personalizada** e **Da galeria**. Com cada uma dessas opções, você deve selecionar uma região do Azure que se alinha com a maior parte de sua base de usuários.

Se você estiver migrando um site existente, a opção **Criação personalizada** permite que você crie ou associe um banco de dados SQL ou banco de dados MySQL. Essa opção também oferece a capacidade de especificar várias opções de controle de origem para implantação, como GitHub ou o Team Foundation Server (TFS). Se você já estiver gerenciando seu site usando um mecanismo de controle de origem, isso fornece uma maneira rápida para configurar seu Site do Azure para implantação.

A opção **Da galeria** permite configurar um novo site com uma das várias estruturas, como Drupal ou WordPress. Isso pode ser útil para configurar rapidamente um novo site que pode ser personalizado dentro da estrutura escolhida.

Como a maioria dos serviços do Azure, você deve selecionar uma região do Azure para seu novo Site. O Azure tem várias regiões no mundo. Depois que você implantar o seu site da web para qualquer região, ele estará acessível globalmente na internet. No entanto, várias regiões fornecem maior flexibilidade. Uma vantagem óbvia é implantar sites em regiões que estão mais próximas aos usuários. 

Para obter detalhes sobre as etapas para criar um novo site da web, consulte [Como criar e implantar um Site da Web][howtocreatewebsites].

##<a name="deploywebsite"></a>Implantar o Site
Há várias maneiras para implantar seu site da web para o Azure. Se você selecionou uma estrutura da galeria, você já tem um site inicial implantado. No entanto, para fazer qualquer progresso, você ainda deve configurar algum tipo de procedimento de edição e implantação. Algumas das opções de implantação incluem:

- Usar um cliente FTP.
- Implantar do controle de origem.
- Publicar a partir do Visual Studio.
- Publicar do [WebMatrix][webmatrix].

Cada uma dessas opções tem várias vantagens. A capacidade de publicar a partir de um cliente de FTP é uma solução simples e direta para enviar novos arquivos para seu site. Isso também significa que as ferramentas de publicação existentes ou processos que dependem de FTP podem continuar a trabalhar com Sites do Azure. O controle de origem fornece melhor controle sobre lançamentos do conteúdo do site, pois as alterações podem ser rastreadas, publicadas e revertidas para versões anteriores, se necessário. As opções para publicar diretamente do Visual Studio ou do Web Matrix é uma conveniência para os desenvolvedores que usam qualquer uma dessas ferramentas. É um cenário útil para esse recurso durante os estágios iniciais de um projeto ou para criação de protótipos. Em ambos os casos, a publicação e testes frequentes são potencialmente mais convenientes de dentro do ambiente de desenvolvimento. 

Várias das tarefas de implantação aqui envolvem o uso das informações no Portal de Gerenciamento do Azure. Vá para o site da web, selecione a guia **Painel** e, em seguida, procure a seção **Olhada rápida**. A captura de tela a seguir mostra várias opções.

![GlobalWebQuickGlance][GlobalWebQuickGlance]

Algumas ferramentas de controle de origem e clientes FTP requerem acesso de nome de usuário/senha. Para um novo Site, as credenciais não são criadas automaticamente. No entanto, você pode facilmente fazer a isso clicando em **Redefinir suas credenciais de implantação**. Depois de concluído, você pode usar qualquer cliente FTP para implantar seu site da web usando essas credenciais junto com o **Nome do Host FTP** na mesma página **Painel**.

![GlobalWebFTPSettings][GlobalWebFTPSettings]

Observe que o nome de usuário de implantação/FTP é uma combinação do nome do Site da Web e o nome de usuário fornecido. Portanto, se seu site fosse "http://contoso.azurewebsite.net" e se seu nome de usuário fosse "myuser", o nome de usuário para a implantação e FTP seria "contoso\myuser".

Também é possível implantar através de um serviço de gerenciamento de controle de origem, como o GitHub ou o TFS Online. Clique na opção para **Configurar a implantação do controle de origem**. Em seguida, siga as instruções para o sistema de controle de origem ou serviço de sua escolha. Para obter instruções passo a passo para a publicação de um repositório local Git, consulte [Publicando do controle de origem para sites do Azure][publishingwithgit].

Se você planeja usar o Visual Studio para criar e gerenciar seu site, você pode optar por publicar diretamente do Visual Studio. Um método é clicar na opção **Baixar o perfil de publicação**. Isso permite que você salve um arquivo publishsettings que pode ser importado para o Visual Studio para publicação na web. 

<div class="dev-callout">
<strong>Observação</strong>
<p>É importante manter o arquivo <i>publishsettings</i> seguro e fora do código-fonte de controle, porque ele contém nomes de usuário e senhas para implantação e também quaisquer sequências de conexão do banco de dados vinculadas.</p>
</div>

Também é possível importar as informações de assinatura diretamente no Visual Studio. Por exemplo, considere um projeto local do ASP.NET no Visual Studio. Clique com o botão direito do mouse no projeto da web e selecione **Publicar**. O botão **Importar** na caixa de diálogo **Publicar na Web** permite que você importe um arquivo que contém as configurações de assinatura do Azure ou o arquivo publishsettings que você baixou do painel Sites da Web. A captura de tela a seguir mostra estas opções.

![GlobalWebVSPublish][GlobalWebVSPublish]

Para obter mais informações sobre como publicar para o Azure do Visual Studio, consulte Implantando um aplicativo da Web ASP.NET a um Site do Azure. 

Mais uma opção para o desenvolvimento e a implantação é o WebMatrix do Portal de Gerenciamento do Azure.

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Para obter mais informações sobre essa opção, consulte [Desenvolver e implantar um site da web com o Microsoft WebMatrix][aspnetgetstarted].

Embora estas etapas fornecem o que você precisa para implantar seu site .COM, você também deve criar um plano para gerenciar o ciclo de publicação de conteúdo contínuo. Essas opções podem variar de mover uma solução personalizada, a reimplantações periódicas para um site que muda com pouca frequência, para um sistema de gerenciamento de conteúdo cheio de recursos (CMS). Se você estiver criando um novo site, você deve observar que há opções na galeria para usar as estruturas existentes do CMS, como [Drupal][drupal] ou [Umbraco][umbraco].

##<a name="customdomain"></a>Adicionar um domínio personalizado
Se esta for sua presença global na web, você deve associar o seu nome de domínio registrado com o site. Existem muitos provedores de terceiros que fornecem serviços de registro de domínio. Cada um desses provedores suporta a criação de diferentes tipos de registros DNS para gerenciar seu domínio. Um registro DNS ajuda a mapear uma URL amigável, como "www.contoso.com", para o endereço IP ou URL atual que hospeda o site. 

<div class="dev-callout">
<strong>Observação</strong>
<p>Na discussão a seguir, há dois tipos de registros DNS de seu interesse. Em primeiro lugar, um registro CNAME pode redirecionar de uma URL, como "www.contoso.com", para uma URL diferente, como "contoso.azurewebsites.net". Em segundo lugar, um registro A pode mapear uma URL, como "www.contoso.com", para um endereço IP, como 172.16.48.1.</p>
</div>

Para Sites do Azure, primeiro você deve criar um registro CNAME para o Site do Azure. Essa configuração é feita através do site do registrador de terceiros. A seguir está um exemplo de registro CNAME.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Tipo</th>
   <th align="left" valign="top">Host</th>
   <th align="left" valign="top">Resposta</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>

Se o domínio está registrado recentemente, ele pode demorar um dia ou mais para resolver em todos os servidores DNS, que operam em entradas DNS em cache. No entanto, se o domínio já existe, a alteração CNAME deve acontecer dentro de um minuto. Observe que o registro CNAME fornece um mapeamento entre o seu domínio (que deve ser qualificado com um alias de subdomínio, como "www") para a URL do Site do Azure. Nenhum dos lados do registro CNAME inclui o prefixo "http://".

No Portal de Gerenciamento do Azure, confirme que você está executando os modos **Compartilhado** ou **Padrão** na guia **Escala** (os domínios personalizados não são suportados para sites **Gratuitos**). Em seguida, vá para a guia **Configurar** e clique no botão **Gerenciar domínios**. Isso permite que você associe o site com o nome de domínio personalizado. 

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Antes de colocar o seu domínio personalizado na lista, você deve primeiro ir para seu provedor DNS e criar um registro CNAME para o seu domínio personalizado (www.contoso.com) que aponta para a URL do Site da Web do Azure (contoso.azurewebsites.net). Depois de propagados, você poderá inserir o domínio personalizado na caixa de diálogo mostrado na captura de tela anterior. A presença do registro CNAME para www.contoso.com que aponta para esse site garante que você tem a autoridade para usar esse nome de domínio com este site. Neste ponto, você pode criar um registro com o endereço IP na parte inferior da caixa de diálogo.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Tipo</th>
   <th align="left" valign="top">Host</th>
   <th align="left" valign="top">Resposta</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>A.</strong></td>
   <td valign="top">contoso.com</td>
   <td valign="top">172.16.48.1</td>
   <td valign="top">8000</td>
</tr>
</table>

Para obter mais informações, consulte [Configurando um nome de domínio personalizado para um sites do Azure][customdns].

##<a name="ssl"></a>Proteja o Site da Web com SSL
Se o site contém informações somente leitura, não é necessário fornecer acesso seguro ao site. No entanto, se você coleta informações do usuário, realiza comércio eletrônico ou gerencia quaisquer outros dados confidenciais, você deve proteger o site. A segurança é um assunto grande e este artigo não pode cobrir todas as técnicas e práticas recomendadas. No entanto, é importante destacar o processo de habilitação Secure Sockets Layer (SSL) para o seu Site da Web. O SSL permite que os usuários se conectem ao seu site de forma criptografada com endereços HTTPS em vez de HTTP. Há etapas específicas necessárias para usar o SSL com Sites do Azure. 

Os Sites da Web do Azure fornecem automaticamente uma conexão segura para a URL do site atual. Por exemplo, se seu site for http://contoso.azurewebsites.net, você pode se conectar via SSL simplesmente alterando "http" para "https", como em **https**://contoso.azurewebsites.net.

No entanto, se você estiver usando um nome de domínio personalizado, você deve tomar medidas para carregar um certificado e habilitar o SSL por meio do Portal de Gerenciamento do Azure para seu site da web. As etapas a seguir fornecem um resumo desse processo, mas você pode encontrar instruções detalhadas no tópico [Configurando um certificado SSL para um site do Azure][ssl].

Primeiro, obtenha um certificado SSL de uma Autoridade de Certificação. Se você pretende proteger seu domínio com diversos subdomínios (por exemplo, www.contoso.com e staging.contoso.com), você precisará obter um certificado curinga (*.contoso.com). Eles podem custar mais, portanto você deve decidir se a flexibilidade deste tipo de certificado justifica o custo.

Depois que você obter o certificado da autoridade de certificação, você não pode simplesmente carregá-lo no Azure no mesmo formato. É necessário gerar um arquivo .pfx usando o comando openssl. O comando openssl é parte do projeto OpenSSL. As fontes são distribuídas no [site da OpenSSL][openssl], mas normalmente você pode encontrar uma versão pré-compilada da ferramenta na internet também. No exemplo a seguir, um certificado, myserver.crt e o arquivo de chave particular, myserver.key, são usados para criar um arquivo .pfx.

	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

Para carregar o certificado do Azure, primeiro vá para a guia **Escala** e verifique se você está executando o modo **Padrão**. O SSL para domínios personalizados não é suportado para os modos **Gratuito** ou **Compartilhado**. Na guia **Configurar**, clique no botão **Carregar um certificado**.

![GlobalWebUplodateCert][GlobalWebUplodateCert]

Em seguida, na seção **Conexões ssl**, mapeie o certificado para o nome de domínio que ele protege. Há duas opções para este mapeamento: SNI SSL e SSL baseado em IP.

![GlobalWebSSLBindings][GlobalWebSSLBindings]

A opção **SSL baseado em IP** é a maneira tradicional para mapear o endereço IP dedicado público para o nome de domínio. Isso funciona com todos os navegadores. A opção **SNI SSL** permite que vários domínios compartilhem o mesmo endereço IP e ainda tenham diferentes certificados SSL associados para cada domínio. O SNI SSL não funciona com alguns navegadores mais antigos (para obter mais informações sobre compatibilidade, consulte a [Entrada da Wikipedia para SNI SSL][sni]). Há uma cobrança mensal (rateada por hora) associada a cada certificado SSL e o preço varia de acordo com a opção de SSL com base em IP ou SNI. Para obter informações sobre os preços, consulte [Detalhes de preços dos sites][sslpricing]. Para obter mais informações sobre este processo, consulte [Configurando um certificado SSL para um Site do Azure][ssl].

##<a name="monitor"></a>Monitorar o Site
Depois do site estar ativamente manipulandp as solicitações dos usuários, é importante usar o monitoramento. Por exemplo, você pode querer saber se a carga do usuário está causando a elevação do tempo de CPU, o que poderia indicar a necessidade de expandir o site. Ou as ineficiências do aplicativo podem aumentar o tempo de resposta ou levar a erros. Esta seção aborda alguns dos recursos de monitoramento internos no Portal de Gerenciamento do Azure.

A guia **Monitorar** contém algumas medidas fundamentais para seu Site da Web em formato de gráfico. 

![GlobalWebMonitor1][GlobalWebMonitor1]

Você pode personalizar as métricas neste gráfico usando o botão Adicionar métricas.

![GlobalWebMonitor2][GlobalWebMonitor2]

Para sites executando no modo **Padrão**, você também pode habilitar o monitoramento e alerta de pontos de extremidade. Na guia **Configurar**, vá para a seção **monitoramento** e configure um ponto de extremidade. Esse ponto de extremidade é executado de um ou mais locais que você especificar e periodicamente tenta acessar seu site da web. São coletadas informações de erro e de tempo. 

Na guia **Monitorar**, esse ponto de extremidade é exibido mostrando o tempo de resposta. Se você selecionar a métrica do ponto de extremidade, você pode adicionar uma regra de alerta clicando no ícone **Adicionar regra**.

![GlobalWebMonitor3][GlobalWebMonitor3]

A regra pode enviar e-mail aos administradores ou outros indivíduos quando o tempo de resposta exceder o limite especificado.

![GlobalWebMonitor4][GlobalWebMonitor4]

Se você descobrir que o site requer dimensionamento, isso pode ser feito na guia **Escala** manualmente ou por meio da pré-visualização de escala automática. A guia escala oferece opções para dimensionar para cima (máquinas dedicadas maiores) ou para fora (instâncias adicionais compartilhadas ou instâncias dedicadas do mesmo tamanho). No entanto, a visualização de escala automática só suporta dimensionar para fora. Para obter mais detalhes sobre essa opção, consulte Para obter mais informações sobre como monitorar o site da web, consulte a seção "Escalar com demanda do usuário" do cenário [Campanha de Marketing Digital][scenariodigitalmarketing]. Também consulte [Como monitorar Sites][howtomonitor].

##<a name="summary"></a>Resumo
Para criar o site da sua organização (.COM), as tarefas padrão incluem monitorar uma estrutura de desenvolvimento, a criação de sites, a implantação, a atribuição de domínio personalizado e monitoramento. Para sites que devem proteger os dados do usuário, o SSL é altamente recomendável. Este artigo fornece uma visão geral sobre realizar essas tarefas usando os Sites do Azure. Para obter mais informações, consulte os seguintes artigos técnicos referenciados no documento.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Área</th>
   <th align="left" valign="top">Recursos</th>
</tr>
<tr>
   <td valign="middle"><strong>Plano</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/choose-web-app-service">Sites da web, serviços de nuvem e MVs do Azure: quando usar o quê?</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Criar</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-create-websites/">Como criar e implantar um site</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Implantar</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/common-tasks/publishing-with-git/">Publicação do controle de origem para Sites do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/">Implantação de um aplicativo da Web ASP.NET a um Site do Azure</a><br/>- <a href="http://www.windowsazure.com/pt-br/develop/net/tutorials/website-with-webmatrix/">Desenvolver e implantar um site da web com o Microsoft WebMatrix</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Domínios personalizados</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/common-tasks/custom-dns-web-site/">Configurando um nome de domínio personalizado para um site do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/develop/net/common-tasks/enable-ssl-web-site/">Configurar um certificado SSL para um Site do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoramento</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/">Como monitorar Sites</a></td>
</tr>
</table>

  [websitesoverview]:/pt-br/documentation/services/web-sites/
  [csoverview]:/pt-br/documentation/services/cloud-services/
  [vmoverview]:/pt-br/documentation/services/virtual-machines/
  [chooseservice]:/pt-br/manage/services/web-sites/choose-web-app-service
  
  
  [scenariodigitalmarketing]:/pt-br/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [howtocreatewebsites]:/pt-br/manage/services/web-sites/how-to-create-websites/
  [webmatrix]:http://www.microsoft.com/web/webmatrix/
  [publishingwithgit]:/pt-br/develop/net/common-tasks/publishing-with-git/
  [aspnetgetstarted]:/pt-br/develop/net/tutorials/get-started/
  [drupal]:https://drupal.org/
  [umbraco]:http://umbraco.com/
  [customdns]:/pt-br/develop/net/common-tasks/custom-dns-web-site/
  [ssl]:/pt-br/develop/net/common-tasks/enable-ssl-web-site/
  [openssl]:http://www.openssl.org/
  [sni]:http://en.wikipedia.org/wiki/Server_Name_Indication
  [sslpricing]:/pt-br/pricing/details/web-sites/#service-ssl
  [howtomonitor]:/pt-br/manage/services/web-sites/how-to-monitor-websites/
  
 [GlobalWebCreate]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [GlobalWebQuickGlance]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [GlobalWebMonitor1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [GlobalWebMonitor2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [GlobalWebMonitor3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [GlobalWebMonitor4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [GlobalWebVSPublish]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [GlobalWebSSLBindings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [GlobalWebUplodateCert]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [GlobalWebCustomDomain]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_CustomDomain.png
  [GlobalWebWebMatrix]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [GlobalWebFTPSettings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  
  
  
  
  
  
  
  
  
  
  


<properties 
	pageTitle="Comparação de Sites do Azure, Serviços de Nuvem e Máquinas Virtuais" 
	description="Saiba quando usar Websites do Azure, Serviços de Nuvem e Máquinas Virtuais para hospedar aplicativos Web." 
	services="web-sites, virtual-machines, cloud-services" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="tdykstra"/>

# Comparação de Sites do Azure, Serviços de Nuvem e Máquinas Virtuais

## Visão geral

O Azure oferece várias maneiras de hospedar sites: [Sites do Azure][], [Serviços de Nuvem][] e [Máquinas Virtuais][]. Este artigo ajuda você a entender as opções e fazer a escolha certa para seu aplicativo Web.

Os Sites do Azure são a melhor opção para a maioria dos aplicativos Web. A implantação e o gerenciamento estão integrados na plataforma, os sites podem ser dimensionados rapidamente para dar suporte a altas cargas de tráfego e o gerenciador de balanceamento de carga e tráfego integrado oferece alta disponibilidade. Você pode mover sites existentes para sites da Web do Azure facilmente com uma [ferramenta de migração online](https://www.migratetoazure.net/), usar um aplicativo de software livre da Galeria de Aplicativos Web ou criar um novo site usando a estrutura e as ferramentas de sua escolha. O recurso [WebJobs][] facilita a adição do processamento de tarefas em segundo plano ao seu aplicativo. 

Se precisar de mais controle sobre o ambiente do servidor Web, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor, os Serviços de Nuvem do Azure geralmente são a melhor opção.

Se tiver um aplicativo existente que necessitaria de modificações substanciais para ser executado nos Sites ou nos Serviços de Nuvem do Azure, você pode escolher as Máquinas Virtuais do Azure para simplificar a migração para a nuvem. No entanto, configurar, proteger e manter as máquinas virtuais corretamente requer muito mais tempo e conhecimentos de TI em comparação aos Sites do Azure e aos Serviços de Nuvem. Se estiver considerando as Máquinas Virtuais do Azure, assegure-se de levar em conta o esforço contínuo de manutenção necessário para corrigir, atualizar e gerenciar seu ambiente de máquinas virtuais.  

O diagrama a seguir ilustra o grau relativo de controle em relação à facilidade de uso de cada uma dessas opções de hospedagem na Web no Azure. 

![ChoicesDiagram][ChoicesDiagram]

##<a name="scenarios"></a>Cenários e recomendações

Abaixo estão alguns cenários de aplicação comuns com recomendações sobre qual opção de hospedagem na Web do Azure pode ser a mais adequada para cada um deles.

- [Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.](#onprem)
- [Preciso de uma maneira confiável de hospedar meu site corporativo que seja bem dimensionável e ofereça alcance global.](#corp)
- [Possuo um aplicativo IIS6 em execução no Windows Server 2003.](#iis6)
- [Sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.](#smallbusiness)
- [Sou um designer gráfico ou Web designer e desejo projetar e criar sites para meus clientes.](#designer)
- [Eu estou migrando o meu aplicativo multicamadas com um front-end da Web para a nuvem.](#multitier)
- [Meu aplicativo depende de ambientes Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.](#custom)
- [Meu site usa software livre e quero hospedá-lo no Azure.](#oss)
- [Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.](#lob)
- [Desejo hospedar uma API REST ou um serviço Web para clientes móveis.](#mobile)


### <a id="onprem"></a> Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.

Os Sites do Azure são uma ótima solução para aplicativos de negócios complexos. Eles permitem desenvolver aplicativos que são dimensionados automaticamente em uma plataforma com carga equilibrada, são protegidos pelo Active Directory e se conectam aos seus recursos locais. Eles facilitam o gerenciamento desses aplicativos por meio de um portal de gerenciamento e APIs de nível mundial, e permitem que você obtenha informações sobre como os clientes estão os utilizando com ferramentas de informações sobre os aplicativos. O novo recurso [Webjobs][] permite executar processos e tarefas em segundo plano como parte de sua camada Web, enquanto a conectividade híbrida e os [recursos VNET](../fundamentals-introduction-to-azure/#networking/) facilitam a conexão com recursos locais. Os Sites do Azure oferecem SLA de três 9s e permitem que você:

* Execute seus aplicativos de maneira confiável em uma plataforma de nuvem com autorrecuperação e autocorreção. 
* Dimensione automaticamente em uma rede global de datacenters.
* Faça backup e restaurações para recuperação de desastres. 
* Mantenha a conformidade com os padrões ISO, SOC2 e PCI.
* Integre-se com o Active Directory

### <a id="corp"></a> Preciso de uma maneira confiável de hospedar meu site corporativo que seja bem dimensionável e ofereça alcance global. 

Os Sites do Azure são uma ótima solução para hospedar aplicativos de negócios. Eles permitem que os sites sejam dimensionados rápida e facilmente para atender à demanda em uma rede global de datacenters. Eles oferecem alcance local, tolerância a falhas e gerenciamento de tráfego inteligente. Tudo em uma plataforma que oferece ferramentas de gerenciamento de nível mundial, permitindo que você obtenha informações sobre o funcionamento e o tráfego do site de forma rápida e fácil. Os Sites do Azure oferecem SLA de três 9s e permitem que você:

* Execute seus Websites de maneira confiável em uma plataforma de nuvem de autorrecuperação e autocorreção. 
* Dimensione automaticamente em uma rede global de datacenters.
* Faça backup e restaurações para recuperação de desastres. 
* Gerencie logs e o tráfego com ferramentas integradas. 
* Mantenha a conformidade com os padrões ISO, SOC2 e PCI.
* Integre-se com o Active Directory

### <a id="iis6"></a> Possuo um aplicativo IIS6 em execução no Windows Server 2003.

Os Sites do Azure fazem com que seja fácil evitar os custos com infraestrutura associados à migração de aplicativos IIS6 mais antigos. A Microsoft criou [ferramentas de migração de fácil uso e instruções de migração detalhadas](https://www.movemetowebsites.net/) que permitem que você verifique a compatibilidade e identifique mudanças que precisam ser feitas. A integração com Visual Studio, TFS e ferramentas de CMS comuns torna mais fácil implantar aplicativos IIS6 diretamente na nuvem. Uma vez implantado, o Portal de Gerenciamento do Azure oferece ferramentas de gerenciamento robustas que permitem reduzir verticalmente para gerenciar custos e expansões para atender à demanda, conforme necessário. Com a ferramenta de migração, você pode:

* Migrar rápida e facilmente seu aplicativo Web do Windows Server 2003 legado para a nuvem.
* Optar por deixar seu banco de dados SQL conectado localmente para criar um aplicativo híbrido. 
* Mover automaticamente seu banco de dados SQL juntamente com o aplicativo herdado. 

### <a id="smallbusiness"></a>Sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.

Os Sites do Azure são uma ótima solução para esse cenário, pois você pode começar a usá-los gratuitamente e, em seguida, adicionar mais recursos quando precisar deles. Cada site gratuito vem com um domínio fornecido pelo Azure (*sua_empresa*.azurewebsites.net) e a plataforma inclui ferramentas de implantação e gerenciamento integradas, bem como uma galeria de aplicativos que fazem com que seja fácil começar. Há muitos outros serviços e opções de dimensionamento que permitem que o site evolua com maior demanda dos usuários. Com os Sites do Azure, você pode:

- Começar com a camada livre e escalar verticalmente conforme necessário.
- Usar a Galeria de Aplicativos para configurar rapidamente aplicativos Web populares, como o WordPress.
- Adicionar mais recursos e serviços do Azure ao seu aplicativo conforme necessário.
- Proteger seu site com HTTPS.

### <a id="designer"></a> Sou um designer gráfico ou da Web e desejo projetar e criar sites para meus clientes

Para desenvolvedores da Web e Web designers, os Websites do Azure se integram facilmente com uma variedade de estruturas e ferramentas, incluem suporte à implantação para Git e FTP e oferecem uma integração coesa com ferramentas e serviços como o Visual Studio e o Banco de Dados SQL. Com os Sites, você pode:

- Usar ferramentas de linha de comando para [tarefas automatizadas][scripting].
- Trabalhar com idiomas populares como [.Net][dotnet], [PHP][], [Node.js][nodejs], e [Python][].
- Selecionar três níveis diferentes de dimensionamento para expandir até capacidades muito altas.
- Integração com outros serviços do Azure, como [Banco de dados SQL][sqldatabase], [Barramento de Serviço][servicebus] e [Armazenamento][], ou ofertas de parceiros da [Azure Store][azurestore], como MySQL e MongoDB.
- Integrar ferramentas como o Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Eu estou migrando o meu aplicativo multicamada com um front-end da Web para a nuvem

Se você estiver executando um aplicativo multicamada, como um servidor da Web que se comunica com um banco de dados, os Websites do Azure são uma boa opção que oferece uma integração coesa com o Banco de Dados SQL do Azure. E você pode usar o novo recurso WebJobs para executar processos de back-end.

Escolha o Serviço de Nuvem para uma ou mais de suas camadas se precisar de mais controle sobre o ambiente do servidor, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor.

Escolha as Máquinas Virtuais para uma ou mais de suas camadas se quiser usar sua própria imagem de máquina ou executar o software para servidores ou serviços que não podem ser configurados nos Serviços de Nuvem. 

### <a id="custom"></a>Meu aplicativo depende de ambientes Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.

Se seu aplicativo requer instalação complexa ou configuração do software e do sistema operacional, as Máquinas Virtuais provavelmente são a melhor solução. Com as Máquinas Virtuais, você pode:

- Usar a Galeria de Máquinas Virtuais para iniciar com um sistema operacional, como Windows ou Linux, e personalizá-lo conforme os requisitos de seu aplicativo. 
- Criar e carregar uma imagem personalizada de um servidor local existente para ser executado em uma máquina virtual no Azure. 

### <a id="oss"></a>O meu site usa software livre e quero hospedá-lo no Azure

Se sua estrutura de software livre tiver suporte nos Sites, as linguagens e estruturas de que seu aplicativo precisa serão configuradas automaticamente. Os Sites permitem que você:

- Trabalhe com diversas linguagens de software livre populares como [.NET][dotnet], [PHP][], [Node.js][nodejs] e [Python][]. 
- Instale o WordPress, Drupal, Umbraco, DNN e muitos outros aplicativos Web de terceiros. 
- Migre um aplicativo existente ou crie um novo aplicativo na Galeria de Aplicativos. 

Se sua estrutura de software livre não tiver suporte nos Sites, você pode executá-la em uma das duas outras opções de hospedagem na Web do Azure. Com os Serviços de Nuvem, você usa tarefas de inicialização para instalar e configurar qualquer software livre necessário que seja executado no Windows. Com as Máquinas Virtuais, você instala e configura o software na imagem da máquina, que pode ser baseada em Windows ou Linux. 

### <a id="lob"></a>Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa

Se você deseja criar um aplicativo de linha de negócios, seu site pode exigir acesso direto a serviços ou dados da rede corporativa. Isso é possível em Websites, Serviços de Nuvem e Máquinas Virtuais que usam o [serviço de Rede Virtual do Azure](/services/virtual-network/). Nos Websites, você pode usar o novo [recurso de integração do VNET](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite que seus aplicativos Azure sejam executados como se estivessem em sua rede corporativa.

### <a id="mobile"></a>Desejo hospedar uma API REST ou um serviço Web para clientes móveis

Serviços da Web baseados em HTTP permitem que você ofereça suporte a uma ampla variedade de clientes, inclusive clientes móveis. Estruturas como a API Web ASP.NET são integradas com o Visual Studio para fazer com que seja mais fácil criar e consumir serviços REST.  Esses serviços são expostos em um ponto de extremidade da Web, portanto, é possível usar qualquer técnica de hospedagem na Web no Azure para dar suporte a esse cenário. No entanto, os Sites são uma ótima opção para hospedar APIs REST. Com os Sites, você pode:

- Criar rapidamente um site para hospedar o serviço Web HTTP em um dos datacenters globalmente distribuídos do Azure.
- Migrar serviços existentes ou criar novos.
- Obter o SLA de disponibilidade com uma única instância ou escalar horizontalmente para várias máquinas exclusivas. 
- Usar o site publicado para fornecer APIs REST para quaisquer clientes HTTP, incluindo clientes móveis.

##<a name="features"></a>Comparação de recursos

A tabela a seguir compara os recursos dos Sites, Serviços de Nuvem e Machines Virtuais para ajudá-lo a fazer a melhor escolha. Para obter as informações mais recentes sobre o SLA para cada opção, consulte os [Acordos de Nível de Serviço do Azure](/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Sites</th>
   <th align="left" valign="middle">Serviços de Nuvem (funções Web)</th>
   <th align="left" valign="middle">Máquinas Virtuais</th>
   <th align="left" valign="middle">Observações</th>
</tr>
<tr>
   <td valign="middle"><p>Implantação quase instantânea</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">Implantar um aplicativo ou atualização de um aplicativo em um Serviço de Nuvem ou criar uma Máquina Virtual leva no mínimo alguns minutos; implantar um aplicativo em um site leva segundos.</td>
</tr>
<tr>
   <td valign="middle"><p>Escalar verticalmente para máquinas maiores sem reimplantação</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Instâncias do servidor Web compartilham conteúdos e configuração, o que significa que você não precisa implantar ou configurar novamente conforme realiza o dimensionamento.</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Vários ambientes de implantação (produção e preparo)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Gerenciamento de atualização automática do sistema operacional</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Alternância ininterrupta entre plataformas (alterne facilmente entre 32 e 64 bits)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Implantar código com GIT, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Implantar o código com a Implantação da Web</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">Os Serviços de Nuvem dão suporte ao uso da Implantação da Web para implantar atualizações em instâncias de função individuais. No entanto, eles não podem ser usados para a implantação inicial de uma função, e se a Implantação da Web for usada para uma atualização, você precisará implantar separadamente em cada instância de uma função. Várias instâncias são necessárias para se qualificar para o SLA de Serviço de Nuvem para ambientes de produção.</td>
</tr>
<tr>
   <td valign="middle"><p>Suporte do WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Acesso a serviços como o Barramento de Serviço, Armazenamento, Banco de Dados SQL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Camada de serviços Web ou Web hospedada de uma arquitetura multicamada</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Camada intermediária de host de uma arquitetura multicamada</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Os Websites podem hospedar facilmente uma camada média da API REST, e o recurso <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> dos Websites pode hospedar tarefas de processamento em segundo plano. Você pode executar o WebJobs em um site dedicado para alcançar a escalabilidade independente para a camada.</td>
</tr>
<tr>
   <td valign="middle"><p>Suporte integrado do MySQL como serviço</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Os Serviços de Nuvem podem integrar o MySQL como serviço por meio de ofertas do ClearDB, mas não como parte do fluxo de trabalho do Portal de Gerenciamento.</td>
</tr>
<tr>
   <td valign="middle"><p>Suporte para ASP.NET, ASP clássico, Node.js, PHP, Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Escalar horizontalmente para várias instâncias sem reimplantação</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Máquinas Virtuais podem ser escaladas horizontalmente para várias instâncias, mas os serviços em execução nessas máquinas devem ser escritos para lidar com este dimensionamento. Você precisa configurar um balanceador de carga para rotear solicitações entre máquinas e criar um Grupo de Afinidade para impedir reinícios simultâneos de todas as instâncias devido a manutenções ou falhas de hardware.</td>
</tr>
<tr>
   <td valign="middle"><p>Suporte para SSL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Para os Sites, o SSL para nomes de domínio personalizados tem suporte somente no modo Básico e Padrão. Para obter informações sobre como usar SSL com Websites, consulte <a href="../web-sites-configure-ssl-certificate/">Configurar um certificado SSL para um Website do Azure</a>.</td>
</tr>
<tr>
   <td valign="middle"><p>Integração do Visual Studio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Depuração remota</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Implantação de código com TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Isolamento de rede com a <a href="/services/virtual-network/">Rede Virtual do Azure</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Consulte também <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integração de Rede Virtual dos Websites do Azure</a></td>
</tr>
<tr>
   <td valign="middle"><p>Suporte a <a href="/services/traffic-manager/">Gerenciador de Tráfego do Azure</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Monitoramento do ponto de extremidade integrado</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Acesso da área de trabalho remota a servidores</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Instalação de qualquer MSI personalizado</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Capacidade de definir/executar tarefas de inicialização</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Capacidade de ouvir eventos de ETW</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.


## <a id="nextsteps"></a> Próximas etapas

Para obter mais informações sobre as três opções de hospedagem na Web, consulte os recursos a seguir:

* [Apresentando o Azure](../fundamentals-introduction-to-azure/)
* [Opções de hospedagem de computação fornecidas pelo Azure](../fundamentals-application-models/)

Para começar a usar as opções escolhidas para seu aplicativo, consulte os seguintes recursos:

* [Sites do Azure](/documentation/services/websites/)
* [Serviços de nuvem do Azure](/documentation/services/cloud-services/)
* [Máquinas Virtuais do Azure](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Sites do Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Serviços de Nuvem]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configurar um certificado SSL para um Site do Azure]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Armazenamento]: http://www.windowsazure.com/documentation/services/storage/

<!--HONumber=47-->

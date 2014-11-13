<properties pageTitle="Compara&ccedil;&atilde;o de Sites do Azure, Servi&ccedil;os de Nuvem e M&aacute;quinas virtuais" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Saiba quando usar Websites do Azure, Servi&ccedil;os de Nuvem e M&aacute;quinas Virtuais para hospedar aplicativos Web." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Servi&ccedil;os de Nuvem" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Comparação de Webites do Azure, Serviços de Nuvem e Máquinas Virtuais

## Visão geral

O Azure oferece várias maneiras de hospedar websites: [Websites do Azure][Websites do Azure], [Serviços de Nuvem][Serviços de Nuvem] e [Máquinas Virtuais][Máquinas Virtuais]. Este artigo ajuda você a entender as opções e fazer a escolha certa para seu aplicativo Web.

Os Websites do Azure são a melhor escolha para a maioria dos aplicativos Web. A implantação e o gerenciamento estão integrados na plataforma, os sites podem ser dimensionados rapidamente para suportar altas cargas de tráfego e o gerenciador de balanceamento de carga e tráfego integrado oferece alta disponibilidade. Você pode mover sites existentes para Websites do Azure facilmente com uma [ferramenta de migração online][ferramenta de migração online], usar um aplicativo de software livre da Galeria de Aplicativos Web ou criar um novo site usando a estrutura e as ferramentas de sua escolha. O recurso [WebJobs][WebJobs] (atualmente em visualização) facilita a adição do processamento de tarefas em segundo plano em seu aplicativo.

Se precisar de mais controle sobre o ambiente do servidor da Web, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor, os Serviços de Nuvem do Azure geralmente são a melhor opção.

Se você tiver um aplicativo existente que necessitaria de modificações substanciais para ser executado em Websites do Azure ou nos Serviços de Nuvem do Azure, você pode escolher as Máquinas Virtuais do Azure para simplificar a migração para a nuvem. No entanto, configurar, proteger e manter as máquinas virtuais corretamente requer muito tempo e conhecimento de TI em comparação aos Websites do Azure e aos Serviços de Nuvem. Se estiver considerando as Máquinas Virtuais do Azure, assegure-se de levar em conta o contínuo esforço de manutenção necessário para corrigir, atualizar e gerenciar seu ambiente de máquinas virtuais.

O diagrama a seguir ilustra o grau relativo de controle em relação à facilidade de uso de cada uma dessas opções de hospedagem na Web no Azure.

![ChoicesDiagram][ChoicesDiagram]

## Sumário

-   [Cenários e recomendações][Cenários e recomendações]
-   [Comparação de recursos][Comparação de recursos]
-   [Próximas etapas][Próximas etapas]

## <a name="scenarios"></a>Cenários e recomendações

Abaixo estão alguns cenários de aplicação comuns com recomendações sobre qual opção de hospedagem na Web do Azure pode ser a mais adequada para cada um deles.

-   [Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.][Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.]
-   [Preciso de uma maneira confiável de hospedar meu Website corporativo que seja bem escalada e ofereça alcance global.][Preciso de uma maneira confiável de hospedar meu Website corporativo que seja bem escalada e ofereça alcance global.]
-   [Possuo um aplicativo IIS6 em execução no Windows Server 2003.][Possuo um aplicativo IIS6 em execução no Windows Server 2003.]
-   [Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.][Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.]
-   [Sou um designer gráfico ou Web designer e desejo projetar e criar Websites para meus clientes.][Sou um designer gráfico ou Web designer e desejo projetar e criar Websites para meus clientes.]
-   [Eu estou migrando o meu aplicativo multicamadas com um front-end da Web para a nuvem.][Eu estou migrando o meu aplicativo multicamadas com um front-end da Web para a nuvem.]
-   [Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.][Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.]
-   [O meu site usa software livre e quero hospedá-lo no Azure.][O meu site usa software livre e quero hospedá-lo no Azure.]
-   [Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.][Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.]
-   [Desejo hospedar uma API REST ou um serviço da Web para clientes móveis.][Desejo hospedar uma API REST ou um serviço da Web para clientes móveis.]

### <span id="onprem"></span></a> Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.

Os Websites do Azure são uma ótima solução para aplicativos de negócios complexos. Eles permitem desenvolver aplicativos que são escalados automaticamente em uma plataforma com carga equilibrada, são protegidos pelo Active Directory e se conectam aos seus recursos no local. Eles facilitam o gerenciamento desses aplicativos por meio de um portal de gerenciamento e APIs de nível mundial, e permitem que você obtenha informações sobre como os clientes estão os utilizando com ferramentas de informações sobre os aplicativos. O novo recurso [Webjobs][WebJobs] permite executar processos e tarefas em segundo plano como parte de sua camada da Web, enquanto que a conectividade híbrida e os [recursos de VNET][recursos de VNET] facilitam a conexão de volta aos recursos no local. Os Websites do Azure oferecem SLA de três 9s e permitem que você:

-   Execute seus aplicativos de maneira confiável em uma plataforma de nuvem de autorrecuperação e autocorreção.
-   Escale automaticamente em uma rede global de datacenters.
-   Faça backup e restaurações para recuperação de desastres.
-   Esteja de acordo com os padrões ISO, SOC2 e PCI.
-   Integre-se com o Active Directory

### <span id="corp"></span></a> Preciso de uma maneira confiável de hospedar meu Website corporativo que seja bem escalada e ofereça alcance global.

Os Websites do Azure são uma ótima solução para hospedar Websites corporativos. Ele permite que os sites sejam escalados rápida e facilmente para atender à demanda em uma rede global de datacenters. Ele oferece alcance local, tolerância a falhas e gerenciamento de tráfego inteligente. Tudo em uma plataforma que oferece ferramentas de gerenciamento de nível mundial, permitindo que você obtenha informações sobre o funcionamento e o tráfego do site de forma rápida e fácil. Os Websites do Azure oferecem SLA de três 9s e permitem que você:

-   Execute seus Websites de maneira confiável em uma plataforma de nuvem de autorrecuperação e autocorreção.
-   Escale automaticamente em uma rede global de datacenters.
-   Faça backup e restaurações para recuperação de desastres.
-   Gerencie logs e o tráfego com ferramentas integradas.
-   Esteja de acordo com os padrões ISO, SOC2 e PCI.
-   Integre-se com o Active Directory

### <span id="iis6"></span></a> Possuo um aplicativo IIS6 em execução no Windows Server 2003.

Os Websites do Azure fazem com que seja fácil evitar os custos com infraestrutura associados à migração de aplicativos IIS6 mais antigos. A Microsoft criou [ferramentas de migração de fácil uso e instruções de migração detalhadas][ferramentas de migração de fácil uso e instruções de migração detalhadas] que permitem que você verifique a compatibilidade e identifique mudanças que precisam ser feitas. A integração com Visual Studio, TFS e ferramentas de CMS comuns torna mais fácil implantar aplicativos IIS6 diretamente na nuvem. Uma vez implantado, o portal de gerenciamento do Azure oferece ferramentas de gerenciamento robustas que permitem realizar uma redução vertical para gerenciar custos e o dimensionamento para atender à demanda conforme necessário. Com a ferramenta de migração, você pode:

-   Migrar rápida e facilmente seu aplicativo Web do Windows Server 2003 legado para a nuvem.
-   Escolha deixar seu banco de dados SQL conectado localmente para criar um aplicativo híbrido.
-   Mova automaticamente seu banco de dados SQL juntamente com o aplicativo legado.

### <span id="smallbusiness"></span></a> Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.

Os Websites do Azure são uma ótima solução para esse cenário, pois você pode começar a usá-los gratuitamente e, em seguida, adicionar mais recursos quando precisar deles. Cada Website gratuito vem com um domínio fornecido pelo Azure (*sua\_empresa*.azurewebsites.net) e a plataforma inclui ferramentas de implantação e gerenciamento integradas, bem como uma galeria de aplicativos que fazem com que seja fácil começar. Há muitos outros serviços e opções de dimensionamento que permite que o site evolua com maior demanda do usuário. Com os Websites do Azure, você pode:

-   Começar com a camada livre e, em seguida, dimensionar conforme necessário.
-   Use a Galeria de Aplicativos para a rápida criação de aplicativos Web populares, como o WordPress.
-   Adicione mais recursos e serviços do Azure para seu aplicativo conforme necessário.
-   Proteja seu Website com HTTPS.

### <span id="designer"></span></a> Sou um designer gráfico ou Web designer e desejo projetar e criar Websites para meus clientes

Para desenvolvedores da Web e Web designers, os Websites do Azure se integram facilmente com uma variedade de estruturas e ferramentas, incluem suporte à implantação para Git e FTP e oferecem uma integração coesa com ferramentas e serviços como o Visual Studio e o Banco de Dados SQL. Com Sites, você pode:

-   Usar ferramentas de linha de comando para [tarefas automatizadas][tarefas automatizadas].
-   Trabalhar com idiomas populares como [.Net][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Selecione três níveis diferentes de dimensionamento para aumentar proporcionalmente capacidades muito altas.
-   Integração com outros serviços do Azure, como [Banco de Dados SQL][Banco de Dados SQL], [barramento de serviço][barramento de serviço] e [armazenamento][armazenamento], ou ofertas de parceiro do [Azure Store][Azure Store], como MySQL and MongoDB.
-   Integração com ferramentas como o Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <span id="multitier"></span></a> Eu estou migrando o meu aplicativo multicamada com um front-end da Web para a nuvem

Se você estiver executando um aplicativo multicamada, como um servidor da Web que se comunica com um banco de dados, os Websites do Azure são uma boa opção que oferece uma integração coesa com o Banco de Dados SQL do Azure. E você pode usar o novo recurso WebJobs (na visualização) para executar processos de back-end.

Escolha Serviço de Nuvem para uma ou mais de suas camadas se precisar de mais controle sobre o ambiente do servidor, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor.

Escolha Máquinas Virtuais para uma ou mais de suas camadas se quiser usar sua própria imagem de máquina ou executar o software do servidor ou serviços que não podem ser configurados nos Serviços de Nuvem.

### <span id="custom"></span></a>Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.

Se seu aplicativo requer instalação complexa ou configuração do software e do sistema operacional, máquinas virtuais é provavelmente a melhor solução. Com Máquinas Virtuais, você pode:

-   Usar a Galeria de Máquina Virtual para iniciar com um sistema operacional, como Windows ou Linux, e personalizar para seus requisitos de aplicativo.
-   Criar e carregar uma imagem personalizada de um servidor local existente para ser executado em uma máquina virtual no Azure.

### <span id="oss"></span></a>O meu site usa software livre e quero hospedá-lo no Azure

Se sua estrutura de software livre for suportada nos Websites, as linguagens e estruturas de que seu aplicativo precisa serão configuradas automaticamente. Os Websites permitem que você:

-   Use vários idiomas open source populares como [.NET][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Instale o WordPress, Drupal, Umbraco, DNN e muitos outros aplicativos Web de terceiros.
-   Migre um aplicativo existente ou crie um novo aplicativo na Galeria de Aplicativos.

Se sua estrutura de software livre não for suportada nos Websites, você pode executá-la em uma das duas outras opções de hospedagem na Web do Azure. Com os Serviços de Nuvem, você usa tarefas de inicialização para instalar e configurar qualquer software livre necessário que é executado no Windows. Com as Máquinas Virtuais, você instala e configura o software na imagem da máquina, que pode ser baseada em Windows ou Linux.

### <span id="lob"></span></a> Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa

Se você deseja criar um aplicativo de linha de negócios, seu site pode exigir acesso direto a serviços ou dados da rede corporativa. Isso é possível em Websites, Serviços de Nuvem e Máquinas Virtuais que usam o [serviço de Rede Virtual do Azure][serviço de Rede Virtual do Azure]. Nos Websites, você pode usar o novo [recurso de integração do VNET][recurso de integração do VNET], que permite que seus aplicativos Azure sejam executados como se estivessem em sua rede corporativa.

### <span id="mobile"></span></a> Desejo hospedar uma API REST ou um serviço da Web para clientes móveis

Serviços da Web baseados em HTTP permitem que você ofereça suporte a uma ampla variedade de clientes, inclusive clientes móveis. Estruturas como a API da Web do ASP.NET são integradas com o Visual Studio para fazer com que seja mais fácil criar e consumir serviços REST. Esses serviços são expostos a partir de um ponto de extremidade da Web, portanto, é possível usar qualquer técnica de hospedagem na Web no Azure para dar suporte a esse cenário. No entanto, os Websites são uma ótima opção para hospedar APIs REST. Com Sites, você pode:

-   Criar rapidamente um Website para hospedar o serviço da Web HTTP em um dos datacenters globalmente distribuídos do Azure.
-   Migrar serviços existentes ou criar novos.
-   Obter o SLA de disponibilidade com uma única instância ou dimensionar para várias máquinas exclusivas.
-   Use o site publicado para fornecer APIs REST para quaisquer clientes HTTP, incluindo clientes móveis.

## <a name="features"></a>Comparação de Recursos

A tabela a seguir compara os recursos de Sites, serviços de nuvem e máquinas virtuais para ajudá-lo a fazer a melhor escolha. Para obter as informações mais recentes sobre SLA para cada opção, consulte os [Acordos de Nível de Serviço do Azure][Acordos de Nível de Serviço do Azure].

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Sites</th>
<th align="left">Serviços de nuvem (funções Web)</th>
<th align="left">Máquinas Virtuais</th>
<th align="left">Observações</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Implantação quase instantânea</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">Implantar um aplicativo ou uma atualização de um aplicativo em um Serviço de Nuvem ou criar uma Máquina Virtual leva no mínimo alguns minutos; implantar um aplicativo em um Website leva segundos.</td>
</tr>
<tr class="even">
<td align="left"><p>Dimensionar para máquinas maiores sem reimplantação</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Instâncias do servidor da Web compartilham conteúdos e configuração, o que significa que você não precisa implantar ou configurar novamente conforme realiza o dimensionamento.</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Vários ambientes de implantação (produção e preparo)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Gerenciamento de atualização automática do sistema operacional</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Alternância ininterrupta entre plataformas (alterne facilmente entre 32 bits e 64 bits)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Implantar código com GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Implantar o código com a implantação da Web</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">Os Serviços de Nuvem oferecem suporte ao uso da Implantação da Web para implantar atualizações em instâncias de função individuais. No entanto, eles não podem ser usados para a implantação inicial de uma função, e se a Implantação da Web for usada para uma atualização, você precisará implantar separadamente em cada instância de uma função. Várias instâncias são necessárias para se qualificar para SLA de Serviço de Nuvem para ambientes de produção.</td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte do WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Acesso a serviços como o Service Bus, Armazenamento, Banco de Dados SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Camada de serviços da Web ou Web hospedada de uma arquitetura multicamada</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Camada intermediária de host de uma arquitetura multicamada</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Os Websites podem hospedar facilmente uma camada média da API REST, e o recurso <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> dos Websites (atualmente na visualização) pode hospedar tarefas de processamento em segundo plano. Você pode executar o WebJobs em um Website dedicado para alcançar escalabilidade independente para a camada.</td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte integrado do MySQL como serviço</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Os Serviços de Nuvem podem integrar o MySQL como serviço por meio de ofertas do ClearDB, mas não como parte do fluxo de trabalho do Portal de Gerenciamento.</td>
</tr>
<tr class="even">
<td align="left"><p>Suporte para ASP.NET, classic ASP, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Dimensionamento para várias instâncias sem reimplantação</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Máquinas Virtuais podem ser dimensionadas para várias instâncias, mas os serviços em execução nessas máquinas devem ser escritos para lidar com este dimensionamento. Você precisa configurar um balanceador de carga para rotear solicitações entre máquinas e criar um Grupo de Afinidade para impedir reinícios simultâneos de todas as instâncias devido a manutenções ou falhas de hardware.</td>
</tr>
<tr class="even">
<td align="left"><p>Suporte para SSL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Para Websites, o SSL para nomes de domínio personalizados só tem suporte no modo Básico e Padrão. Para obter informações sobre como usar SSL com Websites, consulte <a href="../web-sites-configure-ssl-certificate/">Configurar um certificado SSL para um Website do Azure</a>.</td>
</tr>
<tr class="odd">
<td align="left"><p>Integração do Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Depuração Remota</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Implantar código com TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Isolamento de rede com a <a href="/pt-br/services/virtual-network/">Rede Virtual do Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Consulte também <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integração de Rede Virtual dos Websites do Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte a <a href="/pt-br/services/traffic-manager/">Gerenciador de Tráfego do Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Monitoramento do ponto de extremidade integrado</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Acesso remoto a área de trabalho para servidores</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Instalação de qualquer MSI personalizado</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Capacidade de definir/executar tarefas de inicialização</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Capacidade de usar eventos ETW personalizados para monitoramento e depuração</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>Próximas etapas

Para obter mais informações sobre as três opções de hospedagem na Web, consulte os recursos a seguir:

-   [Apresentando o Azure][Apresentando o Azure]
-   [Modelos de execução do Azure][Modelos de execução do Azure]

Para começar a usar as opções escolhidas para seu aplicativo, consulte os seguintes recursos:

-   [Websites do Azure][2]
-   [Serviços de nuvem do Azure][Serviços de nuvem do Azure]
-   [Máquinas Virtuais do Azure][Máquinas Virtuais do Azure]

  [Websites do Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Serviços de Nuvem]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ferramenta de migração online]: https://www.migratetoazure.net/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Cenários e recomendações]: #scenarios
  [Comparação de recursos]: #features
  [Próximas etapas]: #nextsteps
  [Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos no local.]: #onprem
  [Preciso de uma maneira confiável de hospedar meu Website corporativo que seja bem escalada e ofereça alcance global.]: #corp
  [Possuo um aplicativo IIS6 em execução no Windows Server 2003.]: #iis6
  [Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.]: #smallbusiness
  [Sou um designer gráfico ou Web designer e desejo projetar e criar Websites para meus clientes.]: #designer
  [Eu estou migrando o meu aplicativo multicamadas com um front-end da Web para a nuvem.]: #multitier
  [Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.]: #custom
  [O meu site usa software livre e quero hospedá-lo no Azure.]: #oss
  [Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.]: #lob
  [Desejo hospedar uma API REST ou um serviço da Web para clientes móveis.]: #mobile
  [recursos de VNET]: ../fundamentals-networking/
  [ferramentas de migração de fácil uso e instruções de migração detalhadas]: https://www.movemetowebsites.net/
  [tarefas automatizadas]: http://www.windowsazure.com/pt-br/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/pt-br/develop/net/
  [PHP]: http://www.windowsazure.com/pt-br/develop/php/
  [Node.js]: http://www.windowsazure.com/pt-br/develop/nodejs/
  [Python]: http://www.windowsazure.com/pt-br/develop/python/
  [Banco de Dados SQL]: http://www.windowsazure.com/pt-br/documentation/services/sql-database/
  [barramento de serviço]: http://www.windowsazure.com/pt-br/documentation/services/service-bus/
  [armazenamento]: http://www.windowsazure.com/pt-br/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/pt-br/gallery/store/
  [serviço de Rede Virtual do Azure]: /pt-br/services/virtual-network/
  [recurso de integração do VNET]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
  [Acordos de Nível de Serviço do Azure]: /pt-br/support/legal/sla/
  [Apresentando o Azure]: ../fundamentals-introduction-to-azure/
  [Modelos de execução do Azure]: ../fundamentals-application-models/
  [2]: /pt-br/documentation/services/websites/
  [Serviços de nuvem do Azure]: /pt-br/documentation/services/cloud-services/
  [Máquinas Virtuais do Azure]: /pt-br/documentation/services/virtual-machines/

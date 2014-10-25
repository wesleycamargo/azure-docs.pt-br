<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Websites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Comparação de Sites do Azure, Serviços de Nuvem e Máquinas virtuais

O Azure oferece várias maneiras de hospedar seus aplicativos da Web, como [Sites do Web do Azure][Sites do Web do Azure], [Serviços de Nuvem][Serviços de Nuvem] e [Máquinas Virtuais][Máquinas Virtuais]. Após analisar as várias opções, pode ser que você não saiba qual delas atende melhor às suas necessidades ou você pode estar incerto sobre conceitos como IaaS vs PaaS. Este artigo ajuda você a entender as opções e fazer a escolha certa para seu cenário da Web. Embora todas as três opções permitem que você execute aplicativos da Web altamente escalável no Azure, existem diferenças que podem ajudar a guiar sua decisão.

Em muitas situações, os Sites do Azure são a melhor opção. Ele fornece opções simples e flexíveis para implantação e gerenciamento e é capaz de hospedar sites de alto volume. Você pode criar rapidamente um novo site com softwares populares, como WordPress, da Galeria de Aplicativos da Web, ou pode mover um site existente para o sites do Azure. Usando o [SDK WebJobs do Azure][SDK WebJobs do Azure] (atualmente no modo de visualização), você também pode adicionar processamento de trabalho em segundo plano.

Você também tem a opção para hospedar aplicativos da Web em serviços de nuvem do Azure ou máquinas de virtuais do Azure. Essas opções são boas escolhas quando sua camada Web requer o nível adicional de controle e personalização que eles fornecem; no entanto, esse controle maior tem um custo de maior complexidade na criação de aplicativos, gerenciamento e implantação. O diagrama a seguir ilustra as compensações entre as três opções.

![ChoicesDiagram][ChoicesDiagram]

Os sites são mais fáceis de instalar, gerenciar e monitorar, mas há menos opções de configuração. O ponto-chave é que, quando você não tem uma razão convincente para colocar seu front-end da Web em máquinas virtuais ou serviços de nuvem, você usa Sites do Azure. O restante deste documento fornece as informações necessárias para tomar uma decisão consciente. Isso inclui:

-   [Cenários][Cenários]
-   [Resumos de serviço][Resumos de serviço]
-   [Comparação de Recursos][Comparação de Recursos]

## <a name="scenarios"></a>Cenários

### Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.

Os Sites do Azure (WAWS) é uma ótima solução para esse cenário, pois você pode começar a usá-lo gratuitamente e, em seguida, adicionar mais recursos quando precisar deles. Por exemplo, cada site gratuito vem com um domínio fornecido pelo Azure (*sua\_empresa*.azurewebsites.net). Quando você estiver pronto para começar a usar seu próprio domínio, é possível adicionar até US$ 9,80 por mês (a partir de 1/2014). Há muitos outros serviços e opções de dimensionamento que permite que o site evolua com maior demanda do usuário. Com **Sites do Azure**, você pode:

-   Começar com a camada livre e, em seguida, dimensionar conforme necessário.
-   Use a Galeria de Aplicativos para rápida instalação de aplicativos da Web populares, como o WordPress.
-   Adicione mais recursos e serviços do Azure para seu aplicativo conforme necessário.
-   Proteger seu site com HTTPS usando o certificado fornecido com o nome de domínio *sua\_empresa*.azurewebsites.net.

### Tenho um designer gráfico ou da Web e desejo projetar e criar sites para meus clientes

Para desenvolvedores da Web, os Sites do Azure oferece o que você precisa para criar aplicativos da Web sofisticados. Sites oferecem integração total com ferramentas como o banco de dados do Visual Studio e SQL. Com **Sites**, os desenvolvedores podem:

-   Usar ferramentas de linha de comando para [tarefas automatizadas][tarefas automatizadas].
-   Trabalhar com idiomas populares como [.Net][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Selecione três níveis diferentes de dimensionamento para aumentar proporcionalmente capacidades muito altas.
-   Integração com outros serviços do Azure, como [Banco de Dados SQL][Banco de Dados SQL], [barramento de serviço][barramento de serviço] e [armazenamento][armazenamento], ou ofertas de parceiro do [Azure Store][Azure Store], como MySQL and MongoDB.
-   Integração com ferramentas como o Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### Eu estou migrando o meu aplicativo multicamada com um front-end da Web para a nuvem

Se você estiver executando um aplicativo multicamada, como um servidor da Web que se comunica com um servidor de banco de dados para armazenar e recuperar dados do site, você tem várias opções no Azure. Estas opções arquiteturais inclui os sites, os serviços de nuvem e as máquinas virtuais. Primeiro, **Sites** é uma boa opção para a camada da Web da sua solução e pode ser usado com o banco de dados do Azure SQL para criar uma arquitetura de duas camadas. Sites também permitem executar o plano de fundo ou processos de longa duração usando a visualização do SDK WebJobs do Azure. Se você precisa de arquitetura mais complexa ou opções de dimensionamento mais flexíveis, os serviços de nuvem ou máquinas virtuais são uma opção melhor.

Os **serviços de nuvem** permitem que você:

-   Hospedar serviços da Web, camada intermediária e back-end em funções Web e de trabalho dimensionáveis.
-   Hospede somente os serviços de camada intermediária e backend em funções de trabalho, mantendo o front-end em sites do Azure.
-   Dimensione serviços de front-end e back-end independentemente.

As **máquinas virtuais** permitem que você:

-   Migre mais facilmente ambientes altamente personalizados como uma imagem de máquina virtual.
-   Execute um software ou serviços que não podem ser configurados em sites ou serviços de nuvem.

### Meu aplicativo depende em ambientes do Windows ou Linux altamente personalizados

Se seu aplicativo requer instalação complexa ou configuração do software e do sistema operacional, máquinas virtuais é provavelmente a melhor solução. Com **Máquinas Virtuais** , você pode:

-   Usar a Galeria de Máquina Virtual para iniciar com um sistema operacional, como Windows ou Linux, e personalizar para seus requisitos de aplicativo.
-   Criar e carregar uma imagem personalizada de um servidor local existente para ser executado em uma máquina virtual no Azure.

### O meu site usa software de fonte aberta e quero hospedá-lo no Azure

Todas as três opções permitem que você hospede idiomas e estruturas de open source. Os **serviços de nuvem** requerem o uso de tarefas de inicialização para instalar e configurar qualquer software open source necessário que é executado no Windows. Com **máquinas virtuais**, instale e configure o software na imagem da máquina, que pode ser Windows ou baseados em Linux. Se a estrutura de open source é o suporte em Sites, isso fornece uma maneira mais simples para hospedar esses tipos de aplicativos, pois Sites podem ser configurados automaticamente com os idiomas e as estruturas necessárias para seu aplicativo. Os **Sites** permitem que você:

-   Use vários idiomas open source populares como [.NET][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Instale o WordPress, Drupal, Umbraco, DNN e muitos outros aplicativos da Web de terceiros.
-   Migre um aplicativo existente ou crie um novo aplicativo na Galeria de Aplicativos.

### Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa

Se você deseja criar um aplicativo de linha de negócios, seu site pode exigir acesso direto a serviços ou dados da rede corporativa. Isso é possível em **Sites**, **Serviços de Nuvem** e **Máquinas virtuais**. Existem diferenças na abordagem que você assumir, que incluem o seguinte:

-   Sites podem seguramente se conectar aos recursos locais com o uso do relé do Service Bus. Isso permite que os serviços na rede corporativa executem tarefas em nome do aplicativo da Web sem mover tudo para a nuvem ou configurar uma rede virtual.
-   Serviços de Nuvem e Máquinas Virtuais podem tirar vantagem da Rede Virtual. Na verdade, a Rede Virtual permite que máquinas em execução no Azure se conectem a uma rede local. O Azure torna-se uma extensão do seu datacenter corporativo.

### Desejo hospedar uma API REST ou um serviço da Web para clientes móveis

Serviços da Web baseados em HTTP permite que você ofereça suporte a uma ampla variedade de clientes, inclusive clientes móveis. Estruturas como a API da Web do ASP.NET são integradas com o Visual Studio para torná-lo mais fácil de criar e consumir serviços REST. Esses serviços são expostos a partir de um ponto de extremidade da Web, portanto, é possível usar qualquer técnica de hospedagem na Web no Azure para dar suporte a esse cenário. No entanto, **Sites** são uma ótima opção para hospedar APIs REST. Com Sites, você pode:

-   Criar rapidamente um Site para hospedar o serviço da Web HTTP em um dos centros de dados globalmente distribuídos do Azure.
-   Migrar serviços existentes ou criar novos, potencialmente tirando proveito do API da Web do ASP.NET no Visual Studio.
-   Obter o SLA de disponibilidade com uma única instância ou dimensionar para várias máquinas exclusivas.
-   Use o site publicado para fornecer APIs REST para quaisquer clientes HTTP, incluindo clientes móveis.

## <a name="services"></a>Resumos de serviço

Os [Sites do Azure][Sites do Web do Azure] permitem criar sites altamente dimensionáveis rapidamente no Azure. Você pode usar o Portal do Azure ou as ferramentas de linha de comando para configurar um site com linguagens populares, como o .NET, PHP, Node.js e Python. Estruturas de suporte já foram implantadas e não exigem mais etapas de instalação. A galeria de Sites do Azure contém muitos aplicativos de terceiros, como Drupal e WordPress, bem como as estruturas de desenvolvimento, como Django e CakePHP. Depois de criar um site, você pode migrar um site existente ou criar um site totalmente novo. Os Sites eliminam a necessidade de gerenciar o hardware físico e também fornece várias opções de dimensionamento. Você pode mover de um modelo compartilhado de multilocação para um modo padrão onde máquinas exclusivas servem o tráfego de entrada. Os Sites também permitem a integração com outros serviços do Azure, como Banco de Dados SQL, o Service Bus e o Armazenamento. Usando a visualização do [SDK WebJobs do Azure][SDK WebJobs do Azure], você também pode adicionar processamento em segundo plano. Em resumo, Sites do Azure facilitam o foco no desenvolvimento de aplicativos, oferecendo suporte a uma grande variedade de linguagens, aplicativos open source e metodologias de implantação (FTP, Git, Web Deploy ou TFS). Se você não tiver requisitos especializados que exigem serviços de nuvem ou máquinas virtuais, um Site do Azure é provavelmente a melhor opção.

[Serviços de Nuvem][Serviços de Nuvem] permitem criar aplicativos da Web altamente disponíveis e escalonáveis em um ambiente de PaaS (plataforma como serviço) sofisticado. Ao contrário dos Sites, um serviço de nuvem é criado primeiro em um ambiente de desenvolvimento, como Visual Studio, antes de ser implantado no Azure. Estruturas, como PHP, requerem tarefas ou etapas de implantação personalizadas que instalam a estrutura na inicialização da função. A principal vantagem dos serviços de nuvem é a capacidade de suportar as mais complexas arquiteturas multicamadas. Um serviço de nuvem único poderia consistir de uma função Web de front-end e uma ou mais funções de trabalho. Cada camada pode ser dimensionada de forma independente. Há também um nível maior de controle sobre sua infraestrutura de aplicativos da Web. Por exemplo, você pode colocar sua área de trabalho remota em computadores executando instâncias da função. Também é possível criar scripts IIS mais avançados e alterações de configuração de máquina que são executados na inicialização da função, incluindo tarefas que exigem controle do administrador.

[Máquinas virtuais][Máquinas Virtuais] permitem que você execute aplicativos da Web em máquinas virtuais no Azure. Esse recurso é também conhecido como a Infraestrutura como um Serviço (IaaS). Crie novas máquinas do Windows Server ou Linux por meio do portal ou faça o upload de uma imagem de máquina virtual existente. Máquinas virtuais oferecem mais controle sobre o sistema operacional, a configuração e os serviços e software instalados. Isso é uma boa opção para migrar rapidamente aplicativos da Web locais complexos para a nuvem porque as máquinas podem ser movidas como um todo. Com redes virtuais, você também pode conectar essas máquinas virtuais para redes corporativas locais. Como ocorre com os serviços de nuvem, você tem acesso remoto a essas máquinas e a capacidade de executar as alterações de configuração no nível administrativo. No entanto, ao contrário dos Sites e dos Serviços de Nuvem, você deve gerenciar sua arquitetura de aplicativos e imagens de máquina virtual completamente no nível de infraestrutura. Um exemplo básico é que você deve aplicar suas próprias correções ao sistema operacional.

## <a name="features"></a>Comparação de Recursos

A tabela a seguir compara os recursos de Sites, serviços de nuvem e máquinas virtuais para ajudá-lo a fazer a melhor escolha. Caixas com um asterisco são explicadas em mais detalhes nas notas na tabela a seguir.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Sites</th>
<th align="left">Serviços de nuvem (funções Web)</th>
<th align="left">Máquinas Virtuais</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Acesso a serviços como o Service Bus, Armazenamento, Banco de Dados SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Camada de serviços da Web ou Web hospedada de uma arquitetura multicamada</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Camada intermediária de host de uma arquitetura multicamada</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Suporte integrado do MySQL como serviço</p></td>
<td align="left">X</td>
<td align="left">X <sup>1</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte para ASP.NET, classic ASP, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Dimensionamento para várias instâncias sem reimplantação</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X <sup>2</sup></td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte para SSL</p></td>
<td align="left">X <sup>3</sup></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Integração do Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Depuração Remota</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Implantar código com TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Implantar código com GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Implantar o código com a implantação da Web</p></td>
<td align="left">X</td>
<td align="left"><sup>4</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte do WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Implantação quase instantânea</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Instâncias compartilham conteúdo e configuração</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Dimensionar para máquinas maiores sem reimplantação</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Vários ambientes de implantação (produção e preparo)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Isolação de rede com Rede Virtual do Azure</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Suporte para o Gerenciador de Tráfego do Azure</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Acesso remoto a área de trabalho para servidores</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Capacidade de definir/executar tarefas de inicialização</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Gerenciamento de atualização automática do sistema operacional</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Monitoramento do ponto de extremidade integrado</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Troca perfeita da plataforma (32 bits/64 bits)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>1</sup> Funções Web ou de trabalhado podem integrar o MySQL como serviço por meio de ofertas do ClearDB, mas não como parte do fluxo de trabalho do Portal de Gerenciamento.

<sup>2</sup> Embora máquinas virtuais possam ser dimensionadas para várias instâncias, os serviços em execução nessas máquinas devem ser escritos para lidar com este dimensionamento. Um balanceador de carga adicional deve ser configurado para rotear solicitações entre as máquinas. Finalmente, deve ser criado um grupo de afinidade para todas as máquinas participando na mesma função para protegê-las contra reinicializações simultâneas por manutenção ou falhas de hardware.

<sup>3</sup> Para Sites, o SSL para nomes de domínio personalizados só tem suporte no modo padrão. Para obter mais informações, sobre como usar o SSL com Sites, consulte [Configurando um certificado SSL para um Site do Azure][Configurando um certificado SSL para um Site do Azure].

<sup>4</sup> A implantação da Web há suporte para os serviços de nuvem ao implantar funções de instância única. No entanto, as funções de produção exigem várias instâncias para atender o SLA do Azure. Portanto, implantação da Web não é um mecanismo de implantação adequado para serviços de nuvem em produção.

  [Sites do Web do Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Serviços de Nuvem]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/?LinkID=306053
  [SDK WebJobs do Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [Cenários]: #scenarios
  [Resumos de serviço]: #services
  [Comparação de Recursos]: #features
  [tarefas automatizadas]: http://www.windowsazure.com/pt-br/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/pt-br/develop/net/
  [PHP]: http://www.windowsazure.com/pt-br/develop/php/
  [Node.js]: http://www.windowsazure.com/pt-br/develop/nodejs/
  [Python]: http://www.windowsazure.com/pt-br/develop/python/
  [Banco de Dados SQL]: http://www.windowsazure.com/pt-br/documentation/services/sql-database/
  [barramento de serviço]: http://www.windowsazure.com/pt-br/documentation/services/service-bus/
  [armazenamento]: http://www.windowsazure.com/pt-br/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/pt-br/gallery/store/
  [Configurando um certificado SSL para um Site do Azure]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/enable-ssl-web-site/

<properties umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="robb" manager="johndaw" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb" />

# Modelos de execução do Azure

O Azure fornece modelos de execução diferentes para execução de aplicativos. Cada um deles fornece um conjunto de serviços diferente e, portanto, qual deles você escolhe depende exatamente do que você está tentando fazer. Este artigo aborda três, descrevendo cada tecnologia e fornecendo exemplos de quando usá-lo.

## Sumário

-   [Máquinas Virtuais][Máquinas Virtuais]
-   [Sites][Sites]
-   [Serviços de Nuvem][Serviços de Nuvem]
-   [Qual devo usar? Fazendo uma escolha][Qual devo usar? Fazendo uma escolha]

## <span id="VMachine"></span></a>Máquinas Virtuais

As Máquinas Virtuais do Azure permite aos desenvolvedores, ao pessoal de operações de TI e a outras pessoas criar e usar máquinas virtuais na nuvem. Fornecendo o que é conhecido como *Infraestrutura como Serviço (IaaS)*, essa tecnologia pode ser usada de várias maneiras. A [Figura 1][Figura 1] mostra seus componentes básicos.

<a name="Fig1"></a>![01\_CreatingVMs][01\_CreatingVMs]

**Figura 1: As Máquinas Virtuais do Azure fornecem a infraestrutura como serviço.**

Como é mostrado na figura, é possível criar máquinas virtuais usando o Portal de Gerenciamento do Azure ou o API de Gerenciamento de Serviço do Azure baseado em REST. O Portal de Gerenciamento pode ser acessado de qualquer navegador conhecido, inclusive Internet Explorer, Mozilla e Chrome. Para a API REST, a Microsoft fornece ferramentas de criação de scripts de cliente para sistema Windows, Linux e Macintosh. Outro software também é livre para usar essa interface.

No entanto, ao acessar a plataforma, criar uma nova VM exige a escolha de um VHD (disco rígido virtual) para a imagem da VM. Esses VHDs são armazenados em blobs do Azure.

Para começar, você tem duas opções

1.  Carregar seu próprio VHD
2.  Usar os VHDs fornecidos pela Microsoft e seus parceiros na galeria de Máquinas Virtuais do Azure ou no site [VMDepot][VMDepot] open source da Microsoft.

Os VHDs na galeria e na VMDepot incluem imagens limpas do sistema operacional Microsoft e Linux, bem como imagens que incluem o Microsoft e outros produtos de terceiros instalados neles. As opções estão aumentando o tempo todo. Os exemplos incluem várias versões, edições e configurações de:

-   Windows Server
-   Servidores Linux como Suse, Ubuntu e CentOS
-   SQL Server
-   BizTalk Server
-   SharePoint Server

Juntamente com um VHD, você especifica o tamanho da sua nova VM. As estatísticas completas para cada tamanho são listadas [na biblioteca do Azure][na biblioteca do Azure].

-   **Extra Pequeno**, com um núcleo compartilhado e 768MB de memória.
-   **Pequena**, com 1 núcleo e 1,75 GB de memória.
-   **Média**, com 2 núcleos e 3,5 GB de memória.
-   **Grande**, com 4 núcleos e 7 GB de memória.
-   **Extra Grande**, com 8 núcleos e 14 GB de memória.
-   **A6**, com 4 núcleos e 28 GB de memória.
-   **A7**, com 8 núcleos e 56 GB de memória.

Por fim, você escolhe em qual datacenter do Azure sua nova VM deve ser executada, se nos EUA, na Europa ou na Ásia.

Assim que uma VM estiver em execução, você paga por cada hora de execução e para de pagar quando remove essa VM. O valor pago não depende de quanto sua VM é usada, mas exclusivamente dos ponteiros do relógio. Uma VM que fica ociosa por uma hora custa o mesmo preço de uma que esteja totalmente carregada.

Cada VM em execução tem um *disco de sistema operacional* associado que é mantido em um blob. Quando você cria uma VM usando um VHD da galeria, esse VHD é copiado no disco de sistema operacional da sua VM. Todas as alterações feitas no sistema operacional da VM em execução são armazenadas aqui. Por exemplo, se você instalar um aplicativo que modifique o registro do Windows, essa alteração será armazenada no disco de sistema operacional da VM. Na próxima vez que você criar uma VM a partir desse disco de sistema operacional, a VM continuará sendo executada no mesmo estado em que foi deixada. Para VHDs armazenados na galeria, a Microsoft aplica atualizações quando necessário, como patches do sistema operacional. No entanto, para os VHDs nos seus próprios discos de sistema operacional, você é responsável por aplicar essas atualizações (embora o Windows Update seja ativado por padrão).

VMs em execução também podem ser modificados e, em seguida, capturada usando a ferramenta sysprep. O Sysprep remove informações específicas, como o nome da máquina para que uma imagem VHD possa ser usada para criar VMs adicionais com a mesma configuração geral. Essas imagens são armazenadas no Portal de gerenciamento, juntamente com suas imagens carregadas, portanto, podem ser usadas como ponto de partida para outras VMs.

As Máquinas Virtuais também monitoram o hardware que hospeda cada VM criada. Se um servidor físico que estiver executando uma VM falhar, a plataforma iniciará a mesma VM em outra máquina. E, supondo que você tenha o licenciamento de direito, é possível copiar um VHD alterado fora do seu disco de sistema operacional e executá-lo em algum outro lugar, como no seu próprio datacenter local ou em outro provedor de nuvem.

Juntamente com o disco de sistema operacional, sua VM tem um ou mais discos de dados. Embora cada um deles veja sua VM como uma unidade de disco montada, o conteúdo de cada um é, de fato, armazenado em um blob. Cada gravação feita em um disco de dados é armazenada persistentemente no blob subjacente. Assim como todos os blobs, o Azure replica ambos em um único datacenter e pelos datacenters para proteção contra falhas.

As VMs em execução podem ser gerenciadas usando o Portal de Gerenciamento, o PowerShell e outras ferramentas de script, ou diretamente por meio da API REST. (Na verdade, o que você puder fazer usando Portal de Gerenciamento pode ser feito de forma programática por essa API.) Os parceiros da Microsoft, como a RightScale e a ScaleXtreme, também fornecem serviços de gerenciamento que dependem da API REST.

As Máquinas Virtuais do Azure podem ser usadas de várias maneiras. Os principais cenários que a Microsoft visa incluem:

-   **VMs para desenvolvimento e teste.** Os grupos de desenvolvimento normalmente precisam de VMs com configurações específicas para a criação de aplicativos. As Máquinas Virtuais do Azure fornecem uma maneira direta e econômica de criar essas VMs, usá-las e removê-las quando elas não forem mais necessárias.
-   **Aplicativos em execução na nuvem.** Para alguns aplicativos, a execução na nuvem pública faz sentido economicamente. Pense, por exemplo, em um aplicativo com grandes picos na demanda. Sempre é possível comprar máquinas suficientes para seu próprio datacenter para executar esse aplicativo, mas muitas dessas máquinas provavelmente não serão usadas a maior parte do tempo. Executar esse aplicativo no Azure permite que você pague por VMs extras somente quando forem necessárias, desligando-as quando o pico de uma demanda tiver sido encerrado. Ou suponha que você seja um iniciante que precisa de recursos de computação sob demanda rapidamente e sem comprometimento. Mais uma vez, o Azure pode ser a escolha certa.
-   **Extensão do seu próprio datacenter para a nuvem pública.** Com a Rede Virtual do Azure, sua organização pode criar uma rede virtual (VNET) que faça um grupo de VMs do Azure parecer parte da sua própria rede local. Isso permite executar aplicativos como o SharePoint e outros no Azure, uma abordagem que pode ser mais fácil de implantar e/ou menos onerosa do que executá-los no seu próprio datacenter.
-   **Recuperação de desastre.** Em vez de pagar continuamente por um datacenter de backup que raramente é usado, a recuperação de desastres com base na IaaS permite que você pague pelos recursos de computação necessários somente quando realmente precisar deles. Por exemplo, se seu datacenter primário falhar, você poderá criar VMs em execução no Azure para executar aplicativos essenciais e desligá-las quando não precisar mais delas.

Essas não são as únicas possibilidades, mas são bons exemplos de como é possível usar as Máquinas Virtuais do Azure.

### Agrupando VMs: Serviços de Nuvem

Ao criar uma nova VM com as Máquinas Virtuais do Azure, você pode optar por executá-la de modo autônomo ou torná-la parte de um grupo de máquinas virtuais em execução conjunta em um *serviço de nuvem*. (Apesar da semelhança dos nomes, não confunda esse conceito com os Serviços de Nuvem, o nome da tecnologia PaaS do Azure; não são a mesma coisa.) Cada VM autônoma recebe seu próprio endereço IP público, enquanto todas as VMs no mesmo serviços de nuvem são acessadas por meio de um único endereço IP público. A [Figura 2][Figura 2] mostra isso.

<a name="Fig2"></a>![02\_CloudServices][02\_CloudServices]

**Figura 2: Cada VM autônoma tem seu próprio endereço IP público, enquanto as VMs agrupadas em um serviço de nuvem são expostas por um único endereço IP público.**

Por exemplo, se você estava criando uma máquina virtual para criar e testar um aplicativo simples, você provavelmente usa um VM autônomo. Entretanto, se você estiver criando um aplicativo multicamadas com um front-end da Web, um banco de dados e, talvez, até mesmo uma camada intermediária, muito provavelmente você conectará várias VMs em um serviço de nuvem, como sugerido na Figura 2.

Agrupar VMs em um serviço de nuvem também permite usar outras opções. O Azure fornece balanceamento de carga para VMs no mesmo serviços de nuvem, distribuindo as solicitações de usuários entre elas. As VMs conectadas dessa maneira também podem se comunicar diretamente entre si pela rede local em um datacenter do Azure.

As VMs no mesmo serviço de nuvem também podem ser agrupadas em um ou mais *conjuntos de disponibilidade*. Para entender por que isso é importante, pense em um aplicativo Web que execute várias VMs front-end. Se todas essas VMs forem implantadas na mesma máquina física ou até mesmo no mesmo rack de máquinas, uma única falha de hardware poderá torná-las inacessíveis. No entanto, se essas VMs forem agrupadas em um conjunto de disponibilidade, o Azure as implantará pelo datacenter, de modo que nenhuma delas compartilhe um único ponto de falha.

### Cenário: Executando um aplicativo com o SQL Server

Para ter uma ideia melhor de como as Máquinas Virtuais do Azure funcionam, vale observar um par de cenários um pouco mais detalhadamente. Suponha, por exemplo, que você queira criar um aplicativo Web confiável e escalonável para execução no Azure. Uma maneira de fazer isso é executar a lógica do aplicativo em um ou mais VMs do Azure e usar o SQL Server para gerenciamento de dados. A [Figura 3][Figura 3] mostra isso.

<a name="Fig3"></a>![03\_AppUsingSQLServer][03\_AppUsingSQLServer]

**Figura 3: Um aplicativo em execução nas Máquinas Virtuais do Azure pode usar o SQL Server para armazenamento.**

Neste exemplo, ambos os tipos de VMs são criados de VHDs padrão na galeria. A lógica do aplicativo é executada no Windows Server 2008 R2, de modo que o desenvolvedor cria três VMs desse VHD e instala seu aplicativo em cada uma. Uma vez que todas essas VMs estão no mesmo serviços de nuvem, ele pode configurar o balanceamento de carga de hardware para distribuir solicitações entre elas. O desenvolvedor também cria duas VMs do VHD da galeria contendo o SQL Server 2012. Assim que elas estiverem em execução, ele configura o SQL Server em cada instância para usar o espelhamento de banco de dados sem failover automático. Isso não é obrigatório - o aplicativo pode usar apenas uma única instância do SQL Server - mas usar essa abordagem melhora a confiabilidade.

### Cenário: Executando um Farm do SharePoint

Suponha que uma organização queira criar um farm do SharePoint mas não queira executar o farm em seu próprio datacenter. Talvez o datacenter local não tenha muitos recursos, ou talvez a unidade de negócios que está criando o farm não queira lidar com seu grupo de TI interno. Em casos como esse, executar o SharePoint nas Máquinas Virtuais do Azure pode fazer sentido. A [Figura 4][Figura 4] mostra isso.

<a name="Fig4"></a>![04\_SharePointFarm][04\_SharePointFarm]

**Figura 4: As Máquinas Virtuais do Azure permitem executar um farm do SharePoint na nuvem.**

Uma farm do SharePoint tem vários componentes, cada um executado em uma VM do Azure criada por um VHD diferente. Veja o que é necessário:

-   Microsoft SharePoint. Existem imagens de avaliação na galeria ou a organização fornece seu próprio VHD.
-   Microsoft SQL Server. O SharePoint depende do SQL Server, assim a organização cria VMs executando o SQL Server 2012 de um VHD padrão da galeria.
-   Windows Server Active Directory. O SharePoint também exige o Active Directory, assim a organização cria controladores de domínio na nuvem usando uma imagem do Windows Server da galeria. Isso não é estritamente necessário (também é possível usar controladores de domínio locais), mas o SharePoint interage frequentemente com o Active Directory, de modo que a abordagem mostrada aqui terá melhor desempenho.

Embora não seja mostrado na figura, esse farm do SharePoint provavelmente é conectado a uma rede local usando a Rede Virtual do Azure. Isso permite que as VMs - e os aplicativos que ela contém - pareçam ser locais para as pessoas que usarem essa rede.

Como mostram os exemplos, você pode usar as Máquinas Virtuais do Azure para criar e executar novos aplicativos na nuvem, para executar aplicativos existentes ou de outras maneiras. Qualquer opção que você escolher, o objetivo da tecnologia é o mesmo: fornecendo uma base com finalidade geral para o público a computação em nuvem.

## <span id="WebSites"></span></a>Sites

As pessoas usam tecnologias da Web de várias maneiras diferentes. Uma empresa pode precisar migrar ou configurar um site de presença que pode lidar com milhões de visitas por semana e ser implantado em vários datacenters em todo o mundo. Uma agência de design na Web pode trabalhar com uma equipe de desenvolvedores para criar um aplicativo Web personalizado capaz de lidar com milhares de usuários. Um desenvolvedor corporativo pode precisar configurar um aplicativo para controlar os relatórios de despesas na nuvem para usuários autenticados do seu Active Directory corporativo. Um consultor de TI pode usar um aplicativo de software livre conhecido para configurar um sistema de gerenciamento de conteúdo para uma pequena empresa.
Todas essas coisas podem ser alcançadas usando as Máquinas Virtuais do Azure. Porém, criar e gerenciar VMs brutas exige alguma habilidade e algum esforço. Se você precisar implementar um site ou aplicativo da Web, há uma solução mais fácil (e mais barata): a abordagem comumente conhecida como Plataforma como um Serviço (PaaS). Como mostra a Figura 5, o Azure fornece isso com os sites.

<a name="Fig5"></a>![05\_Websites][05\_Websites]

**Figura 5: Os Sites do Azure oferece suporte a sites estáticos, aplicativos Web conhecidos e aplicativos Web personalizados criados com várias tecnologias.**

Sites do Azure são baseados em serviços de nuvem do Azure para criar uma plataforma como uma solução de serviço otimizada para executar aplicativos da Web. Como mostra a figura, os sites executados em um conjunto de VMs únicas que podem conter vários sites criados por vários usuários, bem como VMs padrão que pertencem a um usuário individual. As VMs são uma parte de um grupo de recursos gerenciados pelos sites do Azure e, portanto, permitem alta confiabilidade e tolerância a falhas.
A introdução é simples. Com os sites do Azure, os usuários podem selecionar dentre uma variedade de aplicativos, estruturas e modelo e criar um site em segundos. Em seguida, eles podem usar suas ferramentas de desenvolvimento favoritas (WebMatrix, Visual Studio, qualquer outro editor) e opções de controle de origem para configurar a integração contínua e desenvolver como uma equipe. Os aplicativos que confiam no banco de dados do MySQL podem consumir um serviço do MySQL fornecido pelo Azure by ClearDB, um parceiro da Microsoft.
Os desenvolvedores podem criar aplicativos web grandes e escaláveis com sites. A tecnologia oferece suporte à criação de aplicativos usando ASP.NET, PHP, Node.js e Python. Os aplicativos podem usar sessões complexas, por exemplo, e aplicativos Web existentes podem ser movidos para essa plataforma de nuvem sem alterações. Os aplicativos criados nos Sites são livres para usar outros aspectos do Azure, como Service Bus, Banco de Dados SQL e Armazenamento de Blob. Também é possível executar várias cópias de um aplicativo em diferentes VMs, com os Sites balanceando a carga de solicitações automaticamente entre elas. E como novas instâncias do site são criadas no VM que já existe, o início de uma nova instância de aplicativo acontece muito rapidamente; é significativamente mais rápido do que esperar que uma nova VM seja criada.
Como a [Figura 5][Figura 5] mostra, você pode publicar código e outro conteúdo nos sites de várias maneiras. Você pode usar tecnologia FTP, FTPS ou WebDeploy da Microsoft. Os sites também oferecem suporte ao código de publicação dos sistemas de controle do código-fonte, incluindo Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server e Team Foundation Service baseado em nuvem.

## <span id="CloudServices"></span></a>Serviços de Nuvem

As Máquinas Virtuais do Azure fornecem IaaS, enquanto os Sites do Azure fornecem hospedagem Web. A terceira opção de computação, Serviços de Nuvem, fornecem *Plataforma como Serviço (PaaS)*. Essa tecnologia foi desenvolvida para oferecer suporte a aplicativos que são escalonáveis, confiáveis e cuja operação é barata. Isso também significa livrar os desenvolvedores da preocupação com o gerenciamento da plataforma que estão usando, permitindo que eles se concentrem inteiramente em seus aplicativos. A [Figura 6][Figura 6] ilustra a ideia.

<a name="Fig6"></a>![06\_CloudServices2][06\_CloudServices2]

**Figura 6: Os Serviços de Nuvem do Azure fornecem a Plataforma como Serviço.**

Como as outras opções de computação do Azure, os Serviços de Nuvem dependem de VMs. A tecnologia fornece duas opções de VM levemente diferentes: as instâncias das *funções Web* executam uma variante do Windows Server com IIS, enquanto as instâncias das *funções de trabalho* executam a mesma variante do Windows Server sem IIS. Um aplicativo de Serviços de Nuvem depende de uma combinação dessas duas opções.

Por exemplo, um aplicativo simples pode usar apenas uma função Web, enquanto um aplicativo mais complexo pode usar uma função Web para lidar com solicitações de entrada de usuários, em seguida, passar o trabalho que essas solicitações criam a uma função de trabalho para processamento. (Essa comunicação pode usar o Service Bus ou as Filas do Azure.)

Como sugerido pela figura, todas as VMs em um único aplicativo são executadas no mesmo serviço de nuvem, conforme descrito anteriormente com as Máquinas Virtuais do Azure. Em razão disso, os usuários acessam o aplicativo por meio de um único endereço IP público, com a carga de solicitações balanceada automaticamente entre as VMs do aplicativo. E assim como os serviços de nuvem criados usando as Máquinas Virtuais do Azure, a plataforma implantará a VM em um aplicativo de Serviços de Nuvem de maneira a evitar um único ponto de falha de hardware.

Ainda que os aplicativos sejam executados em máquinas virtuais, é importante entender que os Serviços de Nuvem fornecem PaaS, e não IaaS. Aqui está uma forma de pensar: Com a IaaS, como as Máquinas Virtuais do Azure, primeiramente você cria e configura o ambiente em que seu aplicativo será executado, em seguida, implanta o aplicativo nesse ambiente. Você é responsável por gerenciar grande parte desse mundo, realizando tarefas como a implantação de novas versões com aplicações de patches do sistema operacional em cada VM. Em contrapartida, na PaaS, é como se o ambiente já existisse. Tudo o que você precisa fazer é implantar o aplicativo. O gerenciamento da plataforma em que ele é executado, incluindo implantar novas versões do sistema operacional, é sua responsabilidade.

Com os Serviços de Nuvem, você não cria máquinas virtuais. Em vez disso, fornece um arquivo de configuração que informa ao Azure quantas delas você deseja, como três instâncias da função Web e duas instâncias da função de trabalho, e a plataforma as cria para você. Você ainda escolhe o tamanho que as VMs devem ter (as opções são as mesmas das VMs do Azure), mas não as cria explicitamente. Se o aplicativo precisar manipular uma carga de trabalho maior, é possível solicitar mais VMs, e o Azure criará essas instâncias. Se a carga diminuir, essas instâncias podem ser desligadas e você não paga mais por elas.

Um aplicativo dos Serviços de Nuvem geralmente é disponibilizado aos usuários por um processo de duas etapas. Primeiro, o desenvolvedor carrega o aplicativo na área de preparo da plataforma. Quando o desenvolvedor estiver pronto para ativar o aplicativo, ele usará o Portal de Gerenciamento do Azure para solicitar que o aplicativo seja colocado em produção. Essa troca entre o preparo e a produção pode ser feita sem tempo de inatividade, o que permite que um aplicativo em execução seja atualizado para uma nova versão sem perturbar seus usuários.

Os Serviços de Nuvem também fornecem monitoramento. Assim como as Máquinas Virtuais do Azure, eles detectarão um servidor físico com falha e reiniciarão as VMs que estavam em execução nesse servidor em numa nova máquina. Mas os Serviços de Nuvem também detectam as VMs e os aplicativos com falha, não apenas as falhas de hardware. Diferentemente das Máquinas Virtuais, os Serviços têm um representante dentro de cada função Web e de trabalho, de modo que podem iniciar novas instâncias de aplicativo e VMs quando uma falha ocorre.

A natureza da PaaS dos Serviços de Nuvem tem outras implicações também. Uma das mais importantes é que os aplicativos com base nessa tecnologia devem ser escritos para serem executados corretamente quando houver falha de alguma instância da função de trabalho ou da Web. Para isso, um aplicativo dos Serviços de Nuvem não deve manter o estado de suas próprias VMs no sistema de arquivos. Diferentemente das VMs criadas com as Máquinas Virtuais do Azure, as gravações feitas nas VMs dos Serviços de Nuvem não são persistentes; não há nada parecido com um disco de dados das Máquinas Virtuais. Um aplicativo dos Serviços de Nuvem deve gravar explicitamente todo o estado no Banco de Dados SQL, em blobs, tabelas ou em algum outro armazenamento externo. Criar aplicativos dessa forma facilita o dimensionamento deles e os torna mais resistente a falhas, que são metas importantes dos Serviços de Nuvem.

## <span id="WhatShouldIUse"></span></a>Qual devo usar? Fazendo uma escolha

Os três modelos de execução do Azure permitem criar aplicativos escalonáveis e confiáveis na nuvem. Dada essa importante semelhança, qual deles devo usar? A resposta dependerá do que você está tentando fazer.

As Máquinas Virtuais fornecem a solução mais comum. Se desejar o maior controle possível ou se você precisar de VMs genéricas, para desenvolvimento e teste, por exemplo, essa é a melhor opção. As Máquinas Virtuais também são a melhor opção para execução de aplicativos locais padronizados na nuvem, conforme ilustrado pelo exemplo do SharePoint descrito anteriormente. E como as VMs que você cria com essa tecnologia podem se parecer com suas VMs locais, essa provavelmente também é a melhor opção para a recuperação de desastres. Obviamente, o dilema é: quanto mais poder, mais responsabilidade - a IaaS exige a realização de algumas tarefas administrativas.

Os Sites são a opção certa quando você deseja criar um site simples. Isso pode ser percebido se seu site for baseado em um aplicativo existente, como Joomla, WordPress ou Drupal. Os Sites também são uma boa escolha para criar um aplicativo Web de baixa administração, mesmo um que deva ser totalmente escalonável, ou para mover um aplicativo Web IIS existente para a nuvem pública. Esse modelo também fornece implantação rápida. Uma nova instância do seu aplicativo pode começar a ser executada quase que imediatamente, enquanto a implantação de uma nova VM com as Máquinas Virtuais ou os Serviços de Nuvem pode levar vários minutos.

Os Serviços de Nuvem, que era o modelo de execução inicial fornecido pelo Azure, é explicitamente uma abordagem PaaS. Embora a linha entre a PaaS e a hospedagem Web seja confusa, os Serviços de Nuvem diferem de algumas formas importantes dos Sites, incluindo:

-   Diferentemente dos Sites, os Serviços de Nuvem fornecem acesso administrativo às VMs do seu aplicativo. Isso permite instalar softwares arbitrários necessários ao seu aplicativo, algo que não é possível com os Sites.
-   Como os Serviços de Nuvem oferecem funções Web e funções de trabalho, eles são melhor opção que os Sites para aplicativos multicamadas que precisam separar VMs para sua lógica de negócios.
-   - Os Serviços de Nuvem fornecem ambientes separados de preparo e produção, o que torna as atualizações de aplicativo uma tarefa mais fácil do que nos Sites.
-   Diferentemente dos Sites, é possível usar tecnologias de rede, como a Rede Virtual do Azure e o Connect do Azure, para conectar computadores locais aos aplicativos de Serviços de Nuvem.
-   - Os Serviços de Nuvem permitem usar a Área de Trabalho Remota para se conectar diretamente às VMs de um aplicativo, o que não é possível fazer com os Sites.

Sendo uma PaaS, os Serviços de Nuvem também oferecem algumas vantagens sobre as Máquinas Virtuais do Azure. Mais tarefas de gerenciamento são realizadas para você, por exemplo, a implantação de atualizações do sistema operacional. Desse modo, os custos das operações provavelmente serão menores do que na abordagem IaaS das Máquinas Virtuais do Azure.

Todos os três modelos de execução do Azure tem prós e contras. Fazer a melhor escolha requer compreensão deles, saber o que você está tentando realizar e então escolher o que for mais adequado.

Às vezes, nenhum modelo de execução sozinho é o certo. Em situações como essa, é perfeitamente legal combinar as opções. Por exemplo, suponha que você esteja criando um aplicativo onde deseja os benefícios de gerenciamento das funções Web dos Serviços de Nuvem, mas também precisa usar o SQL Server padrão por motivos de compatibilidade ou desempenho. Nesse caso, a melhor opção é combinar modelos de execução, como mostrado na [Figura 7][Figura 7].

<a name="Fig7"></a>![07\_CombineTechnologies][07\_CombineTechnologies]

**Figura 7: Um único aplicativo pode usar vários modelos de execução.**

Conforme ilustrado pela figura, as VMs dos Serviços de Nuvem são executadas em um serviço de nuvem separado das VMs das Máquinas Virtuais. No entanto, as duas podem se comunicar eficientemente, de modo que criar um aplicativo desse modo às vezes é a melhor opção.

O Azure fornece diferentes modelos de execução porque as plataformas em nuvem precisam oferecer suporte a vários cenários diferentes. Qualquer pessoa que queira usar essa plataforma de modo eficiente - e se você leu este artigo até agora, provavelmente inclui você - precisa entender cada um deles.

  [Máquinas Virtuais]: #VMachine
  [Sites]: #WebSites
  [Serviços de Nuvem]: #CloudServices
  [Figura 1]: #Fig1
  [VMDepot]: http://vmdepot.msopentech.com/
  [na biblioteca do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn197896.aspx
  [Figura 2]: #Fig2
  [Figura 3]: #Fig3
  [Figura 4]: #Fig4
  [Figura 5]: #Fig5
  [Figura 6]: #Fig6
  [Figura 7]: #Fig7

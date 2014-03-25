# Apresentando o Windows Azure

O Windows Azure é a plataforma de aplicativos da Microsoft para a nuvem pública. Você pode usar essa plataforma de diferentes maneiras. Por exemplo, é possível usar o Windows Azure para criar um aplicativo Web que é executado e armazena seus dados nos datacenters da Microsoft. Você pode usar o Windows Azure apenas para armazenar dados, com os aplicativos que usam esses dados em execução no local (isto é, fora da nuvem pública). É possível usar o Windows Azure para criar máquinas virtuais para desenvolvimento e teste ou para executar o SharePoint e outros aplicativos. Ele também pode ser usado para criar aplicativos altamente escalonáveis com muitos e muitos usuários. Como a plataforma oferece uma ampla variedade de serviços, é possível fazer tudo isso e muito mais.

Porém, para realizar qualquer uma das tarefas acima, é preciso entender os conceitos básicos. Mesmo que você não saiba nada sobre computação em nuvem, este artigo o guiará pelos princípios do Windows Azure. O objetivo é fornecer uma base para compreender e usar essa plataforma na nuvem.


## Sumário

-   [Os componentes do Windows Azure](#components)
-   [Computação/Modelos de Execução](#models)
-   [Gerenciamento de dados](#data)
-   [Rede](#networking)
-   [Análise empresarial](#analytics)
-   [Mensagens](#messaging)
-   [Caching](#caching)
-   [Identidade](#identity)
-   [HPC (Computação de Alto Desempenho)](#HPC)
-   [Mídia](#media)
-   [Comércio](#commerce)
-   [SDKs](#sdk)
-   [Introdução](#start)




<h2><a id="components"></a>Os componentes do Windows Azure</h2>

Para entender o que o Windows Azure oferece, é útil agrupar seus
serviços em categorias distintas. A Figura 1 mostra uma maneira de fazer isso.

![Os componentes do Windows Azure](./media/intro-to-windows-azure/IntroAzure1.png)   
 **Figura 1: o Windows Azure fornece serviços de aplicativo acessíveis pela Internet que são executados nos datacenters Windows Azure.**

Para começar a usar o Windows Azure, é preciso conhecer, pelo menos, os conceitos básicos sobre cada um de seus componentes. Você também pode usar o [cartaz O que é Windows Azure?](http://www.microsoft.com/pt-br/download/details.aspx?id=35473) para uma maneira rápida e visual de obter uma visão geral. As cores das caixas da Figura 1 correspondem a seu agrupamento no cartaz. 

O restante deste artigo aborda as tecnologias mostradas na figura, descrevendo o que cada uma oferece e quando usá-las.



<h2><a id="models"></a>Computação/Modelos de Execução</h2>

Uma das tarefas mais básicas da plataforma na nuvem é executar aplicativos. O Windows Azure oferece quatro opções para fazer isso: Máquinas Virtuais, Serviços de Nuvem, Sites e Serviços Móveis.  Cada um dos três modelos de execução do Windows Azure possui sua própria função a ser desempenhada. 

As Máquinas Virtuais do Windows Azure oferecem um ambiente de computação sob demanda para fins gerais. Os Serviços de Nuvem são uma ótima opção para a criação de aplicativos escalonáveis e confiáveis e com baixos custos administrativos. Os Sites da Web do Windows Azure oferecem uma variedade de aplicativos, estruturas e modelos para criar aplicativos web grandes e escaláveis e sites de presença rapidamente e gerenciar com eficiência o desenvolvimento, teste e operações. Da mesma forma, os Serviços Móveis do Windows Azure aceleram o desenvolvimento de aplicativos para dispositivos móveis, fornecendo maneiras prontas de armazenar dados na nuvem, autenticar usuários e enviar notificações por push.

Essas tecnologias podem ser usadas separadamente ou combinadas, conforme a necessidade, para criar a base certa para o seu aplicativo. A abordagem escolhida depende de quais problemas você está tentando resolver.


###Máquinas Virtuais###

A capacidade de criar uma máquina virtual sob demanda, seja de uma imagem padrão ou de uma fornecida por você, pode ser bastante útil. Adicione a capacidade de pagar por essa VM por minuto e apenas enquanto ela estiver sendo executada e ela se torna ainda mais útil. Essa abordagem, geralmente conhecida como Infraestrutura como Serviço (IaaS), é fornecida pelas Máquinas Virtuais do Windows Azure. 

Para criar uma VM, você especifica qual VHD usar e o tamanho da VM. Você então paga por cada momento em que a VM estiver em execução. As Máquinas Virtuais do Windows Azure oferecem uma galeria de VHDs predefinidas. Isso inclui opções fornecidas pela Microsoft, como o Windows Server 2008 R2, Windows Server 2012 e Windows Server 2008 R2 com SQL Server, juntamente com imagens Linux fornecidas pelos parceiros da Microsoft. Você é livre para criar VMs de seus próprios VHDs e também adicioná-las à galeria.
 
Seja qual for a origem da imagem, você pode armazenar persistentemente todas as alterações feitas enquanto uma VM estiver em execução. Na próxima vez que uma VM for criada desse VHD, tudo é obtido de onde você parou. Também é possível copiar o VHD alterado fora do Windows Azure e executá-lo localmente.

As VMs do Windows Azure podem ser usadas de diferentes maneiras. Você pode usá-las para criar uma plataforma barata de desenvolvimento e teste que pode ser desligada quando tiver terminado de usá-la. Também é possível criar e executar aplicativos que usem qualquer linguagem e biblioteca de sua preferência. Esses aplicativos podem usar qualquer uma das opções de gerenciamento de dados fornecidas pelo Windows Azure e você pode optar por usar o SQL Server ou outro DBMS em uma ou mais máquinas virtuais. Outra opção é usar as VMs do Windows Azure como uma extensão do seu datacenter local, executando o SharePoint ou outros aplicativos. Para oferecer suporte a essa opção, é possível criar domínios do Windows na nuvem executando o Active Directory nas VMs do Windows Azure. Essa abordagem geral para computação em nuvem pode ser usada para solucionar muitos problemas diferentes. O que você faz com ela é por sua conta.

###Sites###

Uma das tarefas mais comuns que as pessoas fazem nas nuvens é executar sites e aplicativos web. As Máquinas Virtuais do Windows Azure permitem isso, porém, ainda deixam você com a responsabilidade de administrar uma ou mais VMs. E se você apenas quisesse um site no qual alguma outra pessoa cuidasse do trabalho administrativo para você?

Isso é exatamente o que os Sites do Windows Azure oferecem. Esse modelo de execução oferece um ambiente de web gerenciado usando o Portal de Gerenciamento do Windows Azure, bem como as APIs. Você pode mover um site existente para os Sites do Windows Azure não alterados ou pode criar outro diretamente na nuvem. Assim que um site estiver em execução, será possível adicionar ou remover instâncias dinamicamente, dependendo dos Sites do Windows Azure para balancear a carga de solicitações entre elas. Os Sites do Windows Azure oferecem uma opção compartilhada, onde o site é executado em uma máquina virtual com outros sites, e uma opção padrão que permite que um site seja executado em sua própria VM. A opção padrão também permite que você aumente o tamanho (computação energia) das ocorrências, se necessário.

Os Sites do Windows Azure são úteis para corporações, desenvolvedores e agências de web design. Para empresas, é uma solução fácil de gerenciar, flexível, altamente segura e altamente disponível para a execução de sites da web de presença. Para desenvolvimento, ele oferece suporte ao .NET, PHP e Node.js e Python, juntamente com o Banco de Dados SQL e o MySQL (da ClearDB, uma parceira da Microsoft) para armazenamento relacional. Eles também fornecem suporte interno para vários aplicativos conhecidos, incluindo WordPress, Joomla e Drupal. O objetivo é fornecer uma plataforma amplamente útil, de baixo custo e escalonável para criação de sites e aplicativos web na nuvem pública.

###Serviços de nuvem###

Suponha que você queira criar um aplicativo em nuvem que possa oferecer suporte a vários usuários simultaneamente, não exija muito esforço administrativo e nunca falhe. Você pode ser um fornecedor de software estabelecido, por exemplo, que está decidido a adotar o SaaS (Software como Serviço) criando uma versão de um dos seus aplicativos na nuvem. Ou você pode ser um iniciante criando um aplicativo de consumidor com grandes expectativas de que ele cresça rapidamente. Se estiver desenvolvendo com o Windows Azure, que modelo de execução você deve usar?

Os Sites do Windows Azure permitem criar esse tipo de aplicativo web, mas há algumas restrições. Por exemplo, se você não tiver acesso administrativo, significa que não poderá instalar softwares arbitrários.  As Máquinas Virtuais do Windows Azure proporcionam muita flexibilidade, incluindo acesso administrativo, e você certamente pode usá-las para criar um aplicativo bastante escalonável, mas ainda terá que lidar com muitos aspectos de confiabilidade e administração. O que você deseja é uma opção que proporcione o controle necessário, mas que também manipule a maior parte do trabalho exigido pela confiabilidade e administração.

Isso é exatamente o que é fornecido pelos Serviços de Nuvem do Windows Azure. Essa tecnologia foi desenvolvida explicitamente para oferecer suporte a aplicativos escalonáveis, confiáveis e que exigem baixa administração. Esse é um exemplo do que normalmente chamamos de PaaS (Plataforma como Serviço). Para usá-la, você pode criar um aplicativo usando a tecnologia de sua preferência, como C#, Java, PHP, Python, Node.js etc. Seu código é executado em máquinas virtuais (conhecidas como instâncias) que executam uma versão do Windows Server. 

Mas essas VMs são diferentes daquelas que você cria com as Máquinas Virtuais do Windows Azure. Em primeiro lugar, o Windows Azure em si as gerencia, realizando tarefas como instalar patches do sistema operacional e distribuir automaticamente novas imagens com patches. (Isso indica que seu aplicativo não deve manter o estado nas instâncias da função Web ou de trabalho; ele deve ser mantido em uma das opções de gerenciamento de dados do Windows Azure descritas na próxima seção.) O Windows Azure também monitora as VMs, reiniciando todas que falharem. 

Existem duas funções a serem escolhidas quando você cria uma instância, ambas baseadas no Windows Server. A principal diferença entre as duas é que a instância de uma função Web é executada no IIS, enquanto a instância de uma função de trabalho, não. No entanto, ambas são gerenciadas da mesma forma, e é comum que um aplicativo use ambas. Por exemplo, uma instância da função Web pode aceitar solicitações de usuários e passá-las a uma instância da função de trabalho para processamento. Para dimensionar o aplicativo vertical ou horizontalmente, é possível solicitar que o Windows Azure crie mais instâncias da função ou desligue as instâncias existentes. E assim como as Máquinas Virtuais do Windows Azure, você será cobrado apenas pelo tempo durante o qual cada instância da função de trabalho ou Web é executada. 


###Serviços Móveis###

Se estiver criando um aplicativo para dispositivos móveis, os Serviços Móveis do Windows Azure aceleram o desenvolvimento fornecendo maneiras prontas de armazenar dados na nuvem, autenticar usuários e enviar notificações por push.  
As bibliotecas de cliente nativo para o Android, iOS, HTML/JavaScript, Windows Phone e Windows Store facilitam tornar os aplicativos disponível em todas as principais plataformas móveis.  Uma API do REST aberta e flexível também permite que você use a funcionalidade de autenticação e dados de Serviços Móveis com aplicativos em praticamente qualquer plataforma.  Um único Serviço Móvel pode fazer vários aplicativos clientes para que você possa fornecer uma experiência de usuário consistente entre os dispositivos. 

Os Serviços Móveis permitem que você faça o provisionamento e o gerenciamento simples dos dados armazenados em um Banco de Dados SQL, autentique usuários por meio de provedores de identidade conhecidas, como Facebook, Twitter, Microsoft ou conta do Google, e usar os serviços de notificação para enviar notificações push para seu aplicativo. Com o código do servidor você pode facilmente usar opções de armazenamento de dados como armazenamento de blob ou MongoDB, consumir Complementos da Windows Azure Store como SendGrid ou Pusher, usar outros Serviços do Windows Azure como o Service Bus e funções de trabalho ou até mesmo se conectar a sistemas locais.  Um serviço pode ser dimensionado conforme um aplicativo torna-se mais popular e o monitoramento e registro em log são suportados.

Embora certamente seja possível criar back-end de um aplicativo móvel usando Máquinas Virtuais, Serviços de Nuvem ou Sites da Web, ao optar pelos Serviços Móveis você pode gastar muito menos tempo escrevendo os componentes subjacentes do serviço.


<h2><a id="data"></a>Gerenciamento de Dados</h2>

Os aplicativos precisam de dados, e diferentes tipos de aplicativos precisam de diferentes tipos de dados. Por esse motivo, o Windows Azure fornece várias maneiras diferentes de armazenar e gerenciar dados. 

Uma dessas capacidades já foi mencionada: a capacidade de executar o SQL Server ou outro DBMS em uma VM criada com as Máquinas Virtuais do Windows Azure. (É importante perceber que essa opção não se limita aos sistemas relacionais; você também tem a liberdade de executar as tecnologias NoSQL, como MongoDB e Cassandra.) Executar seu próprio sistema de banco de dados é uma tarefa direta (ele replica o que costumávamos em nossos datacenters), mas também exige controle da administração desse DBMS. Para facilitar a vida, o Windows Azure tem três opções de gerenciamento de dados que são amplamente gerenciadas para você. A Figura 2 mostra as opções.


![Gerenciamento de dados do Windows Azure](./media/intro-to-windows-azure/IntroAzure3.png)   
 **Figura 2: para gerenciamento de dados, o Windows Azure fornece armazenamento relacional, tabelas NoSQL escalonáveis e armazenamento binário não estruturado.**

Cada uma dessas três opções aborda uma necessidade diferente: armazenamento relacional, acesso rápido a volumes potencialmente grandes de dados tipados simples e armazenamento binário não estruturado. Nos três casos, os dados são replicados automaticamente pelos três computadores diferentes em um datacenter do Windows Azure para fornecer alta disponibilidade. Também vale a pena observar que as três opções podem ser acessadas pelos aplicativos do Windows Azure ou pelos aplicativos em execução em qualquer outro lugar, como no datacenter local, laptop ou telefone. E apesar de aplicá-los, você paga por todos os serviços de gerenciamento de dados do Windows Azure com base no uso, incluindo a cobrança de um gigabyte por mês pelos dados armazenados. 

###Banco de dados SQL###

Para armazenamento relacional, o Windows Azure fornece o Banco de Dados SQL. Antigamente chamado de SQL Azure, o Banco de Dados SQL fornece todos os principais recursos de um sistema de gerenciamento de banco de dados relacional, incluindo transações atômicas, acesso simultâneo aos dados por vários usuários com integridade de dados, consultas ANSI SQL e um modelo de programação conhecido. Assim como o SQL Server, o Banco de Dados SQL pode ser acessado usando o Entity Framework, ADO.NET, JDBC, entre outras tecnologias conhecidas de acesso a dados. Ele também oferece suporte à maioria das linguagens T-SQL, juntamente com as ferramentas do SQL Server, como o SQL Server Management Studio. Para qualquer pessoa familiarizada com o SQL Server (ou outro banco de dados relacional), usar o Banco de Dados SQL é fácil.

Porém, o Banco de Dados SQL não é apenas um DBMS na nuvem: ele é um serviço PaaS. Você ainda controla os dados e quem os acessa, mas o Banco de Dados SQL cuida do enfadonho trabalho administrativo, como o gerenciamento da infraestrutura de hardware e a atualização automática do banco de dados e do software do sistema operacional. O Banco de Dados SQL também fornece uma opção de federação que distribui dados pelos diversos servidores. Isso é útil para aplicativos que trabalham com grandes volumes de dados ou precisam distribuir solicitações de acesso a dados por vários servidores para melhor desempenho.

Se estiver criando um aplicativo do Windows Azure (usando algum dos três modelos de execução) que precisa de armazenamento relacional, o Banco de Dados SQL pode ser uma excelente opção. Embora os aplicativos em execução fora da nuvem também possam usar esse serviço, há muitos outros cenários. Por exemplo, os dados armazenados no Banco de Dados SQL podem ser acessados em diferentes sistemas cliente, incluindo desktops, laptops, tablets e telefones. E como essa tecnologia fornece alta disponibilidade interna por meio da replicação, usar o Banco de Dados SQL pode ajudar a minimizar o tempo de inatividade.

###Tabelas###

Suponha que você queira criar um aplicativo do Windows Azure que precise de acesso rápido ao dados tipados, talvez muitos deles, mas não precise executar consultas SQL complexas nesses dados. Por exemplo, imagine que você está criando um aplicativo de consumidor que precise armazenar informações de perfil do cliente de cada usuário. Seu aplicativo será muito popular, de modo que é preciso permitir muitos dados, mas você não fará muita coisa com esses dados a não ser armazená-los e depois recuperá-los de formas simples. Esse é exatamente o tipo de cenário no qual as Tabelas do Windows Azure fazem sentido.

Não se confunda com o nome: essa tecnologia não oferece armazenamento relacional. (Na verdade, é um exemplo de uma abordagem NoSQL chamada de armazenamento de chave/valor.) No lugar, as Tabelas do Windows Azure permitem que um aplicativo armazene propriedades de vários tipos, como cadeia de caracteres, inteiros e datas. Assim, um aplicativo pode recuperar um grupo de propriedades fornecendo uma chave exclusiva para esse grupo. Embora operações complexas como junções não tenham suporte, as tabelas oferecem acesso rápido aos dados tipados. Elas também são bastante escalonáveis, com uma única tabela é possível manter volume extremamente grande de terabytes de dados. E correspondendo à sua simplicidade, fica menos caro usar tabelas do que o armazenamento relacional do Banco de Dados SQL. 

###Blobs###

A terceira opção para o gerenciamento de dados, Blobs do Windows Azure, foi desenvolvida para armazenar dados binários não estruturados. Assim como as Tabelas, os Blobs fornecem armazenamento acessíveis financeiramente e um único blob pode ser tão grande quanto um terabyte. Um aplicativo que armazena vídeos, por exemplo, ou dados de backup, ou outras informações binárias pode usar blobs para armazenamento simples e barato. Os aplicativos do Windows Azure também podem usar unidades do Windows Azure, que permitem aos blobs fornecer armazenamento persistente para um sistema de arquivos do Windows montado em uma instância do Windows Azure. O aplicativo enxerga os arquivos comuns do Windows, mas o conteúdo é armazenado em um blob.


<h2><a id="networking"></a>Rede</h2>

Hoje, o Windows Azure é executado em vários datacenters distribuídos pelos Estados Unidos, pela Europa e pela Ásia. Ao executar um aplicativo ou armazenar dados, você pode optar por usar um ou mais desses datacenters. Também é possível se conectar a esses datacenters de várias maneiras:

- Você pode usar a Rede Virtual do Windows Azure para conectar sua própria rede local a um conjunto definido de VMs do Windows Azure.

- Se seu aplicativo do Windows Azure estiver sendo executado em vários datacenters, você poderá usar o Windows Azure Gerenciador de Tráfego para rotear solicitações de usuários de maneira inteligente entre as instâncias do aplicativo.

A Figura 3 ilustra essas três opções.

![Rede do Windows Azure](./media/intro-to-windows-azure/IntroAzure4.png)   
 **Figura 3: o Windows Azure permite criar uma VPN em nuvem e distribuir solicitações de usuário de forma inteligente em diferentes datacenters.**

###Rede Virtual###

Uma maneira útil de usar uma nuvem pública é tratá-la como uma extensão do seu próprio datacenter. Como é possível criar VMs sob demanda e depois removê-las (e parar de pagar) quando elas não forem mais necessárias, você pode ter o poder da computação somente quando desejá-la. E uma vez que as Máquinas Virtuais do Windows Azure permitem criar VMs que executam SharePoint, Active Directory e outros software locais conhecidos, essa abordagem pode funcionar com os aplicativos que você já tem.

Mas para que isso seja realmente útil, convém que os usuários tratem esses aplicativos como se eles estivessem em execução no seu próprio datacenter. Isso é exatamente o que a Rede Virtual do Windows Azure permite. Usando um dispositivo de gateway VPN, um administrador pode configurar uma VPN (rede virtual privada) entre sua rede local e um grupo definido de VMs em execução no Windows Azure. Como você atribui seus próprios endereços IPv4 às VMs na nuvem, elas parecem estar na sua própria rede. Os usuários na sua organização podem acessar os aplicativos que essas VMs contêm como se estivessem sendo executados localmente.

###Gerenciador de Tráfego###

Um aplicativo do Windows Azure com usuários em apenas uma única parte do mundo pode ser executado em apenas um datacenter do Windows Azure. Entretanto, um aplicativo com usuários espalhados pelo mundo tem mais probabilidade de ser executado em vários datacenters, talvez até em todos eles. Nesta segunda situação, você enfrenta um problema: como você atribui usuários de forma inteligente às instâncias do aplicativo? Na maior parte do tempo, provavelmente você desejará que cada usuário acesse o datacenter mais próximo, pois é bem provável que este apresente o melhor tempo de resposta. Mas e se essa cópia do aplicativo estiver sobrecarregada ou indisponível? Nesse caso, seria adequado rotear as respectivas solicitações automaticamente para outro datacenter. O Gerenciador de Tráfego do Windows Azure faz exatamente isso. 

O proprietário de um aplicativo define regras que especificam como as solicitações dos usuários devem ser roteadas para os datacenters e conta com o Gerenciador de Tráfego para aplicar essas regras. Normalmente, os usuários podem ser roteados para o datacenter do Windows Azure mais próximo, por exemplo, mas são enviados para outro quando o tempo de resposta do datacenter padrão excede um determinado limite. Para aplicativos com muitos usuários distribuídos globalmente, é útil ter um serviço interno para lidar com problemas como esse.


<h2><a id="analytics"></a>Análise empresarial</h2>

Analisar dados é uma parte fundamental da forma como as empresas usam a tecnologia da informação. Uma plataforma na nuvem fornece um pool de recursos sob demanda, pagos por uso, o que a torna uma boa base para esse tipo de computação. Consequentemente, o Windows Azure apresenta duas opções de análise empresarial. A Figura 4 ilustra as opções.

![Análise do Windows Azure](./media/intro-to-windows-azure/IntroAzure5.png)   
 **Figura 4: para análise empresarial, o Windows Azure fornece relatórios e suporte para big data.**

A análise de dados pode ter muitas formas, de modo que essas duas opções são bastante diferentes. Vale a pena observar cada uma delas separadamente.

###Gerando relatórios com o Banco de Dados SQL###
Uma das maneiras mais comuns de usar dados armazenados é criando relatórios com base nesses dados.
 
Executar os SQL Server Reporting Services (SSRS) nas Máquinas Virtuais do Windows Azure permite que você crie facilmente recursos de relatórios acessíveis no seu aplicativo do Windows Azure. Você pode criar relatórios com tabelas, gráficos, mapas, medidores e mais em vários formatos, incluindo HTML, XML, PDF e Excel. 

Também é possível fazer análises com dados do Banco de Dados SQL usando ferramentas locais de business intelligence. Para um cliente, o Banco de Dados SQL parece com o SQL Server, de modo que as mesmas tecnologias podem funcionar com ambos.  
 

###HDInsight (Hadoop)###

Por muitos anos, a análise de dados em massa foi feita em dados relacionais armazenados em um data warehouse criada com um DBMS relacional. Esse tipo de análise empresarial continua sendo importante e ainda será por um longo tempo. Mas e se os dados que você deseja analisar são tão grandes que os bancos de dados relacionais não podem lidar com eles? E suponha que os dados não sejam relacionais? Eles podem ser logs de servidor em um datacenter, por exemplo, ou dados de eventos históricos de sensores, ou algo parecido. Em casos assim, você tem o que é conhecido como problema de big data. É necessária outra abordagem.

A tecnologia dominante de hoje para analisar big data é o Hadoop. Um projeto de software livre da Apache, essa tecnologia armazena dados usando o HDFS (Hadoop Distributed File System) e permite que os desenvolvedores criem tarefas MapReduce para analisar esses dados. O HDFS distribui os dados por vários servidores e em seguida executa partes da tarefa MapReduce em cada um, deixando que o big data sejam processado paralelamente.

HDInsight é o nome do serviço do baseado em Apache Hadoop do Windows Azure. Como sugere a Figura 4, o HDInsight permite que o HDFS armazene dados no cluster e distribua-os em várias VMs. Ele também dissemina a lógica de um trabalho de MapReduce entre as VMs. Assim como no Hadoop local, os dados são processados localmente (a lógica e os dados nos quais ela trabalha estão na mesma VM) e em paralelo para melhor desempenho. O HDInsight também pode armazenar dados no Windows Azure Storage Vault (ASV), que usa os blobs.  O ASV permite que você economize dinheiro, pois você pode excluir o cluster HDInsight quando não estiver em uso ainda mantendo seus dados na nuvem.
 
O HDinsight oferece suporte a outros componentes do ecossistema do Hadoop, incluindo Hive e Pig. A Microsoft também criou componentes que tornam mais fácil trabalhar com dados produzidos pelo HDInsight usando as ferramentas de BI tradicionais, como o adaptador HiveODBC e o Explorador de Dados, que funcionam com o Excel.





<h2><a id="messaging"></a>Mensagens</h2>

Não importa o que esteja fazendo, o código frequentemente precisa interagir com outro código.  Em algumas situações, tudo o que é necessário é uma mensagem básica enfileirada. Em outros casos, são necessárias interações mais complexas. O Windows Azure fornece algumas maneiras diferentes de resolver esses problemas. A Figura 5 ilustra as opções. 

![Mensagens do Windows Azure](./media/intro-to-windows-azure/IntroAzure6.png)   
 **Figura 5: para conectar aplicativos, o Windows Azure fornece filas, publicação/assinatura e conexões síncronas pela nuvem.**

###Filas###

O enfileiramento é uma ideia simples: um aplicativo coloca uma mensagem na fila e essa mensagem, por fim, é lida por outro aplicativo. Se seu aplicativo precisar apenas desse serviço direto, as Filas do Windows Azure podem ser a melhor opção. 

Um uso comum das Filas hoje em dia é para permitir que uma instância da função Web comunique-se com uma instância da função de trabalho dentro do mesmo aplicativo dos Serviços de Nuvem. Por exemplo, suponha que você crie um aplicativo do Windows Azure para compartilhamento de vídeo. O aplicativo é composto por código PHP em execução em uma função web que permite aos usuários carregar e assistir a vídeos, juntamente com uma função de trabalho implementada em C#, que converte o vídeo carregado em vários formatos. Quando uma instância da função Web obtém um novo vídeo de um usuário, ela pode armazenar o vídeo em um blob e, em seguida, enviar uma mensagem a uma função de trabalho por uma fila, informando-a onde encontrar esse novo vídeo. Uma instância da função de trabalho (não importa qual) lerá a mensagem na fila e realizará as conversões de vídeo necessárias em segundo plano. A estruturação de um aplicativo dessa maneira permite o processamento assíncrono, além de facilitar o dimensionamento do aplicativo, pois o número de instâncias da função Web e de instâncias da função de trabalho pode ser diversificado de modo independente.

###Service Bus###

Estejam eles em execução na nuvem, no data center, em um dispositivo móvel ou em qualquer outro lugar, os aplicativos precisam interagir. O objetivo do Service Bus do Windows Azure é permitir que os aplicativos executados quase em todos os lugares troquem dados.

Conforme mostra a Figura 5, o Service Bus fornece um serviço de enfileiramento. No entanto, esse serviço não é idêntico às Filas, descritas recentemente. Diferentemente das Filas do Windows Azure, por exemplo, o Service Bus fornecem mecanismos de enfileiramento (um a um) e de publicação e assinatura. Com publicação e assinatura, um aplicativo pode enviar mensagens para um tópico enquanto outros aplicativos podem criar assinaturas para esse tópico. Isso permite comunicação de um para muitos entre um conjunto de aplicativos, permitindo que a mesma mensagem seja lida por vários destinatários. E o enfileiramento não é a única opção: o Service Bus também permite comunicação direta por meio de seu serviço de retransmissão, fornecendo um meio seguro de interagir por meio de firewalls. As retransmissões do Service Bus permitem que os aplicativos se comuniquem trocando mensagens por meio de um ponto de extremidade hospedado na nuvem, em vez de localmente.

Os aplicativos que se comunicam por meio do Service Bus podem ser aplicativos ou softwares do Windows Azure em execução em alguma outra plataforma na nuvem. Entretanto, eles também podem ser aplicativos que são executados fora da nuvem. Por exemplo, pense em uma companhia aérea que implementa serviços de reserva em computadores dentro de seu próprio datacenter. A companhia precisa expor esses serviços a muitos clientes, incluindo check-in em quiosques de aeroportos, terminais de agente de reserva e talvez, até mesmo, telefones de clientes. Ela pode usar o Service Bus para fazer isso, criando interações acopladas imprecisamente entre os vários aplicativos.



<h2><a id="caching"></a>Caching</h2>

Os aplicativos tendem a acessar os mesmos dados repetidamente. Uma maneira de aprimorar o desempenho é manter uma cópia desses dados mais perto do aplicativo, minimizando o tempo necessário para recuperá-los. O Windows Azure fornece dois serviços diferentes para essa finalidade: cache de dados na memória usado pelos aplicativos do Windows Azure e uma rede de distribuição de conteúdo (CDN) que armazena em cache os dados do blob no disco mais próximo dos seus usuários. A Figura 6 mostra ambos.

![Caching do Windows Azure](./media/intro-to-windows-azure/IntroAzure7.png)   
 **Figura 6: um aplicativo do Windows Azure pode armazenar dados em cache na memória, e as cópias de um blob podem ser armazenadas em cache em sites do mundo inteiro.**

###Caching###

Acessar dados armazenados em algum dos serviços de gerenciamento de dados do Windows Azure (Banco de dados SQL, Tabelas ou Blobs) é muito rápido. Porém, acessar dados armazenados na memória é ainda mais rápido. Por esse motivo, manter uma cópia dos dados acessados frequentemente na memória pode aumentar o desempenho do aplicativo. É possível usar o Caching na memória do Windows Azure para fazer isso. 

Um aplicativo dos Serviços de Nuvem pode armazenar dados nesse cache e recuperá-los diretamente sem precisar acessar o armazenamento persistente. Como mostrado na Figura 6, o cache pode ser mantido dentro das VMs do seu aplicativo ou ser fornecido pelas VMs dedicadas exclusivamente ao caching. Em ambos os casos, o cache pode ser distribuído, com os dados nele contidos espalhados por várias VMs em um datacenter do Windows Azure. 

Um aplicativo que lê repetidamente um catálogo de produtos pode se beneficiar do uso desse tipo de caching, por exemplo, uma vez que os dados necessários serão disponibilizados mais rapidamente. A tecnologia também oferece suporte ao bloqueio, permitindo que ele seja usado com dados de leitura/gravação, bem como somente leitura. E os aplicativos ASP.NET podem usar o serviço para armazenar dados da sessão com apenas uma alteração na configuração. 

###CDN###

Suponha que você precise armazenar dados de blob que serão acessados por usuários no mundo todo. Talvez seja um vídeo do último jogo da Copa do Mundo, por exemplo, ou atualizações de driver, ou um e-book popular. Armazenar uma cópia dos dados em vários datacenters do Windows Azure ajudará, mas se houver muitos usuários, provavelmente não será suficiente. Para um desempenho ainda melhor, você pode usar a CDN do Windows Azure.

A CDN tem dezenas de sites pelo mundo, cada um capaz de armazenar cópias dos blobs do Windows Azure. Na primeira vez que um usuário em alguma parte do mundo acessar um blob específico, as informações que ele contém serão copiadas de um datacenter do Windows Azure em um armazenamento CDN local na região geográfica. Depois disso, os acessos dessa parte do mundo usarão a cópia blob armazenada em cache na CDN - eles não precisarão fazer todo o caminho até o datacenter mais próximo do Windows Azure. O resultado é um acesso mais rápido aos dados frequentemente acessados por usuários, em qualquer lugar do mundo.



<h2><a id="identity"></a>Identidade</h2>

Trabalhar com identidade faz parte da maioria dos aplicativos. Por exemplo, saber quem um usuário é, permite que um aplicativo decida como deve interagir com ele. Para ajudar nisso, a Microsoft fornece o Active Directory do Windows Azure.

Assim como a maioria dos serviços de diretório, o Active Directory do Windows Azure armazena informações sobre usuários e as organizações às quais eles pertencem. Com ele, os usuários podem fazer logon e recebem tokens que podem apresentar aos aplicativos para provar sua identidade. Ele também permite a sincronização de informações do usuário, sendo executado em locais de sua rede local. Embora os mecanismos e os formatos de dados usados pelo Active Directory do Windows Azure não sejam idênticos aos usados no Active Directory do Windows Server, as funções que ele executa são bastante semelhantes. 

É importante entender que o Active Directory do Windows Azure foi desenvolvido basicamente para uso pelos aplicativos em nuvem. Ele pode ser usado pelos aplicativos executados no Windows Azure, por exemplo, em outras plataformas na nuvem. Também pode ser usado pelos próprios aplicativos em nuvem da Microsoft, como os do Office 365. No entanto, se desejar estender o datacenter para a nuvem usando as Máquinas Virtuais do Windows Azure e a Rede Virtual do Windows Azure, o Active Directory do Windows Azure não é a opção certa. Em vez disso, será conveniente executar o Active Directory do Windows Server nas VMs em nuvem, conforme descrito anteriormente.

Para permitir que os aplicativos acessem as informações que ele contém, o Active Directory do Windows Azure fornece uma API RESTful chamada Gráfico do Active Directory do Windows Azure. Essa API permite que os aplicativos que são executados em qualquer plataforma acessem objetos de diretório e os relacionamentos entre eles.  Por exemplo, um aplicativo autorizado pode usar essa API para saber mais sobre um usuário, os grupos aos quais ele pertence, entre outras informações. Os aplicativos também podem enxergar os relacionamentos entre os usuários (o gráfico social), o que os permite trabalhar de modo mais inteligente com as conexões entre as pessoas.

Outro recurso desse serviço, o Access Control do Active Directory do Windows Azure, torna mais fácil para um aplicativo aceitar informações de identidade do Facebook, Google, Windows Live ID e outros provedores de identidade conhecidos. Em vez de exigir que o aplicativo entenda os diversos formatos de dados e protocolos usados por cada um desses provedores, o Access Control converte todos eles em um único formato comum. Ele também permite que um aplicativo aceite logons de um ou mais domínios Active Directory. Por exemplo, um fornecedor que oferece um aplicativo SaaS pode usar o Access Control do Active Directory do Windows Azure para fornecer aos usuários em cada um de seus clientes um logon único ao aplicativo.

Os serviços de diretório são um suporte importante de uma computação local. Não é de se surpreender que eles também sejam importantes na nuvem.

<h2><a id="media"></a>Mídia</h2>

A reprodução de vídeos constitui uma grande parte do tráfego de Internet hoje em dia, e essa porcentagem será ainda maior no futuro. Apesar disso, fornecer vídeo na web não é algo simples. Há muitas variáveis, como o algoritmo de codificação e a resolução de vídeo da tela do usuário. Vídeos também tendem a ter picos de demanda, como um pico de noite de sábado quando muitas pessoas decidem assistir um filme on-line. 

Dada sua popularidade, é seguro apostar que muitos aplicativos novos que usam vídeo serão criados. Mas todos eles precisarão solucionar alguns dos mesmos problemas, e fazer com que cada um resolva esses problemas por conta própria não faz nenhum sentido. Uma abordagem melhor é criar uma plataforma que ofereça soluções comuns para muitos aplicativos. E criar essa plataforma em nuvem tem algumas vantagens claras. Pode ser amplamente disponível em um formato pago pelo uso e também pode lidar com a variabilidade na demanda com a qual os aplicativos de vídeo sempre se deparam.

Os Serviços de Mídia do Windows Azure resolvem esse problema. Eles fornecem um conjunto de componentes de nuvem que tornam mais fácil a vida das pessoas que criam e executam aplicativos usando vídeo e outras mídias. A Figura 7 ilustra a tecnologia.

![Serviços de Mídia do Windows Azure](./media/intro-to-windows-azure/IntroAzure8.png)   
 **Figura 7: os Serviços de Mídia são uma plataforma para aplicativos que oferecem vídeo e outras mídias para clientes em todo o mundo.**


Como mostra a figura, os Serviços de Mídia oferecem um conjunto de componentes para aplicativos que funcionam com vídeo e outras mídias. Por exemplo, ele inclui um componente de inclusão de vídeo para carregar o vídeo nos Serviços de Mídia (onde ele é armazenado nos Blobs do Windows Azure), um componente de codificação que oferece suporte a vários formatos de áudio e vídeo, um componente de proteção de conteúdo que oferece gerenciamento de direitos digitais, um componente para inserir anúncios em um fluxo de vídeo, componentes de streaming e muito mais. Os parceiros da Microsoft também podem fornecer os componentes para a plataforma para que a Microsoft distribua esses componentes e cobre em seu nome. 

Os aplicativos que usam essa plataforma podem ser executados no Windows Azure ou em outro lugar. Por exemplo, um aplicativo de desktop para uma casa de produção de vídeo pode permitir que seus usuários carreguem vídeo nos Serviços de Mídia para, em seguida, processá-los de várias maneiras. Como alternativa, um serviço de gerenciamento de conteúdo com base em nuvem em execução no Windows Azure pode depender dos Serviços de Mídia para processar e distribuir vídeo. Onde quer que ele seja executado e seja o que for que ele faz, cada aplicativo escolhe os componentes que precisa usar, acessando-os por meio de interfaces RESTful. 

Para distribuir o que produz, um aplicativo pode usar o CDN do Windows Azure, outro CDN ou apenas enviar bits diretamente aos usuários. No entanto, vídeos criados usando os Serviços de Mídia podem ser consumidos por vários sistemas de clientes, incluindo Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash e Silverlight. O objetivo é tornar mais fácil a criação de aplicativos de mídia modernos. 

Para obter um panorama mais visual do funcionamento dos Serviços de Mídia, faça o download do [pôster Serviços de Mídia do Windows Azure][Windows Azure Media Services Poster]


<h2><a id="HPC"></a>HPC (Computação de Alto Desempenho)</h2>

Uma das maneiras mais atraentes de usar uma plataforma na nuvem é para HPC (computação de alto desempenho). A essência da HPC é executar código em várias máquinas ao mesmo tempo. No Windows Azure, isso significa executar muitas máquinas virtuais simultaneamente, todas trabalhando paralelamente para resolver algum problema. Fazer isso exige algum modo de programar aplicativos, isto é, distribuir o respectivo trabalho entre essas instâncias. Para permitir isso, o Windows Azure fornece o HPC Scheduler. 

Esse componente pode trabalhar com aplicativos HPC criados para usar MPI (Message Passing Interface), que é padrão da indústria. O software que limita a análise de elementos, como simulações de acidentes de carro, é um exemplo desse tipo de aplicativo. E existem muitos outros. O HPC Scheduler também pode ser usado com os então chamados aplicativos embaraçosamente paralelos, como as simulações Monte Carlo. Seja qual for o problema solucionado, o valor que ele oferece é o mesmo: o HPC Scheduler lida com o problema complexo de programar o trabalho de computação paralela entre várias máquinas virtuais do Windows Azure. O objetivo é facilitar a criação de aplicativos HPC em execução na nuvem. 

<h2><a id="commerce"></a>Comércio</h2>

O surgimento de Software como Serviço está transformando a maneira como criamos aplicativos. Ele também está transformando a forma como vendemos aplicativos. Como um aplicativo SaaS reside na nuvem, faz sentido que seus clientes em potencial devam procurar soluções on-line. E essa alteração se aplica a dados assim como aplicativos. Por que as pessoas não devem procurar a nuvem em busca de conjuntos de dados disponíveis no mercado? A Microsoft aborda essas duas preocupações com o [Windows Azure Marketplace](http://datamarket.azure.com/) e a [Windows Azure Store (as páginas podem estar em inglês)](http://www.windowsazure.com/pt-br/store/overview/), ilustradas na Figura 8.

![Windows Azure Commerce](./media/intro-to-windows-azure/IntroAzure9.png)   
 **Figura 8: o Windows Azure Marketplace e a Windows Azure Store permitem encontrar e comprar aplicativos do Windows Azure e conjuntos de dados comerciais.**

A diferença entre os dois é que o Marketplace está fora do Portal de Gerenciamento do Windows Azure, mas a Store pode ser acessada a partir do portal. Clientes potenciais podem pesquisar para encontrar aplicativos do Windows Azure que atendam às suas necessidades e, em seguida, inscrever-se para usá-los por meio do criador do aplicativo ou diretamente por meio do Marketplace ou da Store. Os clientes também podem pesquisar conjuntos de dados comerciais, incluindo dados demográficos, dados financeiros, dados geográficos e muito mais. Quando eles encontram algo que gostem, podem acessar do fornecedor, diretamente pelos locais da web Marketplace ou Store de ou, em alguns casos, do Portal de Gerenciamento. Os aplicativos também podem usar a API de pesquisa Bing por meio do Marketplace, dando-lhes acesso aos resultados de pesquisas na web.


<h2><a id="sdk"></a>SDKs</h2>

Em 2008, a primeira versão de pré-lançamento do Windows Azure oferecia suporte apenas para desenvolvimento .NET. Hoje, no entanto, você pode criar aplicativos do Windows Azure em praticamente qualquer linguagem. Atualmente, a Microsoft fornece SDKs específicos a cada linguagem para .NET, Java, PHP, Node. js, Ruby e Python. Também há um SDK geral do Windows Azure que fornece suporte básico para qualquer linguagem, como C++. 

Estes SDKs vão ajudá-lo a desenvolver, implantar e gerenciar aplicativos do Windows Azure. Eles estão disponíveis em www.windowsazure.com ou no GitHub e podem ser usados com o Visual Studio e o Eclipse. O Windows Azure também oferece ferramentas de linha de comando que os desenvolvedores podem usar qualquer editor ou ambiente de desenvolvimento, incluindo ferramentas para implantar aplicativos no Windows Azure em sistemas Linux e Macintosh. 

Além de ajudar a desenvolver aplicativos, estes SDKs também oferecem bibliotecas de cliente que ajudam você criar software executando fora da nuvem que use os serviços do Windows Azure. Por exemplo, você pode criar um aplicativo em execução em um hoster que dependa de blobs do Windows Azure ou criar uma ferramenta que implante aplicativos do Windows Azure por meio da interface de gerenciamento do Windows Azure. 


<h2><a id="start"></a>Introdução</h2>

Agora que você tem a visão geral, a próxima etapa é escrever seu primeiro aplicativo do Windows Azure. Escolha a linguagem, [obtenha o SDK adequado](http://www.windowsazure.com/pt-br/downloads/) e vá em frente. Computação em nuvem é o novo padrão: comece agora.



[Cartaz dos Serviços de Mídia do Windows Azure]: http://www.microsoft.com/pt-br/download/details.aspx?id=38195


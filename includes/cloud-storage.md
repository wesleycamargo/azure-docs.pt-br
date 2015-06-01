#Gerenciamento de dados e análise empresarial

O gerenciamento e a análise de dados na nuvem são tão importantes quanto em qualquer outro lugar. Para que seja possível fazer isso, o Azure fornece uma gama de tecnologias para trabalhar com dados relacionais e não relacionais. Este artigo apresenta cada uma das opções.

##Sumário      

- [Armazenamento de Blob](#blob)
- [Executando um DBMS em uma máquina virtual](#dbinvm)
- [Banco de Dados SQL](#sqldb)
	- [Sincronização de Dados do SQL](#datasync)
	- [Relatórios de dados do SQL usando máquinas virtuais](#datarpt)
- [Armazenamento de tabela](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Armazenamento de Blob

A palavra "blob" é a abreviação de "objeto binário grande" e descreve exatamente o que é um blob: uma coleção de informações binárias. No entanto, embora sejam simples, os blobs são bastante úteis. A [Figura 1](#Fig1) ilustra as noções básicas do armazenamento de blob do Azure.

<a name="Fig1"></a>![Diagrama de Blobs][blobs]
 
**Figura 1: armazenamento de blob do Azure armazena dados binários - blobs - em contêineres.**

Para usar blobs, primeiro você deve criar uma *conta de armazenamento* do Azure. Para isso, especifique o datacenter do Azure que armazenará os objetos criados ao usar essa conta. Independentemente de onde estiverem, cada blob que você criar pertencerá a um contêiner na sua conta de armazenamento. Para acessar um blob, um aplicativo fornece uma URL com o formato:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt;

&lt;*Conta do Armazenamento*&gt; será um identificador exclusivo atribuído quando uma nova conta de armazenamento for criada, enquanto &lt;*Contêiner*&gt; e &lt;Nome do Blob*&gt; forem os nomes de um contêiner específico e um blob nesse contêiner.

O Azure fornece dois tipos diferentes de blobs. As opções são:

- Blobs de *bloco*, cada um dos quais pode conter até 200 gigabytes de dados. Como o nome sugere, um blob de bloco é subdividido em certo número de blocos. Se ocorrer uma falha durante a transferência de um blob de bloco, a retransmissão poderá continuar com o bloco mais recente em vez de enviar o blob inteiro novamente. Blobs de bloco são uma abordagem bastante geral para armazenamento, e são do tipo de blob mais usado hoje em dia.

- Blobs de *página*, os quais podem chegar a um terabyte cada. Os blobs de página foram criados para acesso aleatório e, portanto, cada um é dividido em certo número de páginas. Um aplicativo é gratuito para leitura e gravação de páginas individuais aleatoriamente no blob. Nas Máquinas Virtuais do Azure, por exemplo, as VMs que você criar usam blobs de página como armazenamento persistente para discos de sistema operacional e discos de dados.

Se você escolher blobs de bloco ou blobs de página, os aplicativos podem acessar dados do blob de diversas maneiras. As opções incluem o seguinte:

- Diretamente por meio de um protocolo RESTful (por exemplo, com base em HTTP). Aplicativos do Azure e os aplicativos externos, incluindo aplicativos que são executados no local, podem usar essa opção.
- Usando a biblioteca do cliente de armazenamento do Azure, que fornece uma interface mais amigável para o desenvolvedor sobre o protocolo RESTful de acesso do blob bruto. Mais uma vez, aplicativos do Azure e os aplicativos externos podem acessar blobs usando essa biblioteca.
- Usando unidades do Azure, uma opção que permite ao aplicativo do Azure tratar um blob de página como uma unidade local com um sistema de arquivos NTFS. Para o aplicativo, o blob de página se parece com um sistema de arquivos comum do Windows acessado por meio de E/S padrão de arquivo. Na verdade, leituras e gravações são enviadas para o blob de página subjacente que implementa a unidade do Azure. 

Para proteger contra falhas de hardware e aumentar a disponibilidade, cada blob é replicado através de três computadores em um datacenter do Azure. A gravação em um blob atualiza todas as três cópias, assim as leituras posteriores não verão resultados inconsistentes. Você também pode especificar que os dados do blob devem ser copiados para outro datacenter do Azure no mesmo local, mas a pelo menos 500 milhas de distância. Essa cópia, chamada *replicação geográfica*, ocorre dentro de poucos minutos após uma atualização para o blob, e é útil para recuperação de desastres.

Os dados nos blobs também podem estar disponíveis por meio da *CDN (Rede de Fornecimento de Conteúdo)*. do Azure. Ao armazenar em cache cópias de dados de blobs em dezenas de servidores em todo o mundo, a CDN pode acelerar o acesso às informações acessadas repetidamente.

Simples assim, os blobs são a escolha certa em muitas situações. O armazenamento e o streaming de áudio e vídeo são exemplos óbvios, por serem como backups e outros tipos de arquivamento de dados. Os desenvolvedores também podem usar blobs para manter qualquer tipo de dados não estruturados que desejarem. Ter uma maneira direta de armazenar e acessar dados binários pode ser surpreendentemente útil.


## <a name="dbinvm"></a>Executando um DBMS em uma máquina virtual

Atualmente, muitos aplicativos dependem de algum tipo de sistema de gerenciamento de bancos de dados (DBMS). Os sistemas relacionais, como o SQL Server são as opções mais usadas, mas as abordagens não relacionais, normalmente conhecidas como tecnologias *NoSQL*, se tornam mais populares a cada dia. Para permitir que aplicativos em nuvem usem essas opções de gerenciamento de dados, as Máquinas Virtuais do Azure permitem que você execute um DBMS (relacional ou NoSQL) em uma VM. A [Figura 2](#Fig2) mostra isso com o SQL Server.

<a name="Fig2"></a>![Diagrama do SQL Server em uma máquina virtual][SQLSvr-vm]
 
**Figura 2: máquinas virtuais do Azure permitem executar um DBMS em uma VM com persistência fornecida pelos blobs.**

Para desenvolvedores e administradores de bancos de dados, esse cenário é muito parecido com a execução do mesmo software em seu próprio datacenter. No exemplo mostrado aqui, por exemplo, quase todos os recursos do SQL Server podem ser usados, e você tem acesso administrativo total ao sistema. Você também tem a responsabilidade de gerenciar o servidor de banco de dados, é claro, como se fosse executado localmente.

Como a [Figura 2](#Fig2) mostra, seus bancos de dados parecem estar armazenados no disco local da VM em execução no servidor. Internamente, no entanto, cada um desses discos é gravado em um blob do Azure. (É semelhante ao uso de uma SAN em seu próprio datacenter, com um blob que atua de forma muito parecida com um LUN.) Como com qualquer blob do Azure, os dados que ele contém são replicados três vezes em um datacenter e, se você solicitar, serão replicados geograficamente para outro datacenter na mesma região. Também é possível usar opções como espelhamento de banco de dados do SQL Server para obter maior confiabilidade.

Outra maneira de usar o SQL Server em uma VM é criar um aplicativo híbrido, onde os dados se localizam no Azure enquanto a lógica do aplicativo é executada no local. Por exemplo, isso pode fazer sentido quando aplicativos que são executados em vários locais ou em vários dispositivos móveis devem compartilhar os mesmos dados. Para tornar a comunicação mais simples entre o banco de dados na nuvem e a lógica local, uma organização pode usar a Rede Virtual do Azure para criar uma conexão VPN (rede virtual privada) entre um datacenter do Azure e seu próprio datacenter local.


## <a name="sqldb"></a>Banco de Dados SQL

Para muitas pessoas, executar um DBMS em uma VM é a primeira opção que vem à mente para o gerenciamento de dados estruturados na nuvem. No entanto, não é a única opção, e nem sempre é a melhor escolha. Em alguns casos, o gerenciamento de dados usando uma plataforma como uma abordagem de Serviço (PaaS) faz mais sentido. O Azure oferece uma tecnologia PaaS chamada de Banco de dados SQL que permite fazer isso para dados relacionais. A [Figura 3](#Fig3) ilustra essa opção.

<a name="Fig3"></a>![Diagrama de Banco de dados SQL][SQL-db]
 
**Figura 3: o Banco de Dados SQL fornece um serviço de armazenamento relacional PaaS compartilhado.**

O Banco de dados SQL não dá a cada cliente sua própria instância física do SQL Server. Em vez disso, ele fornece um serviço multilocatário, com um servidor de Banco de dados SQL lógico para cada cliente. Todos os clientes compartilham a capacidade de computação e de armazenamento que o serviço oferece. E como acontece com o Armazenamento de Blob, todos os dados no Banco de dados SQL são armazenados em três computadores separados em um datacenter do Azure, dando uma alta disponibilidade (HA) integrada aos seus bancos de dados.

Para um aplicativo, o Banco de dados SQL é muito parecido com o SQL Server. Os aplicativos podem executar consultas no SQL em tabelas relacionais, usar procedimentos armazenados no T-SQL e executar transações em várias tabelas. Além disso, como os aplicativos acessam o Banco de dados SQL usando o protocolo TDS (Tabular Data Stream), o mesmo protocolo usado para acessar o SQL Server, eles podem funcionar com dados usando o Entity Framework, ADO.NET, JDBC e outras interfaces de acesso a dados familiares.

Mas, como o Banco de dados SQL é um serviço de nuvem sendo executado em datacenters do Azure, você não precisa gerenciar qualquer um dos aspectos físicos do sistema, como o uso do disco. Você também não precisa se preocupar sobre a atualização de software ou lidar com outras tarefas administrativas de nível baixo. Cada organização de clientes ainda controla seus próprios bancos de dados, é claro, incluindo seus esquemas e logons de usuário, mas muitas das tarefas administrativas comuns são feitas para você.

Embora o Banco de dados SQL seja muito parecido com o SQL Server para aplicativos, ele não se comporta exatamente como um DBMS em execução em uma máquina virtual ou física. Por ele ser executado no hardware compartilhado, seu desempenho variará com a carga colocada nesse hardware por todos os seus clientes. Isso significa que o desempenho de, digamos, um procedimento armazenado no Banco de dados SQL pode variar de um dia para o próximo.

Hoje, o Banco de dados SQL permite que você crie um banco de dados mantendo até 150 gigabytes. Se você precisar trabalhar com bancos de dados maiores, o serviço fornece uma opção chamada *Federação*. Para fazer isso, um administrador de bancos de dados cria dois ou mais *membros da federação*, cada um deles é um banco de dados separado com seu próprio esquema. Os dados são distribuídos entre esses membros, algo que geralmente é conhecido como *fragmentação*, com cada membro atribuído a uma *chave de federação* exclusiva. Um aplicativo executa consultas no SQLs sobre esses dados, especificando a chave de federação que identifica o membro de federação ao qual a consulta deve ter como destino. Isso permite usar uma abordagem relacional tradicional com grandes quantidades de dados. Como sempre, há desvantagens; as consultas nem as transações podem abranger os membros da federação, por exemplo. Mas, quando um serviço PaaS relacional é a melhor opção e essas desvantagens são aceitáveis, usar o SQL Federation pode ser uma boa solução.

O Banco de dados SQL pode ser usado por aplicativos que são executados no Azure ou em outro local, como em seu datacenter local. Isso é útil para aplicativos em nuvem que precisam de dados relacionais, bem como aplicativos no local que podem se beneficiar do armazenamento de dados em nuvem. Um aplicativo móvel pode depender do Banco de dados SQL para gerenciar dados relacionais compartilhados, por exemplo, como poderia um aplicativo de inventário que é executado em vários revendedores em todo o mundo.

Pensar sobre o Banco de dados SQL gera um problema óbvio (e importante): quando você deve executar o SQL Server em uma VM e quando o Banco de dados SQL é a melhor opção? Como sempre, há compensações, e então qual abordagem é melhor varia de acordo com suas necessidades.

Uma maneira simples de pensar sobre isso é exibir o Banco de dados SQL como sendo de novos aplicativos, enquanto o SQL Server em uma VM é uma opção melhor quando você está movendo um aplicativo existente local para a nuvem. No entanto, também pode ser útil examinar essa decisão de forma mais refinada. Por exemplo, o Banco de dados SQL é mais fácil de usar, uma vez que a instalação e a administração são mínimas. Mas, ao executar o SQL Server em uma VM pode haver um desempenho mais previsível - não é um serviço compartilhado - e ele também oferece suporte a bancos de dados maiores não federados que o Banco de dados SQL. Ainda assim, o Banco de dados do SQL fornece replicação interna de dados e processamento, com eficiência, dando um DBMS de alta disponibilidade com muito pouco trabalho. Enquanto o SQL Server proporciona maior controle e um conjunto de opções um pouco mais amplo, o Banco de dados SQL é mais simples de configurar e significativamente muito menos trabalho para gerenciar.

Por fim, é importante destacar que o Banco de dados SQL não é o único serviço de dados PaaS disponível no Azure. Os parceiros da Microsoft também oferecem outras opções. Por exemplo, o ClearDB oferece uma oferta de PaaS de MySQL PaaS, enquanto a Cloudant vende uma opção NoSQL. Os serviços de dados PaaS são a solução certa em muitas situações e, portanto, essa abordagem de gerenciamento de dados é uma parte importante do Azure.


### <a name="datasync"></a>Sincronização de Dados do SQL

Enquanto o Banco de dados SQL mantém três cópias de cada banco de dados em um único datacenter do Azure, ele automaticamente não replica dados entre os datacenters do Azure. Em vez disso, ele fornece a Sincronização de dados do SQL, um serviço que você pode usar para fazer isso. A [Figura 4](#Fig4) mostra isso.

<a name="Fig4"></a>![Diagrama da Sincronização de Dados do SQL][SQL-datasync]
 
**Figura 4: a sincronização de dados do SQL sincroniza os dados no Banco de Dados SQL com dados em outros datacenters locais e do Azure.**

Como mostra o diagrama, a Sincronização de dados do SQL pode sincronizar dados em diferentes locais. Suponha que você está executando um aplicativo em vários datacenters do Azure, por exemplo, com dados armazenados no Banco de dados SQL. Você pode usar a Sincronização de dados do SQL para manter esses dados sincronizados. A Sincronização de dados do SQL também pode sincronizar dados entre um datacenter do Azure e uma instância do SQL Server em execução em um datacenter local. Isso pode ser útil para manter uma cópia local dos dados usados por aplicativos locais e uma cópia em nuvem usada por aplicativos em execução no Azure. E, embora não seja mostrado na figura, a Sincronização de dados do SQL também pode ser usada para sincronizar dados entre o Banco de dados do SQL e o SQL Server em execução em uma VM no Azure ou em outro lugar.

A sincronização pode ser bidirecional e você determina exatamente quais dados serão sincronizados e com que frequência será feita. (A sincronização entre bancos de dados não é atômica, porém, sempre há pelo menos algum atraso). E, apesar de ser usada, a configuração da sincronização com a Sincronização de dados do SQL é inteiramente controlada pela configuração; não há nenhum código a ser gravado.


### <a name="datarpt"></a>Relatórios de dados do SQL usando máquinas virtuais

Depois que um banco de dados contiver dados, alguém provavelmente desejará criar relatórios usando esses dados. O Azure pode executar os SQL Server Reporting Services (SSRS) nas máquinas virtuais do Azure, o que é funcionalmente equivalente à execução local do SQL Server Reporting Services. Em seguida, você pode usar os SSRS para executar relatórios dos dados armazenados em um Banco de dados SQL do Azure. A [Figura 5](#Fig5) mostra como o processo funciona.

<a name="Fig5"></a>![Diagrama de relatórios SQL][SQL-report]
 
**Figura 5: SQL Server Reporting Services em execução nas máquinas virtuais do Azure fornecem serviços de relatório de dados no Banco de Dados SQL.**

Antes que um usuário possa ver um relatório, alguém define a aparência desse relatório (etapa 1). Com SSRS em uma VM, isso pode ser feito usando uma das duas ferramentas: ferramentas de dados do SQL Server, parte do SQL Server 2012, ou seu predecessor, ou o Business Intelligence (BI) Development Studio. Assim como ocorre com SSRS, essas definições de relatório são expressas em linguagem RDL. Após os arquivos RDL de um relatório terem sido criados, eles serão carregados para uma VM na nuvem (etapa 2). A definição de relatório agora está pronta para uso.

Em seguida, um usuário do aplicativo acessa o relatório (etapa 3). O aplicativo passa essa solicitação para a VM SSRS (etapa 4), que entra em contato com o Banco de dados SQL ou outras fontes de dados para obter os dados necessários (etapa 5). SSRS usa esses dados e os arquivos RDL relevantes para processar o relatório (etapa 6), em seguida, retorna o relatório para o aplicativo (etapa 7), que o exibe para o usuário (etapa 8).

O cenário mostrado aqui, a incorporação de um relatório em um aplicativo, não é a única opção. Também é possível exibir relatórios no Gerenciador de Relatórios na VM, no SharePoint na VM, ou de outras maneiras. Os relatórios também podem ser combinados com um relatório que contém um link para outro.

SSRS em uma VM do Azure oferecem funcionalidade completa como solução de relatório na nuvem. Os relatórios podem usar qualquer fonte de dados com o suporte do SSRS. Aplicativos e relatórios podem incluir código incorporado ou assemblies para oferecer suporte a comportamentos personalizados. O processamento e a execução do relatório são rápidos porque o mecanismo e o conteúdo do servidor de relatório são executados juntos no mesmo servidor virtual.



## <a name="tblstor"></a>Armazenamento de tabela

Os dados relacionais são úteis em muitas situações, mas nem sempre é a escolha certa. Por exemplo, se o seu aplicativo precisar de acesso rápido e simples a grandes quantidades de dados estruturados de modo irregular, um banco de dados relacional poderá não funcionar bem. Uma tecnologia NoSQL será provavelmente a melhor opção.

O Armazenamento de tabela do Azure é um exemplo desse tipo de abordagem do NoSQL. Apesar do nome, o Armazenamento de tabela não oferece suporte a tabelas relacionais padrão. Em vez disso, ele fornece o que é conhecido como um *repositório de chave/valor*, associando um conjunto de dados com uma chave particular e, em seguida, permite que um aplicativo acesse esses dados, fornecendo a chave. A [Figura 6](#Fig6) ilustra os princípios básicos.

<a name="Fig6"></a>![Diagrama de armazenamento de tabela][SQL-tblstor]
 
**Figura 6: o armazenamento de tabela do Azure é um armazenamento de chave/valor que fornece acesso rápido e simples a grandes quantidades de dados.**

Assim como os blobs, cada tabela é associada a uma conta de armazenamento do Azure. As tabelas também são nomeadas como os blobs, com uma URL do formato

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Como mostra a figura, cada tabela é dividida em um número de partições, cada uma delas pode ser armazenada em uma máquina separada. (Isso é uma forma de fragmentação, assim como acontece com o SQL Federation.) Os aplicativos do Azure e os aplicativos executados em qualquer lugar podem acessar uma tabela usando o protocolo RESTful OData ou a biblioteca do cliente de armazenamento do Azure.

Cada partição em uma tabela contém um número de *entidades*, cada uma contendo até 255 *propriedades*. Cada propriedade tem um nome, um tipo (por exemplo, Binário, Bool, DateTime, Int ou String) e um valor. Ao contrário do armazenamento relacional, essas tabelas não têm nenhum esquema fixo e entidades tão diferentes na mesma tabela podem conter propriedades com tipos diferentes. Por exemplo, uma entidade pode ter apenas uma propriedade String contendo um nome, enquanto outra entidade na mesma tabela tem duas propriedades Int que contêm um número de identificação do cliente e uma avaliação de crédito.

Para identificar uma entidade específica em uma tabela, um aplicativo fornece a chave da entidade. A chave tem duas partes: uma *chave de partição* que identifica uma partição específica e uma *chave de linha* que identifica uma entidade nessa partição. Na [Figura 6](#Fig6), por exemplo, o cliente solicita a entidade com a chave da partição A e a chave de linha 3, e o armazenamento de tabela retorna essa entidade, incluindo todas as propriedades que ele contém.

Essa estrutura permite que as tabelas sejam grandes - uma única tabela pode conter até 100 terabytes de dados - e permite o acesso rápido aos dados que elas contêm. No entanto, também há limitações. Por exemplo, não há nenhum suporte para atualizações transacionais que abrangem tabelas e até mesmo partições em uma única tabela. Um conjunto de atualizações para uma tabela só pode ser agrupado em uma transação atômica se todas as entidades envolvidas estão na mesma partição. Não há também como consultar uma tabela que se baseia no valor de suas propriedades, nem há suporte para uniões em várias tabelas. E diferentemente dos bancos de dados relacionais, as tabelas não têm suporte para procedimentos armazenados.

O armazenamento de tabela do Azure é uma boa opção para aplicativos que precisam de acesso rápido e barato a grandes quantidades de dados estruturados de modo irregular. Por exemplo, um aplicativo da Internet que armazena informações de perfil para muitos usuários pode usar tabelas. O acesso rápido é importante nessa situação, e o aplicativo provavelmente não precisa da capacidade total do SQL. Desistir dessa funcionalidade para ganhar velocidade e tamanho, às vezes, pode fazer sentido, e por isso o armazenamento de tabela é apenas uma solução adequada para alguns problemas.


## <a name="hadoop"></a>Hadoop

As organizações têm criado data warehouses durante décadas. Essas coleções de informações, mais frequentemente armazenadas em tabelas relacionais, permitem que as pessoas trabalhem com e aprendam a partir de dados de várias maneiras diferentes. Por exemplo, com o SQL Server é comum usar ferramentas, como o SQL Server Analysis Services para fazer isso.

Mas, suponha que você deseja fazer análise de dados não relacionais. Os dados podem assumir várias formas: informações de sensores ou etiquetas RFID, arquivos de log em farms de servidores, dados de sequência de cliques produzidos por aplicativos da Web, imagens de equipamentos médicos de diagnóstico e muito mais. Esses dados também podem ser realmente grandes; muito grandes para serem usados com eficiência com um data warehouse tradicional. Problemas de dados grandes como esse, raros há poucos anos, agora têm se tornado muito comum.

Para analisar esse tipo de dados grandes, o nosso setor amplamente convergiu para uma única solução: a tecnologia de software livre Hadoop. O Hadoop é executado em um cluster de máquinas físicas ou virtuais, difundindo os dados que ele trabalha nessas máquinas e processando-os em paralelo. Quanto mais máquinas o Hadoop tem que usar, mais rápido ele pode concluir qualquer trabalho que estiver fazendo.

Esse tipo de problema é um ajuste natural para a nuvem pública. Em vez de manter um exército de servidores locais que podem ficar ociosos a maior parte do tempo, ao executar o Hadoop na nuvem, você cria (e paga por isso) VMs somente quando precisar delas. E o melhor é que, os dados grandes que você deseja analisar com Hadoop são criados na nuvem, evitando que você tenha de deslocá-los. Para ajudá-lo a explorar essas sinergias, a Microsoft fornece um serviço Hadoop no Azure. A [Figura 7](#Fig7) mostra os componentes mais importantes desse serviço.

<a name="Fig7"></a>![Diagrama de hadoop][hadoop]

**Figura 7: o Hadoop no Microsoft Azure executa trabalhos de MapReduce que processam dados em paralelo usando várias máquinas virtuais.**

Para usar o Hadoop no Azure, primeiro você solicita que essa plataforma de nuvem crie um cluster Hadoop, especificando o número de VMs que você precisa. A configuração de um cluster Hadoop não é uma tarefa trivial, por isso permitir que o Windows Azure faça isso por você faz sentido. Ao terminar de usar o cluster, feche-o. Não é necessário pagar por recursos de computação que você não esteja usando.

Um aplicativo de Hadoop, normalmente chamado de um *trabalho*, utiliza um modelo de programação conhecido como *MapReduce*. Como mostra a figura, a lógica de um trabalho de MapReduce é executado simultaneamente em várias VMs. Ao processar dados em paralelo, o Hadoop pode analisar dados muito mais rapidamente que as soluções de máquina única.

No Azure, os dados com os quais um trabalho do MapReduce funciona normalmente são mantidos no armazenamento de blob. Entretanto, no Hadoop, os trabalhos do MapReduce esperam que os dados sejam armazenados no *HDFS (Hadoop Distributed File System)*. O HDFS é semelhante, de algum modo, ao armazenamento de blob; por exemplo, ele replica dados no vários servidores físicos. Em vez de duplicar essa funcionalidade, o Hadoop no Azure expõe o armazenamento de blob por meio da API do HDFS, como mostra a figura. Enquanto a lógica em um trabalho de MapReduce pensa que está acessando arquivos comuns HDFS, o trabalho, na verdade, está trabalhando com dados transmitidos para ele a partir de blobs. E para dar suporte ao caso em que vários trabalhos são executados com os mesmos dados, o Hadoop no Azure também permite copiar dados dos blobs para o HDFS executado em VMs.

Atualmente, os trabalhos de MapReduce, em geral, são gravados em Java, uma abordagem a que o Hadoop no Azure oferece suporte. A Microsoft também adicionou suporte para a criação de trabalhos de MapReduce em outras linguagens, como C#, F# e JavaScript. O objetivo é tornar essa tecnologia de dados grandes mais acessível a um grupo maior de desenvolvedores.

Com HDFS e MapReduce, o Hadoop inclui outras tecnologias que permitem às pessoas analisarem dados sem gravar um trabalho de MapReduce. Por exemplo, o Pig é uma linguagem de alto nível desenvolvida para análise de dados grandes, enquanto o Hive oferece uma linguagem semelhante ao SQL chamada de HiveQL. Pig e Hive, de fato, geram trabalhos de MapReduce que processam dados HDFS, mas eles ocultam essa complexidade dos seus usuários. Ambas são fornecidas com Hadoop no Azure.

A Microsoft também fornece um driver de HiveQL para o Excel. Ao usar um suplemento do Excel, os analistas de negócios podem criar consultas de HiveQL (e, portanto, trabalhos de MapReduce) diretamente do Excel e, em seguida, processar e visualizar os resultados usando o PowerPivot e outras ferramentas do Excel. O Hadoop no Azure inclui outras tecnologias, como as bibliotecas de aprendizado de máquina Mahout, o sistema de mineração de gráficos Pegasus e muito mais.

Análise de dados grandes é importante e, portanto, o Hadoop também é. Ao fornecer o Hadoop como um serviço gerenciado no Azure, juntamente com links para ferramentas conhecidas, como o Excel, a Microsoft tem como objetivo fazer com que essa tecnologia seja acessível a um conjunto mais amplo de usuários.

Mais amplamente, os dados de todos os tipos são importantes. É por isso que o Azure inclui uma variedade de opções para gerenciamento de dados e análise empresarial. Qualquer que seja o aplicativo que está tentando criar, é provável que você encontre algo nessa plataforma de nuvem que funcionará para você.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

<!--HONumber=52-->

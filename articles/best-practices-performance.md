<properties 
	pageTitle=">Práticas recomendadas de desempenho - Azure" 
	description="Conheça as práticas recomendadas para o desempenho no Azure." 
	services="cloud-services, sql-database, storage, service-bus, virtual-network" 
	documentationCenter=".net" 
	authors="Rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/1/2014" 
	ms.author="robb"/>

# Práticas recomendadas para o desempenho nos aplicativos do Azure

Este guia fornece diretrizes prescritivas sobre as práticas recomendadas e as técnicas que devem ser seguidas para otimizar o desempenho dos aplicativos do Azure. 

Observe que o Azure oferece muitas vantagens: o desempenho é apenas uma delas. As recomendações contidas neste documento concentram-se principalmente no desempenho. Há outros cenários onde o desempenho não é tão importante: por exemplo, talvez você queira tirar proveito do descarregamento do gerenciamento de hardware físico para o Azure, ou o recurso "Pago pelo uso" pode ser particularmente atraente. Este artigo não tenta avaliar cenários em que o desempenho não é prioritário. 

## Visão geral ##
 O desempenho pode ser definido como ["a quantidade de trabalho útil realizado em comparação com o tempo e os recursos utilizados."](http://go.microsoft.com/fwlink/?LinkId=252650) 


A definição tem dois lados: métricas e recursos. As métricas de desempenho são números que devem ser obtidos para atender aos requisitos dos negócios. Incluem elementos como tempo de resposta, taxa de transferência, disponibilidade etc. O desempenho também inclui o nível de uso de recursos necessários para atingir uma determinada métrica de nível de desempenho. Como o custo é quase sempre um requisito de negócios, e recursos custam dinheiro, o desempenho significa usar recursos da maneira mais eficiente possível. 

### Ciclo de vida de desempenho
Você pode afetar o desempenho em dois pontos diferentes no ciclo de vida do aplicativo:

- durante o design, onde você pode tomar decisões arquitetônicas fundamentais que podem afetar o desempenho; e
- em tempo de execução, onde você pode identificar afunilamentos, realizar monitoramento e medição etc.

As duas atividades são necessárias. Este white paper concentra-se principalmente na fase de design, porque os erros arquitetônicos são mais difíceis de corrigir em tempo de execução.

#### Modelagem do aplicativo
É importante criar um modelo dos cenários de cliente mais importantes de seu aplicativo. Aqui, "importante" significa ter o maior impacto no desempenho. A identificação desses cenários e das atividades do aplicativo necessárias permitirá que você execute um teste de verificação de conceito.

#### Testes de verificação de conceito do desempenho ###

O teste de desempenho completo é uma etapa crítica durante o design e a implantação do aplicativo. Os aplicativos do Azure consistem em várias partes, que podem incluir componentes internos personalizados, bem como os fornecidos pela Microsoft. A Microsoft não pode testar todas as combinações possíveis de testes de desempenho desses componentes. Portanto, o teste completo e apropriado do desempenho de seu aplicativo é uma etapa crítica de qualquer implantação. 

Com base no modelo do aplicativo que você criou, você deve realizar testes de verificação de conceito assim que possível e fazer um teste de carga para validar a arquitetura do aplicativo para verificar se seu aplicativo atende aos requisitos de desempenho em termos de escalabilidade e latência. É extremamente importante validar a arquitetura e as pressuposições iniciais. Você não deseja descobrir que seu aplicativo não pode sustentar a carga esperada quando se tornar ativo. O Visual Studio fornece recursos para realizar testes de carga, descritos em [Visual Studio dos testes de carga do Visual Studio no Azure](http://www.visualstudio.com/get-started/load-test-your-app-vs). 

### O que é diferente em relação ao desempenho no Azure ###

As melhorias de desempenho mais significativas que podem ser obtidas em aplicativos do Azure são provenientes do dimensionamento e do particionamento dos recursos. A criação de aplicativos escalonáveis no Azure requer a utilização da expansão de recursos por seu particionamento físico: bancos de dados SQL, armazenamento, nós de computação etc. Esse particionamento permite a execução paralela de tarefas do aplicativo e, portanto, é a base para o alto desempenho, porque o Azure tem os recursos de um data center inteiro disponíveis e manipula o particionamento para você. Para atingir esse nível de desempenho é necessário usar os padrões de design de expansão adequados. 


Paradoxalmente, ao atingir esse alto desempenho para o aplicativo como um todo, cada operação individual tem um desempenho menor no Azure do que o seu equivalente no local, devido à maior latência de rede, mais confiabilidade devido às operações de failover etc. Mas o paralelismo, habilitado pelo uso de particionamento dos recursos compensa em muito a deficiência do desempenho individual. 

### Atividades de Design necessárias ###

Alguns fatores de desempenho mudam de acordo com o cenário de seu aplicativo. O próximo capítulo lida com essas coisas: dimensionamento e particionamento de recursos, localização de dados de forma apropriada e otimização do uso dos serviços do Azure. 


O capítulo seguinte trata dos fatores de desempenho que pertencem a qualquer aplicativo do Azure: latência de rede, conexões transitórias etc. 

## Design para desempenho em um ambiente de nuvem ##

Você deve considerar as seguintes áreas dependentes do cenário durante o design de um aplicativo do Azure ou ao migrar um aplicativo local para o Azure: 

- Expansão e particionamento de recursos: é o mecanismo fundamental para obter paralelismo e, portanto, alto desempenho. 
* Arquitetura de dados: qual tipo de armazenamento de dados usar para as diferentes partes de dados do aplicativo 
* Otimizações de serviços individuais do Azure 

O Azure obtém sua vantagem de desempenho máximo da capacidade de expandir e particionar recursos, o que permite paralelização maciça de atividades. Isso é bastante óbvio ao considerar um enorme banco de dados SQL do Azure, mas também é verdadeiro para qualquer recurso que possa se tornar um gargalo. 

O Azure oferece as seguintes opções para armazenamento de dados, e a escolha da opção correta tem um grande impacto sobre o desempenho: 

* Banco de Dados SQL Azure 
* Caching do Azure 
* Armazenamento da tabela do Azure 
* Armazenamento do blob do Azure 
* Unidades do Azure 
* Filas do Azure 
* Sistema de mensagens agenciado do Service Bus do Azure 
* Soluções de armazenamento de "Big Data" como Hadoop 

Como as especificações variam, explicaremos sobre como fazer isso em termos dos seguintes cenários: 

* Serviço de nuvem do Azure usando um banco de dados SQL 
* Serviço de nuvem do Azure usando muitas filas de armazenamento 
* Site do Azure usando MySQL como um banco de dados de back-end 
* Aplicativos de "Big Data" 
* Aplicativos que usam um banco de dados MySQL de back-end 

### Cenário: Banco de Dados SQL em um Serviço de Nuvem###

A maioria dos princípios do design de banco de dados bom ainda se aplicam ao Azure SQL banco de dados. Há um quantidade imensa de material que descreve como criar esquemas de SQL Server ou de Banco de Dados SQL do Azure. As várias referências sobre design de esquema de banco de dados SQL são: 

* [Conceitos básicos de design e modelagem de banco de dados ](http://go.microsoft.com/fwlink/?LinkId=252675) 
* [Etapas do design de banco de dados ](http://go.microsoft.com/fwlink/?LinkId=252676) 
* [Design de banco de dados](http://go.microsoft.com/fwlink/?LinkId=252677) 

Há duas atividades principais de design que são diferentes no Azure: 

* Localização adequada dos dados: isso pode envolver mover alguns dados relacionais para Blobs ou Tabelas do Azure quando apropriado. 
* Garantia da escalabilidade máxima: decidir se e como particionar os dados. 

#### Arquitetura de dados: Mover os dados do banco de dados SQL ####

Alguns dados que geralmente residem em um SQL Server local devem ser movidos para outro lugar no Azure. 

##### Movendo dados para Blobs do Azure ####

Dados Blob, como imagens ou documentos, não devem ser armazenados em um banco de dados SQL, mas em armazenamento de Blob do Azure. Embora esses dados geralmente sejam armazenados no SQL Server local, é muito mais barato usar o Armazenamento de Blob na nuvem. Tipicamente, você substitui chaves estrangeiras que apontavam para os dados blob por identificadores de Armazenamento de Blob para manter a capacidade de recuperar os dados blob, e as consultas que faziam referência aos dados exigem modificação. 

##### Movendo tabelas SQL para o Armazenamento de tabela do Azure #####

Ao decidir se deve usar o armazenamento de tabela do Azure, você deve examinar o custo e o desempenho. O Armazenamento de tabela é muito mais econômico do que os mesmos dados armazenados em um banco de dados SQL. No entanto, considere cuidadosamente até que ponto os dados usam os recursos relacionais do SQL, como junções, filtragem, consultas etc. Se os dados usarem pouco desses recursos serão bons candidatos para armazenamento em uma Tabela do Azure. 

Um padrão de design comum em que você pode considerar o Armazenamento de Tabela envolve uma tabela com várias linhas, como a tabela Clientes no banco de dados de exemplo AdventureWorks comum, onde várias colunas não são usadas pela maioria dos Clientes, mas apenas por um pequeno subconjunto de Clientes. É um padrão de design comum dividir as colunas em uma segunda tabela (talvez chamada CustomerMiscellany), com uma relação de 1 para 0 opcional entre o Cliente e a segunda tabela. Você pode considerar a possibilidade de mover a segunda tabela para o Armazenamento de Tabela. Você precisa avaliar se o tamanho da tabela e os padrões de acesso tornam isso econômico. 

Para obter mais informações sobre o Armazenamento de Tabela, consulte: 

* [Armazenamento de Tabela do Azure e Banco de Dados SQL do Azure - semelhanças e diferenças](http://msdn.microsoft.com/library/jj553018.aspx)
* [Considerações sobre desempenho do Armazenamento de Tabela do Azure](http://go.microsoft.com/fwlink/?LinkId=252663) 
* [Banco de Dados SQL e o Armazenamento de Tabela do Azure](http://go.microsoft.com/fwlink/?LinkId=252664) 
* [Melhorando o desempenho com o envio em lotes de inserções do Armazenamento de Tabela do Azure](http://go.microsoft.com/fwlink/?LinkID=252665), que discute alguns resultados de desempenho.
* [Guia de desempenho e elasticidade do Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=221876) 

#### Particionamento de dados ###

Um dos recursos mais frequentemente particionados são os dados. Se estiver criando um Serviço de Nuvem do Azure, você deverá considerar o uso de fragmentação interna do Banco de Dados SQL disponível por meio de Federações. 

Para obter uma visão geral das Federações de Banco de Dados SQL, consulte [Federações no Banco de Dados SQL ]( http://go.microsoft.com/fwlink/?LinkId=252668).  

##### Tarefas de design para Federações SQL ###

O uso de Federações de Banco de Dados SQL em um Serviço de Nuvem do Azure requer algumas modificações nos princípios do design clássico. No entanto, a maioria das verdades do bom design de um banco de dados SQL Server local são um ponto de partida necessário para a criação de Federações de Banco de Dados SQL. Existem duas tarefas principais de design, decidir: 

* o que federar; e 

* onde colocar tabelas não federadas. 

Para decidir no que federar, você precisa examinar o esquema do banco de dados e identificar as agregações de tabelas relacionadas. Uma agregação é um conjunto de tabelas, todas relacionadas por relações de um-para-muitos por meio de chaves estrangeiras, exceto pela raiz da agregação. 

Por exemplo, no famoso banco de dados de exemplo AdventureWorks, uma agregação possível é o conjunto {Customer, Order, OrderLine e possivelmente mais alguns}. Outro possível agregado é {Supplier, Product, OrderLine, Order}. 

Cada agregação é uma candidata à federação. Você deve avaliar onde espera um crescimento de tamanho e também examinar a carga de trabalho do aplicativo: consultas que "se alinham bem" com o esquema de federação, isto é, que não requerem dados de mais de um membro da federação, funcionarão bem. As que não se alinharem bem exigirão lógica na camada do aplicativo, porque, no momento, o Banco de Dados SQL não oferece suporte a junções entre bancos de dados. 

Para ver um exemplo de uma análise de design, que examina o banco de dados AdventureWorks para federá-lo, e mostra, passo a passo, as considerações envolvidas no design, consulte [Abordagem de escala em primeiro lugar para o design de banco de dados com federações: Parte 1 - Selecionando Federações e selecionando a chave da Federação](http://go.microsoft.com/fwlink/?LinkId=252671). 

Depois de decidir quais tabelas federar, você deve adicionar a chave primária da tabela raiz de agregação como uma coluna para cada uma das tabelas relacionadas. 

Depois de decidir em quais tabelas federar, outro problema é o local de tabelas de referência, bem como outros objetos de banco de dados. Há uma discussão completa sobre esse assunto em [Abordagem de escala em primeiro lugar para o design de banco de dados com federações: Parte 2 - Anotando e implantando esquema para as Federações](http://go.microsoft.com/fwlink/?LinkId=252672). A execução de consultas mais avançadas é descrita na [Parte 2]( http://go.microsoft.com/fwlink/?LinkId=252673). 

                                            
##### Particionamento tipo faça você mesmo #####

Há uma série de exemplos que mostram maneiras de particionar dados. Se decidir não usar Federações para particionar sua instância de Banco de Dados SQL, você deverá escolher um método de particionamento que seja apropriado para seu aplicativo. Estes são alguns exemplos: 

* Uma conta abrangente escrita antes do lançamento das Federações é [Como fragmentar com Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=252678). 
* [SQL Server e a biblioteca de fragmentações do Banco de Dados SQL ]( http://go.microsoft.com/fwlink/?LinkId=252679) 

##### Outros recursos de particionamento #####

É possível particionar outros recursos além do Banco de Dados SQL. Por exemplo, você pode desejar particionar os servidores de aplicativos e dedicá-los a bancos de dados específicos. Vamos pressupor que seu aplicativo contivesse N servidores de aplicativos e N bancos de dados. Se cada servidor do aplicativo tiver permissão para acessar cada banco de dados, o que consumirá N conexões de banco de dados ao quadrado que, em alguns casos, podem atingir um limite rígido do Azure. Mas se restringir cada servidor de aplicativos a apenas alguns bancos de dados, você reduzirá significativamente o número de conexões usadas. 

Dependendo do seu aplicativo, você poderá aplicar um raciocínio semelhante a outros recursos. 


#### Caching ####

O Serviço de Caching do Azure fornece memória elástica distribuída para armazenamento de coisas, como o estado de sessão ASP.net ou valores comumente referenciados de tabelas de referência do Banco de Dados SQL. Como os objetos estão na memória distribuída, há uma possibilidade de ganho considerável no desempenho. Como o Azure lida com a infraestrutura de caching, pouco custo de desenvolvimento é necessário para implementá-lo. 

Planeje fornecer capacidade de caching suficiente para que você possa armazenar objetos acessados frequentemente em cache. No Banco de Dados SQL, com frequência, há tabelas de referência usadas para converter códigos numéricos em cadeias de caracteres descritivas mais longas. Essas tabelas geralmente incluem dados como nomes de país e cidade, valores válidos de CEP, nomes de departamentos dentro de sua empresa etc. Para tabelas menores, pode fazer sentido armazenar a tabela inteira em cache, para outros valores, você pode armazenar apenas os valores usados mais frequentemente. O ganho de desempenho é proveniente das consultas de várias junções que envolvem esses dados: para cada valor localizado no cache, são economizados vários acessos ao disco. Uma boa introdução e discussão sobre desempenho e armazenamento em cache no Azure é [Introdução ao Serviço de Caching do Azure](http://go.microsoft.com/fwlink/?LinkId=252680). Uma postagem de blog mais recente sobre o assunto se encontra em [Considerações sobre o desempenho do Caching do Windows #Azure](http://go.microsoft.com/fwlink/?LinkId=252681). 

#### Cenário: Usando o enfileiramento de mensagens em aplicativos do Azure ####

Um exemplo desse cenário é usar o StreamInsight para popular as filas com mensagens que serão processadas posteriormente. 

As Filas do Azure são usadas para passar mensagens, desassociar subsistemas temporariamente e fornecer balanceamento e redistribuição de carga. 

O Azure tem duas tecnologias alternativas de fila: Filas de Armazenamento do Azure e Barramento de Serviço. 

As Filas de Armazenamento do Azure fornecem recursos, como tamanho de fila grande, acompanhamento do andamento e muito mais. O Service Bus fornece recursos como publicação/assinatura, integração total com o Windows Communication Foundation ("WCF"), detecção automática de duplicadas, entrega PEPS (primeiro a entrar, primeiro a sair) garantida e muito mais. 

Para obter uma comparação mais completa e detalhada sobre as duas tecnologias, consulte [Filas do Azure e Filas do Service Bus do Azure - semelhanças e diferenças](http://go.microsoft.com/fwlink/?LinkId=252682). 

Para obter uma discussão sobre o desempenho do Service Bus, consulte [Práticas recomendadas para melhorias no desempenho usando o sistema de mensagens agenciado do Service Bus](http://go.microsoft.com/fwlink/?LinkID=252683). 

#### Cenário: Aplicativos de "Big Data" ####

Com frequência, "Big Data" é encontrado como um subproduto de outro sistema ou aplicativo. Os exemplos incluem: 

* Logs da Web 

* Outros arquivos de diagnóstico, auditoria e monitoramento 

* Logs sísmicos de empresas petrolíferas 

* Dados de cliques e outras informações deixadas por pessoas que percorrem a Internet 

O "Big Data" pode ser identificado pelos seguintes critérios: 

* Tamanho (normalmente, centenas de terabytes ou maior) 

* Tipo: não relacional, esquema variável, arquivos em um sistema de arquivos 

Os dados geralmente não são adequados para processamento em um banco de dados relacional. 

Existem quatro tipos principais de armazenamento de dados não SQL: 

* Chave-valor 

* Documento 

* Gráfico 

* Família de colunas 

O Azure fornece suporte direto para Hadoop e também permite o uso de outras tecnologias. Para obter informações sobre o Serviço do Azure HDInsight, consulte: 

* [Big Data](/pt-br/solutions/big-data/) 
* [Serviço do Azure HDInsight](/pt-br/documentation/services/hdinsight/)
* [Introdução ao Serviço do Azure HDInsight](hdinsight-get-started.md)

Para obter algumas discussões de questões envolvidas com vários métodos de armazenamento noSQL, consulte: 

* [Familiarizando-se com o NoSQL no Azure](http://go.microsoft.com/fwlink/?LinkId=252729) 
* [AggregateOrientedDatabase](http://go.microsoft.com/fwlink/?LinkID=252731)
* [PolyglotPersistence](http://go.microsoft.com/fwlink/?LinkId=252732) 

#### Otimizações de desempenho de outros serviços individuais do Azure ####

Muitos dos serviços individuais do Azure têm recursos e configurações que podem afetar significativamente o desempenho e, portanto, devem ser analisados. 

##### Unidades do Azure ###

Você pode simular o uso do disco rígido existente do aplicativo por uma Unidade do Azure, o que é suportado por um Blob do Windows e, portanto, é persistente entre falhas da máquina individual. 

##### Armazenamento local #####

Embora não seja persistente entre falhas da máquina, pode ser usado para armazenar informações acessadas com frequência ou para armazenar os resultados intermediários que serão usados em outros lugares. Isso é econômico, já que não há nenhum custo para seu uso. 

##### ACS (Access Control Service) do Azure ###

Os dois principais fatores que afetam o uso do recurso ACS e, portanto, o desempenho, são o tamanho do token e criptografia. Discussão adicional é encontrada em [Diretrizes de desempenho do ACS](http://go.microsoft.com/fwlink/?LinkId=252747). 

##### Serialização #####

A serialização não é uma parte óbvia da otimização do desempenho, mas a redução do tráfego de rede pode ser significativa em alguns cenários de aplicativos. Para obter um exemplo de como os tamanhos de serialização podem variar dependendo do protocolo, consulte as reduções demonstradas em [Aplicativos Web do Azure e serialização](http://go.microsoft.com/fwlink/?LinkId=252749). 

Se a quantidade de dados que está sendo movida for um problema de desempenho, use a menor serialização disponível. Se o desempenho da serialização não for suficiente, considere usar formatos de serialização personalizados ou de terceiros não Microsoft. Como sempre, testes de verificação de conceito são fundamentais. 


### Sites do Azure usando mySQL ###

Os links a seguir fornecem conselhos sobre desempenho para o MySQL: 

* A pesquisa sobre *desempenho* em [http://mysql.com]( http://go.microsoft.com/fwlink/?LinkId=252775) apresenta muitos recursos. 
* Os fóruns em[ http://forums.mysql.com/list.php?24]( http://go.microsoft.com/fwlink/?LinkId=252776) são outros recursos para consulta. 



## Criando sistemas compartilhados ##

O Azure foi desenvolvido para executar vários aplicativos simultâneos, replicados para failover em várias máquinas. Isso afeta o desempenho do aplicativo de várias maneiras: 

* Conexões transitórias 

* Restrições de recursos que limitam o uso máximo 

* Latência da rede 

* Localização física dos serviços 

Essas considerações se aplicam a todas as arquiteturas de aplicativos, porque são determinadas pela infraestrutura física dos data centers do Azure. Para obter informações detalhadas, consulte o [Guia de desempenho e elasticidade do Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkID=252666). 

### Latência de rede ###

O Azure é uma plataforma de recursos compartilhados baseada em serviços, e isso significa que dois tipos de latências ou interrupções ocorrerem regularmente. O primeiro é o tempo necessário para fazer uma solicitação e receber uma resposta pela internet. Como essas solicitações e respostas podem viajar por qualquer número de roteadores antes de serem retornadas para o cliente, tempos limite e desconexões são mais frequentes do que em redes locais, fixas. O segundo é o tempo necessário para um sistema de recursos compartilhados, como o Azure, criar versões de backup de dados para durabilidade e para substituir e redirecionar solicitações para quaisquer instâncias removidas. Essas falhas e latências são importantes para entender como compensar para atingir os requisitos de desempenho do aplicativo. Testes de carga em um nível real fornecerão mais informações sobre as latências que você vê. 

Leve em consideração que provavelmente haverá mais, uma vez que o data center na nuvem provavelmente estará fisicamente mais distante do que seu servidor local. 

### Localização física dos serviços ###

Se possível, colocalize diferentes nós ou camadas do aplicativo no mesmo data center. Caso contrário, o custo e latência de rede serão maiores. 

Por exemplo, localize o aplicativo da Web no mesmo data center que a instância do Banco de Dados SQL que ele acessa e não em outro data center ou no local. 

### Conexões transitórias ###

Seu aplicativo DEVE poder lidar com conexões interrompidas. Conexões interrompidas são inevitáveis e intrínsecas à arquitetura de nuvem (por exemplo, operações, como a substituição de um nó inativo, a divisão de um membro da Federação no Banco de Dados SQL etc.) A melhor estrutura para fazer isso agora é o [Transient Fault Handling Application Block](http://go.microsoft.com/fwlink/?LinkID=236901). 

### Limitação ###

No mundo dos serviços, recursos podem ser muito granulares e você paga somente pelo que usa. No entanto, para todos os recursos há uma garantia mínima de tamanho, velocidade ou taxa de transferência - importante se você precisar de mais do que um determinado tamanho de banco de dados, por exemplo - mas também, em alguns casos, são os limites externos a um serviço que é importante. Como os aplicativos do Azure são executados em um ambiente compartilhado com outros aplicativos, o Azure aplica vários limites de recursos que você deve levar em conta. Se você exceder o limite de um recurso, uma solicitação adicional para esse recurso resultará em uma exceção. 

### Capacidade física ###

Uma parte necessária do planejamento do desempenho é o planejamento da capacidade: se você não puder fornecer armazenamento suficiente de vários tipos, seu aplicativo poderá não funcionar de maneira nenhuma Da mesma forma, memória ou capacidade de processador inadequada pode reduzir a velocidade de execução do seu aplicativo. 


O Azure reduz consideravelmente o esforço envolvido no planejamento da capacidade, porque muitas atividades antigas, principalmente a obtenção e o provisionamento de computadores, foram drasticamente alteradas. No Azure, o planejamento da capacidade não se concentra mais nos elementos físicos da computação, mas, em vez disso, funciona em um nível mais alto de abstração perguntando quantos dos procedimentos a seguir são necessários: 

* Nós de computação 
* Armazenamento de blob 
* Armazenamento de tabela 
* Filas etc. 

E devido à escalabilidade do Azure, as decisões sobre a capacidade inicial não são imutáveis: é relativamente fácil expandir (ou reduzir) os recursos do Azure. Mesmo assim, é importante fazer um planejamento preciso da capacidade, uma vez que isso garantirá que, quando o aplicativo for ativado, não haverá um período de tentativa e erro em relação à capacidade. 

Para aplicativos cujos recursos precisam flutuar drasticamente ao longo do tempo, considere o uso do [Autoscaling Application Block](http://go.microsoft.com/fwlink/?LinkId=252873). Esse Bloco permite definir regras para a expansão e a redução de instâncias de função. Há dois tipos de regras definidas: 

* Regras de restrição, que definem o número mínimo/máximo de instâncias por hora do dia 

* Regras reativas que entrarão em vigor quando alguma condição, como % de uso da CPU ocorre 

Você também pode definir regras personalizadas. Para obter mais informações, consulte o [Autoscaling Application Block](http://go.microsoft.com/fwlink/?LinkId=252873). 



O planejamento da capacidade é uma especialidade à parte, e este artigo pressupõe que você já fez isso. Para obter uma discussão detalhada sobre o planejamento da capacidade no Azure, consulte [Planejamento da capacidade para Filas e Tópicos do Service Bus](http://go.microsoft.com/fwlink/?LinkId=252875).



## Monitoramento e ajuste do desempenho em tempo de execução ##

Mesmo o design mais cuidadoso não garante zero problemas de desempenho em tempo de execução, portanto, é necessário monitorar o desempenho do aplicativo continuamente para verificar se ele está atingindo as métricas de desempenho e para corrigir situações em que essas métricas não são atendidas. Mesmo aplicativos bem-desenvolvidos estão sujeitos a eventos inesperados, como crescimento exponencial no uso ou possíveis alterações no ambiente de tempo de execução, que podem resultar em problemas de desempenho em que um ajuste é necessário. Muitas vezes, a identificação e a resolução de afunilamentos são uma parte significativa do processo. 


A solução de problemas de desempenho em tempo de execução exige trabalho prévio para criar logs e manipular exceções de maneira adequada, para que a solução de problemas possa ser realizada sempre que surjam problemas. Para obter um tratamento abrangente dessa área, consulte [Práticas recomendadas para a solução de problemas de desenvolvimento de aplicativos do Azure](http://go.microsoft.com/fwlink/?LinkID=252876). 

Existem ferramentas disponíveis para monitorar o desempenho contínuo de todos os serviços do Azure. Além disso, recursos de log devem ser incorporados nos aplicativos para fornecer as informações detalhadas necessárias para a solução de problemas de desempenho. 

### Banco de dados SQL ###

Observe que o SQL Profiler não está disponível atualmente no Azure. Existem várias soluções alternativas para obter as informações de desempenho necessárias. Uma alternativa durante o desenvolvimento é executar testes iniciais em uma versão local do banco de dados, onde o SQL Profiler está disponível. 

Também é possível usar o comando Transact-SQL SET STATISTICS e usar o SQL Server Management Studio para exibir o plano de execução gerado por uma consulta, uma vez que a codificação de consultas eficientes é essencial para o desempenho. Para obter uma discussão detalhada e uma explicação passo a passo sobre como fazer isso, consulte [Obtendo visão do desempenho no Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=252877). Outra abordagem interessante é analisar o desempenho entre o [Banco de Dados SQL e o SQL Server no local](http://go.microsoft.com/fwlink/?LinkId=252878). 

Dois tópicos sobre exibições do gerenciamento dinâmico são: 

* [ Monitorando o Banco de Dados SQL usando exibições do gerenciamento dinâmico](http://go.microsoft.com/fwlink/?LinkId=236195)
* [DMVs úteis do Banco de Dados SQL para analisar se o SQL Profiler está fazendo falta](http://go.microsoft.com/fwlink/?LinkId=252879) 

### Ferramentas e Recursos de Análise ###

Várias ferramentas de terceiros não Microsoft estão disponíveis para analisar o desempenho do Azure: 

- [Cerebrata](http://go.microsoft.com/fwlink/?LinkId=252880) 
- [Testes do SQL Server e do Banco de Dados SQL: Enzo SQL Baseline](http://enzosqlbaseline.codeplex.com/) 

Outros recursos 

* [Guia de desempenho e elasticidade do Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkID=252666) 
* [Banco de dados SQL](http://go.microsoft.com/fwlink/?LinkId=246930) 
* [Armazenamento](http://go.microsoft.com/fwlink/?LinkId=246933) 
* [Rede](http://go.microsoft.com/fwlink/?LinkId=252882) 
* [Barramento de Serviço](http://go.microsoft.com/fwlink/?LinkId=246934) 
* [Planejamento do Azure - Guia pós-decisão para integrar o Azure em seu ambiente](http://go.microsoft.com/fwlink/?LinkId=252884) 

<!--HONumber=35.2-->

<!--HONumber=46--> 

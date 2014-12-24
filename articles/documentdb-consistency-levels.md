<properties title="Consistency levels in DocumentDB" pageTitle="Níveis de consistência no Banco de Dados de Documentos" description="DocumentDB has four consistency levels with associated performance levels to help application developers make predictable consistency-availability-latency trade-offs." metaKeywords="Optional" services="documentdb" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="Optional" scriptId="Optional" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Níveis de consistência no Banco de Dados de Documentos
Muitas vezes, os desenvolvedores enfrentam o desafio de escolher entre dois extremos: consistência forte e eventual. A realidade é que existem várias barreiras de consistência entre esses dois extremos. Na maioria dos cenários reais, os aplicativos se beneficiam ao fazer compensações refinadas entre consistência, disponibilidade e latência. O Banco de Dados de Documentos oferece quatro níveis de consistência bem definidos, com níveis de desempenho associados. Isso permite que os desenvolvedores de aplicativos façam compensações previsíveis entre consistência, disponibilidade e latência.  
 
Todos os recursos do sistema, incluindo contas de bancos de dados, bancos de dados, coleções, usuários e permissões são sempre fortemente consistentes para leituras e consultas. Os níveis de consistência são aplicáveis somente aos recursos definidos pelo usuário. Para consultas e operações de leitura em recursos definidos pelo usuário, incluindo documentos, anexos, procedimentos armazenados, gatilhos e UDFs (Funções Definidas pelo Usuário), o Banco de Dados de Documentos oferece quatro níveis de consistência diferentes: Strong, Bounded Staleness, Session e Eventual. Esses níveis de consistência granulares e bem definidos permitem que você faça compensações seguras entre consistência, disponibilidade e desempenho. Esses níveis de consistência contam com o suporte dos níveis de desempenho previsível, garantindo resultados consistentes para seu aplicativo.   

##Níveis de consistência
Você pode configurar um nível de consistência padrão, na conta do banco de dados, que se aplique a todas as coleções (em todos os bancos de dados) na sua conta de banco de dados. Por padrão, todas as leituras e consultas executadas nos recursos definidos pelo usuário usarão o nível de consistência padrão especificado na conta do banco de dados. Porém, você pode diminuir o nível de consistência de uma solicitação de leitura/consulta especificando o cabeçalho da solicitação [x-ms-consistency-level]. Existem quatro tipos de níveis de consistência com suporte do protocolo de replicação do Banco de Dados de Documentos. Eles são descritos resumidamente abaixo. 

>[AZURE.NOTE] Em uma versão futura, pretendemos dar suporte à substituição do nível de consistência padrão, por coleção.  

**Strong**: a consistência Strong garante que uma gravação fique visível somente depois de confirmada permanentemente pela maioria do quorum de réplicas. Uma gravação é confirmada simultaneamente de maneira permanente pelo quorum primário ou secundário, ou é abortada. Uma leitura sempre é confirmada pela maioria do quorum de leitura. Um cliente nunca pode ver uma gravação não confirmada ou parcial, e sempre há a garantia de leitura da última gravação confirmada.   
 
A consistência forte oferece garantias absolutas na consistência de dados, mas oferece o nível mais baixo de desempenho de leitura e gravação.  

**Bounded Staleness**: a consistência Bounded Staleness garante a ordem total de propagação de gravações, mas com leituras possivelmente atrasadas em relação às gravações na maioria dos prefixos K. A leitura sempre é confirmada pela maioria do quorum de réplicas. A resposta de uma solicitação de leitura especifica sua atualização relativa (em termos de K).  

A Bounded Staleness oferece um comportamento mais previsível para consistência de leitura, enquanto oferece gravações com a menor latência. Como as leituras são confirmadas pela maioria do quorum, a latência de leitura não é a menor oferecida pelo sistema.    

**Session**: ao contrário dos modelos globais de consistência oferecidos pelos níveis de consistência Strong e Bounded Staleness, a consistência Session foi adaptada para uma sessão de cliente específica. A consistência Session geralmente é suficiente, já que oferece garantia de leituras monotônicas, além de gravações e a capacidade de ler suas próprias gravações. Uma solicitação de leitura para a consistência Session é executada em uma réplica que pode atender à versão solicitada pelo cliente (parte do cookie de sessão).  

A Session oferece consistência previsível de dados de leitura para uma sessão enquanto oferece gravações com a menor latência. As leituras também têm baixa latência, exceto em casos raros, em que a leitura será atendida por uma única réplica.  

**Eventual**: A consistência Eventual é a forma mais fraca de consistência, em que um cliente pode obter os valores mais antigos do que aqueles visualizados anteriormente, ao longo do tempo. Na ausência de gravações adicionais, as réplicas dentro do grupo serão, por fim, convergidas. A solicitação de leitura é atendida por qualquer secundário.  

A Eventual oferece a consistência de leitura mais fraca, mas oferece a menor latência para leituras e gravações. 

##Consistência de consultas
Por padrão, para recursos definidos pelo usuário, o nível de consistência das consultas é o mesmo que o das leituras. Por padrão, o índice é atualizado sincronamente em cada inserção, substituição ou exclusão de um documento para a coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos. Enquanto o Banco de Dados de Documentos é otimizado para gravação e suporta volumes constantes de gravações de documentos junto com a manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar seu índice, sem pressa. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa em que uma carga de trabalho é basicamente de leitura intensa.  

Modo de indexação|	Leituras|	Consultas  
-------------|-------|---------
Consistente (padrão)|	Escolher entre Strong, Bounded Staleness, Session ou Eventual|	Escolher entre Strong, Bounded Staleness, Session ou Eventual|
Lenta|	Escolher entre Strong, Bounded Staleness, Session ou Eventual|	Eventual  

Assim como nas solicitações de leitura, você pode diminuir o nível de consistência de uma solicitação de consulta específica determinando o cabeçalho [x-ms-consistency-level] para a solicitação.  

##Referências
-	Doug Terry. Consistência de Dados Replicados explicada por meio do beisebol.   
[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Garantia de sessão para Dados Replicados com Consistência Fraca.   
[http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html](http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html)
-	Daniel Abadi. Compensações de consistência no projeto de sistemas de bancos de dados modernos distribuídos: LIMITE é apenas parte da história".   
[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) para quoruns parciais práticos.   
[http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Eventual Consistent - Revisitado.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)

<properties
	pageTitle="Níveis de consistência no Banco de Dados de Documentos | Microsoft Azure"
	description="O Banco de Dados de Documentos tem quatro níveis de consistência que ajudam a equilibrar as trocas entre consistência eventual, disponibilidade e latência."
	keywords="consistência eventual, banco de dados de documentos, azure, Microsoft azure"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="mimig"/>

# Níveis de consistência no Banco de Dados de Documentos

O Banco de Dados de Documentos foi desenvolvido desde o início com foco na distribuição global. Ele se destina a oferecer garantias de baixa latência previsível, um SLA de disponibilidade de 99,99% e vários modelos flexíveis de consistência bem definidos. Atualmente, o Banco de Dados de Documentos fornece quatro níveis de consistência: strong, bounded staleness, session e eventual. Além dos modelos de consistência **strong** e **eventual** frequentemente oferecidos por outros bancos de dados NoSQL, o Banco de Dados de Documentos também oferece dois modelos de consistência cuidadosamente codificados e operacionalizados — **bounded staleness** e **session**, tendo validado sua utilidade em casos de uso verídicos. Coletivamente, esses quatro níveis de consistência permitem que você faça trocas ponderadas entre consistência, disponibilidade e latência.

## Escopo de consistência

A granularidade da consistência engloba uma única solicitação de usuário. Uma solicitação de gravação pode corresponder a uma transação de inserção, substituição, upsert ou exclusão (como ou sem a execução de um pré ou pós-gatilho associado). Ou uma solicitação de gravação pode corresponder à execução transacional de um procedimento armazenado JavaScript que opera em vários documentos em uma partição. Assim como nas gravações, uma transação de leitura/consulta também engloba uma única solicitação de usuário. O usuário pode ter que paginar um amplo conjunto de resultados, abrangendo várias partições, mas cada transação de leitura engloba uma única página e é servida de dentro de uma única partição.

## Níveis de consistência

Você pode configurar um nível de consistência padrão, na conta do banco de dados, que se aplique a todas as coleções (em todos os bancos de dados) na sua conta de banco de dados. Por padrão, todas as leituras e consultas executadas nos recursos definidos pelo usuário usarão o nível de consistência padrão especificado na conta do banco de dados. No entanto, você pode diminuir o nível de consistência de uma determinada solicitação de leitura/consulta, especificando o cabeçalho de solicitação [[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx). Há quatro tipos de nível de consistência compatíveis com o protocolo de replicação do Banco de Dados de Documentos que fornecem uma clara compensação entre garantias de consistência específica e desempenho, conforme descrito abaixo.

![O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha][1]

**Strong**:

- A consistência Strong oferece uma garantia de [linearidade](https://aphyr.com/posts/313-strong-consistency-models) com garantia de que as leituras retornem a versão mais recente de um documento. 
- a consistência Strong garante que uma gravação fique visível somente depois de confirmada permanentemente pela maioria do quorum de réplicas. Uma gravação é confirmada de modo síncrono e permanente pelo quorum primário e secundário, ou é anulada. Uma leitura sempre é confirmada pela maioria do quorum de leitura. Um cliente nunca pode ver uma gravação não confirmada ou parcial, e sempre há a garantia de leitura da última gravação confirmada. 
- As contas do Banco de Dados de Documentos que são configuradas para usar consistência strong não podem associar mais de uma região do Azure à respectiva conta do Banco de Dados de Documentos. 
- O custo de uma operação de leitura (em termos de [unidades de solicitação](documentdb-request-units.md) consumidas) com consistência strong é maior do que com session e eventual, mas igual ao de bounded staleness.
 

**Bounded staleness**:

- A consistência bounded staleness garante que as leituras podem não acompanhar as gravações até, no máximo, as versões *K*, ou prefixos de um documento ou intervalo de tempo *t*. 
- Consequentemente, ao escolher bounded staleness, "staleness" pode ser configurado de duas maneiras: 
    - Número de versões *K* do documento pelas quais as leituras não acompanham as gravações
    - Intervalo de tempo *t* 
- A consistência bounded staleness oferece total de ordem global, exceto na "janela staleness". Observe que a leitura monotônica garante existência em uma região dentro e fora da "janela staleness". 
- A bounded staleness oferece garantia de consistência mais forte do que session ou eventual. Para aplicativos distribuídos globalmente, é recomendável usar bounded staleness para cenários em que deseja ter consistência forte, mas também 99,99% de disponibilidade e baixa latência. 
- As contas do Banco de Dados de Documentos que são configuradas com a consistência bounded staleness podem associar qualquer número de regiões do Azure à respectiva conta do Banco de Dados de Documentos. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com consistência strong é maior do que com session e eventual, mas igual ao da consistência strong.

**Session**:

- Ao contrário dos modelos globais de consistência oferecidos pelos níveis de consistência strong e bounded staleness, a consistência session engloba uma sessão de cliente. 
- A consistência session é ideal para todos os cenários em que há o envolvimento de um dispositivo ou uma sessão de usuário, uma vez que ela garante leituras monotônicas, gravações monotônicas e RYW (leitura de suas próprias gravações). 
- A consistência session oferece consistência previsível para uma sessão, além de taxa de transferência de leitura máxima, ao mesmo tempo que oferece gravações e leituras de latência mais baixa. 
- As contas do Banco de Dados de Documentos que são configuradas com a consistência session podem associar qualquer número de regiões do Azure à respectiva conta do Banco de Dados de Documentos. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com nível de consistência session é menor do que com strong e bounded staleness, mas maior do que com a consistência eventual
 

**Eventual**:

- A consistência eventual garante que, na ausência de qualquer gravação adicional, as réplicas no grupo sejam convergidas. 
- A consistência eventual é a forma mais fraca de consistência, em que um cliente pode obter valores que sejam mais antigos do que aqueles que tinha visto antes.
- A consistência Eventual oferece a consistência de leitura mais fraca, mas oferece a menor latência para leituras e gravações.
- As contas do Banco de Dados de Documentos que são configuradas com a consistência eventual podem associar qualquer número de regiões do Azure à respectiva conta do Banco de Dados de Documentos. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com o nível de consistência eventual é o mais baixo de todos os níveis de consistência do Banco de Dados de Documentos.


## Garantias de consistência

A tabela a seguir captura várias garantias de consistência correspondentes aos quatro níveis de consistência.

| Garantia | Strong | Bounded Staleness | Session | Eventual |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
| **Total de ordem global** | Sim | Sim, fora da "janela staleness" | Não, ordem parcial de "session" | Não |
| **Garantia de prefixo consistente** | Sim | Sim | Sim | Sim |
| **Leituras monotônicas** | Sim | Sim, entre regiões fora da janela staleness e dentro de uma região o tempo todo. | Sim, para a sessão determinada | Não |
| **Gravações monotônicas** | Sim | Sim | Sim | Sim |
| **Leitura de suas gravações** | Sim | Sim | Sim (na região de gravação) | Não |


## Configurando o nível de consistência padrão

1.  No [Portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas do Banco de Dados de Documentos**.

2. Na folha **Contas do Banco de Dados de Documentos**, selecione a conta do banco de dados a ser modificada.

3. Na folha da conta, se a folha **Todas as Configurações** ainda não estiver aberta, clique no ícone **Configurações** na barra de comandos superior.

4. Na folha **Todas as Configurações**, clique na entrada **Consistência Padrão** em **Recurso**.

	![Captura de tela realçando o ícone Configurações e a entrada Consistência Padrão](./media/documentdb-consistency-levels/database-consistency-level-1.png)

5. Na folha **Consistência Padrão**, selecione o novo nível de consistência e clique em **OK**.

	![Captura de tela realçando o nível Consistência e o botão OK](./media/documentdb-consistency-levels/database-consistency-level-2.png)

## Níveis de consistência para consultas

Por padrão, para recursos definidos pelo usuário, o nível de consistência para consultas é o mesmo nível de consistência para leituras. Por padrão, o índice é atualizado sincronamente em cada inserção, substituição ou exclusão de um documento para a coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos. Embora o Banco de Dados de Documentos seja otimizado para gravação e permita volumes constantes de gravações de documentos, manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar seu índice, sem pressa. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa em que uma carga de trabalho é basicamente de leitura intensa.

Modo de indexação|	Leituras|	Consultas  
-------------|-------|---------
Consistente (padrão)|	Escolher entre strong, bounded staleness, session ou eventual|	Escolher entre strong, bounded staleness, session ou eventual|
Lentidão|	Escolher entre strong, bounded staleness, session ou eventual|	Eventual  

Assim como nas solicitações de leitura, você pode diminuir o nível de consistência de uma solicitação de consulta específica determinando o cabeçalho de solicitação [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx).

## Próximas etapas

Se você quiser ler mais sobre níveis de consistência e tradeoffs, recomendamos os seguintes recursos:

-	Doug Terry. Replicated Data Consistency explained through baseball (vídeo). [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-	Doug Terry. Replicated Data Consistency explained through baseball. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Session Guarantees for Weakly Consistent Replicated Data. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story”. [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums. [http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Eventual Consistent - Revisited. [http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

<!---HONumber=AcomDC_0615_2016-->
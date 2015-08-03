<properties 
    pageTitle="Banco de Dados SQL do Azure - ferramentas de bancos de dados elásticos" 
    description="Dimensione facilmente os recursos de banco de dados na nuvem usando ferramentas de banco de dados elástico." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/24/2015" 
    ms.author="sidneyh"/>

# Banco de Dados SQL do Azure - ferramentas de bancos de dados elásticos

## Promessas e desafios

O Banco de dados SQL do Azure fornece recursos de banco de dados praticamente ilimitados, bem como elasticidade para cargas de trabalho transacionais. Habilitar esta funcionalidade é facilitado por recursos como a **biblioteca de cliente do banco de dados elástico** e as **ferramenta de divisão/mesclagem**, conhecida como **ferramentas de banco de dados elástico**. Esses componentes são projetados para simplificar o desenvolvimento e gerenciamento de soluções de banco de dados fragmentado, discutidos neste artigo.

Embora atingir elasticidade e escala para aplicativos em nuvem seja simples para computação e armazenamento de blob, isso permanece sendo um desafio para o processamento de dados com monitoração de estado em bancos de dados relacionais. Vimos esses desafios surgem com mais destaque nos dois cenários abaixo:

* Aumentando e diminuindo a capacidade para a parte do banco de dados relacional da carga de trabalho.
* Gerenciar pontos de acesso que podem surgir afeta um subconjunto específico de dados, como cliente final especialmente movimentado (locatário).

Tradicionalmente, cenários como esses foram resolvidos com o investimento em servidores de banco de dados de grande escala para dar suporte ao aplicativo. No entanto, essa opção é limitada a nuvem em que todo o processamento ocorre em hardware de mercadoria predefinidos. Em vez disso, a distribuição de dados e o processamento em vários bancos de dados estruturados de forma idêntica (um padrão de expansão conhecido como fragmentação) fornece uma alternativa interessante aos métodos tradicionais de ampliação em termos de custo e resiliência.

Recursos de banco de dados elástico do Banco de Dados SQL do Azure tornam a escala horizontal mais simples e mais flexível do que nunca. Se você estiver desenvolvendo Software como um Serviço, os **pools de banco de dados elástico** tornam mais fácil criar bancos de dados individuais para cada um dos seus clientes finais ou locatários e permite que cada um deles aumente ou diminua dinamicamente o consumo de recursos automaticamente, mantendo um orçamento previsível. **Trabalhos de banco de dados elástico** permitem realizar operações de gerenciamento em grande escala de maneira confiável em todo um conjunto de bancos de dados executando scripts T-SQL para realizar alterações de esquema, credenciais de gerenciamento ou qualquer outra operação de manutenção do banco de dados que escolher. Para obter mais informações sobre trabalhos de banco de dados elástico, consulte [Visão geral de trabalhos de banco de dados elástico](sql-database-elastic-jobs-overview.md).

E se você usar bancos de dados separados para cada locatário ou vários intervalos de dados do pacote em cada um de um conjunto de bancos de dados, a biblioteca de cliente do banco de dados elástico e a ferramenta de divisão e mesclagem reduzem o esforço necessário para criar e gerenciar aplicativos que utilizam a fragmentação. Em vez de escrever código no layout de dados do documento nos bancos de dados e conexões de rota para o local correto, a biblioteca de cliente permite que você se concentre na lógica de negócios do aplicativo.

## Dimensionamento horizontal e vertical
A figura a seguir mostra as dimensões horizontais e verticais do dimensionamento.
 
![Escala horizontal versus vertical][4]

O dimensionamento horizontal refere-se à adição ou remoção de bancos de dados para ajustar a capacidade ou desempenho geral. Isso é geralmente chamado de "escalar horizontalmente". A fragmentação é uma abordagem comum para o dimensionamento horizontal, na qual os dados são particionados em um conjunto de bancos de dados estruturados de forma idêntica.

O dimensionamento vertical refere-se ao aumento ou diminuição do nível de desempenho de um banco de dados individual, também conhecido como "escalar verticalmente".

A maioria dos aplicativos de banco de dados de escala de nuvem usará uma combinação dessas duas estratégias. Por exemplo, um aplicativo de Software como um Serviço pode usar o dimensionamento horizontal para provisionar novos clientes finais e o dimensionamento vertical para permitir que o banco de dados de cada cliente final aumente ou reduza em recursos conforme necessário para a carga de trabalho.

Ferramentas de banco de dados elástico simplificam esta tarefa criando aplicativos que dependem de fragmentação, o tratamento da infraestrutura de dimensionamento horizontal. E, ao usar um pool de banco de dados elástico para sua família de bancos de dados, o dimensionamento vertical é tratado automaticamente pelo sistema. Com os pools, você é responsável por definir os limites no sistema geral e o intervalo de variação que você deseja permitir por banco de dados. Você pode alterar manualmente as edições de banco de dados ou níveis de recurso para bancos de dados que não participam de pools de banco de dados elástico. Por exemplo, você pode criar um novo fragmento para lidar com um grande fluxo de dados ao final de um mês. Enquanto os novos dados estiverem sendo recebidos, o fragmento será dimensionado para cima e reduzido de volta conforme a afluência diminui.

Para obter mais informações sobre pools de banco de dados elástico, consulte [Visão geral de pools de banco de dados elástico](sql-database-elastic-pool.md).

## Recursos das ferramentas de banco de dados elástico 

Desenvolver, dimensionar e gerenciar aplicativos de expansão usando fragmentação apresenta desafios para o desenvolvedor e para o administrador. Ferramentas de banco de dados elástico facilitam a vida de ambas essas funções. Os números na figura a seguir descrevem os principais recursos fornecidos pela biblioteca de cliente do banco de dados elástico e ferramenta de divisão/mesclagem. A figura ilustra um ambiente com muitos bancos de dados e cada banco de dados corresponde a um fragmento. As ferramentas facilitam o desenvolvimento de aplicativos do Banco de Dados SQL do Azure fragmentados por meio dos seguintes recursos específicos:

Para obter definições dos termos usados aqui, consulte [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md).

![Capacidades de escala elástica][1]

1.  **Gerenciamento de mapa de fragmento** é a capacidade de um aplicativo de gerenciar vários metadados sobre seus fragmentos. O gerenciamento de mapa de fragmentos é um recurso da biblioteca de cliente de banco de dados elástico. Os desenvolvedores podem usar essa funcionalidade para registrar bancos de dados como fragmentos, descrever os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para os bancos de dados e manter esses metadados como o número e a composição de bancos de dados que evolui para refletir as alterações de capacidade. Gerenciamento de mapa do fragmento constitui uma grande parte do código clichê que os clientes precisavam escrever em seus aplicativos quando eles implementavam fragmentação próprios. Para obter mais detalhes, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).
 
* **Roteamento dependente de dados**: imagine uma solicitação de entrada para o aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar o banco de dados correto que contém os dados para esse valor de chave e, em seguida, abrir uma conexão para processar a solicitação. Roteamento dependente de dados fornece a capacidade de abrir conexões com uma única chamada simples para o mapa do fragmento do aplicativo. O roteamento dependente de dados era outra área do código de infraestrutura que agora é coberta por uma funcionalidade na biblioteca de cliente de banco de dados elástico. Para obter mais detalhes, consulte [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).

* **MSQ (Consultas em Vários Fragmentos)**: consultar vários fragmentos funciona bem quando uma solicitação envolve vários (ou todos) fragmentos. Uma consulta de vários fragmento executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados de fragmentos de participantes são mesclados em um resultado geral definido usando a semântica UNION ALL. A funcionalidade é exposta por meio da biblioteca cliente que trata muitas tarefas, incluindo: gerenciamento de conexões, gerenciamento de threads, tratamento de falhas e os processamento de resultados intermediários. MSQ pode consultar até centenas de fragmentos. Para obter mais detalhes, consulte [Consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).


* **Ferramenta de divisão e mesclagem**: se você optar por não usar o modelo simples de alocação de bancos de dados separados em um pool de banco de dados elástico para cada shardlet (locatário), seu aplicativo pode precisar redistribuir com flexibilidade os dados entre bancos de dados quando as necessidades de capacidade flutuarem conforme o movimento dos negócios. Ferramentas de banco de dados elástico includem a ferramenta de divisão/mesclagem hospedada pelo cliente para rebalancear a distribuição de dados e gerenciamento de pontos de acesso para aplicativos fragmentados em situações que também envolvem a movimentação de dados. Ela desenvolve uma funcionalidade subjacente para mover shardlets sob demanda entre fragmentos diferentes e integra-se ao gerenciamento de mapa de fragmentos para manter mapeamentos consistentes e conexões de roteamento dependente de dados precisos. Para obter mais detalhes, consulte [Divisão e mesclagem com ferramentas de banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md).

Em geral, os clientes que usam as ferramentas de banco de dados elástico podem esperar obter a funcionalidade completa do T-SQL durante o envio de operações de fragmento local em vez de operações entre fragmentos que têm suas próprias semânticas.

## Padrões comuns de fragmentação

**Fragmentação** é uma técnica para distribuir grandes quantidades de dados estruturado de forma idêntica em vários bancos de dados independentes. É especialmente popular com desenvolvedores de nuvem que estiverem criando Software como as ofertas de serviço (SAAS) para clientes finais ou empresas. Esses clientes finais são referidos como "Locatários". Fragmentação pode ser necessária por vários motivos:

* A quantidade total de dados é muito grande para caber dentro das restrições de um único banco de dados 
* A taxa de transferência de transação da carga de trabalho geral excede as capacidades de um único banco de dados 
* Inquilinos podem exigir isolamento físico entre si, para que bancos de dados separados são necessários para cada locatário 
* Seções diferentes de um banco de dados talvez precise residir em regiões diferentes para questões geopolíticas, de desempenho ou de conformidade. 

Em outros cenários, como inclusão de dados de dispositivos distribuídos, a fragmentação pode ser usada para preencher um conjunto de bancos de dados que são organizados temporariamente. Por exemplo, um banco de dados pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas fragmentadas) e as consultas que recuperam informações para um intervalo de datas devem ser roteadas pelo aplicativo para o subconjunto de bancos de dados que abrangem o intervalo em questão.
 
A fragmentação funciona melhor quando todas as transações em um aplicativo podem ser restrita a um único valor de uma chave de fragmentação. Isso garante que todas as transações serão locais para um banco de dados específico.

Alguns aplicativos usam a abordagem mais simples de criar um banco de dados separado para cada locatário. Este é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup/restauração e dimensionamento de recursos na granularidade do locatário. Com a fragmentação de locatário único, cada banco de dados está associado a um valor de ID específico do locatário (ou o valor de chave de cliente), mas essa chave não deve sempre estar presente nos próprios dados. É responsabilidade do aplicativo rotear cada solicitação para o banco de dados apropriado e a biblioteca de cliente do banco de dados elástico pode simplificar isso.

![Único locatário versus multilocatários][3]

Outros cenários de vários locatários pack juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Isso é um **padrão de fragmentação de multilocatário** típico, podendo ser controlado pelo fato de que um aplicativo gerencia grandes números de locatários muito pequenos. Na fragmentação multilocatária, as linhas nas tabelas de banco de dados foram projetadas para executar uma chave que identifica a ID do locatário ou a chave de fragmentação. Novamente, a camada de aplicativo é responsável por rotear a solicitação de um locatário no banco de dados apropriado, e isso pode ter suporte na biblioteca de cliente do banco de dados elástico. Além disso, a segurança no nível de linha pode ser usada para filtrar quais linhas cada locatário pode acessar. Para obter mais detalhes, consulte [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança no nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuir dados entre bancos de dados pode ser necessário com o padrão de fragmentação de multilocatário, e isso é facilitado pela ferramenta de divisão/mesclagem de banco de dados elástico.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/overview.png
[2]: ./media/sql-database-elastic-scale-intro/tenancy.png
[3]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png
[4]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
 

<!---HONumber=July15_HO4-->
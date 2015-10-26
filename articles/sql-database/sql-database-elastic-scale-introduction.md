<properties
    pageTitle="Banco de Dados SQL do Azure - ferramentas de bancos de dados elásticos"
    description="Os desenvolvedores de SaaS (Software como um serviço) podem criar com facilidade bancos de dados elásticos e escalonáveis na nuvem usando estas ferramentas"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/27/2015"
    ms.author="sidneyh"/>

# Visão geral dos recursos do Banco de Dados Elástico

Os recursos do **Banco de Dados Elástico** permitem que você use os recursos de banco de dados praticamente ilimitados do **Banco de Dados SQL do Azure** para criar soluções para cargas de trabalho transacionais e, especialmente, aplicativos SaaS (software como serviço). Recursos do Banco de Dados Elástico são compostos pelo seguinte:

* Ferramentas do Banco de Dados Elástico: estas duas ferramentas simplificam o desenvolvimento e o gerenciamento de soluções de banco de dados fragmentado. As ferramentas são: [biblioteca do cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md) e [ferramenta de divisão e mesclagem do Banco de Dados Elástico](sql-database-elastic-scale-overview-split-and-merge.md). 
* [Pools de Banco de Dados Elástico](sql-database-elastic-pool-guidance.md) (visualização): um pool é uma coleção de bancos de dados aos quais você pode adicionar ou remover bancos de dados a qualquer momento. Os bancos de dados no pool compartilham uma quantidade fixa de recursos (conhecidos como DTUs (unidades de taxa de transferência de banco de dados)). Você paga um preço fixo pelos recursos, o que permite facilmente calcular os custos enquanto também gerencia o desempenho. 
* [Trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md) (visualização): use trabalhos para gerenciar um grande número de bancos de dados SQL do Azure. Execute operações administrativas facilmente, alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, desempenho de coleta de dados ou coleção de telemetria do locatário (cliente), usando trabalhos.
* [Consulta do Banco de Dados Elástico](sql-database-elastic-query-overview.md) (visualização): permite executar uma consulta Transact-SQL que abranja vários bancos de dados. Isso possibilita a conexão com ferramentas de relatório, como Excel, PowerBI, Tableau, etc.

O gráfico a seguir mostra uma arquitetura que inclui os **recursos do Banco de Dados Elástico** em relação a uma coleção de bancos de dados.

![Ferramentas de Banco de Dados Elástico][1]

Para obter uma versão imprimível deste gráfico, vá para [Download de visão geral do banco de dados elástico](http://aka.ms/axmybc).

Neste gráfico, as cores do banco de dados representam esquemas. Bancos de dados com a mesma cor compartilham os mesmos esquemas.

1. Um conjunto de **bancos de dados SQL do Azure** é hospedado no Azure usando a arquitetura de fragmentação. 
2. A **biblioteca de clientes do Banco de Dados Elástico** é usada para gerenciar um conjunto de fragmentos.
3. Um subconjunto dos bancos de dados é colocado em um **pool de Banco de Dados Elástico**. (Confira [Controlar o crescimento explosivo com bancos de dados elásticos](sql-database-elastic-pool.md)). 
4. Um **trabalho de Banco de Dados Elástico** executa scripts T-SQL em todos os bancos de dados.
5. A **ferramenta de divisão e mesclagem** é usada para mover dados de um fragmento para outro.
6. A **consulta ao Banco de Dados Elástico** permite gravar uma consulta que se estenda por todos os bancos de dados no conjunto de fragmentos.
  
## Promessas e desafios

Obter elasticidade e dimensionamento para aplicativos em nuvem foi fácil para armazenamento de blob e computação – simplesmente adicione ou subtraia unidades. Mas continuou sendo um desafio para processamento de dados com estado em bancos de dados relacionais. Vimos esses desafios surgem com mais destaque nos dois cenários abaixo:

* Aumentando e diminuindo a capacidade para a parte do banco de dados relacional da carga de trabalho.
* Gerenciar pontos de acesso que podem surgir afeta um subconjunto específico de dados, como cliente final especialmente movimentado (locatário).

Tradicionalmente, cenários como esses foram resolvidos com o investimento em servidores de banco de dados de grande escala para dar suporte ao aplicativo. No entanto, essa opção é limitada a nuvem em que todo o processamento ocorre em hardware de mercadoria predefinidos. Em vez disso, distribuir dados e processar entre vários bancos de dados estruturados de forma idêntica (um padrão de expansão conhecido como “fragmentação”) oferece uma alternativa interessante às abordagens tradicionais de aumento de escala tanto em termos de custo quanto de elasticidade.

## Dimensionamento horizontal e vertical

A figura a seguir mostra as dimensões horizontal e vertical da colocação em escala, que são as maneiras básicas de dimensionar os bancos de dados elásticos.

![Escala horizontal versus vertical][2]

O dimensionamento horizontal refere-se à adição ou remoção de bancos de dados para ajustar a capacidade ou desempenho geral. Isso também é chamado de “escalar horizontalmente”. A fragmentação, na qual os dados são particionados em um conjunto de bancos de dados estruturados de forma idêntica, é uma maneira comum de implementara a escala horizontal.

O dimensionamento vertical refere-se ao aumento ou diminuição do nível de desempenho de um banco de dados individual. Isso também é conhecido como “escalar verticalmente”.

A maioria dos aplicativos de banco de dados de escala de nuvem usará uma combinação dessas duas estratégias. Por exemplo, um aplicativo de Software como um Serviço pode usar o dimensionamento horizontal para provisionar novos clientes finais e o dimensionamento vertical para permitir que o banco de dados de cada cliente final aumente ou reduza em recursos conforme necessário para a carga de trabalho.

* A escala horizontal é gerenciada com a [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md).

* A escala vertical é realizada usando cmdlets do Azure PowerShell para alterar a camada de serviço ou colocando os bancos de dados em um pool de Banco de Dados Elástico.

## Padrões de locatário único e multilocatários

*Fragmentação* é uma técnica para distribuir grandes quantidades de dados estruturados de forma idêntica entre vários bancos de dados independentes. É especialmente popular com desenvolvedores de nuvem que estiverem criando Software como as ofertas de serviço (SAAS) para clientes finais ou empresas. Esses clientes finais costumam ser chamados de “locatários”. Fragmentação pode ser necessária por vários motivos:

* A quantidade total de dados é muito grande para caber dentro das restrições de um único banco de dados
* A taxa de transferência de transação da carga de trabalho geral excede as capacidades de um único banco de dados
* Inquilinos podem exigir isolamento físico entre si, para que bancos de dados separados são necessários para cada locatário
* Seções diferentes de um banco de dados talvez precise residir em regiões diferentes para questões geopolíticas, de desempenho ou de conformidade.

Em outros cenários, como inclusão de dados de dispositivos distribuídos, a fragmentação pode ser usada para preencher um conjunto de bancos de dados que são organizados temporariamente. Por exemplo, um banco de dados pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas fragmentadas) e as consultas que recuperam informações para um intervalo de datas devem ser roteadas pelo aplicativo para o subconjunto de bancos de dados que abrangem o intervalo em questão.

A fragmentação funciona melhor quando todas as transações em um aplicativo podem ser restrita a um único valor de uma chave de fragmentação. Isso garante que todas as transações serão locais para um banco de dados específico.

Alguns aplicativos usam a abordagem mais simples de criar um banco de dados separado para cada locatário. Este é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup/restauração e dimensionamento de recursos na granularidade do locatário. Com a fragmentação de locatário único, cada banco de dados está associado a um valor de ID específico do locatário (ou o valor de chave de cliente), mas essa chave não deve sempre estar presente nos próprios dados. É responsabilidade do aplicativo rotear cada solicitação para o banco de dados apropriado, e a biblioteca de cliente pode simplificar isso.

![Único locatário versus multilocatários][4]

Outros cenários de vários locatários pack juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Esse é um típico **padrão de fragmentação multilocatário**, e pode ser conduzido pelo fato de que um aplicativo gerencia grandes números de locatários muito pequenos. Na fragmentação multilocatária, as linhas nas tabelas de banco de dados foram projetadas para executar uma chave que identifica a ID do locatário ou a chave de fragmentação. Novamente, a camada de aplicativo é responsável por rotear a solicitação de um locatário no banco de dados apropriado, e isso pode ter suporte na biblioteca de cliente do banco de dados elástico. Além disso, a segurança no nível de linha pode ser usada para filtrar quais linhas cada locatário pode acessar. Para obter mais detalhes, confira [Aplicativos multilocatário com ferramentas de banco de dados elástico e segurança no nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuir dados entre bancos de dados pode ser necessário com o padrão de fragmentação de multilocatário, e isso é facilitado pela ferramenta de divisão/mesclagem de banco de dados elástico.

### Mover dados de bancos de dados de vários locatários para de um locatário
Ao criar um aplicativo SaaS, é comum para oferecer aos clientes em potencial uma versão de avaliação do software. Nesse caso, é econômico usar um banco de dados multilocatário para os dados. No entanto, quando um cliente em potencial se torna um cliente, um banco de dados de um único locatário é melhor, já que fornece maior desempenho. Se o cliente tiver criado dados durante o período de avaliação, use a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) para mover os dados do banco de dados multilocatário para o novo banco de dados de um único locatário.

## Próximas etapas

Para um aplicativo de exemplo que demonstre a biblioteca do cliente, confira [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

Para usar a ferramenta de divisão/mesclagem, é necessário [configurar a segurança](sql-database-elastic-scale-split-merge-security-configuration.md).

Para ver os detalhes do pool do Banco de Dados Elástico, confira [Considerações de preço e desempenho para um pool do banco de dados elástico](sql-database-elastic-pool-guidance.md) ou crie um novo pool com o [tutorial](sql-database-elastic-pool-portal.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

### Comentários por favor!
O que podemos aperfeiçoar? Este tópico explica o recurso de maneira clara? Ou você ficou confuso com alguma parte dele? Nosso objetivo é satisfazer nossos leitores, por isso use os botões de votação e nos conte no que falhamos (ou acertamos). E se quiser que entremos em contato com você, inclua seu email em seus comentários.


<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/tools.png
[2]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]: ./media/sql-database-elastic-scale-introduction/overview.png
[4]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

<!---HONumber=Oct15_HO3-->
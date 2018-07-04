---
title: Modelos de compra do Banco de Dados SQL do Microsoft Azure| Microsoft Docs
description: Saiba sobre o modelo de compra para o Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: dda37600dffaf1e8e1c85c9757696f2d8219febb
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751663"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Modelos de compra Banco de Dados SQL do Microsoft Azure e recursos 

Servidores lógicos no [Banco de Dados SQL do Azure ](sql-database-technical-overview.md) oferece dois modelos de compra para recursos de E/S, armazenamento e computação: um modelo de compra baseado em DTU e um modelo de compra baseado em vCore (versão prévia). 

> [!NOTE]
> [Instâncias Gerenciadas](sql-database-managed-instance.md) no Banco de Dados SQL do Microsoft Azure oferecem o modelo de compra com base no vCore.

A tabela e o gráfico a seguir comparam e contrastam esses dois modelos de compra.

> [!IMPORTANT]
> Para [Modelo de compra baseado em vCore (versão prévia)](sql-database-service-tiers-vcore.md), consulte Modelo de compra baseado em vCore (versão prévia)

|**Modelo de compra**|**Descrição**|**Mais adequado para**|
|---|---|---|
|Modelo baseado em DTU|Esse modelo é baseado em uma medida em pacote de recursos de E/S, armazenamento e computação. Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Mais adequado para clientes que desejam opções de recursos simples e pré-configuradas.| 
|Modelo baseado em vCore|Esse modelo permite escalar recursos de armazenamento e computação de maneira independente. Além disso, permite usar o Benefício Híbrido do Azure para SQL Server para obter redução de custos.|Mais adequado para clientes que valorizam flexibilidade, controle e transparência.|
||||  

![modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>Modelo de compra baseado no vCore (visão prévia)

Um núcleo virtual representa a CPU lógica oferecida com uma opção para escolher entre gerações de hardware. O modelo de compra baseado em vCore (versão prévia) fornece flexibilidade, controle, transparência do consumo de recursos individual e uma forma simples de mover os requisitos de carga de trabalho local para a nuvem. Esse modelo permite escalar computação, memória e armazenamento com base nas necessidades de carga de trabalho. No modelo de compra baseado em vCore, os clientes podem escolher entre camadas de serviço Comercialmente Crítico e de Uso Geral (versão prévia) para ambos os [bancos de dados individuais](sql-database-single-database-scale.md) e [pools elásticos](sql-database-elastic-pool.md). 

O modelo de compra baseado em vCore (visão prévia) permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço. Se o banco de dados ou pool elástico consumir mais de 300 DTU, a conversão para o vCore poderá reduzir o custo. É possível converter usando a API de sua preferência ou o Portal do Azure, sem tempo de inatividade. No entanto, a conversão não é necessária. Se o modelo de compra de DTU atender aos seus requisitos de desempenho e de negócios, você deve continuar utilizando-o. Se você decidir converter do modelo DTU para o modelo vCore, deverá selecionar o nível de desempenho usando a seguinte regra geral: cada 100 DTU na camada Padrão exige pelo menos 1 vCore na camada de Finalidade Geral; cada 125 DTU na camada Premium requer pelo menos 1 vCore no nível Business Critical.

No modelo de compra baseado em vCore (preview), os clientes pagam por:
- Computação (camada de serviço + número de vCores + geração de hardware)*
- Tipo e quantidade de dados e armazenamento de log 
- Número de E/S**
- Armazenamento de backup (RA-GRS)** 

\* Na visualização pública inicial, as CPUs Lógicas Ger 4 são baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz.

\*\* Durante a versão prévia, 7 dias de backups e E/S são gratuitos

> [!IMPORTANT]
> Armazenamento de log e dados, E/S e computação são cobrados por banco de dados ou pool elástico. O armazenamento de backups é cobrado por cada banco de dados. Para obter detalhes sobre os encargos de Instância Gerenciada, veja [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md).
> **Limitações de região:** O modelo de compra baseado em vCore (versão prévia) não está ainda disponível nas seguintes regiões: Europa Ocidental, França Central, Sul do Reino Unido, Oeste do Reino Unido e Sudeste da Austrália.

## <a name="dtu-based-purchasing-model"></a>Modelo de compra com base em DTU

A DTU (Unidade de Transmissão de Dados) representa uma medida combinada de CPU, memória, leituras e gravações. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos de computação e inclui armazenamento para impulsionar diferentes níveis de desempenho do aplicativo. Clientes que preferem a simplicidade de um pacote pré-configurado e pagamentos fixos por mês podem localizar o modelo baseado em DTU mais adequado às suas necessidades. No modelo de compra baseado em DTU, os clientes podem escolher entre as camadas de serviço **Básico**, **Standard** e **Premium** para ambos os [bancos de dados individuais](sql-database-single-database-scale.md) e [pools elásticos](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>O que são DTUs (Unidades de transação do banco de dados)?
Para um único Banco de Dados SQL do Azure em um nível de desempenho específico dentro de uma [camada de serviço](sql-database-single-database-scale.md), a Microsoft garante um certo nível de recursos para o banco de dados (independente de qualquer outro banco de dados na nuvem do Azure), fornecendo um nível previsível de desempenho. A quantidade de recursos é calculada como um número de unidades de transação do banco de dados ou DTUs e é uma medida combinada de recursos de computação, armazenamento e E/S. A proporção entre esses recursos originalmente foi determinada por uma [carga de trabalho de parâmetro de comparação de OLTP](sql-database-benchmark-overview.md) projetada para ser o cenário típico de cargas de trabalho OLTP reais. Quando sua carga de trabalho excede o valor de qualquer um desses recursos, a taxa de transferência é restringida, resultando em desempenho mais lento e inatividade. Os recursos usados pela sua carga de trabalho não afetam os recursos disponíveis para outros Bancos de Dados SQL na nuvem do Azure, e os recursos usados por outras cargas de trabalho não afetam os recursos disponíveis para o Banco de Dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs são úteis para entender a quantidade relativa de recursos entre Bancos de Dados SQL do Azure em diferentes níveis de desempenho e de serviço. Por exemplo, dobrar as DTUs aumentando o nível de desempenho de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados. Por exemplo, um banco de dados Premium P11 com 1.750 DTUs fornece 350x mais capacidade de computação DTU que um banco de dados básico com 5 DTUs.  

Para ver informações mais aprofundadas sobre o consumo de recursos (DTU) da sua carga de trabalho, use a [Análise de Desempenho de Consultas do Banco de Dados SQL do Azure](sql-database-query-performance.md) para:

- Identifique as principais consultas por contagem de CPU/Duração/Execução, que potencialmente podem ser ajustadas para melhorar o desempenho. Por exemplo, uma consulta com uso intensivo de E/S pode se beneficiar do uso de [técnicas de otimização de memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível em uma determinada camada de serviço e nível de desempenho.
- Analise detalhadamente os dados de uma consulta e exiba o texto e o histórico de utilização de recursos.
- Acesse recomendações de ajuste de desempenho que mostram as ações executadas pelo [Assistente do Banco de Dados SQL do Azure](sql-database-advisor.md).

Você pode [alterar os níveis de serviço de DTU](sql-database-service-tiers-dtu.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Para este cenário, você pode usar um pool elástico com um determinado número de eDTUs que são compartilhados entre vários bancos de dados no pool.

![Introdução ao Banco de Dados SQL: DTUs de banco de dados individual por camada e por nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são eDTUs (Unidades de transação de banco de dados elástico)?
Em vez de fornecer um conjunto dedicado de recursos (DTUs) que pode nem sempre ser necessários para um Banco de Dados SQL que está sempre disponível, é possível colocar os bancos de dados em um [pool elástico](sql-database-elastic-pool.md) em um servidor de Banco de Dados SQL que compartilha um pool de recursos entre esses bancos de dados. Os recursos compartilhados em um pool elástico são medidos por Unidades de Transação de Banco de Dados elástico ou eDTUs. Pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis. Um pool elástico garante que os recursos não possam ser consumidos por um banco de dados no pool, enquanto garantem que cada banco de dados no pool sempre tem uma quantidade mínima de recursos disponíveis. 

![Introdução ao Banco de Dados SQL: eDTUs por camada e por nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Um pool é fornecido com um número definido de eDTUs por um preço definido. No pool elástico, os bancos de dados individuais recebem a flexibilidade do dimensionamento automático dentro dos limites configurados. Um banco de dados sob carga mais pesada consumirá mais eDTUs para atender à demanda. Bancos de dados sob cargas mais leves consumirão menos eDTUs. Bancos de dados sem carga não consumirão eDTUs. Ao provisionar recursos para o pool inteiro, em vez de por banco de dados, as tarefas de gerenciamento são simplificadas, fornecendo um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto sobre os bancos de dados no pool. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. Você pode adicionar ou subtrair bancos de dados para o pool ou limitar a quantidade de eDTUs que um banco de dados pode usar sob carga pesada para reservar eDTUs para outros bancos de dados. Se um banco de dados estiver subutilizando recursos de forma previsível, você poderá movê-lo para fora do pool e configurá-lo como um único banco de dados com uma quantidade previsível de recursos necessários.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como determinar o número de DTUs necessárias para minha carga de trabalho?
Se você quiser migrar uma carga de trabalho local existente, ou uma carga de trabalho virtual, para o Banco de Dados SQL do Azure, use a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessárias. Para uma carga de trabalho existente do Banco de Dados SQL do Azure, use [Análise de Desempenho de Consultas do Banco de Dados SQL](sql-database-query-performance.md) para compreender o consumo de recursos do banco de dados (DTUs) e saber mais sobre como otimizar sua carga de trabalho. Você também pode usar o DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para exibir o consumo de recursos da última hora. Como alternativa, o catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) exibe o consumo de recursos para os últimos 14 dias, porém com uma fidelidade menor de médias de cinco minutos.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como posso saber se eu teria benefícios com um pool elástico de recursos?
Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por uma baixa utilização média com picos de utilização relativamente pouco frequentes. O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Para saber mais , confira [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando eu atinjo o máximo de DTUs?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até o máximo permitido para a sua camada de serviço e nível de desempenho selecionados. Se sua carga de trabalho está atingindo um dos limites de CPU / E/S de Dados / E/S de log, você continuará a receber o nível máximo de recursos permitidos, mas provavelmente também ocorrerão maiores latências de consulta. Esses limites não resultam em erros, mas apenas em uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. Se atingir os o máximo permitido de sessões/solicitações de usuários simultâneos (threads de trabalho), você verá erros explícitos. Confira [Limites de recursos do Banco de Dados SQL]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) para saber mais sobre limites de recursos não relacionados a CPU, memória, E/S de dados ou E/S do log de transações.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionando resultados do parâmetro de comparação ao desempenho de um banco de dados real
É importante entender que todos os parâmetros de comparação são representativos e indicativos apenas. As taxas de transação obtidas com o aplicativo de parâmetro de comparação não serão iguais às que podem ser obtidas com outros aplicativos. O parâmetro de comparação compreende um conjunto de tipos de transação diferentes executados em um esquema com uma variedade de tabelas e de tipos de dados. Embora o parâmetro de comparação exercite as mesmas operações básicas comuns a todas as cargas de trabalho OLTP, ele não representa qualquer classe específica de banco de dados ou de aplicativo. O objetivo do parâmetro de comparação é fornecer um guia razoável para o desempenho relativo de um banco de dados que pode ser esperado ao dimensionar horizontal ou verticalmente entre os níveis de desempenho. Na realidade, os bancos de dados de diferentes tamanhos e complexidade, lidam com combinações diferentes de cargas de trabalho e responderão de formas diferentes. Por exemplo, um aplicativo de E/S intensiva pode atingir os limites de E/S mais cedo ou um aplicativo de uso intensivo de CPU pode atingir os limites de CPU mais cedo. Não há nenhuma garantia de que qualquer banco de dados específico será dimensionado da mesma forma que o parâmetro de comparação em um aumento da carga.

O parâmetro de comparação e sua metodologia são descritos em mais detalhes abaixo.

### <a name="benchmark-summary"></a>Resumo do parâmetro de comparação
O ASDB mede o desempenho de uma combinação de operações básicas de banco de dados que ocorrem com mais frequência em cargas de trabalho de processamento de transações online (OLTP). Embora o parâmetro de comparação seja projetado com a computação em nuvem em mente, o esquema do banco de dados, o preenchimento dos dados e as transações foram projetados para ser amplamente representativos dos elementos básicos mais comumente usados em cargas de trabalho OLTP.

### <a name="schema"></a>Esquema
O esquema foi projetado para ter variedade e complexidade suficiente para dar suporte a uma ampla gama de operações. O parâmetro de comparação é executado em um banco de dados composto por seis tabelas. As tabelas recaem em três categorias: tamanho fixo, em escala e crescente. Há duas tabelas de tamanho fixo; três tabelas em escala e uma tabela crescente. As tabelas de tamanho fixo têm um número constante de linhas. As tabelas em escala têm uma cardinalidade proporcional ao desempenho do banco de dados, mas não são alteradas durante o parâmetro de comparação. A tabela crescente é dimensionada como uma tabela em escala na carga inicial, mas depois a cardinalidade é alterada no curso da execução do parâmetro de comparação à medida que as linhas são inseridas e excluídas.

O esquema inclui uma combinação de tipos de dados, incluindo inteiros, numéricos, caracteres e data/hora. O esquema inclui chaves primárias e secundárias, mas nenhuma chave estrangeira – ou seja, não há restrições de integridade referencial entre as tabelas.

Um programa de geração de dados gera os dados para o banco de dados inicial. Os dados numéricos e inteiros são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente em um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para garantir que uma distribuição específica seja mantida. Os campos de texto são gerados de uma lista ponderada de palavras para gerar dados de aparência realista.

O banco de dados é dimensionado com base em um "fator de escala". O fator de escala (abreviado como SF) determina a cardinalidade das tabelas em escala e crescentes. Conforme descrito abaixo na seção Usuários e Definição, o tamanho do banco de dados, o número de usuários e o desempenho máximo são dimensionados proporcionalmente entre si.

### <a name="transactions"></a>Transações
A carga de trabalho consiste em nove tipos de transação, conforme mostrado na tabela a seguir. Cada transação é projetada para realçar um conjunto de características do sistema em particular no mecanismo de banco de dados e no hardware do sistema, com alto contraste em relação às outras transações. Essa abordagem facilita a avaliação do impacto dos diferentes componentes no desempenho geral. Por exemplo, a transação "Leitura Intensa" gera um número significativo de operações de leitura do disco.

| Tipo de transação | DESCRIÇÃO |
| --- | --- |
| Leitura Simples |SELECT; na memória; somente leitura |
| Leitura Média |SELECT; maior parte na memória; somente leitura |
| Leitura Intensa |SELECT; maior parte fora da memória; somente leitura |
| Atualização Simples |UPDATE; na memória; leitura/gravação |
| Atualização Intensa |UPDATE; maior parte fora da memória; leitura/gravação |
| Inserção Simples |INSERT; na memória; leitura/gravação |
| Inserção Intensa |INSERT; maior parte fora da memória; leitura/gravação |
| Excluir |DELETE; combinação de na memória e não na memória; leitura/gravação |
| CPU Intensa |SELECT; na memória; carga de CPU relativamente intensa; somente leitura |

### <a name="workload-mix"></a>Combinação de carga de trabalho
As transações são selecionadas aleatoriamente em uma distribuição ponderada com a combinação geral a seguir. A combinação geral tem uma taxa de leitura/gravação de aproximadamente 2:1.

| Tipo de transação | % de combinação |
| --- | --- |
| Leitura Simples |35 |
| Leitura Média |20 |
| Leitura Intensa |5 |
| Atualização Simples |20 |
| Atualização Intensa |3 |
| Inserção Simples |3 |
| Inserção Intensa |2 |
| Excluir |2 |
| CPU Intensa |10 |

### <a name="users-and-pacing"></a>Usuários e definição
O parâmetro de comparação de desempenho é direcionado de uma ferramenta que envia transações por meio de um conjunto de conexões para simular o comportamento de alguns usuários simultâneos. Embora todas as conexões e transações sejam geradas por máquina, para manter a simplicidade nos referimos a essas conexões como "usuários". Embora cada usuário opere independentemente dos demais, todos os usuários realizam o mesmo ciclo de etapas mostrado abaixo:

1. Estabeleça uma conexão de banco de dados.
2. Repita até receber um sinal para sair:
   * Selecione uma transação aleatoriamente (de uma distribuição ponderada).
   * Execute a transação selecionada e meça o tempo de resposta.
   * Aguarde uma definição de atraso.
3. Feche a conexão de banco de dados.
4. Sair.

A definição de atraso (na etapa 2c) é selecionada aleatoriamente, mas com uma distribuição cuja média é de 1,0 segundo. Portanto, cada usuário pode, em média, gerar no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de dimensionamento
O número de usuários é determinado pelo tamanho do banco de dados (em unidades de fator de escala). Há um usuário para cada cinco unidades de fator de escala. Devido à definição de atraso, um usuário pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um fator de escala de 500 (SF = 500) bancos de dados terá 100 usuários e pode atingir uma taxa máxima de 100 TPS. Conduzir uma taxa TPS mais alta exige mais usuários e um banco de dados maior.

A tabela a seguir mostra o número de usuários realmente mantidos para cada nível de desempenho e da camada de serviço.

| Camada de serviço (nível de desempenho) | Usuários | Tamanho do banco de dados |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2,1 GB |
| Standard (S2) |50 |7,1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Duração da medida
Uma execução válida do parâmetro de comparação exige uma duração da medida permanente de pelo menos uma hora.

### <a name="metrics"></a>Métricas
As principais métricas no parâmetro de comparação são a taxa de transferência e o tempo de resposta.

* A taxa de transferência é a medida de desempenho essencial no parâmetro de comparação. A taxa de transferência é informada em transações por unidade de tempo, contando todos os tipos de transação.
* O tempo de resposta é uma medida da previsibilidade do desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com classes superiores de serviço com um requisito de tempo de resposta mais rigoroso, conforme mostrado abaixo.

| Classe de serviço | Medida de taxa de transferência | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |95º percentil em 0,5 segundo |
| Standard |Transações por minuto |90º percentil em 1,0 segundo |
| Basic |Transações por hora |80º percentil em 2,0 segundos |

## <a name="next-steps"></a>Próximas etapas

- Para [Modelo de compra baseado em vCore (versão prévia)](sql-database-service-tiers-vcore.md), consulte Modelo de compra baseado em vCore (versão prévia)
- Para o modelo de compra com base em DTU, consulte [DTU com base no modelo de compra](sql-database-service-tiers-dtu.md).

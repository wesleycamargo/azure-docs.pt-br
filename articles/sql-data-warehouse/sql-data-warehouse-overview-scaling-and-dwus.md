<properties
   pageTitle="Gerenciar poder de computação no SQL Data Warehouse do Azure (Visão Geral) | Microsoft Azure"
   description="Funcionalidades de escala horizontal de desempenho no SQL Data Warehouse do Azure. Escale horizontalmente por meio de ajuste de DWUs ou, para economizar custos, pause e retome os recursos de computação."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Gerenciar poder de computação no SQL Data Warehouse do Azure (Visão Geral)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-manage-compute.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

A arquitetura do SQL Data Warehouse separa armazenamento e computação, permitindo que cada um seja dimensionado independentemente. Como resultado, você pode escalar horizontalmente o desempenho, economizando custos simultaneamente ao pagar pelo desempenho somente quando necessário.

Esta visão geral descreve os recursos de escala horizontal de desempenho do SQL Data Warehouse e fornece recomendações sobre como e quando usá-los.

- Dimensionar o poder de computação ajustando as DWUs (unidades de data warehouse)
- Pausar ou retomar recursos de computação

<a name="scale-performance-bk"></a>

## Dimensionar o desempenho

No SQL Data Warehouse você pode, com rapidez, escalar horizontalmente o desempenho ou reverter esse processo, aumentando ou diminuindo os recursos de computação de CPU, memória e largura de banda de E/S. Para dimensionar o desempenho, tudo o que você precisa fazer é ajustar o número de DWUs (unidades de data warehouse) que o SQL Data Warehouse aloca para seu banco de dados. O SQL Data Warehouse faz rapidamente a alteração e trata de todas as alterações subjacentes de hardware e software.

>[AZURE.NOTE] Já se foram os dias em que você precisava pesquisar o tipo de processador, a quantidade de memória ou o tipo de armazenamento que são necessários para obter excelente desempenho no seu data warehouse. Ao colocar o Data Warehouse na nuvem, você não precisa mais lidar com problemas de nível baixo de hardware. Em vez disso, o SQL Data Warehouse faz essa pergunta: com que rapidez você deseja analisar seus dados?

### O que é uma DWU?

Uma DWU (*unidade de data warehouse*) é uma medida dos recursos de computação subjacentes do banco de dados em um momento qualquer. À medida que aumenta o número de DWUs, o SQL Data Warehouse executa operações em paralelo (por exemplo, a consulta ou o carregamento de dados) em recursos de CPU e memória mais distribuídos. Isso reduz a latência e melhora o desempenho.

DWUs são com base nas taxas de carga e de varredura. Ao aumentar as DWUs, você aumenta a taxa de carga e taxa de varredura.

- **Taxa de carga**. O número de registros que o SQL Data Warehouse pode incluir por segundo. Especificamente, este é o número de registros que o SQL Data Warehouse pode importar, pelo uso do PolyBase, do Armazenamento de Blobs do Azure para um índice columnstore clusterizado. 

- **Taxa de varredura**. O número de registros que uma consulta pode recuperar do SQL Data Warehouse por segundo. Especificamente, este é o número de registros que o SQL Data Warehouse pode retornar, executando uma consulta de armazenamento de dados em um índice columnstore clusterizado. Para testar a taxa de varredura, usamos uma consulta de armazenamento de dados padrão que verifica um grande número de linhas e, em seguida, executa uma agregação complexa; essa operação exige bastante E/S e CPU.

>[AZURE.NOTE] Nós estamos medindo e realizando importantes aprimoramentos de desempenho e em breve compartilharemos as taxas esperadas. Durante a visualização, faremos aprimoramentos contínuos (por exemplo, aumentar a compactação e o armazenamento em cache) para aumentar essas taxas e garantir que eles sejam dimensionados de maneira previsível.

Para obter uma lista das DWUs, consulte os objetivos de nível de serviço no artigo [limites de capacidade][].

### Como dimensiono o desempenho?

Para aumentar ou diminuir seu poder de computação, basta alterar a configuração de DWUs (unidades de data warehouse) do banco de dados. Nos bastidores, o SQL Data Warehouse altera as alocações de memória e CPU usando recursos de implantação rápida e simples de Banco de Dados SQL.

DWUs são alocadas em blocos de 100, mas nem todos os blocos estão disponíveis. Conforme o número de DWUs aumenta, o desempenho aumenta linearmente. Em níveis mais altos de DWU, você precisa adicionar mais de 100 DWUs para notar uma melhoria significativa no desempenho. Para ajudar a selecionar saltos significativos em DWUs, oferecemos os níveis DWU que fornecerão os melhores resultados.
 
Para ajustar DWUs, você pode usar qualquer um destes métodos individuais.

- [Dimensionar o poder de computação com o portal do Azure][]
- [Dimensionar o poder de computação com o PowerShell][]
- [Dimensionar o poder de computação com APIs REST][]
- [Dimensionar o poder de computação com o TSQL][]

### Quantas DWUs devo usar?
 
O desempenho no SQL Data Warehouse é em escala linear, e a mudança de uma escala de computação para outra (digamos de 100 DWUs para 2.000 DWUs) acontece em segundos. Isso lhe dá a flexibilidade para fazer experiências com configurações de DWU diferentes até determinar o melhor ajuste para o cenário.

> [AZURE.NOTE] Talvez você não veja o desempenho esperado ao dimensionar em volumes de dados menores. Recomendamos começar com volumes de dados de 1 TB ou acima para obter resultados precisos do teste de desempenho.

Recomendações para encontrar a melhor DWU para sua carga de trabalho:

1. Para um data warehouse em desenvolvimento, comece selecionando um número pequeno de DWUs.
2. Monitore o desempenho do seu aplicativo, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Determine quão rápido ou lento o desempenho deve ser para você obter o nível de desempenho ideal para seus requisitos, presumindo uma escala linear.
4. Aumente ou diminua o número de DWUs proporcionalmente à velocidade desejada do desempenho de sua carga de trabalho. O serviço responderá rapidamente e ajustará os recursos de computação para atender aos novos requisitos de DWU.
5. Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

### Quando devo dimensionar as DWUs?

Em geral, queremos que as DWUs sejam simples. Quando você precisar de resultados mais rápidos, aumente suas DWUs e pague por mais desempenho. Quando precisar de menos potência de computação, diminua suas DWUs e pague somente pelo que precisa.

Recomendações para quando dimensionar DWUs:

1. Se o seu aplicativo tiver uma carga de trabalho flutuante, escale ou reduza verticalmente os níveis de DWU a fim de acomodar picos e pontos baixos. Por exemplo, se a carga de trabalho geralmente atinge o pico no fim do mês, planeje a adição de mais DWUs durante esses dias de pico e depois reduza verticalmente quando o período de pico terminar.
1. Antes de executar uma operação de transformação ou carregamento de dados pesados, escale verticalmente as DWUs para que os dados fiquem disponíveis mais rapidamente.

<a name="pause-compute-bk"></a>

## Pausar computação

[AZURE.INCLUDE [Descrição de pausa do SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use qualquer um destes métodos individuais.

- [Pausar computação com o portal do Azure][]
- [Pausar computação com o PowerShell][]
- [Pausar computação com APIs REST][]

<a name="resume-compute-bk"></a>

## Retomar a computação

[AZURE.INCLUDE [Descrição de retomada do SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar um banco de dados, use qualquer um destes métodos individuais.

- [Retomar computação com o portal do Azure][]
- [Retomar computação com o PowerShell][]
- [Retomar computação com APIs REST][]

<a name="next-steps-bk"></a>

## Próximas etapas
Consulte os artigos a seguir que ajudam a entender alguns dos principais conceitos adicionais de desempenho e escala:

- [modelo de simultaneidade][]
- [criando tabelas][]
- [escolher uma chave de distribuição de hash para sua tabela][]
- [estatísticas para melhorar o desempenho][]

<!--Image reference-->

<!--Article references-->

[Dimensionar o poder de computação com o portal do Azure]: ./sql-data-warehouse-manage-compute-tasks-portal.md#task-1-scale-performance
[Dimensionar o poder de computação com o PowerShell]: ./sql-data-warehouse-manage-compute-tasks-powershell.md#task-1-scale-performance
[Dimensionar o poder de computação com APIs REST]: ./sql-data-warehouse-manage-compute-tasks-rest-api.md#task-1-scale-performance
[Dimensionar o poder de computação com o TSQL]: ./sql-data-warehouse-manage-compute-tasks-tsql.md

[limites de capacidade]: ./sql-data-warehouse-service-capacity-limits.md

[Pausar computação com o portal do Azure]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-2-pause-compute
[Pausar computação com o PowerShell]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-2-pause-compute
[Pausar computação com APIs REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-2-pause-compute
[Retomar computação com o portal do Azure]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-3-resume-compute
[Retomar computação com o PowerShell]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-3-resume-compute
[Retomar computação com APIs REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-3-resume-compute

[modelo de simultaneidade]: sql-data-warehouse-develop-concurrency.md
[criando tabelas]: sql-data-warehouse-develop-table-design.md
[escolher uma chave de distribuição de hash para sua tabela]: sql-data-warehouse-develop-hash-distribution-key.md
[estatísticas para melhorar o desempenho]: sql-data-warehouse-develop-statistics.md
[development overview]: sql-data-warehouse-overview-develop.md



<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->
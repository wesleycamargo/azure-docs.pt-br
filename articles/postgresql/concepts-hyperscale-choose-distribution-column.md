---
title: Escolha as colunas de distribuição no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)
description: Boas opções para colunas de distribuição em cenários comuns de hiperescala
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078979"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Escolha as colunas de distribuição no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)

Escolher a coluna de distribuição de cada tabela é **um dos mais importantes** decisões de modelagem. Hiperescala armazena linhas em fragmentos com base no valor da coluna de distribuição de linhas.

Os grupos de escolha correta relacionadas a consultas de dados juntos nos mesmos nós físicos, tornando rápido e adicionando suporte para todos os recursos do SQL. Torna uma escolha incorreta o sistema será executado lentamente e não dão suporte a todos os recursos do SQL entre os nós.

Esta seção fornece dicas de coluna de distribuição para os dois cenários mais comuns de hiperescala.

### <a name="multi-tenant-apps"></a>Aplicativos multilocatário

A arquitetura de multilocatário usa um formulário do banco de dados hierárquico de modelagem para distribuir consultas entre os nós no grupo de servidores.  A parte superior da hierarquia de dados é conhecido como o *ID do locatário*e precisam ser armazenados em uma coluna em cada tabela.

Hiperescala inspeciona consultas para ver qual ID de locatário eles envolvem e localiza o fragmento de tabela correspondente. Ele encaminha a consulta para um nó de trabalho única que contém o fragmento. Executar uma consulta com todos os dados relevantes, colocados no mesmo nó é chamado de colocação.

O diagrama a seguir ilustra a colocação no modelo de dados multilocatário. Ele contém duas tabelas, contas e campanhas, distribuídos por `account_id`. As caixas sombreadas representam fragmentos, cada um dos cuja cor representa qual nó de trabalho contém. Verdes fragmentos são armazenados juntos em um nó de trabalho e o azul em outro. Observe como uma consulta de junção entre contas e campanhas teria todos os dados necessários juntos em um nó ao restringir ambas as tabelas para a mesma conta\_id.

![colocação de multilocatário](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar esse design em seu próprio esquema, identifique o que constitui um locatário em seu aplicativo. Instâncias comuns incluem conta, empresa, organização ou cliente. O nome da coluna será algo como `company_id` ou `customer_id`. Examinar cada uma de suas consultas e pergunte-se: ele funcionaria se tivesse adicionais cláusulas WHERE para restringir todas as tabelas envolvidas em linhas com a mesma ID de locatário?
Escopo das consultas no modelo de multilocatário para um locatário, por exemplo consultas em vendas ou de inventário seriam ter o escopo definidas em um determinado repositório.

#### <a name="best-practices"></a>Práticas Recomendadas

-   **Partição distribuídas tabelas por um locatário comuns\_coluna de id.** Por exemplo, em um aplicativo de SaaS em que os locatários são empresas, o locatário\_id será, provavelmente empresa\_id.
-   **Converta tabelas de entre locatários pequenas em tabelas de referência.** Quando vários locatários compartilham uma pequena tabela de informações, distribuí-lo como uma tabela de referência.
-   **Restringir o aplicativo de todas as consultas de filtro por locatário\_id.** Cada consulta deve solicitar informações para um locatário por vez.

Leia as [multilocatário tutorial](./tutorial-design-database-hyperscale-multi-tenant.md) para obter um exemplo da criação desse tipo de aplicativo.

### <a name="real-time-apps"></a>Aplicativos em tempo real

A arquitetura de multilocação apresenta uma estrutura hierárquica e usa a colocação de dados para encaminhar as consultas por locatário. Por outro lado, arquiteturas em tempo real dependem das propriedades de distribuição específicos de seus dados para obter processamento altamente paralelo.

Usamos "ID da entidade" como um termo para colunas de distribuição no modelo em tempo real. Entidades típicas são usuários, hosts ou dispositivos.

Consultas em tempo real normalmente perguntam para agregações numéricas agrupadas por data ou categoria. Hiperescala envia essas consultas para cada fragmento para resultados parciais e monta a resposta final no nó coordenador. Consultas executadas mais rápido quando como Contribuir com muitos nós quanto possível, e quando nenhum nó único deve fazer uma quantidade desproporcional de trabalho.

#### <a name="best-practices"></a>Práticas Recomendadas

-   **Escolha uma coluna com alta cardinalidade, como a coluna de distribuição.** Para comparação, um \"status\" campo em uma tabela de ordem com valores "novos", "pago" e "enviado" é uma opção ruim de coluna de distribuição. Ele pressupõe que somente esses poucos valores, que limita o número de fragmentos que pode conter os dados e o número de nós que possa processá-los. Entre colunas com alta cardinalidade, é bom adicionalmente escolher aqueles que são frequentemente usados em cláusulas group by ou como chaves de junção.
-   **Escolha uma coluna com a distribuição uniforme.** Se você distribuir uma tabela em uma coluna distorcida para determinados valores comuns, dados da tabela serão tendem a se acumular em determinados fragmentos. Os nós que contenham esses fragmentos acabará fazendo mais trabalho do que outros nós.
-   **Distribua tabelas de fatos e dimensões em suas colunas comuns.**
    A tabela de fatos pode ter somente uma chave de distribuição. Tabelas de junção em outra chave não será colocadas com a tabela de fatos. Escolha uma dimensão para colocar os com base na frequência com a qual ele está associado e o tamanho das linhas de junção.
-   **Altere algumas tabelas de dimensão nas tabelas de referência.** Se uma tabela de dimensão não pode ser colocada com a tabela de fatos, você pode melhorar o desempenho distribuindo cópias da tabela de dimensões para todos os nós na forma de uma tabela de referência.

Leia as [tutorial de painel em tempo real](./tutorial-design-database-hyperscale-realtime.md) para obter um exemplo da criação desse tipo de aplicativo.

### <a name="timeseries-data"></a>Dados de série temporal

Em uma carga de trabalho de série temporal, aplicativos consultar informações recentes ao arquivar informações antigas.

O erro mais comum na modelagem de informações de série temporal em hiperescala é usar o carimbo de hora em si como uma coluna de distribuição. Uma distribuição de hash com base na hora distribuirá horários aparentemente aleatório em fragmentos diferentes, em vez de manter juntos os intervalos de tempo em fragmentos. Consultas que envolvem tempo geralmente fazem referência a intervalos de tempo (por exemplo os dados mais recentes), portanto, essa é uma distribuição de hash poderia levar a rede sobrecarga.

#### <a name="best-practices"></a>Práticas Recomendadas

-   **Não escolha um carimbo de hora como a coluna de distribuição.** Escolha uma coluna de distribuição diferente. Em um aplicativo multilocatário, use a ID de locatário ou em um aplicativo em tempo real, use a ID da entidade.
-   **Use o PostgreSQL particionamento de tabela para o tempo em vez disso.** Use o particionamento de tabela para dividir uma tabela grande de dados ordenadas por tempo em várias tabelas herdadas com cada um contendo os intervalos de tempo diferentes.  Distribuindo uma tabela particionada Postgres em hiperescala cria fragmentos para as tabelas herdadas.

Leia as [timeseries tutorial](https://aka.ms/hyperscale-tutorial-timeseries) para obter um exemplo da criação desse tipo de aplicativo.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [colocação](concepts-hyperscale-colocation.md) entre dados distribuídos ajuda consultas executadas rapidamente

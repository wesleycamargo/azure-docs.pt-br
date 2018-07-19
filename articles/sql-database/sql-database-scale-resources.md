---
title: Recursos de escala do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo explica como dimensionar o banco de dados, adicionando ou removendo os recursos alocados.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/07/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: f55ce511f6ba90c27e149ac90bbd2c8aa0b3c742
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37923434"
---
# <a name="scale-database-resources"></a>Dimensionar os recursos de banco de dados

O Banco de Dados SQL do Azure permite que você adicione dinamicamente mais recursos ao banco de dados com tempo de inatividade mínimo.

## <a name="overview"></a>Visão geral

Quando a demanda de seu aplicativo aumenta de alguns dispositivos e clientes para milhões, o Banco de Dados SQL do Azure é dimensionado de maneira dinâmica e com tempo de inatividade mínimo. A escalabilidade é uma das características mais importantes do PaaS que possibilita adicionar dinamicamente mais recursos ao serviço, quando necessário. O Banco de Dados SQL do Azure permite que você altere com facilidade os recursos (potência da CPU, memória, taxa de transferência de E/S e armazenamento) alocados a seus bancos de dados.  
Você pode atenuar problemas de desempenho devido ao aumento no uso do aplicativo que não pode ser corrigido usando métodos de indexação ou de reescrita de consulta. A adição de mais recursos permite que você responda rapidamente quando seu banco de dados atingir os limites de recursos atuais e precisar de mais capacidade para manipular a carga de trabalho de entrada. O Banco de Dados SQL do Azure também possibilita que você reduza verticalmente os recursos quando eles não forem necessários para reduzir o custo.
Você não precisa se preocupar com a compra do hardware e a alteração da infraestrutura subjacente. O dimensionamento do banco de dados pode ser feito com facilidade por meio do portal do Azure usando um controle deslizante.

![Dimensionar o desempenho do banco de dados](media/sql-database-scalability/scale-performance.svg)

O Banco de Dados SQL do Azure oferece um [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o [modelo de compra baseado em vCore (versão prévia)](sql-database-service-tiers-vcore.md). 
-   O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de computação, memória e recursos de E/S em três camadas de serviço para dar suporte a cargas de trabalho leves e pesadas de banco de dados: Básico, Standard e Premium. Níveis de desempenho dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
-   O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) (versão prévia) permite que você escolha o número de vCores, a quantidade ou a memória e a quantidade e a velocidade de armazenamento.
Você pode criar seu primeiro aplicativo em um único banco de dados pequeno por um valor baixo por mês e alterar sua camada de serviço manualmente ou por meio de programação a qualquer momento para atender às necessidades de sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.


> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. O dimensionamento automático é quando um serviço pode ser dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmico permite o redimensionamento manual sem tempo de inatividade.
>


O Banco de Dados SQL do Azure Individual oferece suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para uma experiência mais *automática*, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados.
No entanto, há scripts que podem ajudar a automatizar a escalabilidade de um Banco de Dados individual SQL do Azure. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um Banco de Dados SQL individual](scripts/sql-database-monitor-and-scale-database-powershell.md).


Todos os três tipos de Bancos de Dados SQL do Azure oferecem alguma capacidade de dimensionar dinamicamente os bancos de dados:
-   No [Banco de Dados Individual SQL do Azure](sql-database-single-database-scale.md), você pode usar modelos de [DTU](sql-database-dtu-resource-limits-single-databases.md) ou [vCore](sql-database-vcore-resource-limits-single-databases.md) para definir a quantidade máxima de recursos que serão atribuídos a cada banco de dados.
-   A [Instância Gerenciada do SQL do Azure](sql-database-managed-instance.md) usa o modo [vCores](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) e permite que você defina o máximo de núcleos de CPU e o máximo de armazenamento alocado para sua instância. Todos os bancos de dados na instância compartilharão os recursos alocados para a instância.
-   Os [Pools Elásticos do SQL do Azure](sql-database-elastic-pool-scale.md) permitem que você defina o limite máximo de recursos por grupo de bancos de dados no pool.

## <a name="alternative-scale-methods"></a>Métodos alternativos de escala
O dimensionamento de recursos é a maneira mais fácil e mais eficiente para melhorar o desempenho do banco de dados sem alterar o código do banco de dados ou do aplicativo.
Em alguns casos, até mesmo os mais altos níveis de desempenho e as mais altas otimizações de desempenho podem não manipular a carga de trabalho de forma bem-sucedida e econômica. Nesses casos, você tem outras opções para dimensionar o banco de dados:
-   A [escala de leitura](sql-database-read-scale-out.md) é um recurso disponível em que você obtém uma réplica somente leitura de seus dados, na qual você pode executar consultas somente leitura mais exigentes, como relatórios. A réplica somente leitura manipulará a carga de trabalho somente leitura sem afetar o uso de recursos no banco de dados primário.
-   A [fragmentação de banco de dados](sql-database-elastic-scale-introduction.md) é um conjunto de técnicas que permite dividir os dados em vários bancos de dados e dimensioná-los de forma independente.

## <a name="next-steps"></a>Próximas etapas
- Para obter informações sobre como melhorar o desempenho do banco de dados alterando o código do banco de dados, confira [Encontrar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para obter informações sobre como deixar a inteligência interna do banco de dados otimizar o banco de dados, confira [Ajuste automático](sql-database-automatic-tuning.md).
- Para obter informações sobre a Escala de Leitura no serviço Banco de Dados SQL do Azure, confira como [usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura](sql-database-read-scale-out.md).
- Para obter informações sobre a Fragmentação de um banco de dados, confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).


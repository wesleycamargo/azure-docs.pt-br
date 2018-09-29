---
title: Azure SQL Database Hyperscale Overview | Microsoft Docs
description: Este tópico descreve a camada de serviço em hiperescala no modelo de compra baseado em vCore no banco de dados SQL e explica como ele é diferente do que as camadas de serviço de uso geral e comercialmente crítico.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: 314a2182c3be5c5f10e075cdbda27f4a716f2536
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160393"
---
# <a name="what-is-the-hyperscale-service-tier-preview-in-azure-sql-database"></a>O que é a camada de serviço em hiperescala (visualização) no banco de dados SQL?

A camada de serviço em hiperescala no banco de dados SQL é a camada de serviço mais recente em que o modelo de compra baseado em vCore. Essa camada de serviço é um armazenamento altamente escalonável e o nível de desempenho de computação que aproveita a arquitetura do Azure para escalar horizontalmente o armazenamento e recursos de computação para um banco de dados do SQL Azure substancialmente além dos limites disponíveis para uso geral e negócios Camadas de serviço críticos.

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>What are the capabilities of the Hyperscale service tier

A camada de serviço Hyperscale no Banco de Dados SQL do Azure fornece os seguintes recursos adicionais:

- Suporte para até um 100 TB de tamanho do banco de dados 
- Backups mais rápidos do banco de dados grandes (com base em instantâneos de arquivo)
- Com mais rapidez (com base em instantâneos de arquivo) de restaurações de banco de dados 
- Maior desempenho geral devido à maior taxa de transferência de log e tempos mais rápidos de confirmação de transação, independentemente dos volumes de dados 
- Rápida expansão - você pode provisionar uma ou mais somente leitura nós para o descarregamento de sua carga de trabalho de leitura e para uso como reserva quente
- Rápida expansão - você pode, em tempo constante, escalar verticalmente seus recursos de computação para acomodar cargas de trabalho pesadas conforme a necessidade e, em seguida, dimensionar os recursos de computação novamente quando não é necessário. 

A camada de serviço em hiperescala elimina muitos dos limites práticos vistos tradicionalmente em bancos de dados de nuvem. Onde mais outros bancos de dados são limitados pelos recursos disponíveis em um único nó, bancos de dados na camada de serviço em hiperescala não têm esses limites. Com sua arquitetura de armazenamento flexíveis, você pode expandir a capacidade de armazenamento conforme necessário. Na verdade, os bancos de dados em hiperescala não são criados com um tamanho máximo definido. Um banco de dados em hiperescala aumenta conforme necessário – e você será cobrado apenas pela capacidade de que usar. Para cargas de trabalho de leitura intensa, a camada de serviço em hiperescala fornece rápida expansão por meio do provisionamento de leitura de réplicas adicionais conforme necessário para o descarregamento de cargas de trabalho leitura. 

Além disso, o tempo necessário para criar backups de banco de dados ou para aumentar ou diminuir a escala não está mais vinculado ao volume de dados no banco de dados. Bancos de dados de hiperescala podem ser armazenados virtualmente instantaneamente. Você também pode dimensionar um banco de dados em dezenas de terabytes para cima ou para baixo em minutos. Esse recurso libera você das preocupações sobre ser encaixotado pelas opções iniciais de configuração. 

Para obter mais informações sobre os tamanhos de computação para a camada de serviço Hyperscale, consulte [Escolhendo uma camada de serviço, computação, memória, armazenamento e recursos de E / S do vCore](sql-database-service-tiers-vcore.md).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviço em hiperescala

A camada de serviço é usado principalmente para clientes que têm bancos de dados grandes tanto no local e hiperescala modernizar seus aplicativos, movendo para a nuvem ou para clientes que já estão na nuvem e são limitados pelo tamanho máximo do banco de dados restrições (1 a 4 TB). Ele também é destinado a clientes que busca de alto desempenho e alta escalabilidade para armazenamento e computação.

A camada de serviço em hiperescala dá suporte a todas as cargas de trabalho do SQL Server, mas basicamente é otimizado para OLTP. A camada de serviço em hiperescala também dá suporte a híbrida analítica e cargas de trabalho (armazém de dados). 

> [!IMPORTANT]
> Pools Elásticos não dão suporte a camada de serviço em hiperescala.

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Arquitetura: Funções para isolar recursos de distribuição

Ao contrário de mecanismos de banco de dados tradicional que centralizou a todas as funções de gerenciamento de dados em um local/processo (até mesmo então chamados bancos de dados distribuídos em produção hoje têm várias cópias de um mecanismo de dados monolítico), um banco de dados em hiperescala separa o mecanismo de processamento de consulta, em que a semântica de diversos mecanismos de dados divergem dos componentes que fornecem armazenamento de longo prazo e a durabilidade dos dados. Dessa forma, a capacidade de armazenamento pode ser perfeitamente expandida até onde conforme necessário (o destino inicial é 100 TB). Réplicas somente leitura compartilham os mesmos componentes de computação, portanto, nenhuma cópia de dados é necessária para criar uma nova réplica legível.

O diagrama a seguir ilustra os diferentes tipos de nós em um banco de dados de hiperescala:

![Arquitetura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Um banco de dados em hiperescala contém os seguintes tipos diferentes de nós:
 
### <a name="compute-node"></a>Nó de computação

O nó de computação é onde reside o mecanismo relacional, portanto, os elementos de linguagem, processamento de consulta e assim por diante, ocorrerem. Todas as interações do usuário com um banco de dados em hiperescala ocorrem por meio de nós de computação. Nós têm caches baseado em SSD (rotulado como RBPEX - extensão do Pool de buffers resiliente no diagrama anterior) para minimizar o número da rede de computação viagens de ida e necessárias para buscar uma página de dados. Há um nó de computação principal em que todas as transações e cargas de trabalho de leitura / gravação são processadas. Há um ou mais nós de computação secundária que atuam como nós em espera ativos para fins de failover, bem como para atuam como nós de computação de somente leitura para o descarregamento de cargas de trabalho de leitura (se essa funcionalidade é desejada).

### <a name="page-server-node"></a>Nó do servidor de páginas

Servidores de página são sistemas que representam um mecanismo de armazenamento dimensionado.  Cada servidor de páginas é responsável por um subconjunto das páginas no banco de dados.  Nominalmente, controles de servidor de cada página 1 terabyte de dados. Nenhum dado é compartilhado em mais de um servidor de página (fora de réplicas que são mantidos por redundância e disponibilidade). O trabalho de um servidor de páginas é servir páginas de banco de dados para os nós de computação sob demanda e manter as páginas atualizadas à medida que as transações atualizam dados. Servidores de página são mantidas atualizadas ao reproduzir os registros de log do serviço de log. Servidores de página também mantenham caches baseado em SSD para melhorar o desempenho. Armazenamento de longo prazo de páginas de dados é mantido no armazenamento do Azure para confiabilidade adicional.

### <a name="log-service-node"></a>Nó de serviço de log

O nó do serviço de log aceita registros de log do nó de computação principal, persisti-los em um cache durável e encaminha os registros de log para o restante de nós de computação (de modo que eles possam atualizar seus caches), bem como os servidores de página relevante, para que os dados possam ser atualizado t aqui. Dessa forma, todas as alterações de dados do nó de computação principal são propagadas por meio do serviço de log para todos os nós de computação secundária e os servidores da página. Por fim, os registros de log são enviados para o armazenamento de longo prazo no armazenamento do Azure, que é um repositório de armazenamento infinita. Esse mecanismo remove a necessidade de truncamento de log com frequência. O serviço de log também tem um cache local para acelerar o acesso.

### <a name="azure-storage-node"></a>Nó de armazenamento do Azure

O nó de armazenamento do Azure é o destino final dos dados de servidores de página. Esse armazenamento é usado para fins de backup, bem como para a replicação entre regiões do Azure. Os backups consistem em instantâneos de arquivos de dados. Restaurar operação são rápidas a partir desses instantâneos e dados podem ser restaurados para qualquer ponto no tempo. 

## <a name="backup-and-restore"></a>Backup e restauração

Os backups são a base de dados de instantâneo de arquivo e, portanto, eles são quase instantâneos. Separação de computação e armazenamento permitem realizar a operação de backup/restauração para a camada de armazenamento reduzir a carga de processamento no nó de computação principal. Como resultado, o backup de um banco de dados grande não afeta o desempenho do nó de computação principal. Da mesma forma, as restaurações são feitas pelo copiando o instantâneo de arquivo e como tal, não têm um tamanho de operação de dados. Para obter restaurações dentro da mesma conta de armazenamento, a operação de restauração é rápida.

## <a name="scale-and-performance-advantages"></a>Vantagens de desempenho e escala

Com a capacidade de criar rapidamente nós de computação adicionais de somente leitura para cima/para baixo, a arquitetura permite significativa ler recursos de escala e hiperescala também pode liberar o nó de computação principal para atender às solicitações de gravação mais. Além disso, os nós de computação podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento compartilhado da arquitetura em hiperescala. 


## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre as camadas de serviço, consulte [camadas de serviço](sql-database-service-tiers.md)
- Para obter detalhes sobre limites de recursos, consulte [vCore limites de recursos](sql-database-resource-limits.md)
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).

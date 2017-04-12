---
title: "Backup e restauração online com o DocumentDB | Microsoft Docs"
description: "Saiba como executar o backup automático e a restauração de bancos de dados NoSQL com o Azure DocumentDB."
keywords: "backup e restauração, backup online"
services: documentdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: raprasa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e9b99a79adf445da8761ee399fb1e1a51f9224fc
ms.lasthandoff: 04/03/2017


---
# <a name="automatic-online-backup-and-restore-with-documentdb"></a>Backup online automático e restauração com o DocumentDB
O Azure DocumentDB faz backup automaticamente de todos os seus dados em intervalos regulares. Os backups automáticos são feitos sem afetar o desempenho ou a disponibilidade de suas operações de banco de dados NoSQL. Todos os backups são armazenados separadamente em outro serviço de armazenamento, e esses backups são replicados globalmente para resiliência contra desastres regionais. Os backups automáticos são destinados a cenários em que você exclui acidentalmente sua coleção do DocumentDB e posteriormente requer uma solução de recuperação de desastre ou de dados.  

Este artigo começa com uma recapitulação rápida da redundância de dados e da disponibilidade de dados no DocumentDB e discute os backups. 

## <a name="high-availability-with-documentdb---a-recap"></a>Alta disponibilidade com DocumentDB - recapitulação
O DocumentDB foi projetado para ser [distribuído globalmente](documentdb-distribute-data-globally.md) : ele permite que você dimensione a produtividade em várias regiões do Azure juntamente com failover controlado por política e APIs e hospedagem múltipla. Como um sistema de banco de dados que oferece [SLAs de disponibilidade de 99,99%](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/), todas as gravações no DocumentDB são permanentemente confirmadas em discos locais por um quorum de réplicas em um data center local antes de confirmar para o cliente. Observe que a alta disponibilidade do DocumentDB se baseia no armazenamento local e não depende de qualquer tecnologia de armazenamento externo. Além disso, se sua conta de banco de dados está associada a mais de uma região do Azure, suas gravações são replicadas em outras regiões também. Para dimensionar seus dados de produtividade e acesso em latências menores, você pode ter quantas regiões de leitura associadas à sua conta de banco de dados quantas quiser. Em cada região de leitura, os dados (replicados) são persistidos em um conjunto de réplicas.  

Conforme ilustrado no diagrama a seguir, uma única coleção do DocumentDB é [particionada horizontalmente](documentdb-partition-data.md). Uma “partição” é indicada por um círculo no diagrama a seguir, e cada partição torna-se altamente disponível por meio de um conjunto de réplicas. Essa é a distribuição local dentro de uma única região do Azure (indicada pelo eixo X). Além disso, cada partição (com seu conjunto de réplicas correspondente) é distribuída globalmente entre várias regiões associadas à sua conta de banco de dados (por exemplo, neste caso, três regiões: Leste dos EUA, Oeste dos EUA e Índia Central). O "conjunto de partição" é uma entidade distribuída globalmente que consiste em várias cópias de seus dados em cada região (indicado pelo eixo Y). Você pode atribuir prioridade às regiões associadas à conta de banco de dados e o DocumentDB fará failover de forma transparente para a próxima região em caso de desastre. Você pode simular o failover manualmente para testar a disponibilidade de ponta a ponta de seu aplicativo.  

A imagem a seguir ilustra o alto grau de redundância com o DocumentDB.

![Alto grau de redundância com o DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![Alto grau de redundância com o DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>Backups online completos, automáticos
Opa, excluí uma coleção ou banco de dados! Com o DocumentDB, não apenas os dados, mas também os backups de dados ficam altamente redundantes e resilientes em desastres regionais. Esses backups automatizados são feitos atualmente a cada quatro horas, aproximadamente, e os últimos 2 backups são armazenados o tempo todo. Se os dados forem acidentalmente descartados ou estiverem corrompidos, [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/) em até 8 horas. 

Os backups são feitos sem afetar o desempenho ou a disponibilidade de suas operações de banco de dados. O DocumentDB utiliza o backup em segundo plano sem consumir os RUs provisionados ou afetar o desempenho, e sem afetar a disponibilidade do banco de dados NoSQL. 

Ao contrário de seus dados que são armazenados dentro do DocumentDB, os backups automáticos são armazenados no serviço de Armazenamento de Blobs do Azure. Para garantir carregamento eficiente/baixa latência, o instantâneo do backup é carregado em uma instância do Armazenamento de Blobs do Azure na mesma região que a região de gravação atual da sua conta de banco de dados do DocumentDB. Para resiliência contra desastres regionais, cada instantâneo dos seus dados de backup no Armazenamento de Blobs do Azure é replicado novamente via GRS (armazenamento com redundância geográfica) para outra região. O diagrama a seguir mostra que a coleção inteira do DocumentDB (com todas as três partições primárias no Oeste dos EUA, neste exemplo) tem o backup feito em uma conta de Armazenamento de Blobs do Azure remota no Oeste dos EUA e, então, replicadas via GRS no Leste dos EUA. 

A imagem a seguir ilustra os backups completos periódicos de todas as entidades do DocumentDB no armazenamento do Azure GRS.

![Backups completos periódicos de todas as entidades de DocumentDB no Armazenamento do Azure GRS](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>Período de retenção para um instantâneo específico
Conforme descrito acima, podemos tirar instantâneos dos dados periodicamente e, de acordo com nossas regulamentações de conformidade, podemos manter o instantâneo mais recente por 90 dias. Se uma coleção ou uma conta for excluída, o DocumentDB armazenará o último backup por 90 dias.

## <a name="restore-database-from-the-online-backup"></a>Restaurar o banco de dados do backup online
Caso exclua seus dados acidentalmente, você pode [criar um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [ligar para o suporte do Azure](https://azure.microsoft.com/support/options/) a fim de restaurar os dados usando o último backup automático. Para obter um instantâneo específico do backup a ser restaurado, o DocumentDB requer que os dados estivessem disponíveis conosco por pelo menos a duração do ciclo de backup do instantâneo.

## <a name="next-steps"></a>Próximas etapas
Para replicar o banco de dados NoSQL em vários data centers, confira [Distribuir os dados globalmente com o DocumentDB](documentdb-distribute-data-globally.md). 

Para entrar em contato com o Suporte do Azure, [crie um tíquete no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



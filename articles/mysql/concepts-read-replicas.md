---
title: Leia réplicas no Banco de Dados do Azure para MySQL.
description: Este artigo descreve as réplicas de leitura do Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: be592cb6bb7c041fab0a2f96a338f4f4bb0ff00a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510917"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leia réplicas no Banco de Dados do Azure para MySQL

O recurso de réplica de leitura permite replicar dados de um banco de dados do Azure para servidor MySQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. Réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo de log binário nativo (binlog) do mecanismo do MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!IMPORTANT]
> Você pode criar uma réplica de leitura na mesma região do seu servidor mestre, ou em qualquer outra região do Azure de sua escolha. A replicação entre regiões está atualmente em visualização pública.

As réplicas são novos servidores que você gerencie o banco de dados semelhante ao regular do Azure para servidores MySQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre os recursos e problemas de replicação do MySQL, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do MySQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

Leitura de réplicas podem aprimorar seu plano de recuperação de desastres. Se houver um desastre regional e seu servidor mestre não está disponível, você pode direcionar sua carga de trabalho para uma réplica em outra região. Para fazer isso, primeiro permitem que a réplica aceitar gravações por meio da função de replicação de parada. Em seguida, você pode redirecionar seu aplicativo, atualizando a cadeia de caracteres de conexão. Saiba mais na [parar a replicação](#stop-replication) seção.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor mestre não tiver nenhum servidor de réplica existente, o mestre primeiro será reiniciada para se prepare para replicação.

Quando você inicia o fluxo de trabalho Criar réplica, um banco de dados em branco do Azure para servidor MySQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se você não tem alerta de armazenamento configurado em seus servidores, recomendamos que você faça isso. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como você faria em um banco de dados regular do Azure para servidor MySQL. Para um servidor nomeado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

Banco de dados do Azure para MySQL fornece o **retardo de replicação em segundos** métrica no Azure Monitor. Essa métrica está disponível apenas para réplicas.

Essa métrica é calculada usando o `seconds_behind_master` métrica disponível do MySQL `SHOW SLAVE STATUS` comando.

Defina um alerta para informá-lo quando o retardo de replicação alcança um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar a replicação

Você pode interromper a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

Quando você optar por interromper a replicação para uma réplica, ele perde todos os links para seu mestre anterior e outras réplicas. Não há nenhum failover automático entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Quando você cria uma réplica para um mestre que não possui nenhuma réplica existente, o mestre primeiro será reiniciada para se prepare para replicação. Leve isso em consideração e execute estas operações durante um período de pouca atividade.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo banco de dados do Azure para servidor MySQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações possam ser alteradas independentemente do servidor mestre: versão do mecanismo de MySQL, vCores, armazenamento, período de retenção de backup e geração de computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

### <a name="stopped-replicas"></a>Réplicas paradas

Se você parar a replicação entre um servidor mestre e uma réplica de leitura, a réplica interrompida se torna um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas tornam-se a servidores autônomos. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados fiquem fora de sincronia e evitar a potencial perda de dados ou corrupção, alguns parâmetros de servidor são bloqueados sejam atualizados quando usar réplicas de leitura.

Os seguintes parâmetros de servidor são bloqueados em servidores mestre e de réplica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O [ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplica. 

### <a name="other"></a>Outro

- O GTID (identificadores de transação globais) não são compatíveis.
- A criação de uma réplica de uma réplica não é suportada.
- Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Esta é uma limitação da tecnologia de replicação do MySQL. Leia mais na [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações.
- Verifique se as tabelas de servidor mestre contêm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.
- Analise a lista completa das limitações de replicação do MySQL no [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure](howto-read-replicas-cli.md)

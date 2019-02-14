---
title: Réplicas de leitura no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve as réplicas de leitura no Banco de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 270231b2ad7d94789595cfa4e681cf6c2b0f0541
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657868"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Réplicas de leitura no Banco de Dados do Azure para PostgreSQL

> [!IMPORTANT]
> O recurso de réplica de leitura está na Visualização Pública.

O recurso de réplica de leitura permite replicar dados de um servidor do Banco de Dados do Azure para PostgreSQL (mestre) para até cinco servidores somente leitura (réplicas) dentro da mesma região do Azure. As réplicas de leitura são atualizadas de forma assíncrona usando a tecnologia de replicação nativa do mecanismo do PostgreSQL.

As réplicas são novos servidores que podem ser gerenciados de maneiras semelhantes a servidores autônomos normais do Banco de Dados do Azure para PostgreSQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento provisionado em GB/mês.

Visite a [página de instruções para aprender a criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-read-replicas"></a>Quando usar réplicas de leitura
O recurso de réplica de leitura é destinado a ajudar a melhorar o desempenho e a escala de cargas de trabalho com uso intensivo de leitura. Por exemplo, as cargas de trabalho de leitura poderiam ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação poderiam ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como as réplicas são somente leitura, elas não aliviam diretamente as cargas de capacidade de gravação sobre o mestre e, portanto, esse recurso não é direcionado a cargas de trabalho com uso intensivo de gravação.

O recurso de réplica de leitura usa a replicação assíncrona do PostgreSQL e, portanto, não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Eventualmente, os dados na réplica se tornam uniformes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="creating-a-replica"></a>Criar uma réplica
O servidor mestre precisa ter **azure.replication_support** definido como REPLICA. A alteração desse parâmetro exige uma reinicialização do servidor para que entre em vigor. (O parâmetro **Azure.replication_support** se aplica somente às camadas de Uso Geral e Otimizada para Memória).

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. O tempo necessário para criar a nova réplica depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. Ele pode variar de alguns minutos a algumas horas.

O recurso de réplica de leitura usa a replicação física do PostgreSQL (e não a replicação lógica). A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

> [!NOTE]
> Se você ainda não tiver um alerta de armazenamento definido em seus servidores, recomendamos que faça isso para que possa saber quando um servidor está se aproximando do limite de armazenamento, pois isso afeta a replicação.

[Saiba como criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Por exemplo, se o nome do servidor for myreplica e o nome de usuário administrador for myadmin, você poderá se conectar a ele do psql da seguinte maneira:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
e, no prompt, insira a senha da conta de usuário.

## <a name="monitoring-replication"></a>Monitorar a replicação
Uma métrica de **Retardo Máximo entre Réplicas** está disponível no Azure Monitor. Essa métrica está disponível apenas no servidor mestre. A métrica mostra o tempo de retardo entre o mestre e a réplica com o maior retardo. 

Também fornecemos uma métrica de **Retardo da Réplica** no Azure Monitor. Essa métrica está disponível apenas para réplicas. 

A métrica é calculada na exibição pg_stat_wal_receiver:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Lembre-se de que a métrica Retardo da Réplica mostra o tempo decorrido desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse retardo.

É recomendável que você defina um alerta que informe a você quando o retardo da réplica atingir um valor que não seja aceitável para sua carga de trabalho. 

Para obter mais informações, consulte diretamente o servidor mestre para conhecer o retardo de replicação em bytes em todas as réplicas.
No PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

No PG 9.6 e abaixo:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor mestre ou de réplica for reiniciado, o tempo necessário para reiniciar e, em seguida, recuperar o atraso será refletido na métrica de Retardo da Réplica.

## <a name="stopping-replication-to-a-replica"></a>Parar a replicação para uma réplica
Você pode optar por parar a replicação entre um mestre e uma réplica. Isso fará com que a réplica seja reiniciada para remover as configurações de replicação. Após a replicação entre um servidor mestre e um servidor de réplica ser parada, o servidor de réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

Este servidor não pode ser transformado em uma réplica novamente.

Certifique-se de que a réplica tenha todos os dados necessários antes de parar a replicação.

Você pode [aprender a parar uma réplica consultando a documentação de instruções](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerações

### <a name="preparing-for-replica"></a>Preparar-se para replicar
**azure.replication_support** precisa ser definido como REPLICA no servidor mestre para que você possa criar uma réplica. A alteração desse parâmetro exige uma reinicialização do servidor para que entre em vigor. Esse parâmetro se aplica somente às camadas de Uso Geral e Otimizada para Memória.

### <a name="stopped-replicas"></a>Réplicas paradas
Se optar por parar a replicação entre um servidor mestre e uma réplica, a réplica será reiniciada para aplicar essa alteração. Assim, a réplica vai se tornar um servidor de leitura/gravação. Depois disso, ele não pode ser transformado em uma réplica novamente.

### <a name="replicas-are-new-servers"></a>Réplicas são novos servidores
As réplicas são criadas como novos servidores do Banco de Dados do Azure para PostgreSQL. Servidores existentes não podem ser transformados em réplicas.

### <a name="replica-server-configuration"></a>Configuração do servidor de réplica
Os servidores de réplica são criados usando as mesmas configurações de servidor que o mestre, o que inclui as seguintes configurações:
- Tipo de preço
- Geração de computação
- vCores
- Armazenamento
- Período de retenção do backup
- Opção de redundância de backup
- Versão do mecanismo do PostgreSQL

Após a criação de uma réplica, o tipo de preço (exceto ao alterar do tipo Básico e para ele), a geração da computação, os vCores, o armazenamento e o período de retenção de backup podem ser alterados independentemente do servidor mestre.

> [!IMPORTANT]
> Antes da atualização da configuração do servidor de um mestre para novos valores, a configuração das réplicas deve ser atualizada para valores iguais ou superiores. Isso garante que as réplicas possam permanecer atualizadas com as alterações feitas no mestre.

Em particular, o Postgres exige que o valor do servidor de réplica para o parâmetro max_connections seja maior ou igual ao valor do mestre; caso contrário, a réplica não será iniciada. No Banco de Dados do Azure para PostgreSQL, o valor de max_connections é definido dependendo do SKU. Para obter mais informações, leia [este documento sobre limites](concepts-limits.md). 

Tentar fazer uma atualização que viole essa regra causará um erro.


### <a name="deleting-the-master"></a>Excluir o mestre
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas serão reiniciadas para refletir essa alteração.

### <a name="other"></a>Outros
- Réplicas de leitura podem ser criadas apenas na mesma região do Azure que o mestre.
- A criação de uma réplica de uma réplica não é suportada.

## <a name="next-steps"></a>Próximas etapas
- [Como criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).

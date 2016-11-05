---
title: Como configurar a persistência de dados para um Cache Redis do Azure Premium
description: Saiba como configurar e gerenciar a persistência de dados para as instâncias da camada Premium do Cache Redis do Azure
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: sdanie

---
# Como configurar a persistência de dados para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo o nova camada Premium.

A camada premium do Cache Redis do Azure inclui recursos como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar a persistência em uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md).

## O que é a persistência de dados?
A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

O Cache Redis do Azure oferece a persistência do Redis usando o [modelo RDB](http://redis.io/topics/persistence), onde os dados são armazenados em uma conta de armazenamento do Azure. Quando a persistência é configurada, o Cache Redis do Azure persiste um instantâneo do cache Redis em um formato binário do Redis em disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com o uso do instantâneo mais recente.

A persistência pode ser configurada na folha de **Novo Cache Redis** durante a criação de cache e na folha **Configurações** para caches premium existentes.

## Criar um cache premium
Para criar um cache e configurar a persistência, entre no [Portal do Azure](https://portal.azure.com) e clique em **Novo**->**Dados + Armazenamento**>**Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar a persistência, primeiro selecione um dos caches **Premium** na folha **Escolha o Tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

Depois de selecionar um tipo de preço premium, clique em **Persistência do Redis**.

![Persistência do Redis][redis-cache-persistence]

As etapas na seção a seguir descrevem como configurar a persistência do Redis em seu novo cache premium. Após configurar a persistência do Redis, clique em **Criar** para criar seu novo cache premium com persistência do Redis.

## Configurar a Persistência do Redis
A persistência do Redis está configurada na folha **Persistência de dados do Redis**. Para novos caches, esta folha é acessada durante o processo de criação de cache, conforme descrito na seção anterior. Para caches existentes, a folha **Persistência de dados do Redis** é acessada a partir da folha **Configurações** para seu cache.

![Configurações do Redis][redis-cache-settings]

Para habilitar a persistência do Redis, clique em **Habilitado** para habilitar o backup do RDB (banco de dados do Redis). Para desabilitar a persistência do Redis em um cache premium habilitado anteriormente, clique em **Desabilitado**.

Para configurar o intervalo de backup, selecione uma **Frequência de Backup** na lista suspensa. As opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** e **24 horas**. Esse intervalo inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado.

Clique em **Conta de Armazenamento** para selecionar a conta de armazenamento a ser usada e escolha a **Chave primária** ou **Chave secundária** a ser usada na lista suspensa **Chave de Armazenamento**. Você deve escolher uma conta de armazenamento na mesma região que o cache e uma conta do **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma maior taxa de transferência.

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, escolha novamente a chave desejada no menu suspenso **Chave de Armazenamento**.
> 
> 

![Persistência do Redis][redis-cache-persistence-selected]

Clique em **OK** para salvar a configuração de persistência.

O próximo backup (ou primeiro backup para caches novos) será iniciado após decorrido o intervalo de frequência de backup.

## Perguntas frequentes sobre persistência
A lista a seguir contém respostas para perguntas frequentes sobre a persistência do Cache Redis do Azure.

* [Posso habilitar a persistência em um cache criado anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso alterar a frequência de backup depois de criar o cache?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de backup de 60 minutos haverá mais de 60 minutos entre os backups?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os backups antigos quando é feito um backup novo?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
* [O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### Posso habilitar a persistência em um cache criado anteriormente?
Sim, a persistência do Redis pode ser configurada na criação do cache e em caches premium existentes.

### Posso alterar a frequência de backup depois de criar o cache?
Sim, você pode alterar a frequência de backup na folha de **Persistência de dados do Redis**. Para obter instruções, consulte [Configurar persistência do Redis](#configure-redis-persistence).

### Por que se eu tiver uma frequência de backup de 60 minutos haverá mais de 60 minutos entre os backups?
O intervalo da frequência de backup não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e usar um processo de backup de 15 minutos para concluir com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

### O que acontece com os backups antigos quando é feito um backup novo?
Todos os backups, exceto pelo mais recente, serão excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos por tempo indeterminado.

### O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?
* Se você tiver dimensionado para um tamanho maior, não haverá nenhum impacto.
* Se você tiver dimensionado para um tamanho menor e tiver uma configuração de [bancos de dados](cache-configure.md#databases) personalizada que é maior do que o [limite de bancos de dados](cache-configure.md#databases) para o novo tamanho, os dados nesses bancos de dados não serão restaurados. Para obter mais informações, consulte [A configuração dos meus bancos de dados personalizados foi afetada durante o dimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
* Se você tiver dimensionado para um tamanho menor e não houver espaço suficiente no menor tamanho para conter todos os dados do último backup, as chaves serão removidas durante o processo de restauração, normalmente usando a política de remoção [allkeys-lru](http://redis.io/topics/lru-cache).

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

* [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_0921_2016-->
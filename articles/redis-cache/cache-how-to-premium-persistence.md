<properties 
	pageTitle="Como configurar a persistência de dados para um Cache Redis do Azure Premium" 
	description="Saiba como configurar e gerenciar a persistência de dados para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Como configurar a persistência de dados para um Cache Redis do Azure Premium

O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo a nova camada Premium, atualmente em visualização.

A camada premium do Cache Redis do Azure inclui clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar a persistência em uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md) e [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]A camada Premium do Cache Redis do Azure está atualmente em visualização. Durante o período de visualização, a persistência não pode ser usada em conjunto com o clustering ou redes virtuais.

## O que é a persistência de dados?
A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados que podem ser carregados no caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

O Cache Redis do Azure oferece a persistência do Redis em que os dados são armazenados em uma conta de armazenamento do Azure. Para a visualização pública, damos suporte ao [modelo RDB](http://redis.io/topics/persistence) e daremos suporte ao [AOF](http://redis.io/topics/persistence) em breve.

## Persistência de dados
Quando a persistência é configurada, o Cache Redis do Azure persiste um instantâneo do cache Redis em um formato binário do Redis em disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com o uso do instantâneo mais recente.

A persistência é configurada na folha **Novo Cache Redis** durante a criação do cache. Para criar um cache, entre no [portal de visualização do Azure](https://portal.azure.com) e clique em **Novo**->**Dados + Armazenamento**>**Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar a persistência, primeiro selecione um dos caches **Premium** na folha **Escolha o Tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

Depois de selecionar um tipo de preço premium, clique em **persistência do Redis**.

![Persistência do Redis][redis-cache-persistence]

Clique em **Habilitado** para habilitar o backup do RDB (banco de dados do Redis).

Selecione uma **Frequência de Backup** na lista suspensa. As opções incluem **60 minutos**, **6 horas**, **12 horas** e **24 horas**. Esse intervalo inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado.

Clique em **Conta de Armazenamento** para selecionar a conta de armazenamento a ser usada. A **Chave de Armazenamento** é preenchida automaticamente, mas se a chave for regenerada para a conta de armazenamento, você pode atualizá-la aqui. Uma conta de **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma taxa de transferência mais alta.

>[AZURE.NOTE]O AOF não está disponível durante o período de visualização da camada premium, mas a equipe do Cache está trabalhando para adicionar esse recurso. Para obter mais informações sobre as vantagens de AOF e RDB de cada, veja [Persistência do Redis](http://redis.io/topics/persistence).

![Persistência do Redis][redis-cache-persistence-selected]

Clique em **OK** para salvar a configuração de persistência.

Depois que o cache for criado, o primeiro backup será iniciado após decorrido o intervalo de frequência de backup.

## Perguntas frequentes sobre persistência

A lista a seguir contém respostas para perguntas frequentes sobre a persistência do Cache Redis do Azure.

## Posso habilitar a persistência em um cache criado anteriormente?

Durante o período de visualização, você pode habilitar e configurar a persistência apenas durante o processo de criação de um cache premium. Durante a visualização pública, não há suporte para a escala de Básico/Standard para Premium, mas ele estará disponível em breve.

## Posso alterar a frequência de backup depois de criar o cache?

Durante o período de visualização, você pode configurar a persistência apenas durante o processo de criação do cache. Para alterar a configuração de persistência, exclua o cache e crie um novo cache com a configuração de persistência desejada. Essa é uma limitação da visualização e em breve haverá suporte para isso.

## Por que se eu tiver uma frequência de backup de 60 minutos haverá mais de 60 minutos entre os backups?

O intervalo da frequência de backup não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e usar um processo de backup de 15 minutos para concluir com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

## O que acontece com os backups antigos quando é feito um backup novo?

Todos os backups, exceto pelo mais recente, serão excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos por tempo indeterminado.

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

-	[Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)
-	[Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

<!---HONumber=Oct15_HO2-->
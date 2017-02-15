---
title: Importar e exportar dados no Cache Redis do Azure | Microsoft Docs
description: "Saiba como importar e exportar dados de e para o armazenamento de blobs com as instâncias do Cache Redis do Azure premium"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 65385aa918222837468f88246d0527c22c677ba7
ms.openlocfilehash: a2cf3abae986d2ab2243a4b2a9123e4c164d2ef1


---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importar e Exportar dados no Cache Redis do Azure
A Importação/Exportação é uma operação de gerenciamento de dados do Cache Redis do Azure que permite importar dados para o Cache Redis do Azure ou exportar dados dele importando e exportando um instantâneo do RDB (Banco de Dados do Cache Redis) de um cache premium para um blob de páginas em uma Conta de Armazenamento do Azure. A Importação/Exportação permite migrar entre diferentes instâncias do Cache Redis do Azure ou popular o cache com os dados antes de usar.

Este artigo fornece um guia para importar e exportar dados com o Cache Redis do Azure, além de respostas para as perguntas mais frequentes.

> [!IMPORTANT]
> Importação/Exportação está em preview e está disponível somente para caches da [camada premium](cache-premium-tier-intro.md) .
>
>

## <a name="import"></a>Importar
A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache Redis do Azure carrega os arquivos RDB do armazenamento do Azure para a memória e insere as chaves no cache.

> [!NOTE]
> Antes de iniciar a operação de importação, verifique se os arquivos RDB (Banco de Dados Redis) estão carregados em blobs de páginas no armazenamento do Azure, na mesma região e assinatura que a instância do Cache Redis do Azure. Para saber mais, consulte [Introdução ao Armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se você tiver exportado seu arquivo RDB usando o recurso de [Exportação do Cache Redis do Azure](#export) , seu arquivo RDB já estará armazenado em um blob de páginas, pronto para importação.
>
>

1. Para importar um ou mais blobs de cache exportados, [navegue até seu cache](cache-configure.md#configure-redis-cache-settings) no Portal do Azure e clique em **Importar dados** na folha **Configurações** da sua instância de cache.

    ![Importar dados][cache-import-data]
2. Clique em **Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados a serem importados.

    ![Escolher uma conta de armazenamento][cache-import-choose-storage-account]
3. Clique no contêiner que contém os dados a serem importados.

    ![Escolher um contêiner][cache-import-choose-container]
4. Selecione um ou mais blobs para importar clicando na área à esquerda do nome do blob e em **Selecionar**.

    ![Escolha os blobs][cache-import-choose-blobs]
5. Clique em **Importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > O cache não pode ser acessado pelos clientes do cache durante o processo de importação e todos os dados existentes no cache serão excluídos.
   >
   >

    ![Importar][cache-import-blobs]

    Você pode monitorar o progresso da operação de importação seguindo as notificações do portal do Azure ou exibindo os eventos na auditoria. Para obter mais informações, consulte a seção "Configurações de solução de problemas e suporte" em [Como configurar o Cache Redis do Azure](cache-configure.md).

    ![Andamento da importação][cache-import-data-import-complete]

## <a name="export"></a>Exportação
A exportação permite exportar os dados armazenados no Cache Redis do Azure para arquivos RDB compatíveis com Redis. Você pode usar esse recurso para mover dados de uma instância do Cache Redis do Azure para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância de servidor do Cache Redis do Azure e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

1. Para exportar o conteúdo atual do cache para o armazenamento, [navegue até seu cache](cache-configure.md#configure-redis-cache-settings) no Portal do Azure e clique em **Importar dados** na folha **Configurações** da sua instância de cache.

    ![Escolher Contêiner de Armazenamento][cache-export-data-choose-storage-container]
2. Clique em **Escolher Contêiner de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar nas mesmas região e assinatura que seu cache.

   > [!IMPORTANT]
   > A Importação/Exportação funciona com blobs de páginas, que têm suporte em contas de armazenamento clássicas e de ARM, mas que não têm suporte em [Contas de armazenamento de blobs](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) atualmente.
   >
   >

    ![Conta de armazenamento][cache-export-data-choose-account]
3. Escolha o contêiner de blob desejado e clique em **Selecionar**. Para usar um novo contêiner, clique em **Adicionar Contêiner** para adicioná-lo primeiramente e selecione-o na lista.

    ![Escolher Contêiner de Armazenamento][cache-export-data-container]
4. Digite um **Prefixo do nome de blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome de blob é usado para prefixar os nomes dos arquivos gerados por esta operação de exportação.

    ![Exportação][cache-export-data]

    Você pode monitorar o progresso da operação de exportação seguindo as notificações do portal do Azure ou exibindo os eventos no log de auditoria. Para obter mais informações, consulte a seção "Configurações de solução de problemas e suporte" em [Como configurar o Cache Redis do Azure](cache-configure.md).

    ![][cache-export-data-export-complete]

    Os caches permanecem disponíveis para uso durante o processo de exportação.

## <a name="importexport-faq"></a>Perguntas frequentes de Importação/Exportação
Esta seção contém perguntas frequentes sobre o recurso de Importação/Exportação.

* [Quais tipos de preço podem usar a Importação/Exportação?](#what-pricing-tiers-can-use-importexport)
* [Posso importar dados de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
* [Meu cache estará disponível durante uma operação de Importação/Exportação?](#will-my-cache-be-available-during-an-importexport-operation)
* [Posso usar a Importação/Exportação com o cluster Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como a Importação/Exportação funciona com uma configuração de bancos de dados personalizada?](#how-does-importexport-work-with-a-custom-databases-setting)
* [De que forma a Importação/Exportação difere da persistência do Redis?](#how-is-importexport-different-from-redis-persistence)
* [Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
* [Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Quais tipos de preço podem usar a Importação/Exportação?
A Importação/Exportação está disponível apenas no tipo de preço premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor Redis?
Sim, além de importar os dados exportados de instâncias de Cache Redis do Azure, você pode importar arquivos RDB de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, como o Linux, Windows ou provedores de nuvem como o Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor Redis desejado para um blob de páginas em uma Conta de Armazenamento do Azure e importe-o para sua instância de Cache Redis do Azure premium. Por exemplo, convém exportar os dados do cache de produção e importá-los para um cache usado como parte de um ambiente de preparo para testes ou migração.

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Meu cache estará disponível durante uma operação de Importação/Exportação?
* **Exportação** - Os caches permanecem disponíveis e você pode continuar a usá-los durante uma operação de exportação.
* **Importação** - Os caches ficam indisponíveis quando uma operação de importação é iniciada e ficam disponíveis para uso após ela ser concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso usar a Importação/Exportação com o cluster Redis?
Sim, é possível importar/exportar entre um cache clusterizado e um não clusterizado. Já que o cluster Redis [só dá suporte a banco de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), quaisquer dados em bancos de dados diferentes de 0 não serão importados. Quando os dados do cache clusterizados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como a Importação/Exportação funciona com uma configuração de bancos de dados personalizada?
Alguns tipos de preço têm diferentes [limites de bancos de dados](cache-configure.md#databases), portanto, haverá algumas considerações a fazer ao importar se você tiver configurado um valor personalizado para a configuração `databases` durante a criação do cache.

* Ao importar para um tipo de preço com um limite de `databases` menor do que o tipo do qual você exportou:
  * Se você estiver usando o número padrão de `databases` , que é 16 para todos os tipos de preço, nenhum dado será perdido.
  * Se você estiver usando um número personalizado de `databases` que se encaixar dentro dos limites para o tipo para o qual você está importando, nenhum dado será perdido.
  * Se os dados exportados continham dados em um banco de dados que excede os limites do novo tipo, os dados de um desses bancos de dados mais altos não serão importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>De que forma a Importação/Exportação difere da persistência do Redis?
A persistência do Cache Redis do Azure possibilita a persistência dos dados armazenados no Redis para o Armazenamento do Azure. Quando a persistência é configurada, o Cache Redis do Azure persiste um instantâneo do cache Redis em um formato binário do Redis em disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, os dados do cache serão restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [Como configurar a persistência de dados para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).

A Importação/Exportação permite trazer dados ou exportá-los do Cache Redis do Azure. Ele não configurar o backup e restauração usando a persistência do Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?
Sim, para ver as instruções do PowerShell, confira [Para importar um cache Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) e [Para exportar um cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?
Se você permanecer na folha **Importar dados** ou **Exportar dados** por mais de 15 minutos antes de iniciar a operação, receberá um erro semelhante ao seguinte.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver esse problema, inicie a operação de importação ou exportação dentro do prazo de 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?
A Importação/Exportação funciona somente com arquivos RDB armazenados como blobs de páginas. Não há suporte para outros tipos de blob no momento, incluindo contas de armazenamento de blobs com as camadas ativas e passivas.

## <a name="next-steps"></a>Próximas etapas
Aprenda a usar mais recursos de cache premium.

* [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png



<!--HONumber=Feb17_HO2-->



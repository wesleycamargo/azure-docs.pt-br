<properties 
	pageTitle="Importar e Exportar dados no Cache Redis do Azure | Microsoft Azure" 
	description="Saiba como importar e exportar dados de e para o armazenamento de blobs com as instâncias do Cache Redis do Azure premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2016" 
	ms.author="sdanie"/>

# Importar e Exportar dados no Cache Redis do Azure

A Importação/Exportação é uma operação de gerenciamento de dados do Cache Redis do Azure que permite importar dados para o Cache Redis do Azure ou exportar dados dele importando e exportando um instantâneo do RDB (Banco de Dados do Cache Redis) de um cache premium para um blob de páginas em uma Conta de Armazenamento do Azure. Isso permite migrar entre diferentes instâncias do Cache Redis do Azure ou popular o cache com os dados antes de usar.

Este artigo fornece um guia para importar e exportar dados com o Cache Redis do Azure, além de respostas para as perguntas mais frequentes.

>[AZURE.IMPORTANT] Importação/Exportação está em preview e está disponível somente para caches da [camada premium](cache-premium-tier-intro.md).

## Importar

A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache Redis do Azure carrega os arquivos RDB do armazenamento do Azure para a memória e insere as chaves no cache.

>[AZURE.NOTE] Antes de iniciar a operação de importação, verifique se os arquivos RDB (Banco de Dados Redis) estão carregados em blobs de páginas no armazenamento do Azure, na mesma região e assinatura que a instância do Cache Redis do Azure. Para saber mais, consulte [Introdução ao Armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se você tiver exportado seu arquivo RDB usando o recurso de [Exportação do Cache Redis do Azure](#export), seu arquivo RDB já estará armazenado em um blob de páginas, pronto para importação.

1. Para importar um ou mais blobs de cache exportados, [navegue até seu cache](cache-configure.md#configure-redis-cache-settings) no Portal do Azure e clique em **Importar dados** na folha **Configurações** da sua instância de cache.

    ![Importar dados][cache-import-data]

2. Clique em **Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados a serem importados.

    ![Escolher uma conta de armazenamento][cache-import-choose-storage-account]

3. Clique no contêiner que contém os dados a serem importados.

    ![Escolher um contêiner][cache-import-choose-container]

4. Selecione um ou mais blobs para importar clicando na área à esquerda do nome do blob e em **Selecionar**.

    ![Escolha os blobs][cache-import-choose-blobs]

5. Clique em **Importar** para iniciar o processo de importação.

    >[AZURE.IMPORTANT] O cache não pode ser acessado pelos clientes do cache durante o processo de importação e todos os dados existentes no cache serão excluídos.

    ![Importar][cache-import-blobs]

    Você pode monitorar o progresso da operação de importação seguindo as notificações do Portal do Azure ou exibindo os eventos no [log de auditoria](cache-configure.md#support-amp-troubleshooting-settings).

    ![Andamento da importação][cache-import-data-import-complete]


## Exportação

A exportação permite exportar os dados armazenados no Cache Redis do Azure para arquivos RDB compatíveis com Redis. Você pode usar esse recurso para mover dados de uma instância do Cache Redis do Azure para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância de servidor do Cache Redis do Azure e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

1. Para exportar o conteúdo atual do cache para o armazenamento, [navegue até seu cache](cache-configure.md#configure-redis-cache-settings) no Portal do Azure e clique em **Importar dados** na folha **Configurações** da sua instância de cache.

    ![Escolha o contêiner de armazenamento][cache-export-data-choose-storage-container]

2. Clique em **Escolher Contêiner de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar nas mesmas região e assinatura que seu cache.

    >[AZURE.IMPORTANT] A Importação/Exportação funciona com blobs de páginas, que têm suporte em contas de armazenamento clássicas e de ARM, mas que não têm suporte em [Contas de armazenamento de blobs](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) atualmente.

    ![Conta de armazenamento][cache-export-data-choose-account]

3. Escolha o contêiner de blob desejado e clique em **Selecionar**. Para usar um novo contêiner, clique em **Adicionar Contêiner** para adicioná-lo primeiramente e selecione-o na lista.

    ![Escolha o contêiner de armazenamento][cache-export-data-container]

4. Digite um **Prefixo do nome de blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome de blob é usado para prefixar os nomes dos arquivos gerados por esta operação de exportação.

    ![Exportação][cache-export-data]

    Você pode monitorar o progresso da operação de exportação seguindo as notificações do Portal do Azure ou exibindo os eventos no [log de auditoria](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Observe que os caches permanecem disponíveis para uso durante o processo de exportação.


## Perguntas frequentes de Importação/Exportação

Esta seção contém perguntas frequentes sobre o recurso de Importação/Exportação.

-	[Quais tipos de preço podem usar a Importação/Exportação?](#what-pricing-tiers-can-use-importexport)
-	[Posso importar dados de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
-	[Meu cache estará disponível durante uma operação de Importação/Exportação?](#will-my-cache-be-available-during-an-importexport-operation)
-	[Posso usar a Importação/Exportação com o cluster Redis?](#can-i-use-importexport-with-redis-cluster)
-	[De que forma a Importação/Exportação difere da persistência do Redis?](#how-is-importexport-different-from-redis-persistence)
-	[Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-	[Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-	[Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### Quais tipos de preço podem usar a Importação/Exportação?

A Importação/Exportação está disponível apenas no tipo de preço premium.

### Posso importar dados de qualquer servidor Redis?

Sim, além de importar os dados exportados de instâncias de Cache Redis do Azure, você pode importar arquivos RDB de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, como o Linux, Windows ou provedores de nuvem como o Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor Redis desejado para um blob de páginas em uma Conta de Armazenamento do Azure e importe-o para sua instância de Cache Redis do Azure premium. Por exemplo, convém exportar os dados do cache de produção e importá-los para um cache usado como parte de um ambiente de preparo para testes ou migração.

### Meu cache estará disponível durante uma operação de Importação/Exportação?

-	**Exportação** - Os caches permanecem disponíveis e você pode continuar a usá-los durante uma operação de exportação.
-	**Importação** - Os caches ficam indisponíveis quando uma operação de importação é iniciada e ficam disponíveis para uso após ela ser concluída.


### Posso usar a Importação/Exportação com o cluster Redis?

Sim, é possível importar/exportar entre um cache clusterizado e um não clusterizado. Como o cluster Redis dá suporte apenas a banco de dados 0, não é possível importar dados que foram armazenados em outro banco de dados que não 0. Quando os dados do cache clusterizados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### De que forma a Importação/Exportação difere da persistência do Redis?

A persistência do Cache Redis do Azure possibilita a persistência dos dados armazenados no Redis para o Armazenamento do Azure. Quando a persistência é configurada, o Cache Redis do Azure persiste um instantâneo do cache Redis em um formato binário do Redis em disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, os dados do cache serão restaurados automaticamente usando o instantâneo mais recente. Para saber mais, consulte [Como configurar a persistência de dados para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).

A Importação/Exportação permite trazer dados ou exportá-los do Cache Redis do Azure. Ele não configurar o backup e restauração usando a persistência do Redis.


### Posso automatizar a Importação/Exportação usando o PowerShell, CLI ou outros clientes de gerenciamento?

Essa funcionalidade não está disponível durante a preview, porém estará disponível em breve.

### Recebi um erro de tempo limite durante minha operação de Importação/Exportação. O que isso significa?

Se você permanecer na folha **Importar dados** ou **Exportar dados** por mais de 15 minutos antes de iniciar a operação, receberá um erro semelhante ao seguinte.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver esse problema, inicie a operação de importação ou exportação dentro do prazo de 15 minutos.

### Recebi um erro ao exportar meus dados para o Armazenamento de Blobs do Azure. O que aconteceu?

A Importação/Exportação funciona somente com arquivos RDB armazenados como blobs de páginas. Não há suporte para outros tipos de blob no momento, incluindo contas de armazenamento de blobs com as camadas ativas e passivas.

    

  
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

<!---HONumber=AcomDC_0518_2016-->
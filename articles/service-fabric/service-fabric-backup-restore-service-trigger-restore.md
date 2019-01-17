---
title: Backup de restauração no Azure Service Fabric | Microsoft Docs
description: Use o recurso de restauração e backup periódico do Service Fabric para restaurar os dados do backup dos seus dados do aplicativo.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: ad89acb63057ff260332384372bcb7719cc8e4f3
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064824"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Backup de restauração no Azure Service Fabric

No Azure Service Fabric, Serviços Confiáveis com estado e Reliable Actors podem manter o estado autoritativo mutável após uma transação de solicitação e resposta ser concluída. Um serviço com estado pode ficar inativo por um longo tempo ou perder informações devido a um desastre. Se isso acontecer, o serviço precisará ser restaurado usando o backup mais recente aceitável para que ele possa continuar funcionando.

Por exemplo, você pode configurar um serviço para fazer backup de seus dados para se proteger contra os seguintes cenários:

- **Caso de recuperação de desastre**: Perda permanente de um cluster inteiro do Service Fabric.
- **Caso de perda de dados**: Perda permanente da maioria das réplicas de uma partição de serviço.
- **Caso de perda de dados**: exclusão acidental ou corrupção do serviço. Por exemplo, um administrador exclui o serviço por engano.
- **Caso de dados corrompidos**: bugs no serviço causam dados corrompidos. Por exemplo, dados corrompidos podem ocorrer quando uma atualização de código de serviço inicia grava dados com falha em uma coleção confiável. Nesse caso, convém reverter o código e os dados para um estado anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Para disparar uma restauração, o _Serviço de Análise de Falha (FAS)_ precisa estar habilitado para o cluster.
- O _Serviço de Restauração de Backup (BRS)_ criou o backup.
- A restauração só pode ser acionada em uma partição.

## <a name="triggered-restore"></a>Restauração disparada

Uma restauração pode ser disparada para qualquer um dos seguintes cenários:

- Restauração de dados para uma _recuperação de desastre_.
- Restauração de dados para _dados corrompidos/perda de dados_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Restauração de dados no caso de recuperação de desastres

Caso um cluster do Service Fabric inteiro seja perdido, você poderá recuperar os dados para as partições do serviço Confiável com Estado e Reliable Actors. O backup desejado pode ser selecionado na lista quando você usa [GetBackupAPI com detalhes de armazenamento de backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A enumeração de backup pode ser feita por aplicativo, serviço ou partição.

Para o exemplo a seguir, suponha que o cluster perdido seja o mesmo cluster mencionado em [Habilitar o backup periódico para o serviço confiável com estado e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Nesse caso, `SampleApp` é implantado com a política de backup habilitada e os backups são configurados no Armazenamento do Azure.

Execute um script do PowerShell para usar a API REST para retornar uma lista dos backups criados para todas as partições dentro do aplicativo `SampleApp`. A API requer que as informações de armazenamento de backup listem os backups disponíveis.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exemplo de saída da execução acima:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Para disparar a restauração, escolha um dos backups. Por exemplo, o backup atual para recuperação de desastre pode ser um dos seguintes:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Para a API de restauração, é necessário fornecer os detalhes _BackupId_ e _BackupLocation_.

Você também precisa escolher uma partição de destino no cluster alternativo conforme detalhado no [esquema de partição](service-fabric-concepts-partitioning.md#get-started-with-partitioning). O backup de cluster alternativo é restaurado para a partição especificada no esquema de partição do cluster perdido original.

Se a ID da partição no cluster alternativo for `1c42c47f-439e-4e09-98b9-88b8f60800c6`, você poderá mapeá-la para a ID de partição do cluster original `974bd92a-b395-4631-8a7f-53bd4ae9cf22` comparando a chave alta e baixa para _Particionamento por Intervalos (UniformInt64Partition)_.

Para _Particionamento Nomeado_, o valor do nome é comparado para identificar a partição de destino em cluster alternativo.

Solicite a restauração em relação à partição do cluster de backup usando a seguinte [API de Restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Você pode acompanhar o progresso de uma restauração com [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

### <a name="data-restore-for-data-corruptiondata-loss"></a>Restauração de dados para _dados corrompidos_/_perda de dados_

Para casos de _perda de dados_ ou _dados corrompidos_, as partições com backup do serviço Confiável com Estado e Reliable Actors podem ser restauradas para qualquer um dos backups escolhidos.

O exemplo a seguir é uma continuação de [Habilitar o backup periódico para Reliable Stateful Service e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste exemplo, uma política de backup está habilitada para a partição e o serviço está fazendo backups em uma frequência desejada no Armazenamento do Azure.

Selecione um backup da saída de [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Nesse cenário, o backup é gerado no mesmo cluster anterior.

Para disparar a restauração, escolha um backup na lista. Para a _perda de dados_/_dados corrompidos_, selecione o backup a seguir:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Para a API de restauração, forneça os detalhes de _BackupId_ e _BackupLocation_. O cluster tem backup habilitado, então o _Serviço de Restauração do Backup (BRS)_ do Service Fabric identifica o local de armazenamento correto da política de backup associada.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Você pode acompanhar o progresso da restauração usando [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

## <a name="track-restore-progress"></a>Acompanhar o progresso da restauração

Uma partição de um serviço Confiável com Estado ou Reliable Actor aceita apenas uma solicitação de backup sob demanda por vez. Uma partição só aceita outra solicitação depois que a solicitação de restauração atual é concluída. Várias solicitações de restauração podem ser disparadas em partições diferentes ao mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

O andamento da solicitação de restauração acontece na seguinte ordem:

1. **Aceito**: o estado de restauração _Aceito_ indica que a partição solicitada foi disparada com os parâmetros de solicitação corretos.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: o estado de restauração _InProgress_ indica que uma restauração está ocorrendo na partição com o backup mencionado na solicitação. A partição relata o estado _perda de dados_.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Êxito**, **Falha** ou **Tempo Limite**: um backup solicitado sob demanda pode ser concluído com qualquer um dos seguintes estados. Cada estado tem os seguintes detalhes de significância e resposta:
    - **Êxito**: uma restauração com _Êxito_ indica um estado de partição recuperado. A partição relata os estados _RestoreEpoch_ e _RestordLSN_ em conjunto com a hora em UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Falha**: o estado de restauração como _Falha_ indica a falha da solicitação de restauração. A causa da falha será relatada.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Tempo limite**: o estado de restauração como _Tempo limite_ indica que a solicitação tem um tempo limite. Crie uma nova solicitação de restauração com um valor maior para [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). O tempo limite padrão é 10 minutos. Certifique-se de que a partição está fora do estado de perda de dados antes de solicitar a restauração novamente.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Restauração automática

Você pode configurar o serviço Confiável com Estado e partições com Reliable Actors no cluster do Service Fabric para _restauração automática_. Na política de backup, defina `AutoRestore` como _true_. Habilitar a _restauração automática_ automaticamente restaurará dados do backup de partição mais recente, se a perda de dados for informada. Para obter mais informações, consulte:

- [Habilitação de Restauração Automática na Política de Backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Referência da API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Referência da API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Próximas etapas
- [Noções básicas sobre a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST de backup e restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

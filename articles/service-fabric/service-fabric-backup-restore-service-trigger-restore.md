---
title: Backup de Restauração no Azure Service Fabric | Microsoft Docs
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
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730435"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Backup de restauração no Azure Service Fabric


Serviços Confiáveis com estado e Reliable Actors no Microsoft Azure Service Fabric podem manter o estado autoritativo mutável além da solicitação e resposta ou de uma transação concluída. Se um serviço com estado ficar inativo por um longo tempo ou perder informações devido a um desastre, ele precisará ser restaurado para o backup mais recente do seu estado para continuar a fornecer o serviço depois de ser reativado.

Por exemplo, pode ser útil fazer o backup dos dados de um serviço para se proteger nos seguintes cenários:

- No caso de perda permanente de um cluster inteiro do Service Fabric. **(caso de recuperação de desastre - DR)**
- Perda permanente da maioria das réplicas de uma partição de serviço. **(Caso de perda de dados)**
- Erros administrativos nos quais o estado é acidentalmente excluído ou corrompido. Por exemplo, um administrador com privilégios suficientes exclui o serviço por engano.**(caso de perda de dados)**
- Bugs no serviço que venham a corromper os dados. Por exemplo, dados corrompidos podem ocorrer quando uma atualização de código de serviço inicia a gravação de dados com falha em uma coleção confiável. Nesse caso, convém reverter o código e os dados para um estado anterior. **(caso de dados corrompidos)**


## <a name="prerequisites"></a>Pré-requisitos
* Para disparar, restaure o _Serviço de Análise de Falha (FAS)_ deve ser habilitado para o cluster
* O backup a restaurar deve ter sido tomado pelo _serviço de restauração do Backup (BRS)_
* A restauração só pode ser acionada em uma partição.

## <a name="triggering-restore"></a>Gatilho de restauração

A restauração pode ser para qualquer um dos seguintes cenários 
* Restauração de dados no caso de _recuperação de desastres_ (DR)
* Restauração de dados no caso de _corrupção de dados / perda de dados_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Restauração de dados no caso de _recuperação de desastres_ (DR)
No caso de um cluster do Service Fabric inteiro ser perdido, os dados para as partições do serviço Confiável com Estado e Reliable Actors podem ser restaurados para um cluster alternativo. O backup desejado pode ser selecionado na enumeração de [GetBackupAPI com detalhes de armazenamento de backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A Enumeração de Backup pode ser enumerada por aplicativo, serviço ou partição.

Vamos supor que o cluster perdido foi mencionado no cluster [Habilitar o backup periódico para serviço Confiável com Estado e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), que havia `SampleApp` implantado, onde a partição estava com política de backup habilitada e os backups estavam acontecendo no Armazenamento do Microsoft Azure. 


Execute o seguinte script do PowerShell para invocar a API REST para enumerar os backups criados para todas as partições dentro do aplicativo `SampleApp` no cluster do Service Fabric. A enumeração da API exige informações de armazenamento, onde estão armazenados os backups de um aplicativo, para enumerar os backups disponíveis. 

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



Para disparar a restauração, é necessário escolher o backup desejado. Permitir que o backup desejado para a recuperação de desastres (DR) atual seja o backup seguinte

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

Para a API de restauração, é necessário fornecer os detalhes __BackupId__ e __BackupLocation__. A partição em cluster alternativo precisa escolher de acordo o [esquema de partição](service-fabric-concepts-partitioning.md#get-started-with-partitioning). É responsabilidade do usuário escolher a partição de destino para restaurar o backup do cluster alternativo, de acordo com o esquema de partição no cluster perdido original.

Suponha que a ID da partição em cluster alternativo seja `1c42c47f-439e-4e09-98b9-88b8f60800c6`, que mapeia para a ID de partição do cluster original `974bd92a-b395-4631-8a7f-53bd4ae9cf22` comparando a chave alta e baixa para _Particionamento por Intervalos (UniformInt64Partition)_.

Para _Particionamento Nomeado_, o valor do nome é comparado para identificar a partição de destino em cluster alternativo.

A restauração é solicitada em relação a partição do cluster de backup por [Restaurar API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
O progresso da restauração pode ser [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Restauração de dados no caso de _corrupção de dados / perda de dados_

Para o caso de _perda de dados_ ou _dados corrompidos_ os dados, para as partições do serviço Confiável com Estado e Reliable Actors podem ser restaurados para qualquer um dos backups escolhidos. O caso a seguir é a continuação do exemplo conforme mencionado em [Habilitar o backup periódico para o serviço Confiável com Estado e Reliable Actors ](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), onde a partição tem uma política de backup habilitada e faz backup em uma frequência desejada no Armazenamento do Microsoft Azure. 

O backup desejado está selecionado na saída de [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Nesse cenário, o backup é gerado a partir do mesmo cluster no passado.
Para disparar a restauração, é necessário escolher o backup na lista. Permitir o nosso backup desejado para a _perda de dados_ / _dados corrompidos_ reais sejam o backup a seguir

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

Para a API de restauração, é necessário fornecer os detalhes __BackupId__ e __BackupLocation__. Uma vez que o cluster tem backup habilitado o _Serviço de Restauração do Backup (BRS)_ do Service Fabric, identifica o local de armazenamento correto da política de backup associada.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

O progresso da restauração pode ser [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Acompanhamento do progresso de restauração

Uma partição de um serviço Confiável com Estado ou Reliable Actor aceita apenas uma solicitação de backup sob demanda por vez. Outra solicitação pode ser aceita apenas quando a solicitação de backup sob demanda atual for concluída. Várias solicitações de restauração podem ser disparadas em partições diferentes ao mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

andamento da solicitação de restauração na seguinte ordem

1. __Aceito__ - O estado de restauração como _Aceito_ indica que o solicitado foi disparado com parâmetros de solicitação correta.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ -o estado de restauração como _InProgress_ indica que a partição passará por uma restauração com o backup mencionado na solicitação. A partição relatará o estado _perda de dados_.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Êxito__/ __Falha__/ __Tempo limite__ - um backup solicitado sob demanda pode estar completamente em qualquer um dos seguintes estados. Cada estado tem os seguintes detalhes de significância e resposta.
       
    * __Êxito__ -o estado de restauração como _Sucesso_ indica o estado da partição que é recuperado. A resposta fornecerá RestoreEpoch e RestordLSN para a partição juntamente com a hora em UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Falha__ - O estado de restauração como _Falha_ indica a falha da solicitação de restauração. A causa da falha será declarada na solicitação.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Tempo limite__ - O estado de restauração como _Tempo Limite_ indica que a solicitação tem um tempo limite. Nova solicitação de restauração com maior [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) é recomendada; padrões de tempo limite é de 10 minutos. É recomendável certificar-se de que a partição está fora do estado de perda de dados, antes de solicitar a restauração novamente.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Restauração automática

As partições para o serviço Confiável com Estado e Reliable Actors no cluster do Service Fabric podem ser configuradas para _auto restauração_. Ao criar a política de backup, a política pode ser `AutoRestore` definida como _verdadeira_.  Habilitar _restauração automática_ para uma partição, restaura os dados do backup mais recente, se a perda de dados é informada.
 
 [Habilitação de Restauração Automática na Política de Backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Referência da API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[referência da API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Próximas etapas
- [Noções básicas sobre a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST de backup e restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

---
title: Backup sob demanda no Azure Service Fabric | Microsoft Docs
description: Use o recurso de restauração e backup do Service Fabric para o backup dos dados do seu aplicativo conforme a necessidade.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730430"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup sob demanda no Azure Service Fabric

Os dados dos serviços Confiáveis com Estado e Reliable Actors podem ser copiados para lidar com cenários de desastre ou perda de dados.

O Service Fabric é equipado com recursos para [backup periódico dos dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e backup dos dados na base de necessidade. O backup sob demanda é útil, pois ele protege contra _perda de dados_/_corrupção de dados_ causado devido a alterações planejadas em seu ambiente ou serviço subjacente.

Os recursos de backup sob demanda são úteis na captura de estado dos serviços, antes de qualquer operação disparada manualmente relacionada ao serviço ou ao ambiente do serviço. Como alterar os binários de serviço, ou seja, atualizar ou fazer downgrade do serviço; como ele terá os dados protegidos contra corrupção de dados por bugs no código do aplicativo.

## <a name="triggering-on-demand-backup"></a>Disparar um backup sob demanda

O backup sob demanda exige detalhes de armazenamento para carregar arquivos de backup. O backup sob demanda ocorrerá no armazenamento especificado na política de backup periódica ou no armazenamento especificado na solicitação de backup sob demanda.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Backup sob demanda para o armazenamento especificado pela política de backup periódica

A partição de um serviço Confiável com estado ou Reliable Actor pode ser solicitada para um extra em um backup baseado em necessidade para armazenamento especificado na política de backup periódica. 

O caso a seguir é a continuação do exemplo conforme mencionado em [Habilitar o backup periódico para o serviço Confiável com Estado e Reliable Actors ](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), onde a partição tem uma política de backup habilitada e faz backup em uma frequência desejada no Armazenamento do Microsoft Azure.

O backup sob demanda para ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22` podem ser acionado por [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API. 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

O [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) podem ser rastreado pela API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup sob demanda para o armazenamento especificado

O backup sob demanda pode ser solicitado para uma partição de um serviço Confiável com Estado ou Reliable Actor junto com as informações de armazenamento. As informações de armazenamento devem ser fornecidas como parte da solicitação de backup sob demanda.

O backup sob demanda para ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22` podem ser acionado por [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API com as informações de armazenamento do Azure conforme exibido abaixo.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

O [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) podem ser rastreado pela API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).


## <a name="tracking-on-demand-backup-progress"></a>Rastreamento do progresso de backup sob demanda

Uma partição de um serviço Confiável com Estado ou Reliable Actor aceita apenas uma solicitação de backup sob demanda por vez. Outra solicitação pode ser aceita apenas quando a solicitação de backup sob demanda atual for concluída. 

Várias solicitações de backup sob demanda podem ser disparadas em partições diferentes ao mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Andamento da solicitação de backup sob demanda pode ser um dos seguintes estados

* **Aceito** - backup iniciado na partição e está em andamento.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Êxito/Falha/Tempo limite** - um backup solicitado sob demanda pode estar completamente em qualquer um dos seguintes estados. Cada estado tem os seguintes detalhes de significância e resposta.

    * **Ê** - estado do backup como _Êxito_ indica que foi realizado com sucesso o backup do estado de partição. A resposta fornecerá __BackupEpoch__ e __BackupLSN__ para a partição juntamente com a hora em UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Falha** - estado de backup como _Falha_ indica que a falha ocorreu durante o backup do estado de partição. A causa da falha será declarada na resposta.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Tempo limite** - backup do estado como _Timeout_ indica que o backup do estado de partição não pôde ser criado em um determinado período de tempo; o valor de tempo limite padrão é 10 minutos. Iniciar uma nova solicitação de backup com maior [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) na solicitação de backup sob demanda, é recomendável nesse cenário.

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>Próximas etapas
- [Noções básicas sobre a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST de backup e restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)


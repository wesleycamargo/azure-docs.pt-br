---
title: Início rápido – Backup e restauração periódicos do Azure Service Fabric (Versão prévia) | Microsoft Docs
description: Use o recurso de restauração e backup periódico do Service Fabric para habilitar o backup periódico de dados dos dados do seu aplicativo.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 50ee0d91b27805e4db785e5df211660900333e7f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990292"
---
# <a name="quickstart-periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Início rápido: backup e restauração periódicos do Azure Service Fabric (Versão prévia)
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clusters independentes](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

O Service Fabric é uma plataforma de sistemas distribuídos que facilita o desenvolvimento e gerenciamento de microsserviços distribuídos confiáveis e baseados em aplicativos na nuvem. Ele permite a execução de microsserviços com e sem estado. Serviços com estado podem manter o estado autoritativo mutável além da solicitação e resposta ou de uma transação concluída. Se um serviço com estado ficar inativo por um longo tempo ou perder informações devido a um desastre, ele precisará ser restaurado para algum backup recente do seu estado para continuar a fornecer o serviço depois de ser reativado.

O Service Fabric replica o estado em vários nós para garantir que o serviço esteja altamente disponível. Mesmo se um nó do cluster falhar, os serviços continuarão disponíveis. Em alguns casos, no entanto, ainda é desejável que os dados de serviço sejam confiáveis contra falhas mais amplas.
 
Por exemplo, pode ser útil fazer o backup dos dados de um serviço para se proteger nos seguintes cenários:
- No caso de perda permanente de um cluster inteiro do Service Fabric.
- Perda permanente da maioria das réplicas de uma partição de serviço
- Erros administrativos nos quais o estado é acidentalmente excluído ou corrompido. Por exemplo, um administrador com privilégios suficientes exclui o serviço por engano.
- Bugs no serviço que venham a corromper os dados. Por exemplo, isso pode ocorrer quando uma atualização de código de serviço inicia a gravação de dados com falha em uma coleção confiável. Nesse caso, convém reverter o código e os dados para um estado anterior.
- Processamento de dados offline. Talvez seja conveniente processar no modo offline os dados de business intelligence que ocorrem separadamente do serviço que gera os dados.

O Service Fabric fornece uma API interna para realizar [backup e restauração](service-fabric-reliable-services-backup-restore.md) pontuais. Os desenvolvedores de aplicativos podem usar essas APIs para fazer backup do estado do serviço periodicamente. Além disso, se os administradores de serviço quiserem disparar um backup de fora do serviço em um momento específico, como antes de atualizar o aplicativo, os desenvolvedores precisarão expor o backup (e a restauração) como uma API do serviço. Manter os backups é um custo adicional a este. Por exemplo, pode ser útil realizar 5 backups incrementais a cada meia hora, seguidos por um backup completo. Após o backup completo, você poderá excluir os backups incrementais anteriores. Essa abordagem requer código adicional, o que significa custos adicionais durante o desenvolvimento do aplicativo.

O backup periódico dos dados do aplicativo é uma necessidade básica para gerenciar um aplicativo distribuído e protegê-lo contra perda de dados ou perda prolongada da disponibilidade do serviço. O Service Fabric fornece um serviço opcional de backup e restauração, que permite que você configure o backup periódico dos Reliable Services com estado (inclusive Serviços de Ator) sem a necessidade de escrever código adicional. Ele também facilita a restauração dos backup obtidos anteriormente. 

> [!NOTE]
> O recurso de backup e restauração periódicos está atualmente em **Versão prévia** e não é compatível com cargas de trabalho de produção. 
>

O Service Fabric fornece um conjunto de APIs para obter a funcionalidade a seguir relacionada ao recurso de backup e restauração periódicos:

- Agende o backup periódico dos serviços confiáveis com estado e do Reliable Actors compatível com upload de backup para locais de armazenamento (externos). Locais de armazenamento compatíveis
    - Armazenamento do Azure
    - Compartilhamento de arquivos (local)
- Enumerar os backups
- Disparar um backup ad hoc de uma partição
- Restaurar uma partição usando o backup anterior
- Suspender backups temporariamente
- Gerenciamento de backups de retenção (em breve)

## <a name="prerequisites"></a>Pré-requisitos
* Cluster do Service Fabric com Fabric versão 6.2 e superior. O cluster deve ser configurado no Windows Server. Consulte o [artigo](service-fabric-cluster-creation-via-arm.md) para ver as etapas para criar um cluster do Service Fabric usando o modelo de recurso do Azure.
* Certificado X.509 para criptografia de segredos necessária para se conectar ao armazenamento para armazenar backups. Consulte o [artigo](service-fabric-cluster-creation-via-arm.md) para saber como obter ou criar um certificado X.509.
* Aplicativo Service Fabric Reliable Stateful criado usando o SDK do Service Fabric versão 3.0 ou superior. Para aplicativos voltados para o .Net Core 2.0, o aplicativo deve ser criado usando o SDK do Service Fabric versão 3.1 ou superior.
* Crie uma conta de armazenamento do Azure para armazenar backups de aplicativo.

## <a name="enabling-backup-and-restore-service"></a>Habilitando o serviço de backup e restauração
Primeiro, você precisa habilitar o _serviço de backup e restauração_ no seu cluster. Obtenha o modelo para o cluster que você deseja implantar. Use os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou crie um modelo do Resource Manager. Habilite o _serviço de backup e restauração_ com as seguintes etapas:

1. Verifique se o `apiversion` está definido como **`2018-02-01`** para o recurso `Microsoft.ServiceFabric/clusters`, caso contrário, atualize-o conforme mostrado no trecho a seguir:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o _serviço de backup e de restauração_ adicionando a seção `addonFeatures` a seguir abaixo da seção `properties`, conforme mostrado neste trecho: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configure um certificado X.509 para criptografia de credenciais. Isso é importante para garantir que as credenciais fornecidas se conectem ao armazenamento criptografadas antes de persistirem. Configure um certificado de criptografia adicionando a seção `BackupRestoreService` a seguir abaixo da seção `fabricSettings`, conforme mostrado neste trecho: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Depois de atualizar o modelo de cluster com as alterações anteriores, aplique-as e permita que a implantação/atualização seja concluída. Depois de concluído, o _serviço de backup e restauração_ começa a ser executado no seu cluster. O URI deste serviço é `fabric:/System/BackupRestoreService` e o serviço pode ser localizado na seção de serviço do sistema no Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitar o backup periódico para o serviço confiável com estado e Reliable Actors
Vamos percorrer as etapas para habilitar o backup periódico do serviço confiável com estado e do Reliable Actors. Essas etapas pressupõem que
- O cluster está configurado usando a segurança X.509 com o _serviço de backup e restauração_.
- Um serviço confiável com estado é implantado no cluster. Para a finalidade deste guia de início rápido, o URI do aplicativo é `fabric:/SampleApp` e o URI para o serviço confiável com estado que pertence a este aplicativo é `fabric:/SampleApp/MyStatefulService`. Esse serviço é implantado com uma única partição, e a ID da partição é `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- O certificado do cliente com função de administrador é instalado em _Meu_ nome do armazenamento (_Pessoal_) do local de repositório de certificados do _CurrentUser_ no computador, de onde os scripts a seguir serão invocados. Este exemplo usa `1b7ebe2174649c45474a4819dafae956712c31d3` como a impressão digital desse certificado. Para obter mais informações sobre certificados do cliente, consulte [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Criar política de backup

A primeira etapa é criar a política de backup que descreve a agenda de backup, o armazenamento de destino para os dados de backup, o nome da política e o máximo de backups incrementais permitidos antes de disparar o backup completo. 

Para armazenamento de backup, use a conta de Armazenamento do Azure criada acima. O contêiner `backup-container` está configurado para armazenar backups. Um contêiner com esse nome é criado, caso ainda não exista, durante o carregamento de backup. Preencha `ConnectionString` com uma cadeia de conexão válida para a conta de Armazenamento do Azure, substituindo `account-name` por seu nome de conta de armazenamento, e `account-key` por sua chave de conta de armazenamento.

Execute o seguinte script do PowerShell para invocar a API REST necessária para criar nova política. Substitua `account-name` pelo nome da sua conta de armazenamento, e `account-key` pela sua chave de conta de armazenamento.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Habilitar backup periódico
Depois de definir a política de backup para atender aos requisitos de proteção de dados do aplicativo, a política de backup deve ser associada a ele. Dependendo do requisito, a política de backup pode ser associada um aplicativo, serviço ou partição.

Execute o seguinte script do PowerShell para invocar a API REST necessária para associar a política de backup com o nome `BackupPolicy1` criada no etapa anterior com o aplicativo `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Verificar se os backups periódicos estão funcionando

Depois de habilitar o backup para o nível do aplicativo, todas as partições que pertencem aos serviços confiável com estado e Reliable Actors nesse aplicativo passarão por backup periódicos de acordo com a política de backup associada. 

![Evento de integridade do backup de partição][0]

### <a name="list-backups"></a>Listar Backups

Backups associados a todas as partições que pertencem aos serviços confiáveis com estado e Reliable Actors do aplicativo podem ser enumerados usando a API _GetBackups_. Os backups podem ser enumerados por aplicativo, serviço ou partição.

Execute o seguinte script do PowerShell para invocar a API HTTP para enumerar os backups criados para todas as partições dentro do aplicativo `SampleApp`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="preview-limitation-caveats"></a>Limitação da versão prévia
- Nenhum cmdlet do PowerShell interno ao Service Fabric.
- Não há suporte para a CLI do Service Fabric.
- Não há suporte para a limpeza de backup automatizada. O [script de retenção de backup](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) pode ser referenciado para configurar a automação externa baseada em script para limpar os backups.
- Não há suporte para clusters do Service Fabric no Linux.

## <a name="next-steps"></a>Próximas etapas
- [Noções básicas sobre a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST de backup e restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png


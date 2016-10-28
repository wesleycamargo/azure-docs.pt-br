
<properties
   pageTitle="Personalizar as configurações de cluster do Service Fabric e a política de Atualização da Malha | Microsoft Azure"
   description="Este artigo descreve as configurações de malha e as políticas de atualização de malha que você pode personalizar."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="chackdan"/>

# Personalizar as configurações de cluster de Service Fabric e a política de Atualização da Malha

Este documento explica como personalizar as várias configurações de malha e a política de atualização de malha para o cluster do Service Fabric. Você pode personalizá-los no portal ou usando um modelo do Resource Manager.

## Configurações de malha que você pode personalizar


Aqui estão as configurações de Malha que você pode personalizar:

### Nome da Seção: Segurança

|**Parâmetro**|**Valores permitidos**|**Diretrizes ou Descrição resumida**|
|-----------------------|--------------------------|--------------------------|
|ClusterProtectionLevel|None ou EncryptAndSign| None (padrão) para clusters não seguros, EncryptAndSign para clusters seguros. |

### Nome da Seção: Hospedagem

|**Parâmetro**|**Valores permitidos**|**Diretrizes ou Descrição resumida**|
|-----------------------|--------------------------|--------------------------|
|ServiceTypeRegistrationTimeout|Tempo em segundos, o padrão é de 300| Tempo máximo permitido para o ServiceType a ser registrado com a malha|
|ServiceTypeDisableFailureThreshold|Número inteiro, o padrão é 1| Esse é o limite para a contagem de falhas após o qual o FailoverManager (FM) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento.|
|ActivationRetryBackoffInterval|Tempo em segundos, o padrão é de 5|Intervalo de retirada em cada falha de ativação; em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto da falha de ativação contínua e do intervalo de retirada de ativação.|
|ActivationMaxRetryInterval|Tempo em segundos, o padrão é de 300| Em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a ActivationMaxFailureCount. ActivationMaxRetryInterval especifica o intervalo de tempo de espera antes de tentar novamente após cada falha de ativação |
|ActivationMaxFailureCount|Número inteiro, o padrão é 10| Número de vezes que o sistema tenta realizar novamente a ativação que falhou antes de desistir |

### Nome da Seção: FailoverManager

|**Parâmetro**|**Valores permitidos**|**Diretrizes ou Descrição resumida**|
|-----------------------|--------------------------|--------------------------|
|PeriodicLoadPersistInterval|Tempo em segundos, o padrão é de 10| Isso determina a frequência com que o FM verifica se há novos relatórios de carregamento|

### Nome da Seção: Federação

|**Parâmetro**|**Valores permitidos**|**Diretrizes ou Descrição resumida**|
|-----------------------|--------------------------|--------------------------|
|LeaseDuration|Tempo em segundos, o padrão é de 30|Duração de uma concessão entre um nó e seus vizinhos.|
|LeaseDurationAcrossFaultDomain|Tempo em segundos, o padrão é de 30|Duração de uma concessão entre um nó e seus vizinhos entre domínios de falha.|

### Nome da Seção: ClusterManager

|**Parâmetro**|**Valores permitidos**|**Diretrizes ou Descrição resumida**|
|-----------------------|--------------------------|--------------------------|
|UpgradeStatusPollInterval|Tempo em segundos, o padrão é de 60|A frequência de sondagem do status de atualização do aplicativo. Esse valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress|
|UpgradeHealthCheckInterval|Tempo em segundos, o padrão é de 60|A frequência de verificação do status de integridade durante atualizações de um aplicativo monitorado|
|FabricUpgradeStatusPollInterval|Tempo em segundos, o padrão é de 60|A frequência de sondagem do status de atualização de malha. Esse valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|FabricUpgradeHealthCheckInterval|Tempo em segundos, o padrão é de 60|A frequência de verificação do status de integridade durante uma atualização de Malha monitorada|



## Próximas etapas

Leia estes artigos para obter mais informações sobre gerenciamento de cluster:

[Adicionar, transferir e remover certificados do cluster do Azure ](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->

<properties
   pageTitle="Atualização de aplicativos: parâmetros de atualização | Microsoft Azure"
   description="Descreve os parâmetros relacionados à atualização de um aplicativo do Service Fabric, incluindo verificações de integridade a serem executadas e políticas para desfazer automaticamente a atualização."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2016"
   ms.author="subramar"/>



# Parâmetros de atualização de aplicativo

Este artigo descreve os vários parâmetros que se aplicam durante a atualização de um aplicativo do Service Fabric do Azure. Os parâmetros incluem o nome e a versão do aplicativo. Eles são botões que controlam o tempo limite e as verificações de integridade que são aplicadas durante a atualização, e especificam as políticas que devem ser aplicadas quando uma atualização falha.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName | Nome do aplicativo que está sendo atualizado. Exemplos: malha:/VisualObjects, malha:/ClusterMonitor |
| TargetApplicationTypeVersion | Versão do tipo de aplicativo visado pela atualização. |
| FailureAction | A ação a ser tomada pelo Service Fabric se a atualização falhar. O aplicativo pode ser revertido para a última versão antes da atualização (reversão) ou a atualização de aplicativo pode parar no domínio de atualização atual e ter o modo de atualização alterado para manual. Os valores permitidos são Manual e Reversão. |
| HealthCheckWaitDurationSec | O tempo de espera (em segundos) após a conclusão da atualização no domínio de atualização antes que o Service Fabric avalie a integridade do aplicativo. Essa duração também pode ser considerada como o tempo pelo qual um aplicativo deve estar em execução antes que possa ser considerado íntegro. Se a verificação de integridade for aprovada, o processo de atualização passa para o próximo domínio de atualização. Se a verificação de integridade falhar, a Malha do Serviço aguarda um intervalo (o UpgradeHealthCheckInterval) antes de tentar novamente a verificação de integridade até que o HealthCheckRetryTimeout seja atingido. O valor padrão recomendado é 0 segundos. |
| HealthCheckRetryTimeoutSec | A duração (em segundos) em que a Malha de Serviço continua a executar a avaliação de integridade antes de declarar a atualização com falha. O padrão é 600 segundos. Esta duração é iniciada depois que a HealthCheckWaitDuration for atingida. Nesse HealthCheckRetryTimeout, a Malha de Serviço pode executar várias verificações de integridade no aplicativo. O valor padrão é 10 minutos e deve ser adequadamente personalizado para o seu aplicativo. |
| HealthCheckStableDurationSec | A duração (em segundos) de espera para verificar se o aplicativo é estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir que alterações na integridade deixem de ser detectadas logo após a verificação de integridade. O valor padrão é 0 segundos e deve ser adequadamente personalizado para o seu aplicativo. |
| UpgradeDomainTimeoutSec | Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se esse tempo limite for atingido, a atualização é interrompida e segue a ação especificada pela UpgradeFailureAction. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeTimeout | Um tempo limite (em segundos) que se aplica a toda a atualização. Se esse tempo limite for atingido, a atualização é interrompida e a UpgradeFailureAction será disparada. O valor padrão nunca é (infinito) e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeHealthCheckInterval | A frequência com que o status de integridade é verificado. Este parâmetro é especificado na seção ClusterManager do _manifesto_ do _cluster_. (Isso não é especificado como parte do cmdlet de atualização.) O valor padrão é de 60 segundos. |






<br>
## Avaliação de integridade do serviço durante a atualização do aplicativo

<br> Os critérios de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados ao iniciar uma atualização, o Service Fabric usará as políticas de integridade do aplicativo especificadas no ApplicationManifest.xml da instância do aplicativo que está sendo atualizada.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError | O valor padrão é Falso. Trata os eventos de integridade de aviso do aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, o Service Fabric não avalia os eventos de integridade de aviso como falhas (erros); portanto, a atualização pode continuar mesmo se houver eventos de aviso. |
| MaxPercentUnhealthyDeployedApplications | O valor padrão e recomendado é 0. Especifica o número máximo de aplicativos implantados (confira a [seção Integridade](service-fabric-health-introduction.md)) que podem comprometer a integridade antes que o aplicativo seja considerado não íntegro e sofra falha na atualização. Essa é a integridade do pacote de aplicativo no nó; portanto, é útil para detectar problemas imediatos durante a atualização, quando o pacote de aplicativo implantado no nó não está íntegro (falha, etc). Em um caso típico, as réplicas do aplicativo terão a carga balanceada para outro nó, o que faz com que o aplicativo pareça íntegro e permite que a atualização continue. Ao especificar uma integridade MaxPercentUnhealthyDeployedApplications estrita, o Service Fabric pode detectar um problema com o pacote de aplicativos rapidamente e ajudar a produzir em uma atualização rápida em caso de falha. |
| MaxPercentUnhealthyServices | O valor padrão e recomendado é 0. Especifica o número máximo de serviços na instância do aplicativo que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e tenha atualização com falha. |
| MaxPercentUnhealthyPartitionsPerService | O valor padrão e recomendado é 0. Especifica o número máximo de partições em um serviço que podem não estar íntegras antes que o serviço seja considerado não íntegro. |
| MaxPercentUnhealthyReplicasPerPartition | O valor padrão e recomendado é 0. Especifica o número máximo de réplicas em partição que podem não estar íntegras antes que a partição seja considerada não íntegra. |
| UpgradeReplicaSetCheckTimeout | **Serviço sem monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que haja instâncias adicionais do serviço disponíveis. Se a contagem de instâncias de destino for mais de um, o Service Fabric espera que mais de uma instância fique disponível, até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, o Service Fabric continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for um, a Malha do Serviço não espera e prossegue imediatamente com a atualização. **Serviço com monitoração de estado**: em um único domínio de atualização, o Service Fabric tenta garantir que o conjunto de réplicas tenha um quorum. O Service Fabric aguarda pela disponibilidade de um quorum até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, o Service Fabric continuará com a atualização, independentemente de quorum. Essa configuração é definida como nunca (infinito) durante a reversão progressiva (aplicada quando a atualização continuar conforme o esperado) e 900 segundos durante a reversão (aplicada quando a atualização tiver erros e for revertida). |
| ForceRestart | Se você atualizar uma configuração ou um pacote de dados sem atualizar o código de serviço, o Service Fabric não reiniciará o serviço enquanto a propriedade ForceRestart não for definida como verdadeira, como parte da chamada de API. Quando a atualização é concluída, a Malha do Serviço notifica o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode se reiniciar. |



<br> <br> Os critérios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância do aplicativo. Isso é para garantir que um aplicativo que contém tipos diferentes de serviço possa ter políticas de avaliação diferentes para cada um dos tipos de serviço. Por exemplo, um tipo de serviço de gateway sem monitoração de estado pode ter um MaxPercentUnhealthyPartitionsPerService que é diferente de um tipo de serviço de mecanismo com monitoração de estado para uma determinada instância de aplicativo.

## Próximas etapas

[Atualizar seu Aplicativo Usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você a fazer uma atualização de aplicativo usando o Visual Studio.

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solucionando Problemas de Atualizações de Aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0420_2016-->
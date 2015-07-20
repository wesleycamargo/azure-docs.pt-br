
<properties
   pageTitle="Atualização de aplicativa Malha do Serviço: parâmetros de atualização"
   description="Este artigo descreve os vários parâmetros relacionados à atualização de um aplicativa Malha do Serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/15/2015"
   ms.author="subramar"/>



# Parâmetros de atualização de aplicativo

Este artigo descreve os vários parâmetros que se aplicam durante a atualização de um aplicativo Malha do Serviço. Os parâmetros incluem o nome e a versão do aplicativo; são botões que controlam o tempo limite e as verificações de integridade aplicadas durante a atualização e especificam as políticas que devem ser aplicadas quando uma atualização falhar.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName | Nome do aplicativo que está sendo atualizado. Exemplos: malha:/VisualObjects, malha:/ClusterMonitor |
| TargetApplicationTypeVersion | Versão do tipo de aplicativo visado pela atualização. |
| FailureAction | A propriedade FailureAction descreve a ação a ser tomada pela Malha do Serviço se a atualização falhar. O aplicativo pode ser revertido para a última versão antes da atualização (reversão) ou parar a atualização de aplicativo no domínio de atualização atual e alterar o modo de atualização para manual. Os valores permitidos são Manual e Reversão. |
| HealthCheckWaitDurationSec | O tempo de espera (em segundos) após a conclusão da atualização no domínio de atualização antes que a Malha do Serviço avalie a integridade do aplicativo. Essa duração também pode ser considerada como o tempo pelo qual um aplicativo deve estar em execução antes que possa ser considerado íntegro. Se a verificação de integridade for aprovada, o processo de atualização passa para o próximo domínio de atualização. Se a verificação de integridade falhar, a Malha do Serviço aguarda um intervalo (o UpgradeHealthCheckInterval) antes de tentar novamente a verificação de integridade até que o HealthCheckRetryTimeout seja atingido. O valor padrão recomendado é 0 segundos. |
| HealthCheckRetryTimeoutSec | A duração (em segundos) em que a Malha de Serviço continua a executar a avaliação de integridade antes de declarar a atualização com falha. O padrão é 600 segundos. Esta duração é iniciada depois que a HealthCheckWaitDuration for atingida. Nesse HealthCheckRetryTimeout, a Malha de Serviço pode executar várias verificações de integridade no aplicativo. O valor padrão é 10 minutos e recomendamos que seja adequadamente personalizado para seu aplicativo. |
| HealthCheckStableDurationSec | A duração (em segundos) de espera para verificar se o aplicativo é estável antes de passar para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir que alterações na integridade deixem de ser detectadas logo após a verificação de integridade. O valor padrão é 0 segundos e recomendamos que seja adequadamente personalizado para seu aplicativo. |
| UpgradeDomainTimeoutSec | Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se esse tempo limite for atingido, a atualização para e segue a ação especificada pela UpgradeFailureAction. O valor padrão nunca é (infinito) e recomendamos que seja personalizado adequadamente para seu aplicativo. |
| UpgradeTimeout | Um tempo limite (em segundos) que se aplica a toda a atualização. Se esse tempo limite for atingido, a atualização para e o UpgradeFailureAction é disparado. O valor padrão nunca é (infinito) e recomendamos que seja personalizado adequadamente para seu aplicativo. |
| UpgradeHealthCheckInterval | Especificado na seção ClusterManager do _manifesto_ do _cluster_ (isso não está especificado como parte do cmdlet de atualização), essa configuração especifica a frequência com que o status de integridade é verificado (o padrão é 60 segundos). |






<br>
## Avaliação de integridade do serviço durante a atualização do aplicativo

<br> Os critérios de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados ao iniciar uma atualização, a Malha de Serviço usa as políticas de integridade do aplicativo especificadas no ApplicationManifest.xml da instância do aplicativo que está sendo atualizada.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError | O valor padrão é Falso. Trata os eventos de integridade de aviso do aplicativo como erro ao avaliar a integridade do aplicativo durante a atualização. Por padrão, a Malha de Serviço não avalia os eventos de integridade de aviso como uma falha (erro); portanto, a atualização pode continuar mesmo se houver eventos de aviso. |
| MaxPercentUnhealthyDeployedApplications | O valor padrão e recomendado é 0. Especifica o número máximo de aplicativos implantados (confira a [seção Integridade](service-fabric-health-introduction.md)) que podem comprometer a integridade antes que o aplicativo seja considerado não íntegro e sofra falha na atualização. Essa é a integridade do aplicativo em pacote que está no nó; portanto, é útil detectar o problema imediatamente durante a atualização, e onde o pacote de aplicativo implantado no nó não está íntegro (falhando, etc). Em um caso típico, as réplicas do aplicativo terão a carga balanceada para outro nó e farão com que o aplicativo pareça íntegro, permitindo assim que a atualização continue. Ao especificar uma integridade MaxPercentUnhealthyDeployedApplications estrita, a Malha do Serviço pode detectar um problema com o pacote de aplicativo rapidamente e resultar em uma atualização rápida em caso de falha. |
| MaxPercentUnhealthyServices | O valor padrão e recomendado é 0. Especifica o número máximo de serviços na instância do aplicativo que podem não estar íntegros antes que o aplicativo seja considerado não íntegro e tenha atualização com falha. |
| MaxPercentUnhealthyPartitionsPerService | O valor padrão e recomendado é 0. Especifica o número máximo de partições em um serviço que podem não estar íntegras antes que o serviço seja considerado não íntegro. |
| MaxPercentUnhealthyReplicasPerPartition | O valor padrão e recomendado é 0. Especifica o número máximo de réplicas em partição que podem não estar íntegras antes que a partição seja considerada não íntegra. |
| UpgradeReplicaSetCheckTimeout | Serviço sem monitoração de estado: em um único domínio de atualização, a Malha do Serviço tenta garantir que haja instâncias adicionais do serviço disponíveis. Se a contagem de instâncias de destino for mais de um, a Malha do Serviço espera que mais de uma instância fique disponível, até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, a Malha do Serviço continua com a atualização, independentemente do número de instâncias de serviço. Se a contagem de instâncias de destino for um, a Malha do Serviço não espera e prossegue imediatamente com a atualização. Serviço com monitoramento de estado: em um único domínio de atualização, a Malha do Serviço tenta garantir que o conjunto de réplicas tenha um quorum. a Malha do Serviço aguarda pela disponibilidade de um quorum até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, a Malha do Serviço continuará com a atualização, independentemente de quorum. Essa configuração é definida como nunca (infinito) durante a reversão progressiva (aplicada quando a atualização continuar conforme o esperado) e 900 segundos durante a reversão (aplicada quando a atualização tiver erros e for revertida). |
| ForceRestart | Se você atualizar uma configuração ou um pacote de dados sem atualizar o código de serviço, a Malha do Serviço não reinicia o serviço enquanto a propriedade ForceRestart não for definida como verdadeira, como parte da chamada de API. Quando a atualização é concluída, a Malha do Serviço notifica o serviço de que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável pela aplicação das alterações. Se necessário, o serviço pode se reiniciar. |



<br> <br> Os critérios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância do aplicativo. Isso é para garantir que um aplicativo que contém tipos diferentes de serviço possa ter políticas de avaliação diferentes para cada um dos tipos de serviço. Por exemplo, um tipo de serviço de gateway sem monitoração de estado pode ter um MaxPercentUnhealthyPartitionsPerService que é diferente de um tipo de serviço de mecanismo com monitoração de estado para uma determinada instância de aplicativo.

## Próximas etapas


[Tutorial de atualização](service-fabric-application-upgrade-tutorial.md)

[Tópicos avançados](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de atualização de aplicativo ](service-fabric-application-upgrade-troubleshooting.md)

[Serialização de dados](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=July15_HO2-->
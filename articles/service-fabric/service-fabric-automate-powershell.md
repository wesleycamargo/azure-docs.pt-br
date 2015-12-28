<properties
	pageTitle="Automatizar o gerenciamento de aplicativos do Service Fabric usando o PowerShell | Microsoft Azure"
	description="Implantar, atualizar, testar e remover aplicativos do Service Fabric usando o PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Implantar, atualizar, testar e remover aplicativos do Service Fabric usando o PowerShell

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns de implantação, atualização, remoção e teste de aplicativos do Service Fabric.

## Pré-requisitos

Antes de prosseguir para as tarefas no artigo, [instale o tempo de execução, o SDK e as ferramentas](service-fabric-get-started.md) que também instalam os módulos do PowerShell do **ServiceFabric** e **ServiceFabricTestability**. [Habilite a execução de script do PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) e [instale e inicie um cluster local](service-fabric-get-started.md#install-and-start-a-local-cluster) para que você possa executar os exemplos no artigo.

Os exemplos neste artigo usam o aplicativo de exemplo[ WordCount](http://aka.ms/servicefabricsamples) (localizado em amostras de Introdução). Baixe e compile o aplicativo de exemplo.

Antes de executar qualquer comando do PowerShell neste artigo, primeiro conecte-se ao cluster local do Service Fabric usando [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx).

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Tarefa: Implantar um aplicativo do Service Fabric

Depois de compilar o aplicativo, e do empacotamento do aplicativo, você poderá implantar o aplicativo em um cluster local do Service Fabric. Primeiro, empacote o aplicativo WordCount no Visual Studio clicando com o botão direito do mouse em **WordCount** no Gerenciador de Soluções e selecionando **Pacote**. Confira [Modelar um aplicativo no Service Fabric](service-fabric-application-model.md) para saber mais sobre os manifestos de aplicativo e de serviço e sobre o layout do pacote. A implantação envolve o carregamento do pacote de aplicativos, o registro do tipo de aplicativo e a criação da instância do aplicativo. Use as instruções desta seção para implantar um novo aplicativo em um cluster.

### Etapa 1: Carregar o pacote do aplicativo
O carregamento do pacote de aplicativos no repositório de imagens coloca-o em um local acessível por componentes internos do Service Fabric. O pacote de aplicativos contém o manifesto de aplicativo necessário, os manifestos de serviço e os pacotes de dados/configuração/código, a fim de criar as instâncias do aplicativo e do serviço. O comando [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) carregará o pacote. Por exemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Etapa 2: Registrar o tipo de aplicativo
O registro do pacote de aplicativos disponibiliza para uso o tipo de aplicativo e a versão declarada no manifesto do aplicativo. O sistema lerá o pacote carregado na etapa anterior, verificará o pacote (equivalente a executar [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), processará o conteúdo do pacote e copiará o pacote processado em um local interno do sistema. Execute o cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver os tipos de aplicativos registrados no cluster, execute o cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### Etapa 3: Criar a instância do aplicativo
Um aplicativo pode ser instanciado usando qualquer versão do tipo de aplicativo que foi registrado com êxito usando o comando [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). O nome de cada aplicativo deve começar com o esquema **fabric:** e ser exclusivo para cada instância do aplicativo. O nome do tipo de aplicativo e a versão do tipo de aplicativo são declarados no arquivo **ApplicationManifest.xml**. Se algum serviço padrão foi definido no manifesto do aplicativo do tipo de aplicativo de destino, ele também será criado nesse momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

O comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) lista todas as instâncias de aplicativos que foram criadas com êxito, juntamente com seu status geral. O comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) lista todas as instâncias de serviço que foram criadas com êxito em uma determinada instância do aplicativo. Os serviços padrão (se houver) serão listados.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Tarefa: Atualizar um aplicativo do Service Fabric

Você pode atualizar um aplicativo do Service Fabric implantado anteriormente. Essa tarefa atualiza o aplicativo WordCount implantado na “Tarefa: implantar um aplicativo do Service Fabric.” Leia [Atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md) para obter mais informações.

### Etapa 1: Atualizar o aplicativo

Faça alterações no código no serviço WordCount.

Depois de atualizar o código do serviço, será necessário incrementar o número de versão do serviço no arquivo **ServiceManifest.xml** (localizado no diretório **PackageRoot** do projeto WordCount). Encontre o elemento **CodePackage** do manifesto e altere a versão do serviço para 2.0.0. As linhas correspondentes no arquivo ServiceManifest.xml devem parecer com o seguinte:

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

Agora, você precisará atualizar o arquivo ApplicationManifest.xml (encontrado no projeto de aplicativo WordCount sob a solução WordCount). Atualize o elemento **ServiceManifestRef** para usar a versão 2.0.0.0 do projeto **WordCountServicePkg**. Além disso, atualize o **ApplicationTypeVersion** de 1.0.0.0 para 2.0.0.0. As linhas correspondentes em ApplicationManifest.xml devem ser assim:

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

Depois de fazer essas alterações, salve os arquivos e recompile o projeto WordCount. Agora, empacote o aplicativo atualizado clicando com o botão direito do mouse no projeto WordCount e escolhendo **Pacote**. Isso deve criar um pacote de aplicativos que pode ser implantado. Seu aplicativo atualizado está pronto para ser implantado agora.

### Etapa 2: Copiar e registrar o pacote de aplicativos atualizado

O aplicativo está compilado, empacotado e pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e digitar [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), deverá ver a versão 1.0.0 do tipo de aplicativo do WordCount implantado. Para o exemplo WordCount, o pacote de aplicativos é encontrado em: *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

Agora, copie o pacote de aplicativos atualizado para o repositório de imagens do Service Fabric (onde os pacotes de aplicativo são armazenados pelo Service Fabric). O parâmetro **ApplicationPackagePathInImageStore** informa à Malha de serviço onde ela pode encontrar o pacote do aplicativo. O comando a seguir copiará o pacote de aplicativos para **WordCountV2** no repositório de imagens:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

A próxima etapa será registrar a nova versão do aplicativo no Service Fabric, o que pode ser feito usando o cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Se esse comando não for bem-sucedido, será necessário recompilar o serviço, conforme mencionado na Etapa 1.

### Etapa 3: Iniciar a atualização
É possível aplicar vários parâmetros de atualização, tempo limite e critérios de integridade para atualizações de aplicativo. Leia os documentos [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e [processo de atualização](service-fabric-application-upgrade.md) para saber mais. Para este passo a passo, deixe o critério de avaliação de integridade do serviço definido com o valor padrão (e valores recomendados). Todos os serviços e instâncias deverão estar _íntegros_ após a atualização. No entanto, você deve aumentar **HealthCheckStableDuration** para 60 segundos (de modo que os serviços sejam íntegros pelo menos 20 segundos antes que a atualização prossiga para o próximo domínio de atualização). Defina também **UpgradeDomainTimeout** como 1200 segundos e **UpgradeTimeout** como 3000 segundos. Por fim, defina **UpgradeFailureAction** para **reversão**, o que solicita ao Service Fabric a reversão do aplicativo para a versão anterior, caso ocorram falhas durante a atualização.

Agora você pode iniciar a atualização do aplicativo usando o cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Observe que o nome do aplicativo é igual ao do aplicativo v1.0.0 implantado anteriormente (fabric:/WordCount). A Malha de serviços usa esse nome para identificar qual aplicativo está sendo atualizado. Se você definir um tempo limite muito curto, poderá receber uma mensagem de erro de tempo limite informando o problema. Consulte [Solucionar problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md) ou aumente o tempo limite.

Você pode monitorar o progresso da atualização do aplicativo usando o [Gerenciador do Service Fabric](service-fabric-visualizing-your-cluster.md) ou usando o cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Em alguns minutos, o cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) deverá mostrar que todos os domínios de atualização foram atualizados (concluídos).

## Tarefa: testar um aplicativo do Service Fabric

Para escrever serviços de alta qualidade, os desenvolvedores precisam ser capazes de induzir falhas de infraestrutura não confiável a fim de testar a estabilidade de seus serviços. O Service Fabric fornece aos desenvolvedores a capacidade de induzir ações de falha e testar os serviços na presença de falhas usando cenários de teste de caos e de failover. Leia [Visão geral da possibilidade de teste](service-fabric-testability-overview.md) para obter informações adicionais.

### Etapa 1: Executar o cenário de teste de caos
O cenário de teste de caos gera falhas no cluster de todo o Service Fabric. O cenário compacta falhas geralmente vistas em meses ou em anos em apenas algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. O exemplo a seguir executa o cenário de teste de caos por 60 minutos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Etapa 2: Executar o cenário de teste de failover
O cenário de teste de failover destina-se a uma partição de serviço específica, em vez de todo o cluster, e não afeta outros serviços. O cenário realiza a iteração por meio de uma sequência de falhas simuladas na validação de serviço enquanto a lógica de negócios é executada. Uma falha na validação do serviço indica um problema que precisa de mais investigação. O teste de failover apenas induz a uma falha de cada vez, ao contrário do cenário de teste de caos que pode induzir várias falhas. O exemplo a seguir executa o teste de failover por 60 minutos em relação ao serviço fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Tarefa: remover um aplicativo do Service Fabric
Você pode excluir uma instância de um aplicativo implantado, remover o tipo de aplicativo provisionado do cluster e remover o pacote de aplicativo da ImageStore.

### Etapa 1: Remover uma instância de aplicativo
Quando uma instância do aplicativo não é mais necessária, você pode removê-la permanentemente usando o cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx). Isso também removerá automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os estados de serviço. Essa operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Etapa 2: Cancelar o registro do tipo de aplicativo
Quando você não precisar mais de uma versão específica de um tipo de aplicativo, cancele seu registro usando o cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). O cancelamento de registro de tipos não utilizados libera espaço no armazenamento usado pelo pacote de aplicativos no repositório de imagens. Um tipo de aplicativo pode ter seu registro cancelado, desde que não existam aplicativos instanciados nele ou atualizações de aplicativo pendentes que façam referência a ele.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Etapa 3: remover o pacote de aplicativos
Após o cancelamento do registro do tipo de aplicativo, o pacote de aplicativos pode ser removido do repositório de imagens usando o cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Próximas etapas
[Ciclo de vida do aplicativo do Service Fabric](service-fabric-application-lifecycle.md)

[Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[Atualização de aplicativo](service-fabric-application-upgrade.md)

[Cmdlets do Service Fabric do Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de capacidade de teste do Service Fabric do Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_1217_2015-->
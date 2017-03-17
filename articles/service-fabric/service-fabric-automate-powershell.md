---
title: Automatizar o gerenciamento de aplicativos do Service Fabric usando o PowerShell | Microsoft Docs
description: Implantar, atualizar, testar e remover aplicativos do Service Fabric usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: d4a08f7eec5f57b06d87d13abe46942ddc24b482
ms.openlocfilehash: d1909225a950b961491d3fe426f1e0d6aa5bdd53
ms.lasthandoff: 02/24/2017


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizar o ciclo de vida do aplicativo usando o PowerShell
Muitos aspectos do [ciclo de vida de um aplicativo do Service Fabric](service-fabric-application-lifecycle.md) podem ser automatizados.  Este artigo mostra como usar o PowerShell para automatizar tarefas comuns de implantação, atualização, remoção e teste de aplicativos do Service Fabric.  APIs gerenciadas e HTTP para gerenciamento de aplicativos também estão disponíveis. Consulte [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) para obter mais informações.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de passar para as tarefas deste artigo, não deixe de:

* Familiarizar-se com os conceitos do Service Fabric descritos em [Visão geral técnica do Service Fabric](service-fabric-technical-overview.md).
* [Instalar as ferramentas, o SDK e o tempo de execução](service-fabric-get-started.md), o que também instala o módulo **ServiceFabric** do PowerShell.
* [Habilitar a execução de script do PowerShell](service-fabric-get-started.md#enable-powershell-script-execution).
* Iniciar um cluster local.  Inicie uma nova janela do PowerShell como administrador e execute o script de instalação de cluster na pasta do SDK: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* Antes de executar qualquer comando do PowerShell deste artigo, primeiro conecte-se ao cluster local do Service Fabric usando [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster): `Connect-ServiceFabricCluster localhost:19000`
* As tarefas a seguir exigem um pacote de aplicativos v1 para implantar e um pacote de aplicativos v2 para atualizar. Baixe o [aplicativo de exemplo **WordCount**](http://aka.ms/servicefabricsamples) (localizado nas amostras da Introdução). Compile e empacote o aplicativo no Visual Studio (clique com o botão direito do mouse em **WordCount** no Gerenciador de Soluções e selecione **Pacote**). Copie o pacote v1 em `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` para `C:\Temp\WordCount`. Copie `C:\Temp\WordCount` para `C:\Temp\WordCountV2`, criando o pacote de aplicativos v2 para a atualização. Abra `C:\Temp\WordCountV2\ApplicationManifest.xml` em um editor de texto. No elemento **ApplicationManifest**, altere o atributo **ApplicationTypeVersion** de "1.0.0" para "2.0.0" para atualizar o número de versão do aplicativo. Salve o arquivo ApplicationManifest.xml alterado.

## <a name="task-deploy-a-service-fabric-application"></a>Tarefa: Implantar um aplicativo do Service Fabric
Depois de compilar e empacotar o aplicativo (ou baixar o pacote de aplicativos), você poderá implantar o aplicativo em um cluster local do Service Fabric. A implantação envolve o carregamento do pacote de aplicativos, o registro do tipo de aplicativo e a criação da instância do aplicativo. Use as instruções desta seção para implantar um novo aplicativo em um cluster.

### <a name="step-1-upload-the-application-package"></a>Etapa 1: Carregar o pacote do aplicativo
O carregamento do pacote de aplicativos no repositório de imagens coloca-o em um local acessível por componentes internos do Service Fabric.  O pacote de aplicativos contém o manifesto de aplicativo, os manifestos de serviço e os pacotes de dados, configuração e código necessários para criar as instâncias do aplicativo e do serviço. Se quiser verificar o pacote do aplicativo localmente, use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  O comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) carrega o pacote. Por exemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Etapa 2: Registrar o tipo de aplicativo
O registro do pacote de aplicativos disponibiliza para uso o tipo de aplicativo e a versão declarada no manifesto do aplicativo. O sistema lê o pacote carregado na etapa 1, verifica o pacote (equivalente a executar [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) localmente), processa o conteúdo do pacote e copia o pacote processado para uma localização interna do sistema.  Execute o cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver todos os tipos de aplicativo registrados no cluster, execute o cmdlet [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype):

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Etapa 3: Criar a instância do aplicativo
Um aplicativo pode ser instanciado usando qualquer versão do tipo de aplicativo registrada com êxito usando o comando [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). O nome de cada aplicativo é declarado no momento da implantação e deve começar com o esquema **fabric** : e ser exclusivo para cada instância do aplicativo. O nome do tipo de aplicativo e a versão do tipo de aplicativo são declarados no arquivo **ApplicationManifest.xml** do pacote de aplicativos. Se algum serviço padrão tiver sido definido no manifesto do aplicativo do tipo de aplicativo de destino, ele também será criado nesse momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

O comando [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) lista todas as instâncias de aplicativos criadas com êxito juntamente com seu status geral. O comando [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) lista todas as instâncias de serviço criadas com êxito em uma determinada instância de aplicativo. Os serviços padrão (se houver) são listados.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tarefa: Atualizar um aplicativo do Service Fabric
Você pode atualizar um aplicativo do Service Fabric implantado anteriormente com um pacote de aplicativos atualizado. Essa tarefa atualiza o aplicativo WordCount implantado na “Tarefa: implantar um aplicativo do Service Fabric.” Leia [Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md) para obter mais informações.

Para manter as coisas simples neste exemplo, somente o número de versão do aplicativo foi atualizado no pacote de aplicativos WordCountV2 criado nos pré-requisitos. Um cenário mais realista envolveria a atualização de seus arquivos de código, configuração ou dados de serviço e a recriação e empacotamento do aplicativo com números de versão atualizados.  

### <a name="step-1-upload-the-updated-application-package"></a>Etapa 1: carregar o pacote de aplicativos
O aplicativo v1 WordCount está pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e digitar [**Get-ServiceFabricApplication**](/powershell/servicefabric/vlatest/get-servicefabricapplication), verá que a versão 1.0.0 do tipo de aplicativo WordCount foi implantada.  

Agora, copie o pacote de aplicativos atualizado para o repositório de imagens do Service Fabric (onde os pacotes de aplicativo são armazenados pelo Service Fabric). O parâmetro **ApplicationPackagePathInImageStore** informa à Malha de serviço onde ela pode encontrar o pacote do aplicativo. O comando a seguir copia o pacote de aplicativos para **WordCountV2** no repositório de imagens:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Etapa 2: registrar o tipo de aplicativo atualizado
A próxima etapa será registrar a nova versão do aplicativo no Service Fabric, o que pode ser feito usando o cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Etapa 3: Iniciar a atualização
É possível aplicar vários parâmetros de atualização, tempo limite e critérios de integridade para atualizações de aplicativo. Leia os documentos [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e [processo de atualização](service-fabric-application-upgrade.md) para saber mais. Todos os serviços e instâncias deverão estar *íntegros* após a atualização.  Defina **HealthCheckStableDuration** como 60 segundos (de modo que os serviços fiquem íntegros por pelo menos 20 segundos antes que a atualização prossiga para o domínio de atualização seguinte).  Defina também **UpgradeDomainTimeout** como 1.200 segundos e **UpgradeTimeout** como 3.000 segundos. Por fim, defina **UpgradeFailureAction** para **reversão**, o que solicita ao Service Fabric a reversão do aplicativo para a versão anterior caso ocorram falhas durante a atualização.

Agora você pode iniciar a atualização do aplicativo usando o cmdlet [Start-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Observe que o nome do aplicativo é igual ao do aplicativo v1.0.0 implantado anteriormente (fabric:/WordCount). A Malha de serviços usa esse nome para identificar qual aplicativo está sendo atualizado. Se você definir um tempo limite muito curto, poderá receber uma mensagem de erro de tempo limite informando o problema. Consulte [Solucionar problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md)ou aumente o tempo limite.

### <a name="step-4-check-upgrade-progress"></a>Etapa 4: verificar o andamento da atualização
Você pode monitorar o progresso da atualização do aplicativo usando o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou o cmdlet [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Depois de alguns minutos, o cmdlet [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) mostrará que todos os domínios de atualização foram atualizados (concluídos).

## <a name="task-test-a-service-fabric-application"></a>Tarefa: testar um aplicativo do Service Fabric
Para escrever serviços de alta qualidade, os desenvolvedores precisam ser capazes de induzir falhas de infraestrutura não confiável a fim de testar a estabilidade de seus serviços. O Service Fabric fornece aos desenvolvedores a capacidade de induzir ações de falha e testar os serviços na presença de falhas usando cenários de teste de caos e de failover.  Leia [Visão geral da possibilidade de teste](service-fabric-testability-overview.md) para obter informações adicionais.

### <a name="step-1-run-the-chaos-test-scenario"></a>Etapa 1: Executar o cenário de teste de caos
O cenário de teste de caos gera falhas no cluster de todo o Service Fabric. O cenário compacta falhas geralmente vistas em meses ou em anos em apenas algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. O exemplo a seguir executa o cenário de teste de caos por 60 minutos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Etapa 2: Executar o cenário de teste de failover
O cenário de teste de failover destina-se a uma partição de serviço específica, em vez de todo o cluster, e não afeta outros serviços. O cenário realiza a iteração por meio de uma sequência de falhas simuladas na validação de serviço enquanto a lógica de negócios é executada. Uma falha na validação do serviço indica um problema que precisa de mais investigação. O teste de failover apenas induz a uma falha de cada vez, ao contrário do cenário de teste de caos que pode induzir várias falhas. O exemplo a seguir executa o teste de failover por 60 minutos em relação ao serviço fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tarefa: remover um aplicativo do Service Fabric
Você pode excluir uma instância de um aplicativo implantado, remover o tipo de aplicativo provisionado do cluster e remover o pacote de aplicativo da ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Etapa 1: Remover uma instância de aplicativo
Quando uma instância de aplicativo não é mais necessária, você pode removê-la permanentemente usando o cmdlet [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). Isso também remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os estados de serviço. Essa operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Etapa 2: Cancelar o registro do tipo de aplicativo
Quando não precisar mais de uma versão específica de um tipo de aplicativo, cancele seu registro usando o cmdlet [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). O cancelamento de registro de tipos não utilizados libera espaço de armazenamento usado pelo pacote de aplicativos no repositório de imagens. Um tipo de aplicativo pode ter seu registro cancelado, desde que não existam aplicativos instanciados nele ou atualizações de aplicativo pendentes que façam referência a ele.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Etapa 3: remover o pacote de aplicativos
Após o cancelamento do registro do tipo de aplicativo, o pacote de aplicativos poderá ser removido do repositório de imagens usando o cmdlet [Remove-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Próximas etapas
[Ciclo de vida do aplicativo do Service Fabric](service-fabric-application-lifecycle.md)

[Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[Atualização de aplicativo](service-fabric-application-upgrade.md)

[Cmdlets do Azure Service Fabric](/powershell/servicefabric/vlatest/servicefabric)



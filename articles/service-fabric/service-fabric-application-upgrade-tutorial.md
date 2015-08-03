<properties
   pageTitle="Tutorial de atualização do aplicativo de malha de serviço"
   description="Este artigo o orienta por meio de uma experiência de atualização de um aplicativo de malha do serviço."
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
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Tutorial do aplicativo de atualização

O mais usado e o método de atualização recomendado é a atualização sem interrupção monitorada. A malha do serviço monitora a integridade do aplicativo que está sendo atualizado com base em um conjunto de diretivas de integridade. Quando os aplicativos em um domínio de atualização (UD) tiverem sido atualizados, a malha do serviço avalia a integridade do aplicativo e determina se prossegue para o próximo domínio de atualização ou retorna um erro para a atualização com base nas diretivas de integridade. Uma atualização do aplicativo monitorada pode ser executada usando APIs, PowerShell ou REST nativos ou gerenciados.

A atualização sem interrupção da malha do serviço monitorados permite que ao administrador de aplicativos configurar a política de avaliação de integridade que malha de serviço usa para determinar se o aplicativo está íntegro. Além disso, ela também permite ao administrador configurar a ação a ser executada quando ocorre um erro na avaliação de integridade como, por exemplo, reversão automática. Esta seção fornece passo a passo de uma atualização monitorada para um dos exemplos do SDK.

## Etapa 1: criar e implantar o exemplo de objetos visuais

Essas etapas podem ser executadas abrindo o projeto no Visual Studio e clicando com o botão direito na Solução e selecionando o comando implantar no item de menu de Malha do serviço. Consulte [gerenciando seu aplicativo de Malha do serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md) para obter mais informações. Como alternativa, pode-se usar o PowerShell.

> [AZURE.NOTE]Antes de qualquer um dos comandos de malha de serviços poder ser usado no PowerShell, é necessário primeiro se conectar ao cluster usando o cmdlet `Connect-ServiceFabricCluster`. Da mesma forma, supõe-se que o Cluster já foi configurado no computador local. Consulte o artigo [configurando o ambiente de desenvolvimento de Malha do serviço](service-fabric-get-started.md).

Depois de criar o projeto no Visual Studio, você pode usar o comando do PowerShell **cópia ServiceFabricApplicationPackage** para copiar o pacote de aplicativo para ImageStore, seguido pelo registro do aplicativo no tempo de execução de Malha de serviço usando o cmdlet **registro ServiceFabricApplicationPackage** e finalmente iniciar uma instância do aplicativo usando o cmdlet **ServiceFabricApplication novo**. Essas três etapas são semelhantes a usar o item de menu de implantação no Visual Studio.

Agora, você pode usar o [Gerenciador de Malha do serviço para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo tem um serviço web que pode ser acessado no Internet Explorer, digitando [http://localhost:80](http://localhost:80) na barra de endereços. Você deve ver alguns objetos visuais flutuantes moverem-se na tela. Além disso, você pode usar **Get-ServiceFabricApplication** para verificar o status do aplicativo.

## Etapa 2: atualização do exemplo de objetos visuais

Você pode notar que a com a versão implantada na Etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais possam girar.

Selecione o projeto VisualObjects.DataService dentro da solução VisualObjects e abra o arquivo VisualObjects.cs no projeto. Dentro desse arquivo, navegue até o método `CreateMovedObject` e localize a linha em que a rotação dos objetos visuais é definida (procure as seguintes linhas no método `CreateMovedObject`). Comente a linha `nextObject.Rotation = 1` e remova o comentário da linha abaixo dela. Seu código deve ser semelhante ao seguinte após as alterações terem sido feitas:

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

Também precisamos atualizar o arquivo *ServiceManifest.xml* (em PackageRoot) do projeto **VisualObjects.DataService**. Atualize o *CodePackage* e a versão de serviço para 2.0 e as linhas correspondentes no arquivo *ServiceManifest.xml* devem ser semelhantes ao seguinte (as partes realçadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

Agora, precisamos atualizar o arquivo *ApplicationManifest.xml* (localizado no projeto **VisualObjects** na solução **VisualObjects**) para usar a versão 2.0 do projeto **VisualObjects.DataService** e também atualizar a versão do aplicativo de 2.0.0.0 para 1.0.0.0. Agora, as linhas correspondentes em *ApplicationManifest.xml* devem ser semelhantes ao seguinte:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

Agora, compile o projeto selecionando o projeto **VisualObjects** e clicando com o botão direito e selecionando a compilação no Visual Studio (se você selecionar a Recompilar todos, você talvez tenha que atualizar as versões para o projeto **VisualObjects.WebService** e também no *ServiceManifest.xml* e no *ApplicationManifest.xml*, já que o código teria sido alterado). Agora vamos empacotar o aplicativo atualizado clicando com o botão direito no projeto *VisualObjects*, selecionando o Menu de malha do serviço e escolhendo Pacote. Isso deve criar um pacote de aplicativos que pode ser implantado. Seu aplicativo atualizado está pronto para ser implantado agora.

## Etapa 3: decida sobre as diretivas de integridade e parâmetros de atualização

Familiarize-se com os [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, o critério de tempos limite e integridade aplicados. Para este passo a passo, nós deixaremos o critério de avaliação de integridade serviço como o padrão (e valores recomendados) que significa que todos os serviços e as instâncias devem estar _íntegros_ após a atualização. Vamos, no entanto, aumentar *HealthCheckStableDuration* para 60 segundos (de modo que os serviços sejam saudáveis pelo menos 20 segundos antes que a atualização prossiga para o próximo domínio de atualização). Vamos definir também *UpgradeDomainTimeout* para 1200 segundos e *UpgradeTimeout* para 3000 segundos. Por fim, vamos também definir *UpgradeFailureAction* para reversão, assim, solicitando que a Malha do serviço reverta o aplicativo para a versão anterior caso encontre problemas durante a atualização. Portanto, os parâmetros de atualização que especificaremos ao iniciar a atualização (na etapa 4) serão os seguintes:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## Etapa 4: preparar o aplicativo para atualização

Agora, o aplicativo está compilado e pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e digitar **Get-ServiceFabricApplication**, você será informado o Aplicativo Tipo 1.0.0.0 do **VisualObjects** foi implantado. O pacote de aplicativo é armazenado no seguinte caminho relativo onde você descompactou o SDK de Malha do serviço - *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. Você deve localizar uma pasta de "Pacote" nesse diretório - esse é o local onde o pacote do aplicativo está armazenado. Verifique os carimbos de hora para garantir que é a compilação mais recente (e talvez seja necessário modificar os caminhos adequadamente também).

Agora vamos copiar o pacote de aplicativo atualizado para o ImageStore de Malha do serviço (onde os pacotes de aplicativos são armazenados pela Malha do serviço). O parâmetro *ApplicationPackagePathInImageStore* informa à Malha de serviço onde ela pode encontrar o pacote do aplicativo. Criamos o aplicativo atualizado no "VisualObjects_V2" com o seguinte comando (talvez seja necessário modificar novamente os caminhos).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects_V2"
```

A próxima etapa é registrar este aplicativo com a malha de serviço, o que pode ser feito usando o seguinte comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects_V2"
```

Se o comando acima não funcionar, é provável que você precise recompilar todos os serviços. Conforme mencionado na Etapa 2, você terá que atualizar sua versão do serviço Web.

## Etapa 5: iniciar a atualização de aplicativo

Agora, estamos prontos para iniciar a atualização de aplicativo usando o seguinte comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Observe o nome do aplicativo conforme descrito no arquivo *ApplicationManifest.xml*. A Malha de serviços usa esse nome para identificar qual aplicativo está sendo atualizado. Se você definir tempos limite muito curtos, você poderá encontrar uma mensagem de erro informando o problema. Consulte a seção de solução de problemas ou aumente os tempos limites.

Agora, enquanto a atualização do aplicativo está sendo realizada, você pode monitorá-lo usando o Gerenciador de malha do serviço, ou usando o seguinte comando do PowerShell: **ServiceFabricApplicationUpgrade Get malha:/VisualObjects**.

Em alguns minutos, o status que usa o comando PowerShell acima deve indicar que todos os domínios de atualização foram atualizados (concluído). E você deve notar que os objetos visuais na janela do navegador agora começaram a girar!

Você talvez queira tentar alterar as versões e mudar da versão 2 para a versão 3 como um exercício ou até mesmo da versão 2 de volta para a versão 1 (sim, você pode atualizar do v2 para v1). Teste as possibilidades com diretivas de integridade e os tempos limite para ficar familiarizado. Quando você estiver implantando um cluster do Azure, os parâmetros usados serão diferentes daqueles que funcionavam ao implantar um cluster local - é recomendável definir os tempos limites de forma prudente.


## Próximas etapas

[Parâmetros de atualização](service-fabric-application-upgrade-parameters.md)

[Serialização de dados](service-fabric-application-upgrade-data-serialization.md)

[Tópicos avançados](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de atualização de aplicativo ](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=July15_HO4-->
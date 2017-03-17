---
title: "Implantação de aplicativos do Service Fabric | Microsoft Docs"
description: "Como implantar e remover aplicativos na Malha do Serviço"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implantar e remover aplicativos usando o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Assim que um [tipo de aplicativo for empacotado][10], ele está pronto para implantação em um cluster do Azure Service Fabric. A implantação envolve as três etapas a seguir:

1. Carregar o pacote de aplicativo no repositório de imagens
2. Registrar o tipo de aplicativo
3. Criar a instância do aplicativo

Depois que um aplicativo é implantado e uma instância está em execução no cluster, você pode excluir a instância do aplicativo e o tipo de aplicativo. Para remover completamente um aplicativo do cluster, siga as seguintes etapas:

1. Remover (ou excluir) a execução da instância do aplicativo
2. Cancelar o registro do tipo de aplicativo se você não precisar mais dele
3. Remover o pacote de aplicativo do repositório de imagens

Se você usar o [Visual Studio para implantar e depurar aplicativos](service-fabric-publish-app-remote-cluster.md) no cluster de desenvolvimento local, todas as etapas anteriores serão tratadas automaticamente por meio de um script do PowerShell.  Esse script é encontrado na pasta *Scripts* do projeto do aplicativo. Este artigo fornece informações sobre o que esse script faz para que você possa executar as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster
Antes de executar qualquer comando do PowerShell neste artigo, sempre comece usando [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) para se conectar ao cluster do Service Fabric. Para se conectar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Para obter exemplos de como se conectar a um cluster remoto ou protegido usando o Azure Active Directory, certificados X509 ou Active Directory do Windows, veja [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicativo
O carregamento do pacote de aplicativos coloca-o em um local acessível por componentes internos do Service Fabric. Se quiser verificar o pacote do aplicativo localmente, use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  O comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) carrega o pacote do aplicativo para o Repositório de Imagens do cluster. O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que faz parte do módulo do PowerShell do SDK do Service Fabric, é usado para obter a cadeia de conexão do repositório de imagens.  Para importar o módulo do SDK, execute:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Suponha que você crie e empacote um aplicativo chamado *MyApplication* no Visual Studio. Por padrão, o nome do tipo de aplicativo listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicativos, que contém o manifesto do aplicativo necessário, manifestos de serviço e os pacotes de códigos/configurações/dados, está localizado em *C:\Users\nome de usuário\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

O comando a seguir lista o conteúdo do pacote de aplicativos:

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

O exemplo a seguir carrega o pacote para o armazenamento de imagens em uma pasta chamada "MyApplicationV1":

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

Se você não especificar o parâmetro *-ApplicationPackagePathInImageStore*, o pacote de aplicativo será copiado para a pasta "Debug" no repositório de imagens.

Confira [Noções básicas sobre a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre o repositório de imagens e a cadeia de conexão de armazenamento de imagens.

## <a name="register-the-application-package"></a>Registrar o pacote de aplicativo
O tipo e a versão do aplicativo declarados no manifesto do aplicativo tornam-se disponíveis para uso quando o pacote do aplicativo é registrado. O sistema lê o pacote carregado na etapa anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado em um local interno do sistema.  

Execute o cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) para registrar o tipo de aplicativo no cluster e disponibilizá-lo para implantação:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" é a pasta no repositório de imagens onde se encontra o pacote de aplicativos. O tipo de aplicativo com o nome "MyApplicationType" e a versão "1.0.0" (ambos são encontradas no manifesto do aplicativo) agora é registrado no cluster.

O comando [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) retornará somente depois que o sistema tiver registrado com êxito o pacote de aplicativos. O tempo que se leva para o registro depende do tamanho e do conteúdo do pacote de aplicativos. O parâmetro **-TimeoutSec** pode ser usado para fornecer um tempo limite maior, se necessário (o tempo limite padrão é 60 segundos).  Caso este seja um pacote de aplicativo grande e os tempos limite estiverem sendo esgotados, use o parâmetro **-Async**.

O comando [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) lista todas as versões do tipo de aplicativo registradas com êxito e seu status de registro.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>Criar o aplicativo
É possível instanciar um aplicativo de qualquer versão do tipo de aplicativo registrada com êxito usando o cmdlet [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). O nome de cada aplicativo deve começar com o esquema *fabric:* e ser exclusivo para cada instância do aplicativo. Quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino também são criados.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Várias instâncias do aplicativo podem ser criadas para qualquer determinada versão de um tipo de aplicativo registrado. Cada instância do aplicativo é executada isoladamente, com seu próprio processo e diretório de trabalho.

Para ver quais apps e serviços nomeados estão em execução no cluster, execute os cmdlets [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice):

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Remover um aplicativo
Quando uma instância de aplicativo não é mais necessária, você pode removê-la permanentemente pelo nome usando o cmdlet [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) também remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os estados de serviço. Essa operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Cancelar o registro de um tipo de aplicativo
Quando uma versão específica de um tipo de aplicativo não é mais necessária, cancele o registro do tipo de aplicativo usando o cmdlet [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). O cancelamento de registro de tipos de aplicativo não utilizados libera espaço de armazenamento usado pelo repositório de imagens. Um tipo de aplicativo pode ter seu registro cancelado, desde que não existam aplicativos instanciados nele e nenhuma atualização de aplicativo pendente que faça referência a ele.

Execute [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) para ver os tipos de aplicativo registrados no cluster no momento:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Execute [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) para cancelar o registro de um tipo de aplicativo específico:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicativo do repositório de imagens
Quando um pacote de aplicativo não for mais necessário, você poderá excluí-lo do repositório de imagens para liberar recursos do sistema.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage solicita um ImageStoreConnectionString
O ambiente do SDK da Malha do Serviço já deve ter os padrões corretos configurados. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster de Malha do Serviço está usando. Você pode encontrar ImageStoreConnectionString no manifesto do cluster, recuperado usando o comando [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest):

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

ImageStoreConnectionString é encontrado no manifesto do cluster:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>Próximas etapas
[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

[Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)

[Diagnosticar e solucionar problemas de um serviço da Malha do Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md


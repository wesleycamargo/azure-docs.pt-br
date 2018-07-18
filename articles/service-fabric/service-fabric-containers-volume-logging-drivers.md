---
title: Driver de Volume dos Arquivos do Azure do Service Fabric (Versão prévia) | Microsoft Docs
description: O Service Fabric oferece suporte a Arquivos do Azure para volumes de backup de seu contêiner. No momento, isso está em visualização.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: 2d98cff1a5869091aa81097bbb34da6e525a2ad5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Driver de Volume dos Arquivos do Azure do Service Fabric (Versão prévia)
O plug-in de volume dos Arquivos do Azure é um [Plug-in de volume de Docker](https://docs.docker.com/engine/extend/plugins_volume/) que fornece [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) com base em volumes para contêineres do Docker. Este plug-in de volume do Docker é empacotado como um aplicativo do Service Fabric que pode ser implantado em clusters do Service Fabric. Seu objetivo é fornecer volumes baseados em Arquivos do Azure para outros aplicativos de contêiner do Service Fabric que são implantados para o cluster.

> [!NOTE]
> A versão 6.255.389.9494 do plug-in de volume dos Arquivos do Azure é uma versão prévia que está disponível com este documento. Como uma versão prévia, **não** tem suporte para uso em ambientes de produção.
>

## <a name="prerequisites"></a>pré-requisitos
* A versão do Windows do plug-in de volume dos Arquivos do Azure funciona no [Windows Server versão 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versão 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) ou apenas em sistemas operacionais posteriores. A versão do Linux do plug-in de volume dos Arquivos do Azure funciona em todas as versões de sistema operacional com suporte pelo Service Fabric.

* Siga as instruções na [Documentação dos Arquivos do Azure](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) para criar um compartilhamento de arquivo para o aplicativo de contêiner do Service Fabric para usar como o volume.

* Você precisará do [Powershell com o módulo do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) instalado.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Implantar o aplicativo dos Arquivos do Azure do Service Fabric

O aplicativo do Service Fabric que fornece os volumes para seus contêineres pode ser baixado através do seguinte [link](https://aka.ms/sfvolume). O aplicativo pode ser implantado para o cluster por meio do [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) ou [APIs FabricClient](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Usando a linha de comando, altere o diretório para o diretório raiz do pacote do aplicativo baixado. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copie o pacote de aplicativo para o repositório de imagens. Execute o comando abaixo com o valor apropriado para [ApplicationPackagePath] e [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrar o tipo de aplicativo

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Crie o aplicativo no comando para criar o aplicativo abaixo. Observe o parâmetro de aplicativo **ListenPort**. Esse valor especificado para esse parâmetro de aplicativo é a porta na qual o plug-in do volume dos Arquivos do Azure escuta solicitações do daemon do Docker. É importante garantir que a porta fornecida para o aplicativo não esteja em conflito com outra porta que o cluster ou seus aplicativos usam.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> O Windows Server 2016 Datacenter não é compatível com mapeamento de montagens de SMB em contêineres ([só há compatibilidade no Windows Server versão 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Essa restrição impede mapeamento do volume de rede e drivers de volume de Arquivos do Azure em versões anteriores à 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implantar o aplicativo em um cluster de desenvolvimento local
A contagem de instâncias de serviço padrão para o aplicativo de plug-in de volume dos Arquivos do Azure é -1, o que significa que há uma instância do serviço implantado em cada nó no cluster. No entanto, ao implantar o aplicativo de plug-in de volume dos Arquivos do Azure em um cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isso pode ser feito por meio do parâmetro de aplicativo **InstanceCount**. Assim, o comando para implantar o aplicativo de plug-in do volume de Arquivos do Azure em um cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Configure seus aplicativos para usar o volume
O trecho de código a seguir mostra como um volume baseado nos Arquivos do Azure pode ser especificado no manifesto do aplicativo do seu aplicativo. O elemento específico de interesse é a marca **Volume**:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

O nome do driver para o plug-in de volume dos Arquivos do Azure é **sfazurefile**. Esse valor é definido para o atributo **Driver** do elemento **Volume** no manifesto do aplicativo.

No elemento **Volume** no trecho de código acima, o plug-in do volume dos Arquivos do Azure requer as seguintes marcas: 
- **Origem** — Refere-se à pasta de origem que pode ser uma pasta na VM que hospeda os contêineres ou um armazenamento remoto persistente
- **Destino** — Esta marcação é o local em que a **Origem** é mapeada no contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner

Conforme exibido nos elementos **DriverOption** no trecho acima, o plug-in do volume de Arquivos do Azure oferece suporte às seguintes opções de driver:

- **shareName** — Nome do compartilhamento de arquivo dos Arquivos do Azure que fornece o volume para o contêiner
- **storageAccountName** — Nome da conta de armazenamento do Azure que contém o compartilhamento de arquivo dos Arquivos do Azure
- **storageAccountKey** — Chave de acesso da conta de armazenamento do Azure que contém o compartilhamento de arquivo dos Arquivos do Azure

Todas as opções do driver acima são necessárias. 

## <a name="using-your-own-volume-or-logging-driver"></a>Usando seu próprio volume ou o log do driver
O Service Fabric também permite o uso do volume personalizado ou drivers de registro em log. Se o driver de volume/log do Docker não estiver instalado em seu cluster, é possível instalá-lo manualmente usando os protocolos RDP/SSH. Você pode executar a instalação usando esses protocolos por meio de um [script de inicialização de conjunto de escala de máquina virtual](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou um [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Um exemplo de script para instalar o [driver de volume do Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Em seus aplicativos, para usar o volume ou a unidade de log que você instalou, é necessário especificar os valores apropriados nos elementos **Volume** e **LogConfig** sob  **ContainerHostPolicies** em seu manifesto de aplicativo. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Próximas etapas
* Para ver exemplos de contêiner, incluindo o driver de volume, visite os [Exemplos de contêiner do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implantar contêineres em um cluster do Service Fabric, consulte o artigo [Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md)



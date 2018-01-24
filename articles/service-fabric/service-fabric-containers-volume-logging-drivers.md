---
title: "Versão prévia do Docker Compose do Azure Service Fabric | Microsoft Docs"
description: "O Azure Service Fabric aceita o formato do Docker Compose para facilitar a orquestração dos contêineres existentes usando o Service Fabric. O suporte para Docker Compose está atualmente em visualização."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 5923cea82fbae25fa670556ae27f6cba77a73940
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Use plug-ins e drivers de log do volume do Docker em seu contêiner
O Service Fabric do Azure suporta realizar especificações de [plug-ins de volume do Docker](https://docs.docker.com/engine/extend/plugins_volume/) e [drivers de log do Docker](https://docs.docker.com/engine/admin/logging/overview/) para o seu serviço de contêiner. Isso permite manter seus dados nos [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) mesmo se o seu contêiner for movido ou reiniciado em um host diferente.

Atualmente, apenas os drivers de volume para contêineres do Linux são suportados. Se você estiver usando contêineres do Windows, você pode mapear um volume para um [ compartilhamento SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) dos Arquivos do Azure sem um driver de volume. Isso requer atualizar suas Máquinas Virtuais (VMs) no seu cluster para a versão mais recente do Windows Server 1709.


## <a name="install-the-docker-volumelogging-driver"></a>Instalar o driver de log/volume do Docker

Se o driver de volume/log do Docker não estiver instalado na sua máquina, é possível instalá-lo manualmente usando os protocolos RDP/SSH. Você pode executar a instalação usando esses protocolos por meio de um [script de inicialização de conjunto de escala de máquina virtual](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou um [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Um exemplo de script para instalar o [driver de volume do Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> O Windows Server 2016 Datacenter não é compatível com montagens de SMB no host ([só há compatibilidade no Windows Server versão 1709](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage)). Isso impede o uso de determinados drivers de volume, como os drivers de volume dos Arquivos do Azure. Em vez disso, é possível montar os compartilhamentos diretamente dentro do contêiner usando **net use**. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Especifique o plug-in ou driver no manifesto
Os plug-ins são especificados no manifesto do aplicativo, conforme mostrado a seguir:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
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

A marcação **Fonte** para o elemento **Volume** refere-se à pasta de origem. A pasta de origem pode ser uma pasta na VM que hospeda os contêineres ou um armazenamento remoto persistente. A marcação **Destino** é o local em que a **Fonte** é mapeada no contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner.

Ao especificar um plug-in do volume, o Service Fabric cria automaticamente o volume usando os parâmetros especificados. A marcação **Fonte** é o nome do volume e a marcação **Driver** especifica o plug-in do driver do volume. As opções podem ser especificadas usando a marcação de **DriverOption** como da seguinte maneira:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Se um driver de log do Docker for especificado, será necessário implantar agentes (ou contêineres) para tratar os logs no cluster. A marcação **DriverOption** pode ser usada para especificar opções para o driver de log.

## <a name="next-steps"></a>Próximas etapas
Para implantar contêineres em um cluster do Service Fabric, consulte [Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md).

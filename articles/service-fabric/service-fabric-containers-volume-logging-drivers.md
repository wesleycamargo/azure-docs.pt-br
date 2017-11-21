---
title: "Versão Prévia do Docker Compose do Azure Service Fabric | Microsoft Docs"
description: "O Azure Service Fabric aceita o formato do Docker Compose para facilitar a orquestração dos contêineres existentes usando o Service Fabric. No momento, esse suporte está na versão prévia."
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
ms.openlocfilehash: 7464611e669165d9ec1f0de7422b20b3f3b8c2b5
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Como usar plugins de volume e drivers de registro em log no seu contêiner

Service Fabric suporta especificação de [Plugins de volume Docker ](https://docs.docker.com/engine/extend/plugins_volume/) e [Drivers de log docker](https://docs.docker.com/engine/admin/logging/overview/) para o seu serviço de contêiner. 

## <a name="install-volumelogging-driver"></a>Instalar o driver de registro em log/volume

Se o driver de registro em log/volume do Docker não estiver instalado no computador, instale-o manualmente por meio de RDP/SSH no computador ou de um script de inicialização VMSS. Por exemplo, para instalar o Driver de Volume do Docker, faça SSH no computador e execute:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Especificar o plug-in ou o driver no manifesto
Os plugins são especificados no manifesto do aplicativo, conforme mostrado no seguinte manifesto:

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

No exemplo anterior, a `Source` marca para `Volume` refere-se à pasta de origem. A pasta de origem pode ser uma pasta na VM que hospeda os contêineres ou um armazenamento remoto persistente. A marca `Destination` é o local para onde `Source` será mapeado no contêiner em execução. 

Ao especificar um plug-in do volume, o Service Fabric cria automaticamente o volume usando os parâmetros especificados. A marca `Source` é o nome do volume e a marca `Driver` especifica o plug-in do driver do volume. As opções podem ser especificadas usando a marca `DriverOption` conforme mostrado no trecho a seguir:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Se um driver de log docker for especificado, será necessário implantar agentes (ou contêineres) para tratar os logs no cluster.  A marca `DriverOption` também pode ser usada para especificar opções de driver de log.

Consulte os seguintes artigos para implantar contêineres em um cluster do Service Fabric:


[Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md)


---
title: "Especificando pontos de extremidade de serviço do Service Fabric | Microsoft Docs"
description: "Como descrever os recursos de ponto de extremidade em um manifesto do serviço, incluindo como configurar pontos de extremidade HTTPS"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9cfdb94d1e030fe9d467389acf8894d79efd17d1
ms.contentlocale: pt-br
ms.lasthandoff: 03/29/2017


---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos em um manifesto do serviço
## <a name="overview"></a>Visão geral
O manifesto do serviço permite que os recursos usados pelo serviço sejam declarados/alterados sem alterar o código compilado. O Azure Service Fabric dá suporte à configuração dos recursos de ponto de extremidade para o serviço. O acesso aos recursos que são especificados no manifesto do serviço pode ser controlado por meio do SecurityGroup no manifesto do aplicativo. A declaração de recursos permite que esses recursos sejam alterados no momento da implantação, o que significa que o serviço não precisa apresentar um novo mecanismo de configuração. A definição de esquema para o arquivo ServiceManifest.xml é instalada com o SDK e as ferramentas do Service Fabric em *C:\Arquivos de Programas\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos de extremidade
Quando um recurso de ponto de extremidade é definido no manifesto do serviço, o Service Fabric atribui portas do intervalo de portas reservadas do aplicativo quando uma porta não é explicitamente especificada. Por exemplo, examine o ponto de extremidade *ServiceEndpoint1* especificado no trecho de manifesto fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica em um recurso. As réplicas do serviço em execução em nós diferentes do cluster podem receber números de porta diferentes, enquanto as réplicas do mesmo serviço em execução no mesmo nó compartilham a porta. As réplicas de serviço podem usar essas portas conforme a necessidade para replicação e escuta de solicitações de clientes.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Consulte [Configurando o Reliable Services com estado](service-fabric-reliable-services-configuration.md) para ler mais sobre como fazer referência a pontos de extremidade por meio do arquivo de configurações do pacote de configuração (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTP para o serviço
O manifesto do serviço a seguir define um recurso de ponto de extremidade TCP e dois recursos de ponto de extremidade HTTP no elemento &lt;Recursos&gt;.

A ACL é automaticamente aplicada aos pontos de extremidade HTTP pelo Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTPS para o serviço
O protocolo HTTPS fornece autenticação de servidor e também é usado para criptografar a comunicação cliente-servidor. Para habilitar o HTTPS em seu serviço do Service Fabric, especifique o protocolo na seção *Recursos -> Pontos de Extremidade -> Ponto de Extremidade* do manifesto do serviço, conforme mostrado anteriormente para o ponto de extremidade *ServiceEndpoint3*.

> [!NOTE]
> O protocolo de um serviço não pode ser alterado durante a atualização do aplicativo. Se ele for alterado durante a atualização, será uma alteração significativa.
> 
> 

Este está um exemplo de ApplicationManifest que precisa ser definido para HTTPS. Deve ser fornecida impressão digital para seu certificado. O EndpointRef é uma referência a EndpointResource no ServiceManifest, para o qual você definiu o protocolo HTTPS. Você pode adicionar mais de um EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```


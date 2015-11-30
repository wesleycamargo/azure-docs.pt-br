<properties
   pageTitle="Especificando pontos de extremidade de serviço do Service Fabric | Microsoft Azure"
   description="Como descrever os recursos de ponto de extremidade em um manifesto de serviço, incluindo a configuração de pontos de extremidade HTTPS"
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
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# Especificando recursos em um manifesto do serviço 

## Visão geral

O manifesto do serviço permite que os recursos sejam usados pelo serviço para declaração/alteração sem alterar o código compilado. A Malha de Serviço dá suporte à configuração dos recursos de ponto de extremidade para o serviço. O acesso aos recursos especificado no manifesto do serviço pode ser controlado por meio do SecurityGroup no manifesto do aplicativo. A declaração dos recursos permite que esses recursos sejam alteradas no momento da implantação em vez de exigir que o serviço introduza um novo mecanismo de configuração.

## Pontos de extremidade

Quando um recurso de ponto de extremidade é definido no manifesto do serviço, o Service Fabric atribui portas do intervalo de portas reservadas de aplicativo se não for especificada uma porta explicitamente (por exemplo, examine o ponto de extremidade *ServiceEndpoint1* abaixo). Além disso, os serviços também podem solicitar uma porta específica em um recurso. As réplicas do serviço em execução em nós de cluster diferente podem receber números de porta diferentes, enquanto as réplicas do mesmo serviço em execução no mesmo nó compartilham a mesma porta. Essas portas podem ser usadas pelas réplicas do serviço para várias finalidades, por exemplo, replicação, escuta de solicitações de clientes etc.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Confira [Configurando serviços confiáveis com monitoração de estado](../Service-Fabric/service-fabric-reliable-services-configuration.md) para ler mais sobre como fazer referência a pontos de extremidade a partir do arquivo de configurações do pacote de configuração (Settings.xml).

## Exemplo: Especificando um ponto de extremidade HTTP para o serviço

O manifesto do serviço a seguir define 1 recurso de ponto de extremidade TCP e 2 recursos de ponto de extremidade HTTP no elemento &lt;Recursos&gt;.

A ACL é automaticamente aplicada aos pontos de extremidade HTTP pela Malha de Serviço.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
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
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through a ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## Exemplo: Especificando um ponto de extremidade HTTPS para o serviço

O protocolo HTTPS fornece autenticação de servidor e também é usado para criptografar a comunicação cliente-servidor. Para habilitar isso no seu serviço do Service Fabric, ao definir o serviço, o protocolo é especificado na seção *Recursos -> Pontos de extremidade -> Ponto de extremidade* do manifesto de serviço, conforme mostrado anteriormente para o ponto de extremidade *ServiceEndpoint3*.

>[AZURE.NOTE]O protocolo de um serviço não pode ser alterado durante a atualização do aplicativo, já que isso seria uma alteração significativa.

 
Aqui está um exemplo de ApplicationManifest que você precisa definir para HTTPS (você precisará fornecer a impressão digital para o certificado). O EndpointRef é uma referência a EndpointResource no ServiceManifest para o qual você definiu o protocolo HTTPS. Você pode adicionar mais de um Endpointcertificates.

```
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
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
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

<!---HONumber=Nov15_HO4-->
---
title: "Alterar as configurações de FabricTransport nos microsserviços do Azure | Microsoft Docs"
description: "Saiba como definir as configurações de comunicação de ator do Azure Service Fabric."
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 4cbca6e496135a312bf4704dd0989f45dcccfc00
ms.lasthandoff: 04/14/2017


---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Definir as configurações de FabricTransport para os Reliable Actors

Aqui estão as configurações que você pode configurar:

- C#: [FabricTansportSettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)
- Java: [FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport._fabric_transport_remoting_settings)

Modifique a configuração padrão de FabricTransport das seguintes maneiras.

## <a name="assembly-attribute"></a>Atributo de assembly

O atributo [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) precisa ser aplicado no cliente do ator e nos assemblies do serviço de ator.

O exemplo a seguir mostra como alterar o valor padrão das configurações OperationTimeout do FabricTransport:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

O exemplo a seguir mostra como alterar os valores padrão MaxMessageSize e OperationTimeoutInSeconds do FabricTransport:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Pacote de configuração

Você pode usar um [pacote de configuração](service-fabric-application-model.md) para modificar a configuração padrão.

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Definir as configurações do FabricTransport para o serviço de ator

Adicione uma seção TransportSettings no arquivo settings.xml.

Por padrão, o código do ator procura SectionName como "&lt;ActorName&gt;TransportSettings". Se isso não for encontrado, ele procurará SectionName como "TransportSettings".

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Definir as configurações do FabricTransport para o assembly do cliente do ator

Se o cliente não estiver sendo executado como parte de um serviço, você poderá criar um arquivo "&lt;Client Exe Name&gt;.settings.xml" no mesmo local do arquivo .exe do cliente. Em seguida, adicione uma seção TransportSettings a esse arquivo. O SectionName deve ser "TransportSettings".

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```


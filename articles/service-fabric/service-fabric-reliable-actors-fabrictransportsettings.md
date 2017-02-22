---
title: "Alterar as configurações de FabricTransport nos microsserviços do Azure | Microsoft Docs"
description: "Saiba como definir as configurações de comunicação de Ator do Azure Service Fabric."
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
ms.sourcegitcommit: f7edee399717ecb96fb920d0a938da551101c9e1
ms.openlocfilehash: 6041541903d4d90710817149be50e05e31fd88f1


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Definindo as configurações de FabricTransport nos Reliable Actors

Esta é a lista de configurações que o usuário pode usar para configurar [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)

Modifique a configuração padrão de FabricTransport das seguintes maneiras.

1.  Usando o Atributo de Assembly – [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute).

  Esse atributo precisa ser aplicado no cliente do Ator e no assembly do serviço do Ator.
  O exemplo a seguir mostra como alterar o valor padrão das configurações FabricTransport OperationTimeout.

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   O segundo exemplo altera os valores padrão de FabricTransport MaxMessageSize e OperationTimeoutInSeconds

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. Usando um [pacote de configuração](service-fabric-application-model.md):

  * Definindo as configurações de FabricTransport para o serviço de ator

    Adicione uma seção TransportSettings no arquivo settings.xml.

    * SectionName: por padrão, o código do Ator procura SectionName como “&lt;ActorName&gt;TransportSettings”. Se não for encontrado, ele procurará sectionName como “TransportSettings”.

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

  * Definindo as configurações de FabricTransport para o assembly do cliente do Ator

    Se o cliente não estiver sendo executado como parte de um serviço, você poderá criar um arquivo “&lt;Client Exe Name&gt;.settings.xml” no mesmo local de client.exe. Em seguida, adicione uma seção TransportSettings a esse arquivo. SectionName deve ser “TransportSettings”.

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



<!--HONumber=Jan17_HO4-->



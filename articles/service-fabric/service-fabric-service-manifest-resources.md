<properties
   pageTitle="Recursos do manifesto do serviço da Malha de Serviço"
   description="Como descrever os recursos em um manifesto do serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# Recursos do Manifesto do Serviço

## Visão geral

O manifesto do serviço permite que os recursos sejam usados pelo serviço para declaração/alteração sem alterar o código compilado. A Malha de Serviço dá suporte à configuração dos recursos de ponto de extremidade para o serviço. O acesso aos recursos especificado no manifesto do serviço pode ser controlado por meio do SecurityGroup no manifesto do aplicativo. A declaração dos recursos permite que esses recursos sejam alteradas no momento da implantação em vez de exigir que o serviço introduza um novo mecanismo de configuração.

## Pontos de extremidade

Quando um recurso de ponto de extremidade é definido no manifesto do serviço, a Malha de Serviço atribui portas do intervalo de portas reservadas do aplicativo. Além disso, os serviços também podem solicitar uma porta específica em um recurso. As réplicas do serviço em execução em nós de cluster diferente podem receber números de porta diferentes, enquanto as réplicas do mesmo serviço em execução no mesmo nó compartilham a mesma porta. Essas portas podem ser usadas pelas réplicas do serviço para várias finalidades, por exemplo, replicação, escuta de solicitações de clientes etc.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Confira [Configurando serviços confiáveis com monitoração de estado](../Service-Fabric/service-fabric-reliable-services-configuration.md) para ler mais sobre como fazer referência a pontos de extremidade a partir do arquivo de configurações do pacote de configuração (Settings.xml).

## Amostra
O manifesto do serviço a seguir define 1 recurso de ponto de extremidade TCP e 2 recursos de ponto de extremidade HTTP no elemento &lt;Recursos&gt;.

A ACL é automaticamente aplicada aos pontos de extremidade HTTP pela Malha de Serviço.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=July15_HO2-->
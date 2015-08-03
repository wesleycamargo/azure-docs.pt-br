<properties
   pageTitle="Tráfego de replicação seguro dos serviços com estado da Malha de Serviços do Azure"
   description="Quando a replicação é habilitada, os serviços com estado replicam seu estado de uma réplica primária para réplicas secundárias e esse tráfego precisa ser protegido contra interceptação e violação."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Proteger o tráfego de replicação

Quando a replicação é habilitada, serviços com estado replicam o estado entre réplicas. Esta página aborda como configurar a proteção de tráfego.

Há dois tipos de configurações de segurança com suporte:

- X509: Usa o certificado X509 usa para proteger o canal de comunicação. Os usuários devem certificar chaves privadas com ACL para permitir que os processos de host de Ator/Serviço use as credenciais de certificado.
- Windows: usa a segurança do Windows (requer o Active Directory) para proteger o canal de comunicação.

A seção a seguir mostra como configurar os dois tipos de configurações acima. A configuração "CredentialType" determina que tipo está sendo usado.

## CredentialType=X509

### Nomes da configuração

|Nome|Comentários|
|----|-------|
|StoreLocation|Local do repositório para localizar o certificado: CurrentUser ou LocalMachine|
|StoreName|Nome do repositório para localizar o certificado|
|FindType|Identifica o tipo de valor fornecido pelo parâmetro FindValue: FindBySubjectName ou FindByThumbPrint|
|FindValue|Pesquise no destino para encontrar o certificado|
|AllowedCommonNames|Uma lista separada por vírgulas de nomes comuns/nomes DNS de certificados usados pelos duplicadores.|
|IssuerThumbprints|Uma lista separada por vírgulas de impressões digitais de certificado de emissor. Quando especificado, o certificado de entrada é validado se emitido por uma das entradas na lista. A validação de cadeia sempre é executada.|
|RemoteCertThumbprints|Uma lista separada por vírgulas de impressões digitais de certificado usadas pelos duplicadores.|
|ProtectionLevel|Indica como os dados são protegidos: Sign, EncryptAndSign ou None|

## CredentialType=Windows

### Nomes da configuração

|Nome|Comentários|
|----|-------|
|ServicePrincipalName|Nome da entidade de serviço registrado para o serviço. Pode ser vazio se os processos de host de serviço/ator forem executados como uma conta de computador (por exemplo: NetworkService, LocalSystem etc.)|
|WindowsIdentities|Uma lista separada por vírgula de identidades do Windows de todos os processos de host de serviço/ator.
|ProtectionLevel|Indica como os dados são protegidos: Sign, EncryptAndSign ou None|

## Exemplos

### Exemplo 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Exemplo 2: CredentialType=Windows
Este trecho de código mostra um exemplo de quando todos os processos de host de serviço/ator são executados como NetworkService ou LocalSystem. O ServicePrincipalName está vazio.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Exemplo 3: CredentialType=Windows
Este trecho de código mostra um exemplo de quando todos os processos de host de serviço/ator são executados como uma conta de serviço gerenciada em grupo com um ServicePrincipalName válido.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO4-->
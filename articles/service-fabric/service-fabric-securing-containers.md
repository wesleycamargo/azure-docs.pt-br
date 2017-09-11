---
title: "Segurança de contêiner do Azure Service Fabric | Microsoft Docs"
description: "Saiba como proteger serviços de contêiner."
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a262730aec6ce5a1c6f3b7d2d41056a6e6edfbe0
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---

# <a name="container-security"></a>Segurança do contêiner

O Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado que está instalado em nós em um cluster do Windows ou Linux (versão 5.7 ou superior). Além disso, o Service Fabric também dá suporte a gMSA (Contas de Serviço Gerenciado do grupo) para contêineres do Windows. 

## <a name="certificate-management-for-containers"></a>Gerenciamento de certificados para contêineres

Você pode proteger seus serviços de contêiner especificando um certificado. O certificado deve ser instalado em nós do cluster. As informações do certificado são fornecidas no manifesto do aplicativo sob a marca `ContainerHostPolicies`, como mostra o trecho de código a seguir:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Ao iniciar o aplicativo, o tempo de execução lê os certificados e gera um arquivo PFX e senha para cada certificado. Este arquivo PFX e a senha podem ser acessados dentro do contêiner usando as seguintes variáveis de ambiente: 

* **Certificate_[CodePackageName]_[CertName]_PFX**
* **Certificate_[CodePackageName]_[CertName]_Password**

O serviço ou o processo de contêiner é responsável por importar o arquivo PFX para o contêiner. Para importar o certificado, você pode usar scripts do `setupentrypoint.sh` ou executar o código personalizado no processo do contêiner. Segue o código de exemplo em C# para importar o arquivo PFX:

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Esse certificado PFX pode ser usado para autenticar o aplicativo ou serviço ou comunicação segura com outros serviços.


## <a name="set-up-gmsa-for-windows-containers"></a>Configurar o gMSA para contêineres do Windows

Para configurar o gMSA (Contas de Serviço Gerenciado de grupo), um arquivo de especificação de credencial (`credspec`) é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O arquivo `credspec` deve conter as informações da conta gMSA. Para saber mais sobre o arquivo `credspec`, veja [Contas de serviço](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). A especificação de credenciais e a marca `Hostname` são especificadas no manifesto do aplicativo. A marca `Hostname` deve corresponder ao nome de conta de gMSA em que o contêiner é executado.  A marca `Hostname` permite que o contêiner para se autenticar em outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no trecho a seguir:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Próximas etapas

* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)


---
title: Executar um serviço do Azure Service Fabric  como um usuário AD ou grupo AD | Microsoft Docs
description: Saiba como executar um serviço como um usuário do Active Directory em um cluster autônomo do Service Fabric Windows.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 1cf23a8f564553e65ac2c0fd34d44d81fe2327ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como um grupo ou usuário do Active Directory
Usando o Azure Service Fabric, é possível proteger aplicativos em execução no cluster em contas de usuário diferentes. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros. Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. Para um cluster do Windows Server autônomo, você pode executar um serviço como um grupo de Conta de Serviço gerenciado (gMSA) ou um [usuário do Active Directory ou grupo](service-fabric-run-service-as-gmsa.md) usando uma política de RunAs. Observe que esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (Azure AD).

Ao usar um usuário de domínio ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

O exemplo a seguir mostra um usuário do Active Directory denominado *TestUser* com a senha de domínio criptografada usando um certificado chamado *MyCert*. É possível usar o comando `Invoke-ServiceFabricEncryptText` do Powershell para criar o texto cifrado secreto. Consulte [Gerenciamento de segredos em aplicativos do Service Fabric](service-fabric-application-secret-management.md) para obter detalhes.

É necessário implantar a chave privada do certificado para descriptografar a senha no computador local usando um método fora de banda (no Azure, isso ocorre por meio do Azure Resource Manager). Em seguida, quando o Service Fabric implanta o pacote de serviço no computador, ele é capaz de descriptografar o segredo e (juntamente com o nome de usuário) autenticar com o Active Directory para execução com essas credenciais.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você também deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como uma próxima etapa, leia os seguintes artigos:
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

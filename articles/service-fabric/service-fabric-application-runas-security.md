---
title: Executar um serviço do Azure Service Fabric nas contas de sistema e de segurança local | Microsoft Docs
description: Saiba como executar um aplicativo do Service Fabric nas contas de sistema e de segurança local.  Criar entidades de segurança e aplique a política Run-As para executar com segurança seus serviços.
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
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como uma conta de usuário ou conta de sistema local
Usando o Azure Service Fabric, é possível proteger aplicativos em execução no cluster em contas de usuário diferentes. Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário local ou em uma conta de sistema local, o que é feito especificando uma política RunAs no manifesto do aplicativo. Os tipos de conta do sistema local com suporte são **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

É possível também definir e criar grupos de usuários para que um ou mais usuários possam ser adicionados a cada grupo para serem gerenciados em conjunto. Isso será útil quando houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo.

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Criar grupos de usuários locais
É possível definir e criar grupos de usuários que permitem que um ou mais usuários sejam adicionados a um grupo. Isso será útil se houverem vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo. O exemplo a seguir mostra um grupo local chamado **LocalAdminGroup** com privilégios de administrador. Dois usuários, Customer1 e Customer2, tornam-se membros desse grupo local este exemplo de manifesto do aplicativo:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>Criar usuários locais
É possível criar um usuário local que pode ser usado para ajudar a proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Entidades do manifesto do aplicativo, o Service Fabric cria contas de usuário local em máquinas nas quais o aplicativo foi implantado. Por padrão, essas contas não têm os mesmos nomes especificados no manifesto do aplicativo (por exemplo, Customer3 no exemplo de manifesto do aplicativo a seguir). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Se um aplicativo exigir que a conta de usuário e senha sejam iguais em todos os computadores (por exemplo, para habilitar a autenticação NTLM), o manifesto do cluster deve definir **NTLMAuthenticationEnabled** como true. O manifesto do cluster também deve especificar um **NTLMAuthenticationPasswordSecret** que será usado para gerar a mesma senha em todos os computadores.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Atribuir políticas aos pacotes de códigos do serviço
A seção **RunAsPolicy** para uma **ServiceManifestImport** especifica a conta da seção de entidades que deve ser usada para executar um pacote de códigos. Ela também associa os pacotes de código do manifesto do serviço com contas de usuário na seção entidades. Você pode especificar isso para os pontos de entrada de configuração ou principal, ou especificar `All` para aplicar a ambos. O exemplo a seguir mostra a aplicação de políticas diferentes:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Se **EntryPointType** não for especificado, o padrão será definido como `EntryPointType=”Main”`. A especificação de um **SetupEntryPoint** é especialmente útil quando você deseja executar determinadas operações de instalação de privilégio elevado em uma conta do sistema. Para obter mais informações, consulte [Executar um script de inicialização do serviço como uma conta de usuário ou sistema local](service-fabric-run-script-at-service-startup.md). O código de serviço real pode ser executado em uma conta de baixo privilégio.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política padrão a todos os pacotes de códigos de serviço
Use a seção **DefaultRunAsPolicy** para especificar uma conta de usuário padrão para todos os pacotes de códigos que não tenham uma **RunAsPolicy** específica definida. Quando a maioria dos pacotes de códigos especificados no manifesto do serviço usado por um aplicativo precisar ser executada com o mesmo usuário, o aplicativo poderá definir apenas uma política RunAs padrão nessa conta de usuário. O exemplo a seguir especifica que quando um pacote de códigos não tiver uma **RunAsPolicy** especificada, ele deverá ser executado em uma **MyDefaultAccount** especificada na seção Entidades.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

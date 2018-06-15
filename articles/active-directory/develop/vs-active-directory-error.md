---
title: Como diagnosticar erros com o serviço conectado do Azure Active Directory
description: O serviço conectado do Active Directory detectou um tipo de autenticação incompatível
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31784880"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosticando erros com o Serviço Conectado do Azure Active Directory

Ao detectar o código de autenticação anterior, o servidor conectado do Azure Active Directory detectou um tipo de autenticação incompatível.

Para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser compilado.  Se este erro ocorrer e você não tiver o código de autenticação anterior em seu projeto, compile o projeto novamente e repita a operação.

## <a name="project-types"></a>Tipos de projeto

O serviço conectado verifica o tipo de projeto que está sendo desenvolvido para que possa injetar a lógica de autenticação adequada ao projeto. Se houver um controlador que deriva de `ApiController` no projeto, o projeto será considerado um projeto WebAPI. Se houver apenas controladores que derivam de `MVC.Controller` no projeto, o projeto será considerado um projeto MVC. O serviço conectado não oferece suporte a qualquer outro tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatível

O serviço conectado também verifica configurações de autenticação configuradas anteriormente ou que são compatíveis com o serviço. Se todas as configurações estiverem presentes, ele será considerado um caso reentrante e o serviço conectado será aberto e exibirá as configurações.  Se apenas algumas das configurações estiverem presentes, ele será considerado um caso de erro.

Em um projeto do MVC, o serviço conectado verifica qualquer uma das configurações a seguir, que são resultado do uso anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o serviço conectado verifica qualquer uma das configurações a seguir em um projeto de API da Web, que são resultado do uso anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o serviço conectado tenta detectar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O serviço conectado detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows
* Contas Individuais de Usuário
* Contas organizacionais

Para detectar a Autenticação do Windows em um projeto do MVC, o serviço conectado procura pelo `authentication` elemento em seu `web.config` arquivo.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Para detectar a Autenticação do Windows em um projeto de API da Web, o serviço conectado procura pelo `IISExpressWindowsAuthentication` elemento em seu `.csproj` arquivo do projeto:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Para detectar a autenticação de Contas de Usuário Individuais, o serviço conectado procura o elemento de pacote no seu `packages.config` arquivo.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Para detectar uma forma anterior de autenticação de Conta Organizacional, o serviço conectado procura o seguinte elemento em`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e tente adicionar o serviço conectado novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).
---
title: Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Abora as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5818efb315a0452beea03cde1adc657a9520dcac
ms.openlocfilehash: 1fe3f3a697618bec5d314c6ebf161da37efc1346
ms.lasthandoff: 03/01/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo do Azure AD

Este artigo descreve como configurar aplicativos para direcionar os usuários para uma página inicial personalizada quando eles acessarem os aplicativos do painel de acesso do Azure Active Directory (Azure AD) e o iniciador do aplicativo do Office 365.

>[!NOTE]
>O Proxy de Aplicativo é um recurso que estará disponível somente se você tiver atualizado para a edição Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
>

Usando o módulo do Powershell do Azure AD, você pode definir URLs de home page personalizadas para instâncias quando quiser que os usuários cheguem em uma página específica no aplicativo (*por exemplo, https://expenseApp-contoso.msappproxy.net/login/login.aspx*).

>[!NOTE]
>Quando você conceder aos usuários acesso a aplicativos publicados, os aplicativos são exibidos no [painel de acesso do Azure AD](active-directory-saas-access-panel-introduction.md) e [lançador de aplicativo do Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).
>

Quando os usuários iniciam o aplicativo, são direcionados por padrão para a URL de domínio raiz do aplicativo publicado. A página de aterrissagem é normalmente definida como a URL da home page. Por exemplo, para o aplicativo de back-end, http://ExpenseApp, a URL é publicada como *https://expenseApp-contoso.msappproxy.net*. Por padrão, a URL da home page é definida como *https://expenseApp-contoso.msappproxy.net*.

## <a name="determine-the-home-page-url"></a>Determinar a URL da home page

Antes de definir a URL da home page, tenha em mente o seguinte:

* Garanta que o caminho especificado seja um caminho de subdomínio da URL raiz do domínio.

 Por exemplo, se o aplicativo publicado puder ser acessado de uma URL de domínio raiz (por exemplo, https://intranet-contoso.msappproxy.net/), a URL da home page configurada deverá começar com https://intranet-contoso.msappproxy.net/.

* Se a URL do domínio raiz é, por exemplo, https://apps.contoso.com/app1/, a URL da página inicial que você configura deve começar com https://apps.contoso.com/app1/.

* Se você fizer uma alteração no aplicativo publicado, essa alteração poderá redefinir o valor da URL da home page. Portanto, quando você decidir atualizar o aplicativo, você deve verificar novamente e, se necessário, atualize a URL da home page.

Na próxima seção, você mostre Configurando uma URL da home page personalizada para o aplicativo publicado. 

## <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Antes de definir uma URL personalizada de home page usando o PowerShell, instale um pacote não padrão do módulo PowerShell do Azure AD. Baixe esse pacote da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), que usa o ponto de extremidade de API do Graph. 

Para instalar o pacote usando o PowerShell, faça o seguinte:

1. Abra uma janela padrão do PowerShell e, em seguida, execute o seguinte comando:

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Se você estiver executando o comando como um não administrador, use a opção **-scope currentuser** opção.
2. Durante a instalação, selecione **Y** para instalar dois pacotes do Nuget.org. Ambos os pacotes são necessários. 

## <a name="set-a-custom-home-page-url-by-using-the-azure-ad-powershell-module"></a>Defina uma URL de home page personalizada usando o módulo do PowerShell do Azure AD

Agora que você instalou o módulo do PowerShell do Azure AD, você está pronto para definir a URL da home page. Para fazer isso, siga as instruções nas próximas duas seções.

### <a name="step-1-find-the-objectid-of-the-app"></a>Etapa 1: Localizar a ObjectID do aplicativo

Obtenha o ID do objeto do aplicativo e, em seguida, procure o aplicativo por sua home page.

1. Abra o PowerShell e, em seguida, importe o módulo do Azure AD usando o seguinte comando:

    ```
    Import-Module AzureAD
    ```

2. Entrar módulo Azure AD usando o seguinte cmdlet e, em seguida, siga as instruções na tela. Entre como o administrador de locatários.

    ```
    Connect-AzureAD
    ```
3. Use este cmdlet para localizar o aplicativo com base na home page que contém *iddemo sharepoint*. Para editar o aplicativo, substitua o seguinte valor com o valor adequado para o aplicativo.

    ```
    Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
    ```
4. Você deve obter um resultado semelhante ao mostrado aqui. Copie o GUID (ObjectID) para usá-lo em “Etapa 2: Atualizar a URL da home page".

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="step-2-update-the-home-page-url"></a>Etapa 2: Atualizar a URL da home page

No mesmo módulo do PowerShell que você usou em "etapa 1: localizar o ID do objeto do aplicativo," faça o seguinte:

1. Confirme que você tenha o aplicativo correto e substitua *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* com o GUID (ObjectID) que você copiou na etapa anterior.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Agora que você confirmou o aplicativo, está pronto para atualizar a home page da seguinte maneira.

2. Crie um objeto de aplicativo em branco para armazenar as alterações que você deseja fazer.  

 >[!NOTE]
 >Isso é apenas uma variável para armazenar os valores que você quer atualizar, portanto nada foi realmente criado.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Defina a URL da home page como o valor desejado. O valor deve ser um caminho de subdomínio do aplicativo publicado. Por exemplo, se você alterar a URL da home page de *https://sharepoint-iddemo.msappproxy.net/* para *https://sharepoint-iddemo.msappproxy.net/hybrid/*, os usuários do aplicativo irão diretamente para a home page personalizada.

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Faça a atualização usando o GUID (ObjectID) que você copiou na "etapa 1: localizar o ID do objeto do aplicativo."

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. Para confirmar que a alteração foi bem-sucedida, reinicie o aplicativo.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Todas as alterações que você fizer no aplicativo poderão redefinir a URL da home page. Se isso ocorrer, repita o processo em "etapa 2: atualizar a URL da home page."

## <a name="next-steps"></a>Próximas etapas

[Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Habilitar o Proxy de aplicativo no Portal do Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)


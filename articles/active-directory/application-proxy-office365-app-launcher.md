---
title: Definir uma home page personalizada para seu aplicativo publicado usando o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
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
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Definir uma home page personalizada para seu aplicativo publicado usando o Proxy de Aplicativo do Azure AD

Este artigo discute como configurar seu aplicativo para direcionar os usuários a uma home page personalizada, quando os usuários acessarem o aplicativo do painel de acesso do Azure AD e do Inicializador do Aplicativo do Office 365.

>[!NOTE]
>O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 
 
Com o módulo do Powershell do Azure AD, você pode definir URLs de home page personalizadas para essas instâncias quando quiser que os usuários cheguem em uma página específica de seu aplicativo. Por exemplo, https://expenseApp-contoso.msappproxy.net/login/login.aspx.

>[!NOTE]
>Quando você fornece aos usuários acesso aos seus aplicativos publicados, exibimos seus aplicativos no [Painel de acesso do Azure AD](active-directory-saas-access-panel-introduction.md) e no [Inicializador de Aplicativo do Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher). 
>

Quando os usuários iniciam seus aplicativos, são direcionados por padrão para a URL de domínio raiz do aplicativo publicado. A página de aterrissagem é normalmente definida como a URL da home page. Por exemplo, para esse aplicativo de back-end, http://ExpenseApp é publicado como https://expenseApp-contoso.msappproxy.net. Por padrão, a URL da home page é definida como https://expenseApp-contoso.msappproxy.net.

## <a name="determine-the-home-page-url"></a>Determinar a URL da home page

Há várias coisas que você precisa saber antes de configurar a URL da home page:

* Você deve garantir que o caminho especificado seja um caminho de subdomínio da URL raiz do domínio.

 Por exemplo, se o aplicativo publicado for acessível a partir de uma URL de home page https://intranet-contoso.msappproxy.net/, a URL da home page configurada deverá começar com https://intranet-contoso.msappproxy.net/. 
 
* Se a URL da home page for https://apps.contoso.com/app1/, a URL da home page deverá começar com https://apps.contoso.com/app1/.

* Se você fizer uma alteração no aplicativo publicado, isso poderá redefinir o valor da URL da home page. Portanto, quando você decidir atualizar o aplicativo, deverá verificar novamente a URL da home page e talvez atualizá-la.


Na próxima seção, você verá como configurar uma URL de home page personalizada para seus aplicativos publicados. 

## <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Antes de definir uma URL personalizada de home page usando o Powershell, você precisa instalar um pacote não padrão do módulo PowerShell do Azure AD.  Baixe esse pacote na [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), que usa o ponto de extremidade da API do GRAPH. 

**Para instalar o pacote usando o Powershell:**

1. Abra o PowerShell padrão.
2. Execute o comando a seguir:

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 Se você estiver executando isso como um usuário não administrador, será necessário usar a opção _-scope currentuser_.
3. Durante a instalação, selecione "Y" para instalar dois pacotes do Nuget.org.  Ambos são necessários para usar o pacote. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Defina uma URL de home page personalizada usando o módulo do Powershell do Azure AD

Agora que o módulo do Powershell do Azure AD está instalado, você está pronto para definir a URL da home page usando duas etapas simples.

1. Localize o aplicativo que você quer atualizar.
2. Atualize a URL da home page do aplicativo.

###<a name="step-1--find-the-objectid-of-the-application"></a>Etapa 1 - Localize A ID de Objeto do aplicativo

Primeiro você deve obter a ID do Objeto do aplicativo e, depois, procurar o aplicativo pela home page.

1. Abra o PowerShell.
2. Importe o módulo do Azure AD.
  
 ```
 Import-Module AzureAD
 ```
3. Faça logon no módulo do Azure AD.  Use o cmdlet abaixo e siga as instruções na tela. Lembre-se de fazer logon como o administrador do locatário.
 
 ```
 Connect-AzureAD
 ```
4. O cmdlet abaixo localiza os aplicativos com base na home page que contém _sharepoint-iddemo_. Este é o aplicativo que você quer editar. Será necessário substituir esse valor pelo valor adequado ao seu aplicativo.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. Você deverá ver um resultado semelhante à resposta abaixo. O GUID (o valor de ID do Objeto abaixo) é o item que você precisará copiar.
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. Copie o valor do GUID (ID do Objeto). Você precisará dele para a próxima etapa.


###<a name="step-2--update-the-homepage-url"></a>Etapa 2 – Atualizar a URL da home page

Use o mesmo módulo do PowerShell para atualizar a URL da home page, assim como você fez para localizar a ID do aplicativo. Após fazer logon no PowerShell, execute as etapas a seguir:

1. Confirme se o aplicativo é o correto e substitua _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ pelo valor da ID do Objeto do aplicativo que você copiou na Etapa 1 acima. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 Agora que você confirmou o aplicativo, está pronto para atualizar a home page da seguinte maneira.
 
2. Crie um objeto de aplicativo em branco para armazenar as alterações que você deseja fazer. Isso é apenas uma variável para armazenar os valores que você quer atualizar, portanto nada foi realmente criado.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. Defina a home page como o valor que você deseja. Lembre-se de que deve ser um caminho de subdomínio do aplicativo publicado. Por exemplo, se você alterar a home page de _https://sharepoint-iddemo.msappproxy.net/_ para _https://sharepoint-iddemo.msappproxy.net/hybrid/_, os usuários irão diretamente para a home page personalizada.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. A última coisa que você precisa fazer é a atualização. Lembre-se de usar o GUID que você copiou na Etapa 1 acima.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. Agora você precisa confirmar a home page personalizada iniciando o aplicativo novamente, afim de verificar se a alteração foi bem-sucedida.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>Lembre-se de que as alterações feitas em seu aplicativo podem redefinir a URL da Home Page. Nesse caso, você precisará repetir esse processo.

##<a name="next-steps"></a>Próximas etapas

[Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Habilitar o Proxy de aplicativo no Portal do Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->



---
title: O que é o agente do Azure AD Connect Admin - Azure AD Connect | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorar seu ambiente local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687240"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente Administrativo do Azure AD Connect? 
Agente do Azure AD Connect administração é um novo componente do Azure Active Directory Connect que pode ser instalado em um servidor do Azure Active Directory Connect. Ele é usado para coletar dados específicos de seu ambiente do Active Directory que ajudam a um Microsoft engenheiro para solucionar problemas quando você abrir um caso de suporte de suporte. 

>[!NOTE]
>O agente de administrador não está instalado e habilitado por padrão.  Você deve instalar o agente para coletar dados para ajudá-lo com casos de suporte.

Quando instalado, o Azure AD Connect administração Agent aguarda solicitações específicas para dados do Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e o envia para o Azure Active Directory, onde ela é apresentada no Microsoft suporte engenheiro. 

As informações que o agente do Azure AD Connect administração recupera do seu ambiente não são armazenadas de forma alguma – ele só será exibido para o engenheiro de suporte da Microsoft para ajudá-lo na investigação e solução de problemas do Azure Active Directory Connect caso de suporte relacionadas que você abriu o Azure AD Connect administração Agent não está instalado no servidor do Azure AD Connect, por padrão. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalar o agente do Azure AD Connect administração no servidor do Azure AD Connect 
Os binários do Azure AD Connect administração Agent são colocados no servidor de acesso do AAD Connect. Para instalar o agente, faça o seguinte: 



1. Abra o powershell no modo de administrador 
2. Navegue até o diretório onde o aplicativo está localizado cd "C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles" 
3. Executar o aplicativo AADConnectAdminAgentSetup.exe 
 
Quando solicitado, insira suas credenciais de administrador global do AD do Azure. 

>[!NOTE]
>Há um problema conhecido em que você será solicitado para suas credenciais várias vezes. Isso será corrigido na próxima versão.

Depois que o agente está instalado, você verá os seguintes duas programas na lista de "Adicionar ou remover programas" no painel de controle do seu servidor: 

![Agente de Admin](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quais dados em meu serviço de sincronização são mostrados para o engenheiro de serviço da Microsoft? 
Quando você abrir um caso de suporte o engenheiro de suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes no Active Directory, o espaço do conector do Active Directory no servidor do Azure Active Directory Connect, o espaço do conector do Active Directory do Azure no Azure Servidor Active Directory Connect e o metaverso no servidor do Azure Active Directory Connect. 

O engenheiro de suporte da Microsoft não é possível alterar todos os dados em seu sistema e não pode ver todas as senhas. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft para acessar meus dados? 
Depois que o agente está instalado, se você não quiser que o engenheiro de serviço da Microsoft para acessar seus dados para uma chamada de suporte, você pode desabilitar a funcionalidade, modificando o arquivo de configuração de serviço, conforme descrito abaixo: 

1.  Abra **C:\Program Files\Microsoft Azure AD Connect administração Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
2.  Desabilitar **UserDataEnabled** configuração conforme mostrado abaixo. Se **UserDataEnabled** configuração existir e estiver definida como true e, em seguida, defini-lo como false. Se a configuração não existir, adicione a configuração conforme mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Salve o arquivo de configuração.
4.  Reinicie o serviço de agente de administração do Azure do AD Connect conforme mostrado abaixo

![Agente de Admin](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).

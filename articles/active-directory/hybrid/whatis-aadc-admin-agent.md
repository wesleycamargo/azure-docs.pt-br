---
title: O que é o agente do Azure AD Connect Admin - Azure AD Connect | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorar seu ambiente local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488189"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o agente do Azure AD Connect administrador? 
Agente do Azure AD Connect administração é um novo componente do Azure Active Directory Connect que está instalado em um servidor do Azure Active Directory Connect. Ele é usado para coletar dados específicos de seu ambiente do Active Directory que ajudam a um Microsoft engenheiro para solucionar problemas quando você abrir um caso de suporte de suporte.

Quando instalado, o Azure AD Connect administração Agent aguarda solicitações específicas para dados do Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e o envia para o Azure Active Directory, onde ela é apresentada no Microsoft suporte engenheiro.

As informações que o agente do Azure AD Connect administração recupera do seu ambiente não são armazenadas de forma alguma – ele só será exibido para o engenheiro de suporte da Microsoft para ajudá-lo na investigação e solução de problemas do Azure Active Directory Connect caso de suporte relacionadas que você abriu

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Como o agente do Azure AD Connect Admin é instalado no servidor do Azure AD Connect? 
Depois que o agente de administração estiver instalado, você verá os seguintes duas programas na lista de "Adicionar ou remover programas" no painel de controle do seu servidor: 

![Agente de Admin](media/whatis-aadc-admin-agent/adminagent1.png)

Não remover ou desinstalar os programas conforme eles são uma parte crítica da instalação do Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quais dados em meu serviço de sincronização são mostrados para o engenheiro de serviço da Microsoft?
Quando você abre um caso de suporte, o engenheiro de suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes no Active Directory, o espaço do conector do Active Directory no servidor do Azure Active Directory Connect, o espaço do conector do Active Directory do Azure no Azure Servidor Active Directory Connect e o metaverso no servidor do Azure Active Directory Connect.

O engenheiro de suporte da Microsoft não é possível alterar todos os dados em seu sistema e não pode ver todas as senhas.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft para acessar meus dados? 
 
Se, você não quiser que o engenheiro de serviço da Microsoft para acessar seus dados para uma chamada de suporte, é possível indicar isso quando você abre um suporte chamar no portal: 

  1.    Abra **C:\Program Files\Microsoft Azure AD Connect administração Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
  2.    Desabilitar **UserDataEnabled** configuração conforme mostrado abaixo. Se **UserDataEnabled** configuração existir e estiver definida como true e, em seguida, defini-lo como false. Se a configuração não existir, adicione a configuração conforme mostrado abaixo.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Salve o arquivo de configuração.
  4.    Reinicie o serviço de agente de administração do Azure do AD Connect conforme mostrado abaixo

![Agente de Admin](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
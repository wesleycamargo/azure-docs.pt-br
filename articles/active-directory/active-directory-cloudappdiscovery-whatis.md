---
title: "Localizar aplicativos na nuvem não gerenciados com o Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Fornece informações sobre a localização e o gerenciamento de aplicativos com o Cloud App Discovery, quais são seus benefícios e como ele funciona."
services: active-directory
keywords: cloud app discovery, gerenciamento de aplicativos
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Localizar aplicativos na nuvem não gerenciados com o Cloud App Discovery
## <a name="summary"></a>Resumo

O Cloud App Discovery no Azure Active Directory agora fornece uma experiência aprimorada de descoberta sem agente pela plataforma Microsoft Cloud App Security. Para usar o Cloud App Discovery, basta entrar com suas credenciais do Microsoft Azure Active Directory Premium P1. Essa atualização está disponível sem custo adicional para todos os clientes do Microsoft Azure Active Directory Premium P1. Comece com o artigo [Configurar o Cloud App Discovery no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started) e, em seguida, experimente [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> A experiência atual do Microsoft Azure Active Directory Cloud App Discovery com a descoberta baseada em agente será desativada em 5 de março de 2018 e, após essa data, os agentes serão desabilitadas e os dados excluídos. Tome providências antes de 5 de março para colocar tudo em funcionamento na nova experiência e evitar a interrupção do serviço.  
 
**Com o Cloud App Discovery é possível:**

* Descubra os aplicativos em nuvem usados e meça o uso pelo número de usuários, volume de tráfego ou número de solicitações Web para o aplicativo.
* Identificar os usuários que estão usando um aplicativo.
* Exporte os dados para a análise offline.
* Coloque esses aplicativos sob controle da TI e habilite o logon único para o gerenciamento de usuário.

## <a name="how-it-works"></a>Como ele funciona
1. Os agentes de uso dos aplicativos são instalados nos computadores dos usuários.
2. As informações de uso do aplicativo coletadas pelos agentes são enviadas por um canal criptografado seguro para o serviço Cloud App Discovery.
3. O serviço Cloud App Discovery avalia os dados e gera relatórios.

![Diagrama do Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Próximas etapas
* [Considerações de privacidade e segurança no Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Configurações de Registro do Cloud App Discovery para Servidores Proxy com Portas Personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery - Log de Alteração de Agente ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)


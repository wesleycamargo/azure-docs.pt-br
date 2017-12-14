---
title: "Sincronização do Azure AD Connect: extensões do Directory | Microsoft Docs"
description: "Este tópico descreve o recurso de extensões de diretório no Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3ab8b02ad30315de23e5d8e7370cc385a53ecf3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões do Directory
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local. Esse recurso permite criar aplicativos de LOB que consumem atributos que você continua gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões de diretório do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Você pode ver os atributos disponíveis usando [gerenciador do Azure AD Graph](https://graphexplorer.azurewebsites.net/) e o [gerenciador do Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer), respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.
![Assistente de Extensão do Esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de Usuário e de Grupo
* Atributos de valor único: String, Boolean, Integer, Binary
* Atributos de vários valores: String, Binary


>[!NOTE]
> Embora o Azure AD Connect dê suporte à sincronização de atributos do AD com múltiplos valores para o Azure AD como extensões de diretório com múltiplos valores, não há nenhum recurso no Azure AD no momento que suporte o uso de extensões de diretório com múltiplos valores.

A lista de atributos é lida do cache de esquema criado durante a instalação do Azure AD Connect. Se você estendeu o esquema do Active Directory com atributos adicionais, o [esquema deve ser atualizado](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) para que esses novos atributos fiquem visíveis.

Um objeto no Azure AD pode ter até 100 atributos de extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for mais longo, ele será truncado pelo mecanismo de sincronização.

Durante a instalação do Azure AD Connect, é registrado um aplicativo no qual esses atributos estão disponíveis. Você pode ver esse aplicativo no Portal do Azure.  
![Aplicativo de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Esses atributos agora estão disponíveis por meio do Graph:  
![Grafo](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos são prefixados com extension\_{AppClientId}\_. O AppClientId tem o mesmo valor para todos os atributos em seu locatário do Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

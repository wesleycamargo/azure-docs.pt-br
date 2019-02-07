---
title: 'Sincronização do Azure AD Connect: Extensões de diretório | Microsoft Docs'
description: Este tópico descreve o recurso de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 2b8892873541b4f792caa3538bfc51fff6c2d15b
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495610"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: Extensões de diretório
É possível usar extensões de diretório para estender o esquema no Azure AD (Microsoft Azure Active Directory) com seus próprios atributos do Active Directory local. Esse recurso permite compilar aplicativos LOB ao consumir atributos que continuam gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões de diretório da API do Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://developer.microsoft.com/graph/). Você pode ver os atributos disponíveis usando o [Explorador do Microsoft Azure AD Graph](https://graphexplorer.azurewebsites.net/) e o [Explorador do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.

>[!NOTE]
>A caixa Atributos Disponíveis diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de Usuário e de Grupo
* Atributos de valor único: cadeia de caracteres, booliano, inteiro, binário
* Atributos de vários valores: cadeia de caracteres, binário


>[!NOTE]
> Embora o Azure AD Connect dê suporte à sincronização de atributos com valores múltiplos do Active Directory com o Azure AD como extensões de diretório com valores múltiplos, atualmente, não há nenhuma maneira de recuperar/consumir os dados carregados em atributos da extensão de diretório com valores múltiplos.

A lista de atributos é lida a partir do cache de esquema criado durante a instalação do Azure AD Connect. Se você estendeu o esquema do Active Directory com atributos adicionais, será necessário [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema), antes que esses novos atributos fiquem visíveis.

Um objeto no Azure Active Directory pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o mecanismo de sincronização irá truncá-lo.

Durante a instalação do Azure AD Connect, é registrado um aplicativo no qual esses atributos estão disponíveis. Você pode ver esse aplicativo no Portal do Azure.

![Aplicativo de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Os atributos são prefixados com a extensão \_{AppClientId}\_. AppClientId tem o mesmo valor para todos os atributos no locatário do Azure Active Directory.

Esses atributos agora estão disponíveis por meio da API do Microsoft Azure AD Graph. Você pode consultá-los usando o [Explorador do Microsoft Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Explorador do Microsoft Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Ou você pode consultar os atributos através da API do Microsoft Graph, usando o [Explorador do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> É necessário solicitar que o atributo seja retornado. Selecione explicitamente atributos como este: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Para obter mais informações, consulte [Microsoft Graph: usar parâmetros de consulta](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).

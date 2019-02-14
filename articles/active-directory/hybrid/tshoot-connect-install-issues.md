---
title: Solucionar problemas de instalação do Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e11a9c913b93f09058a0bde8982354870e547d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188198"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Solucionar problemas: problemas de instalação do Azure AD Connect

## <a name="recommended-steps"></a>**Etapas Recomendadas**
Verifique o [tipo de instalação do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) adequado a você. Se você atende aos critérios de instalação expressa, recomendamos que você escolha a instalação expressa. A instalação expressa oferece as opções mínimas necessárias para concluir a instalação e, portanto, é menor a probabilidade de haver problemas. 

No entanto, se você não atende aos critérios de instalação expressa e precisa fazer a instalação personalizada, veja algumas melhores práticas a serem seguidas para evitar problemas comuns. Para fins de simplicidade, apenas as opções seletivas são mencionadas aqui:

* Verifique se você é um administrador no computador no qual está instalando o AAD Connect. Faça logon no computador com as mesmas credenciais de administrador.

* Deixe todas as opções padrão na página seguinte, exceto em “Usar um SQL Server existente”, caso queira usar o SQL Server existente. Veja [mais detalhes](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) sobre como usar as opções de instalação personalizada. 

    ![Usar um SQL Server existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página seguinte, escolha a opção “Criar nova conta do AD” para evitar problemas de permissão com a conta existente.

    ![Conta da floresta do AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas comuns**

* [Problemas de conectividade com o Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemas de conectividade com o Azure Active Directory online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemas de permissão com o Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Pré-requisitos do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selecionar que tipo de instalação usar para o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introdução ao Azure AD Connect usando configurações expressas](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: atualização de uma versão anterior para a mais recente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: O que é um servidor de preparo?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [O que é o módulo PowerShell ADConnectivityTool?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Próximas etapas
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md).






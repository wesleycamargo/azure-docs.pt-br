---
title: 'Azure AD Connect: Solucionar problemas de âncora de origem durante a instalação | Microsoft Docs'
description: Este tópico fornece etapas para solucionar problemas com a âncora de origem durante a instalação.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114147"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Solução de problemas de âncora de origem durante a instalação
Este artigo explica os diferentes problemas que podem ocorrer durante a instalação e oferece maneiras de resolver esses problemas relacionados a âncora de origem.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de origem inválido no Active Directory do Azure

### <a name="custom-installation"></a>Instalação Personalizada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Active Directory do Azure, Azure AD Connect se aplica a mesma política, a menos que ele seja substituído pelo cliente. O assistente informará qual atributo foi lido. Além disso, o assistente avisará se você tentar substituir a política de âncora de origem.

Durante a operação de leitura, é possível que a política de âncora de origem no Azure Active Directory é inesperada. Nesse caso, o Azure AD Connect não sabe quais a âncora de origem usar e precisa de substituição manual.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Para resolver esse problema, você pode substituir manualmente a âncora de origem selecionando um atributo específico. Continuar com essa opção somente se você tiver certeza de qual atributo para selecionar. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se você alterar a política de âncora de origem, ele pode quebrar a associação entre os usuários locais e seus recursos do Azure associados.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação Expressa
Durante a instalação expressa, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Active Directory do Azure, Azure AD Connect aplica-se a mesma política. Não há nenhuma opção para fazer a substituição manual.

Durante a operação de leitura, é possível que a política de âncora de origem no Azure Active Directory é inesperada. Nesse caso, o Azure AD Connect não sabe o que deve ser a âncora de origem.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Para resolver esse problema, você precisará instalar novamente usando o modo personalizado e substituir manualmente a âncora de origem selecionando um atributo específico. Continuar com essa opção somente se você tiver certeza de qual atributo para selecionar. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se você alterar a política de âncora de origem, ele pode quebrar a associação entre os usuários locais e seus recursos do Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválido no mecanismo de sincronização
Durante a instalação, é possível do Azure AD Connect tenta configurar o mecanismo de sincronização usando uma âncora de origem inválido. Esta operação é provavelmente um problema do produto e a instalação do Azure AD Connect falhará. Entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) se você tiver esse problema.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
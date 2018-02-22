---
title: "Azure Active Directory Domain Services: alertas de solução de problemas | Microsoft Docs"
description: "Alertas de solução de problemas para os Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: ergreenl
ms.openlocfilehash: 8a0b30e6c975bd8f3bfbe70a64c085b729115f24
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – Alertas de solução de problemas
Este artigo fornece guias de solução de problemas para quaisquer alertas que podem ocorrer em seu domínio gerenciado.


Selecione as etapas de solução de problemas que correspondam ao ID de alerta ou mensagem que você encontrar.

| **ID do alerta** | **Mensagem de alerta** | **Resolução** |
| --- | --- | :--- |
| AADDS001 | *LDAP Seguro pela internet está habilitado para o domínio gerenciado. No entanto, o acesso à porta 636 não é protegido usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado a ataques de força bruta da senha.* | [Configuração incorreta de LDAP segura](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *O diretório do Azure AD associado a seu domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Diretório ausente](#aadds100-missing-directory) |
| AADDS101 | *Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.* | [O Azure AD B2C está em execução neste diretório](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Uma Entidade de Serviço necessária para o funcionamento correto do Azure AD Domain Services foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Entidade de Serviço ausente](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *O intervalo de endereços IP para a rede virtual na qual você habilitou os serviços de domínio do Azure AD está em um intervalo de IP público. Serviços de domínio do Azure AD devem ser habilitados em uma rede virtual com um intervalo de endereços IP privados. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Endereço está em um intervalo de IP público](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft não consegue alcançar os controladores de domínio para este domínio gerenciado. Isso pode ocorrer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é a existência de uma rota definida pelo usuário que bloqueia o tráfego de entrada da internet.* | [Erro de Rede](active-directory-ds-troubleshoot-nsg.md) |

## <a name="aadds100-missing-directory"></a>AADDS100: Diretório ausente
**Mensagem de alerta:**

*O diretório do Azure AD associado a seu domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

**Correção:**

Esse erro geralmente é causado por mover incorretamente sua assinatura do Azure para um novo diretório do Azure AD e excluir o antigo diretório do Azure AD que ainda está associado com os serviços de domínio do Azure AD.

Esse erro é irrecuperável. Para resolver, você deve [excluir seu domínio gerenciado existente](active-directory-ds-disable-aadds.md) e recriá-lo no seu novo diretório. Se você estiver tendo problemas em excluir, entre em contato com a equipe de produto do Azure Active Directory Domain Services para [obter suporte](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: O Azure AD B2C está em execução neste diretório
**Mensagem de alerta:**

*Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.*

**Correção:**

>[!NOTE]
>Para continuar usando os serviços de domínio do Azure AD, você deve recriar sua instância de serviços de domínio do Azure AD em um não diretório do Azure AD B2C.

Para restaurar seu serviço, siga estas etapas:

1. [Exclua seu domínio gerenciado](active-directory-ds-disable-aadds.md) do seu diretório do Azure AD existente.
2. Crie um novo diretório que não é um diretório do Azure AD B2C.
3. Siga o guia [Introdução](active-directory-ds-getting-started.md) para recriar um domínio gerenciado.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>ADDS103: Endereço está em um intervalo de IP público

**Mensagem de alerta:**

*O intervalo de endereços IP para a rede virtual na qual você habilitou os serviços de domínio do Azure AD está em um intervalo de IP público. Serviços de domínio do Azure AD devem ser habilitados em uma rede virtual com um intervalo de endereços IP privados. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

**Correção:**

> [!NOTE]
> Para resolver esse problema, você deve excluir seu domínio gerenciado existente e recriá-lo em uma rede virtual com um intervalo de endereços IP privados. Esse processo é disruptivo.

Antes de começar, leia a seção **espaço do endereço IP privado v4** [neste artigo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro da rede virtual, as máquinas podem fazer solicitações para recursos do Azure que estão no mesmo intervalo de endereços IP dos configurados para a sub-rede. No entanto, como a rede virtual é configurada para esse intervalo, essas solicitações serão direcionadas dentro da rede virtual e não alcançarão os recursos web pretendidos. Isso pode levar a erros imprevisíveis com os Azure Active Directory Domain Services.

**Se você possui o intervalo de endereços IP na internet que está configurada em sua rede virtual, esse alerta pode ser ignorado. No entanto, os Azure Active Directory Domain Services não podem confirmar o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] com essa configuração, pois isso poderá resultar em erros imprevisíveis.**


1. [Exclua seu domínio gerenciado](active-directory-ds-disable-aadds.md) do seu diretório.
2. Corrija o intervalo de endereços IP da sub-rede
  1. Navegue até a [página de Redes Virtuais no portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Selecione a rede virtual que você planeja usar para os Serviços de Domínio do Azure AD.
  3. Clique em **Espaço de Endereço** em Configurações
  4. Atualize o intervalo de endereços clicando no intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Verifique se o novo intervalo de endereço está em um intervalo IP privado. Salve suas alterações.
  5. Clique em **Sub-redes** no menu de navegação esquerdo.
  6. Clique na sub-rede que deseja editar na tabela.
  7. Atualize o intervalo de endereços e salve suas alterações.
3. Siga [o guia Introdução ao Uso dos Serviços de Domínio do Azure AD](active-directory-ds-getting-started.md) para recriar seu domínio gerenciado. Certifique-se de escolher uma rede virtual com um intervalo de endereços IP privados.
4. Para associar domínio às suas máquinas virtuais para seu novo domínio, siga [este guia](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Para garantir que o alerta seja resolvido, verifique a integridade do seu domínio em duas horas.


## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).

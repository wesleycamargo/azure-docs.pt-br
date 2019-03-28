---
title: Quais são as condições de localização no acesso condicional do Active Directory do Azure? | Microsoft Docs
description: Saiba como usar a condição de localização para controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 886bfb97b5aac303b99d3e8da0a19b38b7049012
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521781"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Quais são as condições de localização no acesso condicional do Active Directory do Azure? 

Com o [acesso condicional do Azure AD (Azure Active Directory)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados podem acessar seus aplicativos na nuvem. A condição de localização de uma política de acesso condicional permite que você ligue as configurações de controle de acesso para os locais de rede dos usuários.

Este artigo fornece as informações necessárias para configurar a condição de localização. 

## <a name="locations"></a>Locais

O Microsoft Azure AD permite o logon único em dispositivos, aplicativos e serviços de qualquer lugar na internet pública. Com a condição de localização, você pode controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário. Casos de uso comuns para a condição de localização são:

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa.

- Bloqueando o acesso para usuários que acessam um serviço de países e regiões específicas.

Um local é um rótulo para uma rede local que representa uma localização nomeada ou IPs confiáveis de autenticação multifator.


## <a name="named-locations"></a>Localizações nomeadas 

Com localizações nomeadas, você pode criar agrupamentos lógicos de intervalos de endereços IP, países e regiões. 

Você pode acessar suas localizações nomeadas na seção **Gerenciar** da página de acesso condicional.

![Locais](./media/location-condition/02.png)

 


A localização nomeada tem os seguintes componentes:

![Locais](./media/location-condition/42.png)

- **Nome** – O nome de exibição de uma localização nomeada.

- **Intervalos de IP**: um ou mais intervalos de endereço IPv4 no formato CIDR. Não há suporte para a especificação de um intervalo de endereços IPv6.

- **Marcar como local confiável** – Um sinalizador que você pode definir para uma localização nomeada indicar um local confiável. Normalmente, os locais confiáveis são áreas de rede controladas pelo departamento de TI. Além de acesso condicional, localizações nomeadas confiáveis também são usadas por relatórios de segurança de proteção de identidade do Azure e do Microsoft Azure AD para reduzir [falsos positivos](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Países/regiões** - esta opção permite que você selecione um ou mais países ou regiões para definir uma localização nomeada. 

- **Incluir áreas desconhecidas** – Alguns endereços IP não são mapeados para um país específico. Esta opção permite que você escolha se esses endereços IP devem ser incluídos na localização nomeada. Use essa configuração quando a política usando a localização nomeada deve aplicar-se a localizações desconhecidas.

O número de localizações que você pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. Você pode configurar um dos seguintes:

- Uma localização nomeada com até 1200 intervalos de IP.

- Um máximo de 90 localizações nomeadas com um intervalo IP atribuído a cada um delas.




## <a name="trusted-ips"></a>IPs confiáveis

Você também pode configurar intervalos de endereços IP que representam a Intranet local da sua organização nas [configurações do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esse recurso permite que você configure até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, confira [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se você tem IPs confiáveis configurados, eles aparecem como **IPS confiáveis MFA** na lista de locais para a condição de localização.   

### <a name="skipping-multi-factor-authentication"></a>Ignorando a autenticação multifator

Na página de configurações do serviço de autenticação multifator, você pode identificar os usuários da Intranet corporativa selecionando **Ignorar autenticação multifator para solicitações de usuários federados na minha Intranet**. Essa configuração indica que a declaração de rede corporativa interna, que é emitida pelo AD FS, deve ser confiável e usada para identificar o usuário como estando na rede corporativa. Para obter mais informações, consulte [Habilitar o recurso de IPs confiáveis por meio do acesso condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Depois de marcar essa opção, incluir a localização nomeada **IPS confiáveis de MFA** aplicará a todas as políticas com essa opção selecionada.

Para aplicativos móveis e de desktop, que têm vida útil de sessões ativas longa, o acesso condicional é reavaliado periodicamente. O padrão é uma vez a cada hora. Quando a declaração da rede corporativa interna é emitida somente no momento da autenticação inicial, o Microsoft Azure AD pode não ter uma lista de intervalos de IP confiáveis. Nesse caso, é mais difícil determinar se o usuário ainda está na rede corporativa:

1. Verifique se o endereço IP do usuário está em um dos intervalos de IP confiáveis.

2. Verifique se os três primeiros octetos do endereço IP do usuário correspondem aos 3 primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial porque é quando a declaração da rede corporativa interna foi originalmente emitida e o local do usuário foi validado.

Se ambas as etapas falharem, um usuário é considerado como não estando em um IP confiável.



## <a name="location-condition-configuration"></a>Configuração de condição de localização

Quando você configurar a condição de localização, você tem a opção de fazer a distinção entre:

- Qualquer local 
- Todos os locais confiáveis
- Locais selecionados

![Locais](./media/location-condition/01.png)

### <a name="any-location"></a>Qualquer local

Por padrão, selecionar **Qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Essa configuração não está limitada aos endereços IP que você configurou como localização nomeada. Quando seleciona **Qualquer local**, você ainda pode excluir locais específicos de uma política. Por exemplo, você pode aplicar uma política para todos os locais confiáveis de exceções de locais para definir o escopo para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção se aplica a:

- Todos os locais que foram marcados como local confiável
- **IPs confiáveis de MFA** (se configurado)


### <a name="selected-locations"></a>Locais selecionados

Com essa opção, você pode selecionar uma ou mais localizações nomeadas. Para uma política com essa configuração a ser aplicada, um usuário precisa se conectar de qualquer um dos locais selecionados. Quando você clica em **Selecionar**, é aberto o controle de seleção da rede nomeada que mostra a lista de redes nomeadas. A lista também mostra se o local de rede foi marcado como confiável. A localização nomeada chamada **IPs confiáveis de MFA** é usada para incluir as configurações de IP podem ser configuradas na página de configuração do serviço de autenticação multifator.

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="when-is-a-location-evaluated"></a>Quando um local é avaliado?

Políticas de acesso condicional são avaliadas quando: 

- Um usuário inicialmente entra em um aplicativo Web, aplicativo da área de trabalho ou móvel. 

- Um aplicativo da área de trabalho ou móvel que usa autenticação moderna, usa um token de atualização para adquirir um novo token de acesso. Por padrão, é uma vez a cada hora. 

Isso significa que para aplicativos da área de trabalho e móvel que utiliza autenticação moderna, uma alteração no local seria detectada dentro de uma hora da alteração do local de rede. Para aplicativos da área de trabalho e móveis que não utilizam autenticação moderna, a política é aplicada em cada solicitação de token. A frequência da solicitação pode variar de acordo com o aplicativo. Da mesma forma, para aplicativos Web, a política é aplicada no início da entrada e é adequada para o tempo de vida da sessão no aplicativo Web. Devido às diferenças nos tempos de vida da sessão em todos os aplicativos, o tempo entre a avaliação de política também varia. Sempre que o aplicativo solicitar um novo token de entrada, a política será aplicada.


Por padrão, o Microsoft Azure AD emite um token de hora em hora. Depois de remover a rede corporativa, em uma hora a política é aplicada para aplicativos que usam autenticação moderna.


### <a name="user-ip-address"></a>Endereço IP do usuário

O endereço IP que é usado na avaliação de política é o endereço IP público do usuário. Para dispositivos em uma rede privada, esse não é o IP do cliente do dispositivo do usuário na Intranet, é o endereço usado pela rede para se conectar à Internet pública. Se o dispositivo tiver apenas um endereço IPv6, a configurar a condição de localização não é suportado.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e download de localizações nomeadas

Quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou fazer o download de um arquivo CSV com os intervalos de IP. Um carregamento substitui os intervalos de IP na lista com aqueles do arquivo. Cada linha do arquivo contém um intervalo de endereços IP no formato CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxies e VPNs na nuvem 

Quando você usa um proxy ou solução VPN hospedado na nuvem, o endereço IP que o Microsoft Azure AD usa ao avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público dos usuários não é usado porque não há validações que venham de uma fonte confiável, portanto apresentaria um método para simular um endereço IP. 

Quando um proxy na nuvem está em uso, uma política que é usada para solicitar um dispositivo ingressado no domínio pode ser usada, ou a declaração corpnet interna do AD FS.



### <a name="api-support-and-powershell"></a>Suporte à API e PowerShell 

Ainda não há suporte para API e PowerShell para localizações nomeadas ou políticas de acesso condicional.





## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 

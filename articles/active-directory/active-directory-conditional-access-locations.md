---
title: "Condições de localização no acesso condicional do Azure Active Directory | Microsoft Docs"
description: "Saiba como usar a condição de localização para controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 47b4d70c991bd618ea4ea6e5d2fd1dea86911798
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Condições de localização no acesso condicional do Azure Active Directory 

Com o [acesso condicional do Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados podem acessar seus aplicativos na nuvem. A condição de localização de uma política de acesso condicional permite que você ligue as configurações de controle de acesso para os locais de rede dos usuários.

Este artigo fornece as informações necessárias para configurar a condição de localização. 

## <a name="locations"></a>Locais

O Microsoft Azure AD permite o logon único em dispositivos, aplicativos e serviços de qualquer lugar na internet pública. Com a condição de localização, você pode controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário. Casos de uso comuns para a condição de localização são:

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa  

- Bloqueando o acesso para usuários que acessam um serviço de países e regiões específicas. 

Um local é um rótulo para uma rede local que representa uma localização nomeada ou IPs confiáveis de autenticação multifator.


## <a name="named-locations"></a>Localizações nomeadas 

Com localizações nomeadas, você pode criar agrupamentos lógicos de intervalos de endereços IP, países e regiões. 

 A localização nomeada tem os seguintes componentes:

![Locais](./media/active-directory-conditional-access-locations/42.png)

- **Nome** – O nome de exibição de uma localização nomeada.

- **Intervalos de IP** – Endereço IP de um ou mais intervalos no formato CIDR.

- **Marcar como local confiável** – Um sinalizador que você pode definir para uma localização nomeada indicar um local confiável. Normalmente, os locais confiáveis são áreas de rede controladas pelo departamento de TI. Além de acesso condicional, localizações nomeadas confiáveis também são usadas por relatórios de segurança de proteção de identidade do Azure e do Microsoft Azure AD para reduzir [falsos positivos](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **País / regiões** – Esta opção permite que você selecione um ou mais países ou regiões para definir uma localização nomeada. 

- **Incluir áreas desconhecidas** – Alguns endereços IP não são mapeados para um país específico. Esta opção permite que você escolha se esses endereços IP devem ser incluídos na localização nomeada. Eles poderia ser selecionados quando a política usando a localização nomeada deveria aplicar a localizações desconhecidas.

O número de localizações que você pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. Você pode configurar:

- Uma localização nomeada com até 1200 intervalos de IP.

- Um máximo de 90 localizações nomeadas com um intervalo IP atribuído a cada um delas.




## <a name="trusted-ips"></a>IPs confiáveis

Você também pode configurar intervalos de endereços IP que representam a Intranet local da sua organização nas [configurações do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esse recurso permite que você configure até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, confira [IPs confiáveis](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Se você tem IPs confiáveis configurados, eles aparecem como **IPS confiáveis MFA** na lista de locais para a condição de localização.   

### <a name="skipping-multi-factor-authentication"></a>Ignorando a autenticação multifator

Na página de configurações do serviço de autenticação multifator, você pode identificar os usuários da Intranet corporativa selecionando **Ignorar autenticação multifator para solicitações de usuários federados na minha Intranet**. Essa configuração indica que a declaração de rede corporativa interna, que é emitida pelo AD FS, deve ser confiável e usada para identificar o usuário como estando na rede corporativa. Para obter mais informações, consulte [Habilitar o recurso de IPs confiáveis por meio do acesso condicional](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access).

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

![Locais](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Qualquer local

Por padrão, selecionar **Qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Essa configuração não está limitada aos endereços IP que você configurou como localização nomeada. Quando seleciona **Qualquer local**, você ainda pode excluir locais específicos de uma política. Por exemplo, você pode aplicar uma política para todos os locais confiáveis de exceções de locais para definir o escopo para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção se aplica a:

- Todos os locais que foram marcados como local confiável
- **IPs confiáveis de MFA** (se configurado)


### <a name="selected-locations"></a>Locais selecionados

Com essa opção, você pode selecionar uma ou mais localizações nomeadas. Para uma política com essa configuração a ser aplicada, um usuário precisa se conectar de qualquer um dos locais selecionados. Quando você clicar em **Selecionar** é aberto o controle de seleção da rede nomeada que mostra a lista de redes nomeadas. A lista também mostra se o local de rede foi marcado como confiável. A localização nomeada chamada **IPs confiáveis de MFA** é usada para incluir as configurações de IP podem ser configuradas na página de configuração do serviço de autenticação multifator.

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="when-is-a-location-evaluated"></a>Quando um local é avaliado?

Políticas de acesso condicional são avaliadas quando: 

- Um usuário inicialmente entra 

- O Microsoft Azure AD emite um token para o aplicativo de nuvem em que a política de acesso condicional foi definida. 

Por padrão, o Microsoft Azure AD emite um token de hora em hora. Depois de remover a rede corporativa, em uma hora a política é aplicada para aplicativos que usam autenticação moderna.


### <a name="user-ip-address"></a>Endereço IP do usuário

O endereço IP que é usado na avaliação de política é o endereço IP público do usuário. Para dispositivos em uma rede privada, esse não é o IP do cliente do dispositivo do usuário na Intranet, é o endereço usado pela rede para se conectar à Internet pública. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e download de localizações nomeadas

Quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou fazer o download de um arquivo CSV com os intervalos de IP. Um carregamento substitui os intervalos de IP na lista com aqueles do arquivo. Cada linha do arquivo contém um intervalo de endereços IP no formato CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxies e VPNs na nuvem 

Quando você usa um proxy ou solução VPN hospedado na nuvem, o endereço IP que o Microsoft Azure AD usa ao avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público dos usuários não é usado porque não há nenhuma validação que venha de uma fonte confiável, portanto apresentaria um método para simular um endereço IP. 

Quando um proxy na nuvem está em uso, uma política que é usada para solicitar um dispositivo ingressado no domínio pode ser usada, ou a declaração corpnet interna do AD FS.



### <a name="api-support-and-powershell"></a>Suporte à API e PowerShell 

Ainda não há suporte para API e PowerShell para localizações nomeadas ou políticas de acesso condicional.





## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 

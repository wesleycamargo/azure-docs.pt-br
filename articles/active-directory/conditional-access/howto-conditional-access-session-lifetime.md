---
title: Configurar o gerenciamento de sessão de autenticação com o acesso condicional do Azure Active Directory
description: Personalize a configuração de sessão de autenticação do AD do Azure incluindo de entrada do usuário na persistência de sessão de frequência e o navegador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff9ad850b111cf080447b699d35b4ef8205e006
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190213"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurar o gerenciamento de sessão de autenticação com o acesso condicional

Em implantações complexas, as organizações podem ter a necessidade de restringir sessões de autenticação. Alguns cenários podem incluir:

* Acesso a recursos de um dispositivo não gerenciado ou compartilhado
* Acesso a informações confidenciais em uma rede externa
* Usuários de alto impacto
* Aplicativos de negócios críticos

Controles de acesso condicional permitem que você crie políticas que se destinam a casos de uso específicos em sua organização sem afetar todos os usuários.

Antes de mergulhar em detalhes sobre como configurar a política, vamos examinar a configuração padrão.

## <a name="user-sign-in-frequency"></a>Frequência de entrada do usuário

Frequência de entrada define o período de tempo antes que um usuário é solicitado a entrar novamente ao tentar acessar um recurso.

A configuração de padrão do Azure Active Directory (Azure AD) para entrada do usuário na frequência é uma janela de 90 dias. Solicitar credenciais ao usuário geralmente se parece com algo sensato fazer, mas ele pode pela culatra: os usuários são treinados para inserir suas credenciais sem pensar pode, inadvertidamente, fornecem-los para uma solicitação de credenciais mal-intencionado.

Pode parecer alarmante não pedir para um usuário entrar novamente em 90 dias, na verdade, que qualquer violação de políticas de TI revogará a sessão. Alguns exemplos incluem (mas não estão limitados a) uma alteração de senha, um dispositivo não compatível ou conta desabilitar. Você pode explicitamente [revogar as sessões de usuários usando o PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). A configuração padrão do Azure AD se resume ao "não pergunte o que os usuários forneçam suas credenciais se não tiver alterado a postura de segurança de suas sessões".

Frequência de entrada configuração funciona com aplicativos que implementaram OATH2 ou OIDC protocolos de acordo com os padrões. A maioria dos aplicativos nativos da Microsoft para Windows, Mac e celular em conformidade com a configuração.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão persistente do navegador permite aos usuários permanecer conectado depois de fechar e reabrir a janela do navegador.

O padrão do AD do Azure para persistência de sessão de navegador permite que os usuários em dispositivos pessoais, escolha se deseja manter a sessão, mostrando um "Mantenha-se conectado?" Solicitar após uma autenticação bem-sucedida. Se a persistência do navegador estiver configurada no AD FS usando as diretrizes neste artigo [configurações do AD FS único logon](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), nós estão em conformidade com essa política e manter a sessão AD bem do Azure. Você também pode configurar se os usuários em seu locatário verão a "Mantenha-se conectado?" prompt alterando a configuração apropriada da empresa a identidade visual do painel no portal do Azure usando as diretrizes neste artigo [personalizar sua página de entrada do AD do Azure](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurando controles de sessão de autenticação

Acesso condicional é um recurso do Azure AD Premium e requer uma licença premium. Se você quiser saber mais sobre acesso condicional, consulte [O que é acesso condicional no Azure Active Directory?](overview.md#license-requirements-for-using-conditional-access)

> [!WARNING]
> Se você estiver usando o [tempo de vida de token configurável](../develop/active-directory-configurable-token-lifetimes.md) recurso atualmente em visualização pública, observe que não oferecemos suporte ao criar duas políticas diferentes para a mesma combinação de usuário ou aplicativo: uma com esse recurso e outro com recurso de tempo de vida de token configurável. A Microsoft planeja desativar o recurso de tempo de vida de token configurável no dia 15 de outubro e substituí-lo com o recurso de gerenciamento de sessão de autenticação de acesso condicional.  

### <a name="policy-1-sign-in-frequency-control"></a>Política 1: Frequência de entrada de controle

1. Criar nova política
1. Escolha as condições de todos os itens necessárias para o ambiente do cliente, incluindo os aplicativos de nuvem de destino.

   > [!NOTE]
   > É recomendável definir frequência de prompt de autenticação igual principais aplicativos do Microsoft Office, como o Exchange Online e SharePoint Online para obter melhor experiência do usuário.

1. Vá para **controles de acesso** > **sessão** e clique em **frequência de entrada**
1. Insira o valor necessário de dias e horas na primeira caixa de texto
1. Selecione um valor de **horas** ou **dias** na lista suspensa
1. Salvar a diretiva

![Política de acesso condicional configurada para uma entrada na frequência](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD registrado Windows dispositivos entrar para o dispositivo é considerado um prompt. Por exemplo, se você tiver configurado o sinal na frequência de até 24 horas para aplicativos do Office, os usuários no Azure AD registrado Windows dispositivos atenderão a política de frequência de entrada entrando no dispositivo e não deverá novamente ao abrir os aplicativos do Office.

Se você tiver configurado a frequência de entrada diferente para aplicativos da web diferente em execução na mesma sessão do navegador, a política mais rígida será aplicada aos dois aplicativos, porque um token de sessão única de compartilhamento de todos os aplicativos em execução na mesma sessão do navegador.

### <a name="policy-2-persistent-browser-session"></a>Política 2: Sessão persistente do navegador

1. Criar nova política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Observe que esse controle requer escolher "Todos os aplicativos de nuvem" como uma condição

1. Vá para **controles de acesso** > **sessão** e clique em **sessão persistente do navegador**
1. Selecione um valor na lista suspensa
1. Salvar a diretiva

![Política de acesso condicional configurada para o navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Configuração de sessão do navegador persistente no acesso condicional do Azure AD substituirá o "Mantenha-se conectado?" definindo na marca painel no portal do Azure para o mesmo usuário, se você tiver configurado a ambas as políticas da empresa.

## <a name="validation"></a>Validação

Use a ferramenta de hipotéticos para simular um logon do usuário para o aplicativo de destino e outras condições com base em como você configurou sua política. Os controles de gerenciamento de sessão de autenticação são exibidos no resultado da ferramenta.

![Acesso condicional e se a ferramenta de resultados](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implantação de política

Para garantir que sua política funcione conforme o esperado, a melhor prática é testá-la antes de distribuí-la em produção. O ideal é usar um locatário de teste para verificar se a nova política funciona conforme o esperado. Para obter mais informações, consulte o artigo [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* Se você quiser saber como configurar uma política de acesso condicional, consulte o artigo [exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).
* Se você estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o artigo [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).

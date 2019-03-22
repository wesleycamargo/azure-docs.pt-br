---
title: Registro combinado para SSPR do Azure AD e MFA (visualização)
description: (Visualização) do registro de redefinição de senha de autoatendimento e de autenticação de multifator do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6399f5b208bc4d7182622f29bd522cfc9a088e9d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224542"
---
# <a name="combined-security-information-registration-preview"></a>Registro de informações de segurança combinada (visualização)

Antes do registro combinado, usuários registrados a métodos de autenticação para autenticação de multifator do Azure (MFA) e a redefinição de senha de autoatendimento (SSPR) por meio de duas experiências diferentes. As pessoas estavam confusos que métodos semelhantes foram usados para o Azure MFA e o SSPR, mas eles tinham que se registrar para cada recurso separadamente. Agora, com o registro combinado, os usuários podem registrar uma vez e obter os benefícios do Azure MFA e o SSPR.

![Combinar informações de segurança - Meu perfil mostrando informações de segurança registrada para um usuário, incluindo o Microsoft Authenticator e telefone para um usuário de exemplo no diretório.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de habilitar a nova experiência, examine esta documentação voltada para administrador e a documentação e focada no usuário para que entender a funcionalidade e o impacto desse recurso. Base seu treinamento na documentação do usuário para preparar seus usuários para a nova experiência e ajudar a garantir uma distribuição bem-sucedida.

|     |
| --- |
| Registro de informações de segurança combinada para redefinição de senha self-service de autenticação multifator do Azure e o Azure AD é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Se um usuário estiver habilitado para a visualização original e a experiência de registro combinado aprimorada, eles verão a nova experiência. Os usuários que estão habilitados para ambas as experiências só verá a nova experiência de meu perfil. O novo perfil My se alinha com a aparência do registro combinado e fornece uma experiência perfeita para os usuários. Os usuários podem ver meu perfil acessando [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

As páginas de MyProfile são localizadas com base nas configurações de idioma atual no computador acessando a página. Microsoft armazena a linguagem mais recente utilizada no cache do navegador para que tentativas subsequentes para acessar continue a renderizar no último idioma usado. Limpar o cache fará com que as páginas de renderizar novamente. Se você gostaria de forçar um idioma específico adicionando um `?lng=de-DE` ao final da URL onde `de-DE` é definido para o idioma apropriado código forçará as páginas a serem renderizados no idioma.

![Minha interface de perfil que mostra informações de segurança e capacidade de usuário configurar o SSPR ou outros métodos de verificação de segurança adicional.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Métodos disponíveis no registro convergido

Neste momento, registro combinado suporta os seguintes métodos e as ações para esses métodos:

|   | Registro | Alterar | Excluir |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo 5) | Não | Sim |
| Outro aplicativo autenticador | Sim (máximo 5) | Não | Sim |
| Token de hardware | Não | Não  | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone comercial | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não  | Sim |
| Senhas do aplicativo | Sim | Não  | Sim |

> [!NOTE]
> As senhas de aplicativo só estão disponíveis para os usuários que têm sido impostos para MFA. As senhas de aplicativo não estão disponíveis para os usuários que estão habilitados para MFA por meio de uma política de acesso condicional.

Os usuários podem definir as opções a seguir como o método padrão para o MFA:

- Microsoft Authenticator – notificação
- Hardware ou aplicativo autenticador de token – o código
- Telefonema
- Mensagem de texto

Conforme continuamos a adicionar mais métodos de autenticação tais ao Azure AD, esses métodos estarão disponíveis no registro combinado.

## <a name="combined-registration-modes"></a>Modos de registro combinado

Há dois "modos" do registro combinado: interromper e gerenciar.

Modo de interrupção, é uma experiência de assistente, mostrada para um usuário quando eles se registrar ou atualizar suas informações de segurança na entrada.

Gerenciar modo faz parte do perfil do usuário e permite que eles gerenciem suas informações de segurança.

Para ambos os modos, se um usuário tiver registrado anteriormente um método que pode ser usado para MFA, eles serão necessário executar a MFA antes que possam acessar suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

Registro combinado respeita as políticas de MFA e o SSPR, se ambos estiverem habilitadas para seu locatário. Essas políticas controlam se um usuário for interrompido para registrar durante a entrada e quais métodos estarão disponíveis para registrar.

O exemplo a seguir lista vários cenários em que um usuário pode ser solicitado a registrar ou atualizar suas informações de segurança:

* Registro MFA imposto por meio da proteção de identidade: Serão solicitados que os usuários registrem durante a entrada. Ele pode registrar métodos de MFA e SSPR (se o usuário está habilitado para SSPR).
* Registro MFA imposto por meio de MFA por usuário: Serão solicitados que os usuários registrem durante a entrada. Ele pode registrar métodos de MFA e SSPR (se o usuário está habilitado para SSPR).
* Registro MFA imposto por meio do acesso condicional ou outras políticas: Os usuários são solicitados a registrar ao acessar um recurso que requer a MFA. Os usuários serão registrar métodos de MFA e SSPR (se o usuário está habilitado para SSPR).
* Registro SSPR imposto: Os usuários são solicitados a registrar durante a entrada. Eles apenas registram métodos de SSPR
* Atualização SSPR imposta: Os usuários devem revisar suas informações de segurança em um intervalo definido pelo administrador. Os usuários são mostrados suas informações e escolha "Boa aparência" ou fazer alterações, se necessário.

Quando o registro é imposto, os usuários são mostrados o número mínimo de métodos necessários para estar em conformidade com as políticas MFA e o SSPR da maioria para menos seguro.

Exemplo:

* Um usuário está habilitado para SSPR. A política SSPR necessários dois métodos para redefinir e tiver habilitado o telefone, email e código de aplicativo móvel.
   * Esse usuário é necessária para registrar os dois métodos.
      * Aplicativo autenticador e phone são exibidas por padrão.
      * O usuário pode optar por registrar o email em vez do aplicativo authenticator ou no telefone.

O fluxograma a seguir descreve quais métodos são mostrados a um usuário quando interrompida para registrar durante a entrada:

![Combinados gráfico de fluxo de informações de segurança explicando o número de métodos necessário quando mais informações são necessárias ao entrar. Isso pode mudar se apenas é necessário o MFA ou somente SSPR](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se você tiver o MFA e o SSPR habilitado, é recomendável que você imponha o registro de MFA.

Se a política SSPR exige que os usuários, revise suas informações de segurança em intervalos regulares, os usuários são interrompidos durante a entrada e mostrados todos os seus métodos registrados. Eles podem escolher "Boa aparência" se as informações são atualizadas ou eles podem escolher "Editar informações" para fazer alterações.

### <a name="manage-mode"></a>Gerenciar o modo

Os usuários podem acessar gerenciar modo acessando [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou escolhendo "Informações de segurança" do meu perfil. A partir daí, os usuários podem adicionar métodos, excluir ou alterar os métodos existentes, alterar seu método de padrão e muito mais.

## <a name="key-usage-scenarios"></a>Principais cenários de uso

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante a entrada

Um administrador tem o registro forçado.

Um usuário não tiver configurado todas as informações de segurança necessários e navega para o portal do Azure. Depois de inserir seu nome de usuário e senha, o usuário é solicitado a configurar as informações de segurança. O usuário, em seguida, siga as etapas mostradas no Assistente para configurar as informações de segurança necessárias. O usuário pode optar por configurar os métodos que não seja o que é exibido por padrão, se as configurações permitirem. No final do assistente, o usuário examina os métodos que eles configuram e seu método padrão para o MFA. Para concluir o processo de instalação, o usuário confirma as informações e continua para o portal do Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do meu perfil

Um administrador não tem imposta de registro.

Navega de um usuário que tem ainda não configurado todas as informações de segurança necessárias para [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). O usuário, em seguida, escolhe **informações de segurança** na barra de navegação à esquerda. A partir daí, o usuário opta por adicionar um método, seleciona qualquer um dos métodos disponíveis para eles e segue as etapas para configurar esse método. Quando terminar, o usuário verá o método que acabou de configurar na página de informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Excluir informações de segurança do meu perfil

Um usuário que tenha configurado anteriormente pelo menos um método navegar para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O usuário optar por excluir um dos métodos registrados anteriormente. Quando terminar, o usuário não vê esse método na página de informações de segurança.

### <a name="change-default-method-from-my-profile"></a>Alterar o método padrão de meu perfil

Um usuário que tenha configurado anteriormente pelo menos um método que pode ser usado para MFA navegar para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O usuário altera o método padrão atual para um método diferente do padrão. Quando terminar, o usuário verá o novo método de padrão na página de informações de segurança.

## <a name="next-steps"></a>Próximas etapas

[Habilite o registro combinado no seu locatário](howto-registration-mfa-sspr-combined.md)

[Métodos disponíveis para o MFA e o SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a autenticação multifator do Azure](howto-mfa-getstarted.md)

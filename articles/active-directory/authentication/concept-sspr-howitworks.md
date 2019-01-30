---
title: Mergulho Profundo com Senhas de Autoatendimento do Active Directory do Azure
description: Como a redefinição de senha de autoatendimento funciona
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: be7aa43ec6001be78fb405290914f19174559530
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435712"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Redefinição de senha self-service do Azure AD

Como funciona a SSPR (redefinição de senha por autoatendimento)? O que essa opção significa na interface? Continue lendo para saber mais sobre a SSPR do Azure AD (Azure Active Directory).

|     |
| --- |
| Notificação de aplicativo móvel e código de aplicativo móvel como métodos para a redefinição de senha de autoatendimento do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de redefinição de senha?

Quando um usuário entra no portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar:

   * Como a página deve ser localizada?
   * A conta de usuário é válida?
   * A qual organização o usuário pertence?
   * Onde a senha do usuário é gerenciada?
   * O usuário é licenciado para usar o recurso?

Leia as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha:

1. O usuário seleciona o link **Não consigo acessar minha conta** ou acessa [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) diretamente.
   * Com base na localidade do navegador, a experiência é renderizada no idioma apropriado. A experiência de redefinição de senha é localizada nos mesmos idiomas para os quais o Office 365 dá suporte.
   * Para visualizar o portal de redefinição de senha em um idioma localizado diferente, inclua "? Mkt =" no final do URL de redefinição de senha com o exemplo a seguir para o espanhol [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. O usuário insere uma ID de usuário e passa um captcha.
3. O Azure Active Directory verifica se o usuário está apto para usar esse recurso fazendo as seguintes verificações:
   * Verifica se o usuário possui esse recurso habilitado e uma licença do Azure Active Directory atribuída.
     * Se o usuário não tiver esse recurso habilitado ou uma licença atribuída, ele deverá contatar o administrador para redefinir sua senha.
   * Verifica se o usuário possui os métodos de autenticação corretos definidos em sua conta, de acordo com a política do administrador.
     * Se a política exigir apenas um método, ela garantirá que o usuário tenha os dados apropriados definidos para pelo menos um dos métodos de autenticação habilitados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o usuário é aconselhado a entrar em contato com o administrador para redefinir sua senha.
     * Se a política exigir dois métodos, ela garantirá que o usuário tenha os dados apropriados definidos para pelo menos dois dos métodos de autenticação habilitados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o usuário é aconselhado a entrar em contato com o administrador para redefinir sua senha.
     * Se uma função de administrador do Azure for atribuída ao usuário, a diretiva de senha de duas portas será imposta. Mais informações sobre essa política podem ser encontradas na seção [Diferenças da política de redefinição do administrador](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Verifica se a senha do usuário é gerenciada no local (federado, autenticação de passagem ou sincronizada com hash de senha).
     * Se o write-back estiver implantado e a senha do usuário for gerenciada localmente, o usuário poderá continuar a autenticação e a redefinição de sua senha.
     * Se o write-back não estiver implantado e a senha do usuário for gerenciada localmente, o usuário deverá contatar o administrador para redefinir sua senha.
4. Se for determinado que o usuário pode redefinir sua senha com êxito, ele será orientado pelo processo de redefinição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se a SSPR estiver habilitada, você deve selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Às vezes, essas opções referidas como "portões." É altamente recomendável que você **escolha dois ou mais métodos de autenticação** para que seus usuários tenham mais flexibilidade caso não consigam acessar um quando precisarem.

* Notificação de aplicativos para dispositivos móveis (visualização)
* Código de aplicativo móvel (visualização)
* Email
* Telefone celular
* Telefone comercial
* Perguntas de segurança

Os usuários só podem redefinir sua senha se tiverem dados presentes nos métodos de autenticação que o administrador habilitou.

> [!WARNING]
> Contas atribuídas a funções de administrador do Azure será necessárias para usar métodos conforme definido na seção [diferenças de política de redefinição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

![Autenticação][Authentication]

### <a name="number-of-authentication-methods-required"></a>Quantidade necessária de métodos de autenticação

Essa opção determina o número mínimo de métodos de autenticação disponíveis ou os portões que um usuário deve passar para redefinir ou desbloquear sua senha. Pode ser definido para um ou dois.

Os usuários podem optar por fornecer mais métodos de autenticação se o administrador permitir esse método de autenticação.

Se um usuário não tiver os métodos mínimos necessários registrados, ele verá uma página de erro que o direcionará para solicitar a um administrador para redefinir sua senha.

#### <a name="mobile-app-and-sspr-preview"></a>Aplicativo para dispositivos móveis e SSPR (visualização)

Ao usar um aplicativo para dispositivos móveis, como o aplicativo Microsoft Authenticator, como um método de redefinição de senha, você deve estar ciente do seguinte:

* Quando os administradores exigem que um método seja usado para redefinir uma senha, o código de verificação é a única opção disponível.
* Quando os administradores exigem dois métodos a ser usado para redefinir uma senha, os usuários são capazes de usar **um** notificação **ou** habilitado de código de verificação além de quaisquer outros métodos.

| Número de métodos necessários para redefinir | Um | Dois |
| :---: | :---: | :---: |
| Recursos de aplicativos para dispositivos móveis disponíveis | Código | Código ou notificação |

Usuários não têm a opção de registrar seu aplicativo móvel ao se registrar para redefinição de senha de autoatendimento do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Os usuários podem registrar seu aplicativo móvel em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou na nova visualização do registro de informações de segurança em[https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Você deve habilitar o [Registro convergente para a redefinição de senha de autoatendimento e Autenticação Multifator do Azure (visualização pública)](concept-registration-mfa-sspr-converged.md) antes de os usuários poderem acessar a nova experiência em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se você iniciar com uma política que tenha apenas um método de autenticação requerido para reiniciar ou desbloquear registrado e você alterar esse número para dois métodos, o que acontece?

| Número de métodos registrados | Número de métodos necessários | Result |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de redefinir ou desbloquear |
| 1 | 2 | **Incapaz** de redefinir ou desbloquear |
| 2 ou mais | 2 | **Capaz** de redefinir ou desbloquear |

Se você alterar os tipos de métodos de autenticação que um usuário pode usar, você poderá inadvertidamente impedir que os usuários sejam capazes de usar SSPR se eles não tiverem a quantidade mínima de dados disponíveis.

Exemplo:
1. A política original é configurada com dois métodos de autenticação necessários. Somente o número de telefone comercial e as questões de segurança são utilizados. 
2. O administrador altera a política para não usar perguntas de segurança, mas permite o uso de telefone celular e um email alternativo.
3. Os usuários sem o telefone celular ou os campos de e-mail alternativos preenchidos não podem redefinir suas senhas.

## <a name="registration"></a>Registro

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os usuários se cadastram ao entrarem

A ativação dessa opção exige que um usuário conclua o registro de redefinição de senha se entrar em qualquer aplicativo usando o Azure AD. Isso inclui os seguintes aplicativos:

* Office 365
* Portal do Azure
* Painel de acesso
* Aplicativos federados
* Aplicativos personalizados que usam o Azure AD

Ao exigir que o registro seja desativado, os usuários podem registrar-se manualmente. Os usuários podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o link **Registrar redefinição de senha** na guia **Perfil** no Painel de Acesso.

> [!NOTE]
> Os usuários podem ignorar o portal de registro de redefinição de senha selecionando **cancelar** ou fechando a janela. Mas eles são solicitados a registrar cada vez que entrar até que eles concluir seu registro.
>
> Isso não interromperá conexão do usuário se ele já estiver conectado.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes que os usuários precisem reconfirmar suas informações de autenticação

Essa opção determina o período entre a configuração e a reconfirmação das informações de autenticação e somente estará disponível se você habilitar a opção **Exigir que os usuários se registrem ao entrar**.

Os valores válidos são de 0 a 730 dias, com "0", o que significa que os usuários nunca são solicitados a reconfirmar suas informações de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os usuários de redefinições de senha

Se esta opção estiver definida para **Sim**, os usuários que redefinirem suas senhas receberão um e-mail notificando-os de que sua senha foi alterada. O email é enviado por meio do portal de SSPR em seus endereços de email primários e alternativos que estão no arquivo no Azure Active Directory. Ninguém mais é notificado sobre o evento de redefinição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores redefinirem suas senhas

Se essa opção for definida para **Sim**, então, *todos os administradores* receberão um email em seu endereço de email primário registrado no Azure Active Directory. O email notifica que outro administrador alterou sua senha utilizando a SSPR.

Exemplo: Há quatro administradores em um ambiente. O administrador A redefine sua senha usando a SSPR. Os administradores B, C e D recebem um e-mail alertando sobre a redefinição de senha.

## <a name="on-premises-integration"></a>Integração local

Se você instalar, configurar e habilitar o Azure Active Directory Connect, você terá as seguintes opções adicionais para integrações no local. Se essas opções estiverem esmaecidas, o write-back não foi configurado corretamente. Para obter mais informações, consulte [Configurar write-back de senha](howto-sspr-writeback.md).

![Write-back][Writeback]

Esta página fornece um status rápido do cliente de write-back no local, uma das seguintes mensagens é exibida com base na configuração atual:

* Seu cliente de write-back local está em execução.
* O Azure Active Directory está online e conectado ao seu cliente de write-back local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [Atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que você tenha os recursos de conectividade e correções de bugs importantes mais recentes.
* Infelizmente, não podemos verificar seu status de cliente de write-back local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o status da conexão.
* Infelizmente, parece que neste momento não é possível conectarmos ao seu cliente de write-back local. [Solucionar problemas do Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a conexão.
* Infelizmente, não podemos nos conectar ao seu cliente de write-back local porque o write-back de senha não foi configurado corretamente. [Configurar o write-back de senha](howto-sspr-writeback.md) para restaurar a conexão.
* Infelizmente, parece que neste momento não é possível conectarmos ao seu cliente de write-back local. Isso pode ocorrer devido a problemas temporários em nossa extremidade. Se o problema persistir, consulte [Solucionar problemas o Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a conexão.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Write-back de senhas para o diretório local

Este controle determina se o write-back de senha está habilitado para este diretório. Se o write-back estiver ativado, ele indica o status do serviço de write-back local. Isso será útil se você desejar desabilitar o write-back de senha temporariamente sem precisar reconfigurar o Azure AD Connect.

* Se a opção estiver definida para **Sim**, o write-back será habilitado e os usuários federados, com autenticação de passagem ou sincronizados com hash de senha poderão redefinir suas senhas.
* Se a opção estiver definida para **Não**, o write-back será habilitado e os usuários federados, com autenticação de passagem ou sincronizados com hash de senha não poderão redefinir suas senhas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir aos usuários desbloquear contas sem redefinir a senha

Esse controle designa se os usuários que visitam o portal de redefinição de senha devem ter a opção de desbloquear suas contas no Active Directory no local sem ter que redefinir sua senha. Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. Use essa configuração para separar essas duas operações. 

* Se for definida para **Sim**, então, os usuários terão a opção de redefinir sua senha e desbloquear a conta, ou desbloquear sua conta sem precisar redefinir a senha.
* Se for definida para **Não**, os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.

## <a name="password-reset-for-b2b-users"></a>Redefinição de senha para usuários B2B

A reinicialização e a mudança de senha são totalmente suportadas em todas as configurações B2B (entre empresas). A reinicialização da senha do usuário B2B tem suporte nos três casos a seguir:

   * **Os usuários de uma organização parceira com um locatário existente do Azure AD**: Se a organização com a qual você tem uma parceria tiver um locatário do Azure AD, *respeitaremos quaisquer políticas de redefinição de senha habilitadas no locatário*. Para que a reinicialização da senha funcione, a organização parceira precisa ter certeza de que a SSPR do Azure Active Directory está habilitada. Não há nenhum custo adicional para os clientes do Office 365, e ela pode ser habilitada seguindo as etapas em nosso guia [Introdução ao gerenciamento de senha](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
   * **Usuários que se inscreveram usando** a inscrição de autoatendimento: se a organização com a qual você está fazendo parceria usou o recurso [inscrição para autoatendimento](../users-groups-roles/directory-self-service-signup.md) para entrar em um locatário, permitiremos que redefinam a senha com o email com a qual se registraram.
   * **Usuários B2B**: os novos usuários B2B criados com as novas [funcionalidades do Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir suas senhas com o email com o qual se registraram durante o processo de convite.

Para testar este cenário, acesse https://passwordreset.microsoftonline.com com um desses usuários parceiros. Se eles possuem um email alternativo ou um email de autenticação definido, a redefinição de senha funcionará como esperado.

> [!NOTE]
> Contas Microsoft que receberam acesso de convidado a seu locatário do Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de email pessoal, não podem usar a SSPR do Azure AD. É necessário que definam a senha, utilizando as informações localizadas no artigo [Quando não for possível entrar na sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure Active Directory:

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Configuração de write-back de senha de integração local e informações para solucionar problemas"

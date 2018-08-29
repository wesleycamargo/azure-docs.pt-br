---
title: Métodos de autenticação do Azure AD
description: Quais métodos de autenticação estão disponíveis no Azure AD para MFA e SSPR
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 7776ca63dd5c02e470ead35e3dad73c051731fd1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144774"
---
# <a name="what-are-authentication-methods"></a>Quais são os métodos de autenticação?

A redefinição de senha de auto-atendimento do Azure AD (SSPR) e a autenticação de vários fatores (MFA) podem solicitar informações adicionais, conhecidas como métodos de autenticação ou informações de segurança, para confirmar que você é quem diz quando usa os recursos associados.

Os administradores podem definir na política quais métodos de autenticação estão disponíveis para usuários do SSPR e do MFA. Alguns métodos de autenticação podem não estar disponíveis para todos os recursos.

A Microsoft recomenda que os administradores habilitem os usuários a selecionar mais do que o número mínimo necessário de métodos de autenticação, caso eles não tenham acesso a um.

|Método de autenticação|Uso|
| --- | --- |
| Senha | MFA e o SSPR |
| Perguntas de segurança | Somente o SSPR |
| Endereço de email | Somente o SSPR |
| Aplicativo Microsoft Authenticator | Visualização pública para SSPR e MFA |
| sms | MFA e o SSPR |
| Chamada de voz | MFA e o SSPR |
| Senhas de aplicativo | MFA somente em determinados casos |

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Notificação de aplicativo móvel e código de aplicativo móvel como métodos para a redefinição de senha de autoatendimento do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Senha

Sua senha do AD do Azure é considerada um método de autenticação. É o único método que **não pode ser desabilitado**.

## <a name="security-questions"></a>Perguntas de segurança

As perguntas de segurança estão disponíveis **somente na redefinição de senha de auto-atendimento do Azure AD** para contas que não são de administrador.

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas às perguntas de outros usuários.

> [!NOTE]
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há nenhuma maneira de um administrador ler ou modificar as perguntas ou respostas de um usuário.
>

### <a name="predefined-questions"></a>Perguntas predefinidas

* Em qual cidade você conheceu seu primeiro cônjuge/parceiro?
* Em qual cidade seus pais se conheceram?
* Em qual cidade seu irmão mais próximo mora?
* Em qual cidade seu pai nasceu?
* Em qual cidade você teve seu primeiro emprego?
* Em qual cidade sua mãe nasceu?
* Em qual cidade você estava no ano de 2000?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual é o nome de uma faculdade que você tentou entrar, mas que não frequentou?
* Qual é o nome do lugar em que você realizou sua primeira festa de casamento?
* Qual é o segundo nome de seu pai?
* Qual é sua comida favorita?
* Qual é o nome e sobrenome de sua avó materna?
* Qual é o segundo nome de sua mãe?
* Qual é o mês e ano de aniversário de seu irmão mais velho? (por exemplo, novembro de 1985)
* Qual é o segundo nome de seu irmão mais velho?
* Qual é o nome e sobrenome de seu avô paterno?
* Qual é o segundo nome de seu irmão mais novo?
* Em qual escola você concluiu a sexta série?
* Qual era o nome e sobrenome de seu melhor amigo de infância?
* Qual era o nome e sobrenome de seu primeiro parceiro?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual era a marca e o modelo de seu primeiro carro ou sua primeira moto?
* Qual era o nome da primeira escola em que você estudou?
* Qual é o nome do hospital em que você nasceu?
* Qual é o nome da rua da primeira casa em que morou na infância?
* Qual é o nome de seu herói de infância?
* Qual é o nome de seu animal de pelúcia preferido?
* Qual era o nome de seu primeiro animal de estimação?
* Qual era seu apelido de infância?
* Qual era seu esporte favorito no ensino médio?
* Qual foi seu primeiro emprego?
* Quais eram os últimos quatro dígitos de seu primeiro número de telefone?
* Quando criança, o que você queria ser quando crescesse?
* Quem é a pessoa mais famosa que você já conheceu?

Todas as questões de segurança predefinidas são traduzidas e localizadas no conjunto completo de idiomas do Office 365 com base na localidade do navegador do usuário.

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

Perguntas de segurança personalizadas não estão localizadas. Todas as perguntas personalizadas são exibidas no mesmo idioma em que são inseridas na interface do usuário administrativo, mesmo se a localidade do navegador do usuário for diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

* O limite mínimo para a resposta é de três caracteres.
* O limite máximo para a resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas que foram necessárias para se registrar.

## <a name="email-address"></a>Endereço de email

O endereço de e-mail está disponível **somente na redefinição de senha de auto-atendimento do Azure AD**.

A Microsoft recomenda o uso de uma conta de e-mail que não exigiria a senha do Azure AD do usuário para acesso.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança para seu trabalho do Azure AD ou conta de escola ou sua conta da Microsoft.

O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Os usuários não terão a opção de registrar seu aplicativo móvel ao se registrarem para redefinição de senha de autoatendimento. Em vez disso, os usuários podem registrar seu aplicativo para dispositivos móveis em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou na visualização do registro de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

O aplicativo Microsoft Authenticator pode ajudar a impedir o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Os usuários visualizam a notificação e, se for legítimo, selecione Verificar. Caso contrário, eles podem selecionar Deny.

> [!WARNING]
> Para redefinição de senha de autoatendimento quando apenas um método é necessário para redefinir, o código de verificação é a única opção disponível para os usuários **garantirem o mais alto nível de segurança**.
>
> Quando dois métodos forem necessários, os usuários poderão redefinir usando notificação **SEJA** **OU** código de verificação, além de qualquer outro método ativado.
>

Se você ativar o uso da notificação por meio do aplicativo móvel e do código de verificação do aplicativo para dispositivos móveis, os usuários que registrarem o aplicativo Microsoft Authenticator usando uma notificação poderão usar a notificação e o código para confirmar sua identidade.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

O aplicativo Microsoft Authenticator ou outros aplicativos de terceiros podem ser usados como um token de software para gerar um código de verificação OATH. Depois de inserir seu nome de usuário e senha, insira o código fornecido pelo aplicativo na tela de login. O código de verificação oferece uma segunda forma de autenticação.

> [!WARNING]
> Para redefinição de senha de autoatendimento quando apenas um método é necessário para redefinir, o código de verificação é a única opção disponível para os usuários **garantirem o mais alto nível de segurança**.
>

## <a name="mobile-phone"></a>Telefone celular

Duas opções estão disponíveis para usuários com telefones celulares.

Se os usuários não quiserem que seu número de telefone celular seja visível no diretório, mas eles ainda querem usá-lo para redefinição de senha, os administradores não devem preenchê-lo no diretório. Os usuários devem preencher seu atributo **Telefone de Autenticação** por meio do [portal de registro de redefinição de senha](https://aka.ms/ssprsetup). Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: +1 4255551234.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

### <a name="text-message"></a>mensagem de texto

Um SMS é enviado para o número do celular que contém um código de verificação. Digite o código de verificação fornecido na interface de login para continuar.

### <a name="phone-call"></a>chamada telefônica

Uma chamada de voz automatizada é feita para o número de telefone que você fornece. Atenda a chamada e pressione # no teclado do telefone para autenticar

## <a name="office-phone"></a>Telefone comercial

Uma chamada de voz automatizada é feita para o número de telefone que você fornece. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar.

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: +1 4255551234.

O atributo de telefone do escritório é gerenciado pelo seu administrador.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

## <a name="app-passwords"></a>Senhas de aplicativo

Alguns aplicativos que não são de navegador não oferecem suporte à autenticação de vários fatores. Se um usuário tiver sido habilitado para autenticação de vários fatores e tentar usar aplicativos sem navegador, eles não poderão se autenticar. Uma senha de aplicativo permite que os usuários continuem a autenticar

Caso você imponha a Autenticação Multifator do Microsoft Azure por meio de políticas de acesso condicional e não por MFA por usuário, não será possível criar senhas de aplicativo. Aplicativos que usam políticas de acesso condicional para controlar o acesso não precisam de senhas de aplicativo.

Se sua organização for federada para o SSO com o Azure AD e você pretender usar o MFA do Azure, esteja ciente dos seguintes detalhes:

* A senha de aplicativo é verificada pelo Azure AD e, portanto, ignora a federação. A federação é usada apenas durante a configuração de senhas de aplicativo. Para usuários federados (SSO), as senhas são armazenadas no ID organizacional. Se o usuário sair da empresa, essa informação deve fluir para o ID da organização usando o DirSync. A desabilitação/exclusão da conta pode levar até três horas para sincronizar, atrasando a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela senha de aplicativo.
* Nenhum recurso de registro/auditoria de autenticação local está disponível para senhas de aplicativo.
* Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a verificação em duas etapas com os clientes, dependendo de onde eles se autenticam. Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.
* Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativos, selecione a opção **Permitir que usuários criem senhas de aplicativos para fazer login em aplicativos que não são de navegador** em configurações de serviço.

## <a name="next-steps"></a>Próximas etapas

[Ativar redefinição de senha de autoatendimento para sua organização](quickstart-sspr.md)

[Ativar a autenticação de vários fatores do Azure para sua organização](howto-mfa-getstarted.md)

[Habilitar registro convergido para a Autenticação Multifator do Microsoft Azure Active Directory e a redefinição de senha por autoatendimento do Microsoft Azure AD](concept-registration-mfa-sspr-converged.md)

[Documentação de configuração do método de autenticação do usuário final](https://aka.ms/securityinfoguide)

---
title: Guia de implantação de redefinição de senha de autoatendimento - Azure Active Directory
description: Dicas para uma distribuição bem-sucedida da redefinição da senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 9c728ba4c77cd22782adc9916da62cc309a8150b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431649"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Como distribuir com sucesso a redefinição de senha de autoatendimento

Para garantir uma distribuição uniforme da funcionalidade de autoatendimento de redefinição de senha (SSPR) do Azure Active Directory (Azure AD), a maioria dos clientes conclui as seguintes etapas:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Conclua uma distribuição piloto com um pequeno subconjunto da organização.
   * Encontre informações sobre como conduzir o piloto no [Tutorial: Concluir uma distribuição piloto de redefinição de senha self-service do Azure AD](tutorial-sspr-pilot.md).
1. Instrua o suporte técnico.
   * Como eles ajudarão os usuários?
   * Você forçará que os usuários usem SSPR e não permitirá que sua assistência técnica ajude os usuários?
   * Você forneceu as URLs para registro e redefinição?
      * Registro:  https://aka.ms/ssprsetup
      * Redefinir: https://aka.ms/sspr
1. Instrua os usuários.
   * As seções a seguir deste documento examinam a comunicação de exemplo, portais de senha, forçando o registro e preenchendo dados de autenticação.
   * O grupo de produtos do Active Directory do Azure criou um [plano de implantação passo a passo](https://aka.ms/SSPRDeploymentPlan) que as organizações podem usar em paralelo com a documentação encontrado nesse site para tornar um caso de negócios e o plano para a implantação de autoatendimento redefinição de senha.
1. Habilite a redefinição de senha de autoatendimento para toda a organização.
   * Quando você estiver pronto, habilite a redefinição de senha para todos os usuários definindo a opção **Autoatendimento Redefinição de Senha Habilitado** como **Todos**.

## <a name="sample-communication"></a>Comunicação de exemplo

Muitos clientes acham que a maneira mais fácil de fazer com que os usuários usem o SSPR é por meio de uma campanha de email que inclua instruções que sejam fáceis de seguir. [Criamos emails simples e outras garantias que podem ser usadas como modelos para ajudar na distribuição](https://www.microsoft.com/download/details.aspx?id=56768):

* **Em breve**: Um modelo de email que você usa nas semanas ou nos dias antes da distribuição para permitir que os usuários saibam que precisam fazer algo.
* **Disponível agora**: Um modelo de email que você usa no dia do lançamento do programa para orientar os usuários a se registrarem e confirmarem seus dados de autenticação. Se os usuários se registrarem agora, eles terão o SSPR disponível quando precisarem.
* **Lembrete de inscrição**: Um modelo de email para alguns dias ou algumas semanas após a implantação para lembrar aos usuários de se registrarem e confirmarem seus dados de autenticação.
* **Cartazes da SSPR**: Cartazes que podem ser personalizados e exibidos na organização nos dias e nas semanas que antecedem e após a distribuição.
* **Cartões de mesa da SSPR**: Cartões de mesa que podem ser colocados no refeitório, em salas de conferência ou em mesas para incentivar os usuários a concluírem o registro.
* **Adesivos da SSPR**: Modelos de adesivos que podem ser personalizados e impressos para serem colocados em laptops, monitores, teclados ou celulares para lembrar como acessar a SSPR.

![Exemplo de email de SSPR][Email]

## <a name="create-your-own-password-portal"></a>Criar seu próprio portal de senha

Muitos clientes preferem hospedar uma página e criar uma entrada DNS raiz, como https://passwords.contoso.com. Eles preenchem a página com links para as seguintes informações:

* [Portal de redefinição de senha do Azure AD - https://aka.ms/sspr](https://aka.ms/sspr)
* [Portal de registro de redefinição de senha do Azure AD - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal de alteração de senha do Azure AD - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Outras informações específicas da organização

Em quaisquer comunicações por email ou folhetos enviados por você, é possível incluir uma URL personalizada que seja fácil de lembrar para que os usuários possam acessar quando precisarem usar os serviços. Para o seu benefício, criamos uma [página de exemplo de redefinição de senha](https://github.com/ajamess/password-reset-page) que pode ser usada e personalizada de acordo com as necessidades de sua organização.

## <a name="use-enforced-registration"></a>Usar o registro imposto

Se deseja que os usuários se registrem para a redefinição de senha, você pode exigir que eles se registrem quando entrarem usando o Azure AD. Você pode habilitar essa opção no painel de **Redefinição de senha** do seu diretório ativando a opção **Exigir que os Usuários se Registrem ao Entrar** na guia **Registro**.

Os administradores podem exigir que os usuários se registrem novamente após um período de tempo específico. Eles podem definir a opção **Número de dias antes que os usuários sejam solicitados a confirmar novamente suas informações de autenticação** de 0 a 730 dias.

Depois de habilitar essa opção, quando os usuários entrarem, eles verão uma mensagem dizendo que seu administrador solicitou que eles verifiquem as informações de autenticação.

## <a name="populate-authentication-data"></a>Popular os dados de autenticação

Considere [preencher previamente alguns dados de autenticação dos usuários](howto-sspr-authenticationdata.md). Dessa forma, os usuários não precisam se registrar para redefinição de senha antes de serem capazes de usar o SSPR. Desde que os usuários tenham fornecido dados de autenticação que atendam à política de redefinição de senha que você definiu, eles serão capazes de redefinir suas senhas.

## <a name="disable-self-service-password-reset"></a>Desabilitar a redefinição de senha por autoatendimento

Se a organização decidir desabilitar a redefinição de senha de autoatendimento, será um processo simples. Abra o seu locatário do Azure AD e vá para **Redefinição de senha** > **Propriedades** e, em seguida, selecione **Nenhum** em **Redefinição de senha do serviço de autoatendimento habilitada**. Os usuários ainda manterão seus métodos de autenticação registrado para uso futuro.

## <a name="next-steps"></a>Próximas etapas

* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Habilitar registro convergido para a Autenticação Multifator do Microsoft Azure Active Directory e a redefinição de senha por autoatendimento do Microsoft Azure AD](concept-registration-mfa-sspr-converged.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais são as opções de política com SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Personalizar esses modelos de email para se ajustar aos seus requisitos organizacionais"

<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Um tópico que explica o que é o Azure AD Privileged Identity Management e como usar o PIM para melhorar sua segurança de nuvem."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="kgremban"/>

# Gerenciamento de identidades com privilégios do AD do Azure

Com o Privileged Identity Management do Azure Active Directory (AD), você pode gerenciar, controlar e monitorar o acesso em sua organização. Isso inclui o acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

> [AZURE.NOTE] O Privileged Identity Management só está disponível com a edição Premium P2 do Azure Active Directory. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

As empresas desejam minimizar o número de pessoas que têm acesso a informações seguras ou recursos, porque isso reduz a chance de um usuário mal-intencionado obter esse tipo de acesso. No entanto, os usuários ainda precisam executar operações privilegiadas em aplicativos do Azure, Office 365 ou SaaS. As organizações dão aos usuários acesso privilegiado no Azure AD sem monitorar o que esses usuários estão fazendo com seus privilégios de administrador. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

O Azure AD Privileged Identity Management ajuda você a:

- Ver quais usuários são administradores do Azure AD
- Habilitar o acesso administrativo “just in time” para Microsoft Online Services como Office 365 e Intune
- Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador
- Receber alertas sobre o acesso a uma função com privilégios

O Azure AD Privileged Identity Management pode gerenciar as funções organizacionais do Azure AD internas, incluindo:

- Administrador global
- Administrador de cobrança
- Administrador de serviços
- Administrador de usuários
- Administrador de senha

## Administrador de acesso just in time

Historicamente, você pode atribuir um usuário a uma função de administrador por meio do portal clássico do Azure ou do Windows PowerShell. Como resultado, esse usuário se torna **administrador permanente**, sempre ativo na função a ele atribuída. O Azure AD Privileged Identity Management introduz o conceito de um **administrador elegíveis**. Administradores elegíveis devem ser usuários que precisam de acesso privilegiado às vezes, mas não todos os dias. A função fica inativa até que o usuário precise de acesso, então ele conclui um processo de ativação e torna-se um administrador ativo por um tempo predeterminado.

## Habilitar o Privileged Identity Management para seu diretório

Você pode começar a usar o Azure AD Privileged Identity Management acessando o [Portal do Azure](https://portal.azure.com/).

>[AZURE.NOTE] Você deve ser um administrador global com uma conta organizacional (por exemplo, @seudominio.com) e não uma conta da Microsoft (por exemplo, @outlook.com), para habilitar o Azure AD Privileged Identity Management para um diretório.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, selecione seu nome de usuário no canto superior direito do portal do Azure. Selecione o diretório em que você usará o Privileged Identity Management do Azure AD.
3. Selecione **Mais serviços** e use a caixa de texto Filtrar para procurar **Azure AD Privileged Identity Management**.
4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management é aberto.

Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, o [assistente segurança](active-directory-privileged-identity-management-security-wizard.md) o guia pela experiência de atribuição inicial. Depois disso, você se torna automaticamente o primeiro **Administrador de segurança** e um **administrador de função com privilégios** do diretório.

Somente um administrador com função com privilégios pode gerenciar o acesso de outros administradores. Você pode [conceder a outros usuários a capacidade de gerenciar no PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Painel de gerenciamento de identidades com privilégios

O Privileged Identity Manager do Azure AD oferece um painel que fornece informações importantes, como:

- Alertas que indicam oportunidades para melhorar a segurança
- O número de usuários atribuídos a cada função com privilégios
- O número de administradores elegíveis e permanentes
- Revisões de acesso em andamento

![painel PIM - captura de tela][2]

## Gerenciamento de funções com privilégios

Com o Azure AD Privileged Identity Management, você pode gerenciar os administradores adicionando ou removendo os administradores permanentes ou elegíveis para cada função.

![adicionar/remover administradores no PIM - captura de tela][3]

## Definir as configurações de ativação de função

Usando as [configurações de função](active-directory-privileged-identity-management-how-to-change-default-settings.md), você pode configurar as propriedades de ativação da função elegível, incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- As informações que um usuário precisa fornecer durante o processo de ativação de função

![configurações do PIM - ativação do administrador - captura de tela][4]

Observe que na imagem, os botões para **Multi-Factor Authentication** estão desabilitados. Certamente, funções com altos privilégios exigirão MFA para maior proteção.

## Ativação de função  

Para [ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md), um administrador qualificado solicita uma "ativação" com limite de tempo para a função. A ativação pode ser solicitada usando a opção **Ativar minha função** no Gerenciamento de identidades com privilégios do AD do Azure.

Um administrador que deseja ativar uma função precisa inicializar o Gerenciamento de identidades com privilégios do AD do Azure no Portal do Azure.

A ativação de função é personalizável. Nas configurações do PIM, você pode determinar o comprimento de ativação e as informações que o administrador precisa fornecer para ativar a função.

![ativação da função de solicitação do administrador do PIM - captura de tela][5]

## Examinar atividade de função

Há duas maneiras de controlar como seus funcionários e os administradores estão usando funções com privilégios. A primeira opção é usar o [histórico de auditoria](active-directory-privileged-identity-management-how-to-use-audit-log.md). O histórico de auditoria registra em log o controle de alterações em atribuições de funções com privilégios e o histórico de ativação de função.

![histórico da ativação do PIM - captura de tela][6]

A segunda opção é configurar [revisões de acesso](active-directory-privileged-identity-management-how-to-start-security-review.md) regulares. Essas revisões de acesso podem ser executadas pelo revisor (como um gerente de equipe) e atribuídas por ele, ou os funcionários podem examinar por conta própria. Essa é a melhor maneira de monitorar quem ainda precisa ter acesso e quem não precisa mais.


## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0921_2016-->
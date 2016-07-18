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
	ms.date="07/01/2016"
	ms.author="kgremban"/>

# Gerenciamento de identidades com privilégios do AD do Azure

O Azure AD (Active Directory) Privileged Identity Management permite gerenciar, controlar e monitorar o acesso a recursos no Azure AD e em outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

As empresas desejam minimizar o número de pessoas que têm acesso a informações seguras ou recursos, porque isso reduz a chance de um usuário mal-intencionado obter esse tipo de acesso. No entanto, os usuários ainda precisam executar operações privilegiadas em aplicativos do Azure, Office 365 ou SaaS. No final, o trabalho tem que ser feito, então as organizações dão aos usuários acesso privilegiado no Azure AD sem monitorar o que esses usuários estão fazendo com seus privilégios de administrador. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

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

Historicamente, você pode atribuir um usuário a uma função de administrador por meio do Portal de Gerenciamento ou do Windows PowerShell. Como resultado, esse usuário se torna **administrador permanente**, sempre ativo na função a ele atribuída. O Azure AD Privileged Identity Management introduz o conceito de um **administrador elegíveis**. Administradores elegíveis devem ser usuários que precisam de acesso privilegiado às vezes, mas não todos os dias. A função fica inativa até que o usuário precise de acesso, então ele conclui um processo de ativação e torna-se um administrador ativo por um tempo predeterminado.

## Habilitar o Privileged Identity Management para seu diretório

Você pode começar a usar o Azure AD Privileged Identity Management acessando o [Portal do Azure](https://portal.azure.com/).

>[AZURE.NOTE] Você deve ser um administrador global com uma conta organizacional (por exemplo, @seudominio.com), e não uma conta da Microsoft (por exemplo, @outlook.com), para habilitar o Azure AD Privileged Identity Management para um diretório.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você usará o Azure AD Privileged Identity Management.
3. Selecione **Novo** > **Segurança + Identidade** > **Azure AD Privileged Identity Management**.

	![Habilitar o PIM no portal][1]

4. Marque a opção **Fixar no painel** e clique no botão **Criar**. O Painel Privileged Identity Management será aberto.

Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, o [assistente segurança](active-directory-privileged-identity-management-security-wizard.md) lhe guiará pela experiência de atribuição inicial. Depois disso, você se tornará automaticamente o primeiro **Administrador de segurança** e um **administrador com função com privilégios** do diretório.

Somente um administrador com função com privilégios pode gerenciar o acesso de outros administradores. Você pode [conceder a outros usuários a capacidade de gerenciar no PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Painel de gerenciamento de identidades com privilégios

O gerenciador de identidades com privilégios do AD do Azure oferece um painel que fornece informações importantes, como:

- Alertas que indicam oportunidades para melhorar a segurança
- O número de usuários atribuídos a cada função com privilégios
- O número de administradores elegíveis e permanentes
- Revisões de acesso em andamento

![painel PIM - captura de tela][2]

## Gerenciamento de funções com privilégios

Com o Azure AD Privileged Identity Management, você pode gerenciar os administradores adicionando ou removendo os administradores permanentes ou elegíveis para cada função.

![adicionar/remover administradores no PIM - captura de tela][3]

## Definir as configurações de ativação de função

Usando a configuração de ativação de função, você pode configurar as propriedades de ativação da função elegível, incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- As informações que um usuário precisa fornecer durante o processo de ativação de função

![configurações do PIM - ativação do administrador - captura de tela][4]

Observe que na imagem, os botões para **Multi-Factor Authentication** estão desabilitados. Certamente, funções com altos privilégios exigirão MFA para maior proteção.

## Ativação de função  

Para ativar uma função, um administrador elegíveis deve solicitar uma "ativação" com limite de tempo para a função. A ativação pode ser solicitada usando a opção **Ativar minha função** no Gerenciamento de identidades com privilégios do AD do Azure.

Um administrador que deseja ativar uma função precisa inicializar o Gerenciamento de identidades com privilégios do AD do Azure no Portal do Azure.

Qualquer tipo de administrador pode usar o Gerenciamento de identidades com privilégios do AD do Azure para ativar sua própria função.

A ativação de função tem um limite de tempo. Nas configurações de Ativação de Função, você pode definir a duração da ativação, bem como as informações necessárias que o administrador precisa fornecer para ativar a função.

![ativação da função de solicitação do administrador do PIM - captura de tela][5]

## Histórico de ativação de função

Usando o Privileged Identity Management do AD do Azure, também é possível controlar alterações feitas em atribuições de funções privilegiadas e no histórico de ativação de função. Isso pode ser feito usando as opções de log de auditoria:

![histórico da ativação do PIM - captura de tela][6]

## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0706_2016-->
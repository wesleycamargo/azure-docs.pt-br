<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Um tópico que explica o que é o Azure AD Privileged Identity Management e como usar o PIM para melhorar sua segurança de nuvem."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="kgremban"/>

# Gerenciamento de identidades com privilégios do AD do Azure

O Azure AD (Active Directory) Privileged Identity Management permite gerenciar, controlar e monitorar suas identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas em recursos do Azure ou Office 365 ou outros aplicativos SaaS. Isso geralmente significa que as organizações precisam conceder acesso privilegiado permanente no Azure AD. Esse é um risco de segurança cada vez maior para recursos hospedados em nuvem porque as organizações não podem monitorar de maneira suficiente o que esses usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa falha poderá afetar a segurança geral da nuvem. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

O Gerenciamento de identidades com privilégios do AD do Azure:

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

Historicamente, você pode atribuir um usuário a uma função de administrador por meio do Portal de Gerenciamento do Azure anterior ou do Windows PowerShell. Como resultado, esse usuário se torna **administrador permanente** daquela função, sempre ativo na função a ele atribuída. O Azure AD Privileged Identity Management introduz o conceito de um **administrador temporário** para uma função, que é um usuário que precisa concluir um processo de ativação para essa função atribuída. O processo de ativação altera a atribuição do usuário para uma função no Azure AD, de inativo para ativo, para um período de tempo especificado, como oito horas.

## Habilitar o Privileged Identity Management para seu diretório

Você pode começar a usar o gerenciamento de identidades com privilégios do AD do Azure acessando o [Portal do Azure](https://portal.azure.com/). O Azure AD Privileged Identity Management não aparece no portal clássico anterior.

>[AZURE.NOTE] Você deve ser um administrador global com uma conta organizacional, não uma conta da Microsoft, para habilitar o Azure AD Privileged Identity Management para um diretório.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você usará o Azure AD Privileged Identity Management.
3. Clique no ícone **Novo** no painel de navegação esquerdo.
4. Selecione **Segurança + Identidade**.
5. Selecione **Azure AD Privileged Identity Management**.
6. Marque a opção **Fixar no painel** e clique no botão **Criar**. O Painel Privileged Identity Management será aberto.

Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, o [assistente segurança](active-directory-privileged-identity-management-security-wizard.md) lhe guiará pela experiência de atribuição inicial. Após isso, você se tornará automaticamente o primeiro **Administrador de segurança** do diretório.

Somente um administrador de segurança pode usar o aplicativo PIM para gerenciar o acesso de outros administradores. Você pode [conceder a outros usuários a capacidade de gerenciar no PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Painel de gerenciamento de identidades com privilégios

O gerenciador de identidades com privilégios do AD do Azure oferece um painel que fornece informações importantes, como:

- O número de usuários atribuídos a cada função com privilégios  
- O número de administradores temporários e permanentes
- Histórico de acesso de cada administrador

![painel PIM - captura de tela][2]

## Gerenciamento de funções com privilégios

Com o gerenciamento de identidade com privilégios do AD do Azure, você pode gerenciar os administradores adicionando ou removendo os administradores permanentes ou temporários para cada função.

![adicionar/remover administradores no PIM - captura de tela][3]

## Definir as configurações de ativação de função

Usando a configuração de ativação de função, você pode configurar as propriedades de ativação da função temporária, incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- As informações que um usuário precisa fornecer durante o processo de ativação de função  

![configurações do PIM - ativação do administrador - captura de tela][4]

## Ativação de função  

Para ativar uma função, um administrador temporário deve solicitar uma "ativação" com limite de tempo para a função. A ativação pode ser solicitada usando a opção **Ativar minha função** no Gerenciamento de identidades com privilégios do AD do Azure.

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

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0420_2016-->
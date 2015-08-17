<properties
	pageTitle="Gerenciamento de identidades com privilégios do AD do Azure"
	description="Um tópico que explica o que é o Gerenciamento de identidades com privilégios do AD do Azure e como configurá-lo."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevepo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/31/2015"
	ms.author="inhenk"/>

# Gerenciamento de identidades com privilégios do Azure AD

O Azure AD Privileged Identity Management permite gerenciar, controlar e monitorar suas identidades com privilégios e o acesso destas a recursos no Azure AD e em outros serviços online da Microsoft, como Office 365 ou o Microsoft Intune.

Para habilitar os usuários a executarem operações privilegiadas, as organizações geralmente precisam oferecer a muitos de seus usuários acesso privilegiado permanente ao Azure AD, ou então a recursos do Azure ou Office 365 ou outros aplicativos SaaS. Para muitos clientes, isso é um risco de segurança cada vez maior para seus recursos hospedados em nuvem porque eles não podem monitorar suficientemente o que os usuários estão fazendo com seus privilégios de administrador. Além disso, uma conta de usuário comprometida que tem acesso privilegiado poderá afetar sua segurança de nuvem como um todo. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

O gerenciamento de identidades com privilégios do AD do Azure nesta visualização permite que você:

- Descobrir quais usuários são os administradores do AD do Azure
- Habilitar acesso administrativo sob demanda a recursos de diretório "just in time"
- Obter relatórios sobre o histórico de acesso de administrador e sobre as alterações nas atribuições de administrador
- Receber alertas sobre o acesso a uma função com privilégios

Nesta visualização, o gerenciamento de identidades com privilégios do AD do Azure pode gerenciar funções organizacionais internas do Active Directory do Azure:

- Administrador global
- Administrador de cobrança
- Administrador de serviços  
- Administrador de usuários
- Administrador de senha

## Administrador de acesso just in time

Historicamente, você pode atribuir um usuário a uma função de administrador por meio do Portal de Gerenciamento ou do Windows PowerShell. Como resultado, ele se torna **admin permanente**, sempre ativo na função a ele atribuída. Essa visualização adiciona suporte para um **admin temporário**, que é um usuário que precisa concluir um processo de ativação para a função atribuída. O processo de ativação altera a atribuição do usuário para uma função no AD do Azure, de inativo para ativo.

## Habilitando o gerenciamento de identidades com privilégios para seu diretório

Você pode começar a usar o gerenciamento de identidades com privilégios do AD do Azure acessando o [portal do Microsoft Azure](https://portal.azure.com/). Por enquanto, o gerenciamento de identidades com privilégios do AD do Azure aparece apenas no portal do Microsoft Azure. Você deve ser um administrador global para habilitar o gerenciamento de identidades com privilégios do AD do Azure para um diretório.

![][1]

Após inicializar essa extensão, você tornará automaticamente o primeiro **Administrador de segurança** do diretório. Somente um administrador de segurança pode acessar essa extensão para gerenciar o acesso de outros administradores. Durante a inicialização, será adicionado um bloco do gerenciamento de identidades com privilégios do AD do Azure ao quadro inicial do Portal de Visualização do Azure.

## Painel de gerenciamento de identidades com privilégios

O gerenciador de identidades com privilégios do AD do Azure oferece um painel que fornece informações importantes, como:

- O número de usuários atribuídos a cada função com privilégios  
- O número de administradores temporários e permanentes
- O histórico de acesso do administrador

![][2]

## Gerenciamento de funções com privilégios

Com o gerenciamento de identidade com privilégios do AD do Azure, você pode gerenciar os administradores adicionando ou removendo os administradores permanentes ou temporários para cada função.

![][3]

## Definir as configurações de ativação de função

Usando a configuração de ativação de função, você pode configurar as propriedades de ativação da função temporária, incluindo:

- A duração do período de ativação de função
- A notificação de ativação de função
- As informações que um usuário precisa fornecer durante o processo de ativação de função  

![][4]

## Ativação de função  

Para ativar uma função, um administrador temporário deve solicitar uma "ativação" com limite de tempo para a função. A ativação pode ser solicitada usando a opção **Ativar minha função** no Gerenciamento de identidades com privilégios do AD do Azure.

Um administrador que deseja ativar uma função precisa inicializar o Gerenciamento de identidades com privilégios do AD do Azure no Portal de Visualização do Azure.

Qualquer tipo de administrador pode usar o Gerenciamento de identidades com privilégios do AD do Azure para ativar sua própria função.

A ativação de função tem um limite de tempo. Nas configurações de ativação de função, você pode configurar o comprimento de ativação, bem como as informações necessárias que o administrador precisa fornecer para ativar a função.

![][5]

## Histórico de ativação de função

Usando o Gerenciamento de identidades com privilégios do AD do Azure, também é possível controlar alterações em atribuições de funções privilegiadas e no histórico de ativação de função. Isso pode ser feito usando as opções de log de auditoria:

![][6]

## O que vem a seguir

[Blog do Microsoft Azure](http://azure.microsoft.com/blog/) [Controle de acesso baseado em função](../role-based-access-control-configure.md)

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=August15_HO6-->
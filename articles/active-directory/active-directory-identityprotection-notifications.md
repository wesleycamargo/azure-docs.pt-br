<properties
	pageTitle="Notificações do Azure Active Directory Identity Protection | Microsoft Azure"
	description="Saiba como as notificações dão suporte às suas atividades de investigação."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="markvi"/>

#Notificações do Azure Active Directory Identity Protection 


O Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudar você a gerenciar o risco do usuário e eventos de risco:

- Email de alerta de usuário comprometido

- Email de resumo semanal

## Email de alerta de usuário comprometido

Um email de alerta de usuário comprometido é gerado quando o Azure AD Identity Protection identifica uma conta como comprometida. O email inclui um link para os Usuários sinalizados para o relatório de risco no painel do Identity Protection. Recomendamos que as notificações de usuários comprometidos sejam investigadas imediatamente.


## Email de resumo semanal

O email de resumo semanal contém um resumo dos novos eventos de risco.<br> Ele inclui:

- Usuários em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection


<br> ![Correção](./media/active-directory-identityprotection-notifications/400.png "Correção") <br>

É possível desativar o envio de um email de resumo semanal.<br><br> ![Riscos do usuário](./media/active-directory-identityprotection-notifications/62.png "Riscos do usuário") <br>
 

**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Configurações**.<br><br> ![Política de risco do usuário](./media/active-directory-identityprotection-notifications/401.png "Política de risco do usuário") <br>

2. Na seção **Geral**, clique em **Notificações**.<br><br> ![Política de risco do usuário](./media/active-directory-identityprotection-notifications/405.png "Política de risco do usuário") <br>




## Consulte também

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0803_2016-->
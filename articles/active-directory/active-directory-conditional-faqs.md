<properties
	pageTitle="Perguntas frequentes sobre acesso condicional | Microsoft Azure"
	description="Perguntas frequentes sobre acesso condicional "
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Perguntas frequentes sobre acesso condicional

## Quais aplicativos funcionam com políticas de acesso condicional?
Consulte o tópico [Acesso condicional – Quais aplicativos têm suporte](active-directory-conditional-access-supported-apps.md).

## As políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?
As políticas são impostas para usuários de colaboração B2B. No entanto, em alguns casos, o usuário não poderá atender o requisito de política, por exemplo, se sua organização não der suporte à autenticação multifator. Atualmente, a política não é imposta para usuários convidados do SharePoint. A relação de convidado é mantida no SharePoint, assim as contas de usuários convidados não estão sujeitas a políticas de acesso no servidor de autenticação. O acesso de convidado pode ser gerenciado no SharePoint.

## A política do SharePoint Online também se aplica ao OneDrive for Business?
Sim.
 
## Por que não é possível definir políticas em aplicativos cliente, como o Word ou o Outlook?
A política de acesso condicional define os requisitos para acessar um serviço e é imposta quando ocorre a autenticação para esse serviço. Portanto, a política não é definida diretamente em um aplicativo cliente, em vez disso, ela é aplicada quando chama um serviço. Por exemplo, o conjunto de políticas no SharePoint será aplicado aos clientes chamando o SharePoint e a política definida no Exchange será aplicada ao Outlook.

<!---HONumber=AcomDC_0629_2016-->
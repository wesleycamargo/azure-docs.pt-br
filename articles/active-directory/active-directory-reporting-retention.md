<properties
	pageTitle="Políticas de retenção de relatório do Azure Active Directory | Microsoft Azure"
	description="Políticas de retenção sobre dados de relatório no Active Directory do Azure"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Políticas de retenção de relatório do Azure Active Directory

*Esta documentação é parte do [Guia de Relatórios do Active Directory do Azure](active-directory-reporting-guide.md).*

Os relatórios do Azure AD (Azure Active Directory) retêm dados para um determinado número de dias, indicado aqui.

Relatório | Descrição
------------------------------------------------------- | -----------
Entradas de fontes desconhecidas | 30 dias
Entradas após várias falhas | 30 dias
Entradas de várias geografias | 30 dias
Entradas de endereços IP com atividade suspeita | 30 dias
Entradas de dispositivos possivelmente infectados | 30 dias
Atividades de entrada irregulares | 30 dias
Usuários com atividade de entrada anômala | 30 dias
Usuários com credenciais vazadas | 30 dias
Relatório de auditoria | 180 dias
Atividade de redefinição de senha (Azure AD) | 30 dias
Atividade de redefinição de senha (Identity Manager) | 30 dias
Atividade de registro de redefinição de senha (Azure AD) | 30 dias
Atividade de registro de redefinição de senha (Identity Manager) | 30 dias
Atividade de grupos de autoatendimento (Azure AD) | 30 dias
Atividade de grupos de autoatendimento (Identity Manager) | 30 dias
Uso do aplicativo | 30 dias
Atividade de provisionamento de conta | 30 dias
Status de substituição de senha | 30 dias
Erros de provisionamento de conta | 30 dias
Uso do RMS | 30 dias
Usuários RMS mais ativos | 30 dias
Uso de dispositivo do RMS | 30 dias
Uso de aplicativos habilitados para RMS | 30 dias

<!---HONumber=AcomDC_0928_2016-->
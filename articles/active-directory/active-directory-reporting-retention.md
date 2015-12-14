<properties
	pageTitle="Políticas de retenção de relatório do Active Directory do Azure | Microsoft Azure"
	description="Políticas de retenção sobre dados de relatório no Active Directory do Azure"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="11/30/2015"
	ms.author="kenhoff"/>

# Políticas de retenção de relatório do Active Directory do Azure

## Artigos de documentação de relatório

 - [API de Relatório](active-directory-reporting-api-getting-started.md)
 - [Eventos de Auditoria](active-directory-reporting-audit-events.md)
 - [Retenção](active-directory-reporting-retention.md)
 - [Visualizações](active-directory-reporting-previews.md)
 - [Pesquisar](active-directory-reporting-search.md)
 - [Aterramento](active-directory-reporting-backfill.md)
 - [Latências](active-directory-reporting-latencies.md)
 - [Evento "Ator Desconhecido"](active-directory-reporting-unknown-actor.md)

Os relatórios do Azure AD retêm dados de um determinado número de dias, indicado aqui.

Relatório | Descrição
------------------------------------------------------- | -----------
Entradas de fontes desconhecidas | 30 dias
Entradas após várias falhas | 30 dias
Entradas de várias regiões geográficas | 30 dias
Entradas de endereços IP com atividade suspeita | 30 dias
Entradas de dispositivos possivelmente infectados | 30 dias
Atividade de conexão anômala | 30 dias
Usuários com atividade de entrada anômala | 30 dias
Usuários com credenciais insuficientes | 30 dias
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

<!---HONumber=AcomDC_1203_2015-->
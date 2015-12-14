<properties
   pageTitle="Latências do relatório do Active Directory do Azure | Microsoft Azure"
   description="Quantidade de tempo que leva para relatar eventos aparecendo no seu Active Directory do Azure"
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

# Latências de relatório do Active Directory do Azure

## Artigos de documentação de relatório

 - [API de Relatório](active-directory-reporting-api-getting-started.md)
 - [Eventos de Auditoria](active-directory-reporting-audit-events.md)
 - [Retenção](active-directory-reporting-retention.md)
 - [Visualizações](active-directory-reporting-previews.md)
 - [Pesquisar](active-directory-reporting-search.md)
 - [Aterramento](active-directory-reporting-backfill.md)
 - [Latências](active-directory-reporting-latencies.md)
 - [Evento "Ator Desconhecido"](active-directory-reporting-unknown-actor.md)

Relatório | Mínimo | Média | Máximo
------------------------------------------------------- | -------- | ---------- | ----------
**Relatórios de segurança** | | |
Atividade de conexão anômala | 2 horas | 4 horas | 8 horas
Entradas de fontes desconhecidas | 2 horas | 4 horas | 8 horas
Entradas após várias falhas | 2 horas | 4 horas | 8 horas
Entradas de várias regiões geográficas | 2 horas | 4 horas | 8 horas
Entradas de endereços IP com atividade suspeita | 2 horas | 4 horas | 8 horas
Entradas de dispositivos possivelmente infectados | 2 horas | 4 horas | 8 horas
Usuários com atividade de entrada anômala | 2 horas | 4 horas | 8 horas
Usuários com credenciais insuficientes | 2 horas | 4 horas | 8 horas
**Relatórios de aplicativo** | | |
Atividade de provisionamento de conta | 2 horas | 4 horas | 8 horas
Erros de provisionamento de conta | 2 horas | 4 horas | 8 horas
Uso do aplicativo | 2 horas | 4 horas | 8 horas
Status de substituição de senha | 2 horas | 4 horas | 8 horas
**Auditoria e relatórios de atividade** | | |
Relatório de auditoria | 1 minuto | 15 minutos | 30 minutos
Atividade de redefinição de senha (Azure AD) | 2 horas | 4 horas | 8 horas
Atividade de redefinição de senha (Identity Manager) | 2 horas | 4 horas | 8 horas
Atividade de registro de redefinição de senha (Azure AD) | 2 horas | 4 horas | 8 horas
Atividade de registro de redefinição de senha (Identity Manager) | 2 horas | 4 horas | 8 horas
Atividade de grupos de autoatendimento (Azure AD) | 2 horas | 4 horas | 8 horas
Atividade de grupos de autoatendimento (Identity Manager) | 2 horas | 4 horas | 8 horas
**Relatórios RMS** | | |
Usuários RMS mais ativos | 2 horas | 4 horas | 8 horas
Uso do RMS | 2 horas | 4 horas | 8 horas
Uso de dispositivo do RMS | 2 horas | 4 horas | 8 horas
Uso de aplicativos habilitados para RMS | 2 horas | 4 horas | 8 horas
**Relatórios de visualização particular** | | |
Atividade de entrada de todos os usuários | 2 horas | 4 horas | 8 horas

<!---HONumber=AcomDC_1203_2015-->
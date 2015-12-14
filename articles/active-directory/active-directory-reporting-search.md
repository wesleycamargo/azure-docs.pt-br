<properties
	pageTitle="Pesquisa de Relatórios do Active Directory do Azure"
	description="Como pesquisar relatórios de auditoria, atividade e segurança do Active Directory do Azure"
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

# Pesquisa de Relatórios do Active Directory do Azure

## Artigos de documentação de relatório

 - [API de Relatório](active-directory-reporting-api-getting-started.md)
 - [Eventos de Auditoria](active-directory-reporting-audit-events.md)
 - [Retenção](active-directory-reporting-retention.md)
 - [Visualizações](active-directory-reporting-previews.md)
 - [Pesquisar](active-directory-reporting-search.md)
 - [Aterramento](active-directory-reporting-backfill.md)
 - [Latências](active-directory-reporting-latencies.md)
 - [Evento "Ator Desconhecido"](active-directory-reporting-unknown-actor.md)

O Active Directory do Azure oferece aos administradores de diretório a capacidade de pesquisar eventos de auditoria, atividade e segurança do usuário em vários relatórios.

Para localizar o painel de pesquisa, navegue até **Portal de Gerenciamento do Azure -> Seu Active Directory do Azure -> Relatórios.** O painel pode ser encontrado na parte superior da lista de relatórios.

Para pesquisar eventos de auditoria ou atividade para um determinado usuário, selecione um intervalo de datas nos campos De e Para, digite o nome de exibição ou o UPN do usuário e clique no botão da marca de seleção.

## Relatórios incluídos na pesquisa

Ainda nem todos os relatórios estão incluídos nos resultados da pesquisa. Esta tabela indica quais relatórios estão incluídos.

Relatório | Incluso
--------------------------------------------------- | --------
Entradas de fontes desconhecidas | Não
Entradas após várias falhas | Não
Entradas de várias regiões geográficas | Não
Entradas de endereços IP com atividade suspeita | Não
Entradas de dispositivos possivelmente infectados | Não
Atividade de conexão anômala | Não
Usuários com atividade de entrada anômala | Não
Usuários com credenciais insuficientes | Não
Relatório de auditoria | Sim
Atividade de redefinição de senha | Sim
Atividade de registro de redefinição de senha | Sim
Atividade de grupos de autoatendimento | Sim
Uso do aplicativo | Não
Atividade de provisionamento de conta | Sim
Status de substituição de senha | Não
Erros de provisionamento de conta | Não
Uso do RMS | Não
Usuários RMS mais ativos | Não
Uso de dispositivo do RMS | Não


## Saiba mais

 - [Relatórios do Active Directory do Azure](active-directory-view-access-usage-reports.md)
 - [Eventos de Auditoria de Relatórios do Active Directory do Azure](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_1203_2015-->
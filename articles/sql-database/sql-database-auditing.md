---
title: Banco de Dados SQL do Azure - Auditoria | Microsoft Docs
description: A Auditoria do Banco de Dados SQL do Azure rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta do Armazenamento do Azure.
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>Conceitos da auditoria de Banco de Dados SQL
A Auditoria do Banco de Dados SQL do Azure rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta do Armazenamento do Azure.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

A auditoria permite e facilita a adoção de padrões de conformidade, mas não garante a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).

* [Visão geral da auditoria do Banco de Dados SQL do Azure]
* [Configurar a auditoria do banco de dados]
* [Analisar os logs e relatórios de auditoria]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Visão geral da auditoria do Banco de Dados SQL do Azure
A auditoria de Banco de Dados SQL permite:

* **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
* **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
* **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Há dois **Métodos de auditoria**:

* **Auditoria de blob** – os logs são gravados no Armazenamento de Blobs do Azure. Esse é um método de auditoria mais recente, que fornece desempenho maior, dá suporte à auditoria do nível de objeto de granularidade mais alta e é mais econômico.
* **Auditoria de tabela** – os logs são gravados no Armazenamento de Tabelas do Azure.

> [!IMPORTANT]
> A introdução das novas auditorias de blobs traz uma grande mudança na forma como a diretiva de auditoria de servidor está sendo herdada por banco de dados. 

Você pode configurar a auditoria para tipos diferentes de categorias de eventos.

* Para configurar e gerenciar a auditoria usando o Portal do Azure, veja [Auditar usando o Portal do Azure](sql-database-auditing-portal.md).
* Para configurar e gerenciar a auditoria usando o PowerShell, veja [Auditar usando o PowerShell](sql-database-auditing-powershell.md).
* Para configurar e gerenciar a auditoria usando a API REST, veja [Auditar usando a API REST](sql-database-auditing-rest.md).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão do servidor. Uma política de auditoria de servidor padrão se aplica a todos os bancos de dados existentes e recém-criados em um servidor.

## <a name="blob-auditing"></a>Auditoria de blobs

Se a auditoria de blobs de servidor estiver habilitada, ele sempre se aplicará ao banco de dados (todos os bancos de dados no servidor serão auditados), independentemente de:
    - As configurações de auditoria de banco de dados.
    - Se a caixa de seleção "Herdar configurações do servidor" é verificada na folha do banco de dados.

> [!IMPORTANT]
> Habilitar a auditoria de blobs no banco de dados, além de ativá-lo no servidor, **não** irá substituir ou alterar as configurações de auditoria de blobs do servidor: ambas as auditorias continuarão a existir lado a lado. Em outras palavras, o banco de dados será auditado duas vezes em paralelo (uma vez pela política de servidor e uma vez pela política de banco de dados).

Evite habilitar a auditoria de blobs de servidor e a auditoria de blobs do banco de dados juntas, a menos que:
 * Você precisa usar outro *conta de armazenamento* ou *o período de retenção* para um banco de dados específico.
 * Você deseja auditar eventos diferentes tipos ou categorias de um banco de dados que estão sendo auditados para o restante dos bancos de dados neste servidor (por exemplo, se as inserções de tabela precisam ser auditado somente para um banco de dados específico).

Caso contrário, recomendamos habilitar somente auditoria de blobs no nível de servidor e deixar a auditoria de nível de banco de dados desabilitada para todos os bancos de dados.

## <a name="table-auditing"></a>Auditoria de tabela

Se a auditoria de tabela no nível do servidor estiver habilitada, ela só será aplicada ao banco de dados se a caixa de seleção "Herdar configurações do servidor" estiver marcada na folha do banco de dados (ela está marcada por padrão para todos os bancos de dados recém-criados e existentes).

- Se a caixa de seleção for *check*, quaisquer alterações feitas nas configurações de auditoria no banco de dados **substituir** as configurações do servidor do banco de dados.

- Se a caixa de seleção for *desmarcada* e a auditoria de nível de banco de dados está desabilitada, o banco de dados não será auditado.

## <a name="analyze-audit-logs-and-reports"></a>Analise os logs e relatórios de auditoria
Os logs de auditoria são agregados na conta do Azure Storage escolhida durante a instalação.

Você pode explorar os logs de auditoria usando uma ferramenta como o [Azure Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Próximas etapas

* Para configurar e gerenciar a auditoria usando o Portal do Azure, veja [Configurar a auditoria no Portal do Azure](sql-database-auditing-portal.md).
* Para configurar e gerenciar a auditoria usando o PowerShell, veja [Configurar a auditoria com o PowerShell](sql-database-auditing-powershell.md).
* Para configurar e gerenciar a auditoria usando a API REST, veja [Configurar a auditoria com a API REST](sql-database-auditing-rest.md).


<!--Anchors-->
[Visão geral da auditoria do Banco de Dados SQL do Azure]: #subheading-1
[Configurar a auditoria do banco de dados]: #subheading-2
[Analisar os logs e relatórios de auditoria]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png


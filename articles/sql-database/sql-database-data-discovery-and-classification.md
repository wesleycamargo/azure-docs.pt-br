---
title: "Descoberta e Classificação de Dados do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Descoberta e Classificação de Dados do Banco de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 1ff7f3509ccdaa6911cee0487d5a6d90b11eb79f
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Descoberta e Classificação de Dados do Banco de Dados SQL do Azure
A Descoberta e Classificação de Dados (atualmente em versão prévia) fornece recursos internos avançados no Banco de Dados SQL do Microsoft Azure para **descobrir**, **classificar**, **rotular** & **proteger** os dados confidenciais em seus bancos de dados.
Descobrir e classificar seus dados mais confidenciais (negócios/financeiros, serviços de saúde, informações de identificação pessoal etc.) pode desempenhar uma função essencial na estatura de proteção das informações organizacionais. Pode servir como infraestrutura para:
* Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas, como GDPR.
* Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
* Controlar o acesso e proteção da segurança de bancos de dados contendo dados altamente confidenciais.

## <a id="subheading-1"></a>Visão geral
A Descoberta e Classificação de Dados apresenta um conjunto de serviços avançados e novos recursos SQL, formando um novo paradigma de Proteção de Informações do SQL, visando proteger os dados e não apenas o banco de dados:
* **Descoberta e recomendações** – O mecanismo de classificação examina o banco de dados e identifica colunas contendo dados potencialmente confidenciais. Em seguida, fornece uma maneira fácil de revisar e aplicar as recomendações de classificação apropriadas por meio do Portal do Azure.
* **Rotulamento** – Os rótulos de classificação de confidencialidade podem ser marcados persistentemente em colunas usando novos atributos de metadados de classificação introduzidos no SQL Engine. Esses metadados podem ser utilizados para cenários de proteção e auditoria baseada em confidencialidade.
* **Confidencialidade do conjunto de resultados da consulta** – A confidencialidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.
* **Visibilidade** - O estado da classificação do banco de dados pode ser visualizado em um painel detalhado no portal. Além disso, você pode fazer o download de um relatório (no formato Excel) para ser usado para fins de conformidade e auditoria, bem como outras necessidades.

## <a id="subheading-2"></a>Descobrir, classificar e rotular colunas confidenciais
A seção a seguir descreve as etapas para descobrir, classificar e rotular colunas contendo dados confidenciais no banco de dados, bem como visualizar o estado de classificação atual do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:
* Rótulos – Os principais atributos de classificação, usados para definir o nível de confidencialidade dos dados armazenados na coluna.  
* Tipos de informações – Fornece granularidade adicional ao tipo de dados armazenados na coluna.

<br>
**Para classificar o Banco de Dados SQL:**

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Navegue até a configuração  **Descoberta e classificação de dados (versão prévia)**  no seu Banco de Dados SQL.

    ![Painel de navegação][1]

3. A guia **Visão geral** inclui um resumo do estado de classificação atual do banco de dados, incluindo uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para exibir apenas partes específicas do esquema, tipos de informações e rótulos. Se ainda não classificou nenhuma coluna, [pule para a etapa 5](#step-5).

    ![Painel de navegação][2]

4. Para fazer o download de um relatório no formato Excel, clique na opção **Exportar** no menu superior da janela.

    ![Painel de navegação][3]

5.  <a id="step-5"></a>Para começar a classificar os dados, clique na guia **Classificação** na parte superior da janela.

    ![Painel de navegação][4]

6. O mecanismo de classificação explora o banco de dados para colunas contendo dados potencialmente confidenciais e fornece uma lista de **classificações de colunas recomendadas**. Para visualizar e aplicar recomendações de classificação:

    * Para visualizar a lista das classificações de colunas recomendadas, clique no painel de recomendações na parte inferior da janela:

        ![Painel de navegação][5]

    * Revise a lista de recomendações – para aceitar uma recomendação de uma coluna específica, marque a caixa de seleção na coluna esquerda da linha relevante. Também é possível marcar *todas as recomendações* aceitas, marcando a caixa de seleção no cabeçalho da tabela de recomendações.

        ![Painel de navegação][6]

    * Para aplicar as recomendações selecionadas, clique no botão azul **Aceitar recomendações selecionadas**.

        ![Painel de navegação][7]

7. Como alternativa, também é possível **classificar manualmente** as colunas ou, além disso, fazer a classificação baseada em recomendação:

    * Clique em **Adicionar classificação** no menu superior da janela.

        ![Painel de navegação][8]

    * Na janela de contexto que abre, selecione o esquema > tabela > coluna que deseja classificar e o tipo de informação e o rótulo de confidencialidade. Em seguida, clique no botão azul **Adicionar classificação** na parte inferior da janela de contexto.

        ![Painel de navegação][9]

8. Para completar sua classificação e rotular (marcar) de maneira persistente as colunas do banco de dados com os novos metadados de classificação, clique em **Salvar** no menu superior da janela.

    ![Painel de navegação][10]

## <a id="subheading-3"></a>Auditoria de acesso aos dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais.

A [Auditoria do Banco de Dados SQL Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) foi aprimorada para incluir um novo campo no log de auditoria chamado *data_sensitivity_information*, que registra a classificações de confidencialidade (rótulos) dos dados reais que foram retornados pela consulta.

![Painel de navegação][11]

## <a id="subheading-4"></a>Próximas etapas
Considere configurar a [Auditoria do Banco de Dados SQL Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) para monitorar e auditar o acesso aos seus dados confidenciais classificados.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png

---
title: "Executar consultas de análise em vários bancos de dados Azure SQL | Microsoft Docs"
description: "Executar consultas distribuídas entre vários bancos de dados do Azure SQL"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: a0742a004b618dda304618bca21ae715552c16e6
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="run-distributed-queries-across-multiple-azure-sql-databases"></a>Executar consultas distribuídas entre vários bancos de dados do Azure SQL

Neste tutorial, você executará consultas de análise em cada locatário no catálogo. Um trabalho elástico é criado para executar as consultas. O trabalho recupera dados e carrega-os em um banco de dados de análise separado criado no servidor de catálogo. Esse banco de dados pode ser consultado para extrair informações escondidas nos dados operacionais diários de todos os locatários. Como uma saída do trabalho, uma tabela é criada nas consultas que retornam resultados no banco de dados de análise do locatário.


Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar o banco de dados de análise de locatário
> * Criar um trabalho agendado para recuperar dados e preencher o banco de dados de análise

Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:

* O aplicativo WTP foi implantado. Para implantar em menos de cinco minutos, consulte [Implantar e explorar o aplicativo WTP SaaS](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A última versão do SQL Server Management Studio (SSMS) está instalada. [Baixar e Instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Padrão de Análise Operacional do Locatário

Uma das ótimas oportunidades com aplicativos SaaS é usar os dados de locatário avançados que são armazenados na nuvem. Use esses dados para obter informações sobre a operação e o uso do aplicativo e seus locatários. Esses dados podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e na plataforma. É fácil acessar esses dados quando estiverem em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem para acessar esses dados é usar os trabalhos Elásticos, que permitem resultados de consulta que retorna resultados da execução do trabalho a ser capturada em um banco de dados e tabela de saída.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Implantar um banco de dados de resultados de análise de locatário

Este tutorial requer que você tenha um banco de dados implantado para capturar os resultados da execução do trabalho de scripts, que contêm consultas que retornam resultados. Vamos criar um banco de dados chamado tenantanalytics para essa finalidade.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise de Locatário\\*Demo-TenantAnalyticsDB.ps1* no *PowerShell ISE* e defina o seguinte valor:
   * **$DemoScenario** = **2** *Implantar banco de dados de análise operacional*
1. Pressione **F5** para executar o script de demonstração (que chama o script *Deploy-TenantAnalyticsDB.ps1*) que cria o banco de dados de análise de locatário.

## <a name="create-some-data-for-the-demo"></a>Criar alguns dados para a demonstração

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise de Locatário\\*Demo-TenantAnalyticsDB.ps1* no *PowerShell ISE* e defina o seguinte valor:
   * **$DemoScenario** = **1** *Comprar ingressos para eventos em todos os locais*
1. Pressione **F5** para executar o script e criar histórico de compras do tíquete.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Criar um trabalho agendado para recuperar a análise do locatário sobre compras de tíquete

Este script cria um trabalho para recuperar informações sobre compra do tíquete de todos os locatários. Depois de agregadas em uma única tabela, você poderá obter métricas esclarecedoras avançadas sobre padrões de compra de tíquete entre os locatários.

1. Abra o SSMS e conecte-se ao servidor catalog-\<user\>.database.windows.net
1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Locatário Análise\\*TicketPurchasesfromAllTenants.sql*
1. Modifique \<WtpUser\>, utilize o nome de usuário usado quando você implantou o aplicativo WTP na parte superior do script, **sp\_add\_target\_group\_member** e **sp\_add\_jobstep**
1. Clique com botão direito do mouse, selecione **Conexão** e conecte-se ao servidor catalog-\<WtpUser\>.database.windows.net, se ainda não estiver conectado
1. Verifique se está conectado ao banco de dados **jobaccount** e pressione **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo de destino *TenantGroup*, agora é preciso adicionar os membros de destino.
* **sp\_add\_target\_group\_member** adiciona um tipo de membro de destino do *servidor*, o que considera todos os bancos de dados neste servidor (Observe que esse é o servidor customer1-&lt;WtpUser&gt; que contém os bancos de dados do locatário) na hora em que a execução de trabalho deve ser incluída no trabalho.
* **sp\_add\_job** cria um novo trabalho agendado semanalmente chamado "Compras de Tíquetes de todos os Locatários"
* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto de comando T-SQL para recuperar todas as informações de compra de tíquete de todos os locatários e copiar o resultado de retorno definido em uma tabela chamada *AllTicketsPurchasesfromAllTenants*
* As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Examine o valor de status da coluna **ciclo de vida** para monitorar o status. Bem-sucedido significa que o trabalho foi concluído com êxito em todos os bancos de dados de locatário e nos dois bancos de dados adicionais que contêm a tabela de referência.

Executar o script com êxito deve resultar em resultados semelhantes:

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Criar um trabalho para recuperar uma contagem resumida de compras de tíquete de todos os locatários

Este script cria um trabalho para recuperar a soma de todas as compras do tíquete de todos os locatários.

1. Abra o SSMS e conecte-se ao servidor *catalog-&lt;User&gt;.database.windows.net*
1. Abra o arquivo... \\Módulos de aprendizado\\Provisionar e Catalogar\\Análise Operacional\\Análise do Locatário\\*Results-TicketPurchasesfromAllTenants.sql*
1. Modifique &lt;WtpUser&gt;, utilize o nome de usuário usado quando você implantou o aplicativo WTP no script, no procedimento armazenado **sp\_add\_jobstep**
1. Clique com botão direito do mouse, selecione **Conexão** e conecte-se ao servidor catalog-\<WtpUser\>.database.windows.net, se ainda não estiver conectado
1. Verifique se está conectado ao banco de dados **tenantanalytics** e pressione **F5** para executar o script

Executar o script com êxito deve resultar em resultados semelhantes:

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** cria um novo trabalho agendado semanalmente chamado "ResultsTicketsOrders"

* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto de comando T-SQL para recuperar todas as informações de compra de tíquete de todos os locatários e copiar o resultado de retorno definido em uma tabela chamada CountofTicketOrders

* As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Examine o valor de status da coluna **ciclo de vida** para monitorar o status. Bem-sucedido significa que o trabalho foi concluído com êxito em todos os bancos de dados de locatário e nos dois bancos de dados adicionais que contêm a tabela de referência.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Implantar um banco de dados de análise do locatário
> * Criar um trabalho agendado para recuperar dados analíticos entre locatários

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Trabalhos Elásticos](sql-database-elastic-jobs-overview.md)

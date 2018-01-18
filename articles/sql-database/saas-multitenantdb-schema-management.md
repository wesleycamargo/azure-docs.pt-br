---
title: "Gerenciar o esquema de Banco de Dados Azure SQL em um aplicativo multilocatário | Microsoft Docs"
description: "Gerenciar o esquema para vários locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gerenciar o esquema para vários locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure

Este tutorial examina os desafios de manter um número potencialmente grande de bancos de dados em um aplicativo de Software como Serviço (SaaS) na nuvem. As soluções são demonstradas para o gerenciamento das melhorias do esquema que são desenvolvidas e implementadas durante a vida útil de um aplicativo.

Durante a evolução de um aplicativo as alterações podem ocorrer nas suas colunas de tabela ou em outra esquema, ou nos seus dados de referência ou nos itens relacionados a desempenho. Com um aplicativo SaaS, essas alterações devem ser implantadas de maneira coordenada em vários bancos de dados de locatário existentes. E essas alterações devem ser incluídas em bancos de dados de locatário futuros que serão adicionados ao aplicativo. Portanto, as alterações também devem ser incorporadas ao processo que provisiona novos bancos de dados.

#### <a name="two-scenarios"></a>Dois cenários

Este tutorial aborda os dois cenários a seguir:
- Implantar as atualizações de dados de referência para todos os locatários.
- Reajustar um índice em uma tabela que contém os dados de referência.

O recurso de [Trabalhos Elástico](sql-database-elastic-jobs-overview.md) do Banco de Dados SQL do Azure é usado para executar essas operações em bancos de dados de locatário. Os trabalhos também operam no banco de dados de locatário do modelo final. Este modelo é usado quando novos bancos de dados são provisionados.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de trabalho.
> * Consultar vários locatários.
> * Atualizar dados em todos os bancos de dados de locatário.
> * Criar um índice em uma tabela em todos os bancos de dados de locatário.

## <a name="prerequisites"></a>Pré-requisitos

- O aplicativo Wingtip Tickets já deve estar implantado:
    - Para obter instruções, consulte o primeiro tutorial, que apresenta o aplicativo de banco de dados multilocatário de SaaS *Wingtip Tickets*:<br />[Implantar e explorar um aplicativo multilocatário fragmentado que usa o Banco de dados SQL do Azure](saas-multitenantdb-get-started-deploy.md).
        - O processo de implantação é executado em menos de cinco minutos.
    - Você deve ter a versão *multilocatário fragmentada* do Wingtip instalada. As versões para *Autônomo* e *Banco de dados por locatário* não oferecem suporte para este tutorial.

- A última versão do SQL Server Management Studio (SSMS) deve estar instalada. [Baixar e Instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- O Azure PowerShell deve estar instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Este tutorial usa recursos do serviço do Banco de Dados SQL do Azure que estão em uma versão prévia limitada ([trabalhos de Banco de Dados Elástico](sql-database-elastic-database-client-library.md)). Se você deseja fazer este tutorial, forneça sua ID de assinatura para *SaaSFeedback@microsoft.com* com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Esta é uma versão prévia limitada, então entre em contato com *SaaSFeedback@microsoft.com* para conferir perguntas relacionadas ou para obter suporte.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema de SaaS

O modelo de banco de dados multilocatário fragmentado usado nesta amostra permite que um banco de dados de locatários contenha um ou mais locatários. Esta amostra explora o potencial do uso de uma combinação de bancos de dados multilocatário e de locatário único, possibilitando um modelo *híbrido* de gerenciamento de locatários. O gerenciamento desses bancos de dados é complicado. Os [Trabalhos Elásticos](sql-database-elastic-jobs-overview.md) facilitam a administração e o gerenciamento da camada de dados do SQL. Os trabalhos permitem uma execução segura e confiável dos scripts de Transact-SQL como tarefas em um grupo de bancos de dados de locatário. As tarefas são independentes da interação ou entrada do usuário. Esse método pode ser usado para implantar alterações no esquema ou nos dados de referência comuns, em todos os locatários em um aplicativo. Trabalhos Elásticos também podem ser usados para manter uma cópia do modelo final do banco de dados. O modelo é usado para criar novos locatários, garantindo sempre que esquema e os dados de referência mais recentes estão sendo usados.

![tela](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Versão prévia limitada dos Trabalhos Elásticos

Há uma nova versão dos Trabalhos Elásticos, que agora é um recurso integrado do Banco de Dados SQL do Azure. Por integrado queremos dizer que ele não requer serviços ou componentes adicionais. Essa nova versão dos Trabalhos Elásticos está em versão prévia limitada atualmente. Essa versão prévia limitada atualmente dá suporte ao PowerShell para criar contas de trabalho e ao T-SQL para criar e gerenciar trabalhos.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) no Github. Veja as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar um banco de dados de conta de trabalho e uma nova conta de trabalho

Este tutorial exige que você use o PowerShell para criar o banco de dados de conta de trabalho e a conta de trabalho. Assim como o banco de dados MSDB usado pelo SQL Agent, os Trabalhos Elásticos usam um Banco de Dados SQL do Azure para armazenar definições de trabalho, status de trabalho e o histórico. Depois que a conta de trabalho é criada, você pode criar e monitorar trabalhos imediatamente.

1. **No ISE do PowerShell**, abra …*Learning Modules\\Schema Management\\\\Demo-SchemaManagement.ps1*.
2. Pressione **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar um banco de dados de camada *S2* denominado **jobaccount** no servidor de catálogo. O script então cria a conta de trabalho, passando o banco de dados jobaccount como um parâmetro para a chamada de criação da conta de trabalho.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar um trabalho para implantar novos dados de referência para todos os locatários

#### <a name="prepare"></a>Preparar

Cada banco de dados de locatários inclui um conjunto de tipos de local na tabela **VenueTypes**. Os tipos de local definem os tipos de eventos que são hospedados em um local. Neste exercício, você implanta uma atualização em todos os bancos de dados para adicionar dois tipos de local: *Motorcycle Racing* e *Swimming Club*. Esses tipos de local correspondem à imagem de tela de fundo que você vê no aplicativo de eventos de locatário.

Antes de implantar os novos dados de referência, observe o número de tipos de local que já existe, que pode ser 10. Observe clicando no seguinte link para a interface do usuário da Web do Wingtip e, em seguida, clicando no menu suspenso **Tipo de Local**:
- http://events.wingtip-mt.<USER>.trafficmanager.net

Agora você pode contar o número de tipos de local originais. Em particular, observe que nem *Motorcycle Racing* nem *Swimming Club* ainda existem.

#### <a name="steps"></a>Etapas

Agora você cria um trabalho para atualizar a tabela **VenueTypes** em cada banco de dados de locatários adicionando os novos tipos de local.

Para criar um novo trabalho, você usa um conjunto de trabalhos que os procedimentos armazenados do sistema criou no banco de dados *jobaccount*. Os procedimentos foram criados quando a conta de trabalho foi criada.

1. No SSMS, conecte-se ao servidor de locatário: tenants1-mt-\<user\>.database.windows.net

2. Navegue até o banco de dados *tenants1* no servidor *tenants1-mt-\<user\>.database.windows.net*.

3. Consulta a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* ainda não estão na lista de resultados.

4. Conecte-se ao servidor de catálogo, que é *catalog-mt-\<user\>.database.windows.net*.

5. Conecte-se ao banco de dados *jobaccount* no servidor de catálogo.

6. No SSMS, abra o arquivo *…\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Modifique a instrução: defina @User = &lt;user&gt; e substitua o valor User usado quando você implantou o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets.

8. Pressione **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *DeployReferenceData.sql*:

- **sp\_add\_target\_group** cria o nome do grupo de destino *DemoServerGroup* e adiciona os membros de destino ao grupo.

- **sp\_add\_target\_group\_member** adicione os seguintes itens:
    - Um tipo de membro de destino *server*.
        - Este é o servidor *tenants1-mt-&lt;user&gt;* que contém os bancos de dados de locatários.
        - Assim, todos os bancos de dados no servidor são incluídos no trabalho quando o trabalho é executado.
    - Um tipo de membro de destino *database* para o banco de dados final (*basetenantdb*) que reside no servidor *catalog-mt-&lt;user&gt;*,
    - Um tipo de membro de destino *database* para incluir o banco de dados *adhocreporting* que é usado em um tutorial posterior.

- **sp\_add\_job** cria um trabalho chamado *Reference Data Deployment*.

- **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes.

- As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído com êxito. O trabalho atualiza o banco de dados de locatários e atualiza os dois outros bancos de dados que contêm a tabela de referência.

No SSMS, navegue até o banco de dados de locatário no servidor *tenants1-mt-&lt;user&gt;*. Consulta a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* ainda não foram adicionados à tabela. A contagem total de tipos de local deve ter aumentado em duas unidades.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar um trabalho para gerenciar o índice da tabela de referência

Este exercício é semelhante ao exercício anterior. Este exercício cria um trabalho para recriar o índice de chave primária da tabela de referência. Uma recompilação de índice é uma operação de gerenciamento de banco de dados normal que um administrador pode executar após um carregamento de dados grande em uma tabela, para melhorar o desempenho.

1. No SSMS, conecte-se ao banco de dados *jobaccount* no servidor *catalog-mt-&lt;User&gt;.database.windows.net*.

2. No SSMS, abra *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Pressione **F5** para executar o script.

#### <a name="observe"></a>Observar

Observe os seguintes itens no script *OnlineReindex.sql*:

* **sp\_add\_job** cria um novo trabalho chamado *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice.

* As exibições restantes na execução do trabalho no monitor de script. Use essas consultas para examinar o valor do status na coluna **lifecycle** para determinar quando o trabalho foi concluído com êxito em todos os membros do grupo de destino.

## <a name="additional-resources"></a>Recursos adicionais

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Gerenciando bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-overview.md)
* [Criar e gerenciar bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> - Criar uma conta de trabalho para consultar em vários locatários.
> - Atualizar dados em todos os bancos de dados de locatário.
> - Criar um índice em uma tabela em todos os bancos de dados de locatário.

Em seguida, tente o [Tutorial de relatórios ad hoc](saas-multitenantdb-adhoc-reporting.md).


---
title: "Gerenciar o esquema para vários locatários (um aplicativo SaaS de exemplo usando o Banco de Dados SQL do Azure) | Microsoft Docs"
description: "Gerenciar o esquema para vários locatários em um aplicativo SaaS que usa o Banco de Dados SQL do Azure"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 226cda254934fae30410e54148d5cc527e1c7881
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>Gerenciar o esquema para vários locatários no aplicativo SaaS WTP

O tutorial de introdução ao aplicativo WTP mostra como o aplicativo WTP pode provisionar um banco de dados de locatário com seu esquema inicial e registrá-lo no catálogo. Como qualquer aplicativo, o aplicativo WTP evoluirá ao longo do tempo e, às vezes, exigirá alterações no banco de dados. As alterações podem incluir um esquema novo ou alterado, dados de referência novos ou alterados e tarefas de manutenção de rotina do banco de dados para garantir o desempenho ideal do aplicativo. Com um aplicativo SaaS, essas alterações precisam ser implantadas de maneira coordenada em uma frota potencialmente grande de bancos de dados de locatário. As alterações também precisam ser incorporadas ao processo de provisionamento para futuros bancos de dados de locatário.

Este tutorial explora dois cenários: implantação de atualizações de dados de referência para todos os locatários e reajuste de um índice na tabela que contém os dados de referência. Para executar essas operações em todos os locatários é usado o recurso [Trabalhos elásticos](sql-database-elastic-jobs-overview.md) e também um banco de dados de locatário *golden*, que é usado como um modelo para novos bancos de dados.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Criar uma conta de trabalho para consultar em vários locatários
> * Atualizar dados em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário


Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:

* O aplicativo WTP foi implantado. Para implantar em menos de cinco minutos, consulte [Implantar e explorar o aplicativo SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A última versão do SQL Server Management Studio (SSMS) está instalada. [Baixar e Instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Este tutorial usa funcionalidades do serviço do Banco de Dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Como esta é uma versão prévia limitada, você deverá contatar SaaSFeedback@microsoft.com para perguntas relacionadas ou para obter suporte.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introdução aos padrões de gerenciamento de esquema de SaaS

O padrão de SaaS de único locatário por banco de dados se beneficia, de muitas maneiras, do isolamento de dados resultante, mas, ao mesmo tempo, apresenta a complexidade adicional de manter e gerenciar vários bancos de dados. Os [Trabalhos Elásticos](sql-database-elastic-jobs-overview.md) facilitam a administração e o gerenciamento da camada de dados do SQL. Os trabalhos permitem que você execute tarefas (scripts T-SQL) de maneira segura e confiável, independentes da interação ou da entrada do usuário, em relação a um grupo de bancos de dados. Esse método pode ser usado para implantar esquemas e alterações de dados de referência comum em todos os locatários em um aplicativo. Os Trabalhos Elásticos também podem ser usados para manter uma cópia *golden* do banco de dados usado para criar novos locatários, garantindo que ela sempre tenha os dados de esquema e de referência mais recentes.

![tela](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Versão prévia limitada dos Trabalhos Elásticos

Há uma nova versão dos Trabalhos Elásticos, que agora é um recurso integrado do Banco de Dados SQL do Azure (que não requer serviços ou componentes adicionais). Essa nova versão dos Trabalhos Elásticos está em versão prévia limitada atualmente. Essa versão prévia limitada atualmente dá suporte ao PowerShell para criar contas de trabalho e ao T-SQL para criar e gerenciar trabalhos.

> [!NOTE]
> *Este tutorial usa funcionalidades do serviço do Banco de Dados SQL que estão em uma versão prévia limitada (trabalhos de Banco de Dados Elástico). Se você quiser fazer este tutorial, forneça sua ID de assinatura para SaaSFeedback@microsoft.com com o assunto = Elastic Jobs Preview. Após receber a confirmação de que sua assinatura foi habilitada, [baixe e instale as versões de pré-lançamento mais recentes dos cmdlets de trabalhos](https://github.com/jaredmoo/azure-powershell/releases). Como esta é uma versão prévia limitada, você deverá contatar SaaSFeedback@microsoft.com para perguntas relacionadas ou para obter suporte.*

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="create-a-job-account-database-and-new-job-account"></a>Criar um banco de dados de conta de trabalho e uma nova conta de trabalho

Este tutorial exige que você use o PowerShell para criar o banco de dados de conta de trabalho e a conta de trabalho. Como no MSDB e no SQL Agent, os Trabalhos Elásticos usam um Banco de Dados SQL do Azure para armazenar definições de trabalho, status de trabalho e o histórico. Depois que a conta de trabalho é criada, você pode criar e monitorar trabalhos imediatamente.

1. Abra ...\\Módulos de aprendizado\\Gerenciamento de esquema\\*Demo-SchemaManagement.ps1* no **ISE do PowerShell**.
1. Pressione **F5** para executar o script.

O script *Demo-SchemaManagement.ps1* chama o script *Deploy-SchemaManagement.ps1* para criar um banco de dados *S2* denominado **jobaccount** no servidor de catálogo. Em seguida, ele cria a conta de trabalho, passando o banco de dados jobaccount como um parâmetro para a chamada de criação da conta de trabalho.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Criar um trabalho para implantar novos dados de referência para todos os locatários

Cada banco de dados de locatário inclui um conjunto de tipos de local que define os tipos de eventos que são sediados em um local. Neste exercício, você implanta uma atualização em todos os bancos de dados de locatário para adicionar dois tipos de local: *Motorcycle Racing* e *Swimming Club*. Esses tipos de local correspondem à imagem de tela de fundo que você vê no aplicativo de eventos de locatário.

Clique no menu suspenso Tipo de Local e verifique se apenas 10 opções de tipo de local estão disponíveis e, especificamente, se "Motorcycle Racing" e "Swimming Club" não são incluídos na lista.

Agora vamos criar um trabalho para atualizar a tabela *VenueTypes* em todos os bancos de dados de locatário e adicionar os novos tipos de local.

Para criar um novo trabalho, usamos um conjunto de trabalhos que os procedimentos armazenados do sistema criou no banco de dados jobaccount quando a conta de trabalho foi criada.

1. Abra o SSMS e conecte-se ao servidor de catálogo: catalog-\<usuário\>.database.windows.net
1. Conecte-se também ao servidor de locatário: tenants1-\<usuário\>.database.windows.net
1. Navegue até o banco de dados *contosoconcerthall* do servidor *tenants1* e consulte a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* **não estão** na lista de resultados.
1. Abra o arquivo ...\\Módulos de aprendizado\\Gerenciamento de esquema\\DeployReferenceData.sql
1. Modifique \<usuário\>, fornecendo o nome de usuário que você usou quando implantou o aplicativo WTP, em todos os três locais no script
1. Verifique se está conectado ao banco de dados jobaccount e pressione **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo de destino DemoServerGroup, agora é preciso adicionar os membros de destino.
* **sp\_add\_target\_group\_member** adiciona um tipo de membro de destino de *servidor*, que considera todos os bancos de dados nesse servidor (observe que esse é o servidor customer1-&lt;WtpUser&gt; que contém os bancos de dados de locatário) que, no momento da execução do trabalho, devem ser incluídos no trabalho. O segundo está adicionando um tipo de membro de destino de *banco de dados*, especificamente banco de dados "golden", baseTenantDB, que reside no servidor catalog-&lt;WtpUser&gt; e, por fim, outro tipo de membro de grupo de destino de *banco de dados* para incluir o banco de dados adhocanalytics que será usado em um tutorial posterior.
* **sp\_add\_job** cria um trabalho chamado “Reference Data Deployment”
* **sp\_add\_jobstep** cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar a tabela de referência, VenueTypes
* As exibições restantes no script exibem a existência dos objetos e monitoram a execução do trabalho. Examine o valor de status da coluna **ciclo de vida**. O trabalho foi concluído com êxito em todos os bancos de dados de locatário e nos dois bancos de dados adicionais que contêm a tabela de referência.

1. No SSMS, navegue até o banco de dados *contosoconcerthall* do servidor *tenants1* e consulte a tabela *VenueTypes* para confirmar que *Motorcycle Racing* e *Swimming Club* **estão** na lista de resultados.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Criar um trabalho para gerenciar o índice da tabela de referência

De maneira semelhante ao exercício anterior, este exercício cria um trabalho para recompilar o índice na chave primária da tabela de referência, uma operação típica de gerenciamento de banco de dados que um administrador deve executar após um carregamento de grandes volumes de dados em uma tabela.

Crie um trabalho usando os mesmos trabalhos dos procedimentos armazenados do "sistema".

1. Abra o SSMS e conecte-se ao servidor catalog-&lt;WtpUser&gt;.database.windows.net
1. Abra o arquivo ...\\Módulos de aprendizado\\Gerenciamento de esquema\\OnlineReindex.sql
1. Clique com botão direito do mouse, selecione Conexão e conecte-se ao servidor catalog-&lt;WtpUser&gt;.database.windows.net, se ainda não estiver conectado
1. Verifique se está conectado ao banco de dados jobaccount e pressione F5 para executar o script

* sp\_add\_job cria um novo trabalho chamado “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* sp\_add\_jobstep cria a etapa de trabalho que contém o texto do comando T-SQL para atualizar o índice




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Criar uma conta de trabalho para consultar em vários locatários
> * Atualizar dados em todos os bancos de dados de locatário
> * Criar um índice em uma tabela em todos os bancos de dados de locatário

[Tutorial de análise ad hoc](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Gerenciando bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-overview.md)
* [Criar e gerenciar bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-create-and-manage.md)

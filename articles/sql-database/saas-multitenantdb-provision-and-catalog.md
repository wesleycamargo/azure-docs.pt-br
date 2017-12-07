---
title: "Provisionar e catalogar novos locatários em um aplicativo SaaS usando um banco de dados SQL multilocatário do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: e7de7bb545e0ce04dc1b3dd398cc920213d09bae
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Provisionar e catalogar novos locatários em um aplicativo SaaS usando um banco de dados SQL multilocatário fragmentado

Neste tutorial, você aprenderá sobre os padrões para provisionamento e catalogação de locatários ao trabalhar com um modelo de banco de dados multilocatário fragmentado. 

Um esquema multilocatário, que inclui uma ID de locatário na chave primária das tabelas que contêm dados de locatário, permite que vários locatários sejam armazenados em um único banco de dados. Para dar suporte a grandes números de locatários, os dados de locatário são distribuídos em vários fragmentos ou bancos de dados. Os dados de um locatário sempre estão totalmente contidos em um único banco de dados.  Um catálogo é usado para armazenar o mapeamento de locatários em bancos de dados.   

Também é possível optar por popular alguns bancos de dados com apenas um único locatário. Bancos de dados que armazenam vários locatários privilegiam um custo mais baixo por locatário em detrimento do isolamento de locatários.  Bancos de dados que contêm apenas um único locatário favorecerem o isolamento em vez do baixo custo.  Bancos de dados com vários locatários e locatários únicos podem ser combinados no mesmo aplicativo SaaS para otimizar o custo ou o isolamento para cada locatário. Os locatários podem receber seu próprio banco de dados quando provisionados ou podem ser movidos para o seu próprio banco de dados mais tarde.

   ![Aplicativo de banco de dados multilocatário fragmentado com catálogo de locatários](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Padrão do catálogo de locatários

Se os dados de locatário forem distribuídos entre vários bancos de dados, será importante saber o local em que os dados de cada locatário serão armazenados. Um banco de dados de catálogo contém o mapeamento entre um locatário e o banco de dados que armazena seus dados.

Cada locatário é identificado por uma chave no catálogo. Nos aplicativos SaaS Wingtip Tickets, a chave de locatário é formada de um hash do nome do locatário. Isso permite que o aplicativo extraia o nome do locatário de uma URL de página da Web e use-o para se conectar ao banco de dados apropriado. Outros esquemas de chave de locatário também podem ser usados.

Usar um catálogo permite que o nome ou o local de um banco de dados de locatário seja alterado após provisionar sem interromper o aplicativo.  Em um modelo de banco de dados multilocatário, isso acomoda a movimentação de um locatário entre bancos de dados.  O catálogo também pode ser usado para indicar para um aplicativo se um locatário está offline para manutenção ou para outras ações.

O catálogo pode ser estendido para armazenar metadados adicionais de locatário ou de banco de dados, como a camada ou edição de um banco de dados, versão de esquema de banco de dados, nome de locatário e plano de serviço ou SLA e outras informações para permitem o gerenciamento de aplicativos, atendimento ao cliente ou processos de devOps.  

O catálogo também pode ser usado para habilitar relatórios entre locatários, gerenciamento de esquemas e extração de dados para fins de análise. 

### <a name="elastic-database-client-library"></a>Biblioteca de cliente do Banco de Dados Elástico 
Nos aplicativos SaaS Wingtip Tickets, o catálogo é implementado no banco de dados *tenantcatalog* usando os recursos de Gerenciamento de Fragmentos da [EDCL (Biblioteca de Cliente do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). A biblioteca permite que um aplicativo crie, gerencie e use um "mapa de fragmentos" com backup no banco de dados. Um mapa de fragmentos contém uma lista de fragmentos (bancos de dados) e o mapeamento entre chaves (Ids de locatário) e fragmentos.  As funções da EDCL podem ser usadas em aplicativos ou scripts do PowerShell durante o provisionamento de locatários para criar as entradas no mapa de fragmentos, e posteriormente, para conectar-se ao banco de dados correto. A biblioteca armazena informações de conexão em cache para minimizar o tráfego no banco de dados de catálogo e para acelerar a conexão. 

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis no banco de dados do catálogo, mas *não os edite!* Edite os dados de mapeamento somente com o uso de APIs da Biblioteca de Cliente do Banco de Dados Elástico. Manipular diretamente os dados de mapeamento gera o risco de corrupção do catálogo e não há suporte para isso.


## <a name="tenant-provisioning-pattern"></a>Padrão de provisionamento de locatários

Ao provisionar um novo locatário no modelo de banco de dados multilocatário fragmentado, será necessário determinar primeiro se o locatário deve ser provisionado em um banco de dados compartilhado ou considerando seu próprio banco de dados. Se for um banco de dados compartilhado, será necessário determinar se há um espaço em um banco de dados existente ou se um novo banco de dados é necessário. Se um novo banco de dados for necessário, ele deverá ser provisionado no local e na camada de serviço adequados, inicializado usando dados de referência e de esquema adequados e, em seguida, registrado no catálogo. Por fim, o mapeamento de locatários pode ser adicionado para referenciar o fragmento adequado.

Provisione o banco de dados executando scripts SQL, implantando um bacpac ou copiando um banco de dados de modelo. Os aplicativos SaaS Wingtip Tickets copiam um banco de dados de modelo para criar novos bancos de dados de locatário.

A abordagem de provisionamento de banco de dados deve ser compreendida em sua estratégia geral de gerenciamento de esquema, que precisa garantir que novos bancos de dados sejam provisionados com o esquema mais recente.  Isso é explorado com mais detalhes no [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md).  

Os scripts de provisionamento de locatários neste tutorial incluem o provisionamento de um locatário em um banco de dados existente compartilhado com outros locatários e o provisionamento de um locatário em seu próprio banco de dados. Os dados de locatário são inicializados e registrados no mapa de fragmentos de catálogo. No aplicativo de exemplo, os bancos de dados que contêm vários locatários recebem um nome genérico, como *tenants1*, *tenants2*, etc., enquanto os bancos de dados que contêm um único locatário recebem o nome do locatário. As convenções de nomenclatura específicas usadas no exemplo não são uma parte crítica do padrão, uma vez que o uso de um catálogo permite que qualquer nome seja atribuído ao banco de dados.  

## <a name="provision-and-catalog-tutorial"></a>Tutorial para provisionar e catalogar

Neste tutorial, você aprenderá como:

> [!div class="checklist"]

> * Provisionar um locatário em um banco de dados multilocatário
> * Provisionar um locatário em um banco de dados de locatário único
> * Provisionar um lote de locatários em bancos de dados multilocatários e de locatário único
> * Registrar um banco de dados e o mapeamento de locatários em um catálogo

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado. Para implantá-lo em menos de cinco minutos, confira [Implantar e explorar o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets. 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Provisionar um locatário em um banco de dados compartilhado com outros locatários

Para entender como o aplicativo Wingtip Tickets implementa o novo provisionamento de locatários em um banco de dados compartilhado, adicione um ponto de interrupção e percorra o fluxo de trabalho:

1. No _ISE do PowerShell_, abra ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ e defina estes parâmetros:
   * **$TenantName** = **Bushwillow Blues**, o nome de um novo local.
   * **$VenueType** = **blues**, um dos tipos de local predefinidos: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (em minúsculas, sem espaços).
   * **$DemoScenario** = **1**, para *Provisionar um locatário em um banco de dados compartilhado com outros locatários*.

1. Adicione um ponto de interrupção, colocando o cursor em qualquer local na linha 38, que diz: *New-Tenant `* e pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Para executar o script, pressione **F5**.

1. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para intervir no código.

   ![depurar](media/saas-multitenantdb-provision-and-catalog/debug.png)

Rastreie a execução do script usando as opções de menu **Depurar**, **F10** e **F11** para contornar ou intervir nas funções chamadas. Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


A seguir, estão os principais elementos do fluxo de trabalho de provisionamento que você percorrerá:

* **Calcular a nova chave de locatário**. Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
* **Verificar se a chave de locatário já existe**. O catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
* **Inicializar o locatário no banco de dados de locatário padrão**. O banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
* **Registrar o locatário no catálogo** O mapeamento entre a nova chave de locatário e o banco de dados tenants1 existente é adicionado ao catálogo. 
* **Adicionar o nome do locatário a uma tabela de extensão de catálogo**. O nome do local é adicionado à tabela Locatários no catálogo.  Isso mostra como o banco de dados Catálogo pode ser estendido para dar suporte a dados específicos de aplicativos adicionais.
* **Abrir página Eventos para o novo locatário**. A página de eventos *Bushwillow azuis* é aberta no navegador:

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>Provisionar um locatário em seu próprio banco de dados

Agora, percorra o processo ao criar um locatário em seu próprio banco de dados:

1. Ainda em ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_, defina estes parâmetros:
   * **$TenantName** = **Sequoia Soccer**, o nome de um novo local.
   * **$VenueType** = **soccer**, um dos tipos de local predefinidos: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (em minúsculas, sem espaços).
   * **$DemoScenario** = **2**, para *Provisione um locatário em seu próprio banco de dados*.

1. Adicione um novo ponto de interrupção posicionando seu cursor em qualquer lugar na linha 57, que diz: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* e pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Pressione **F5** para executar o script.

1. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para entrar no código.  Use **F10** e **F11** para contornar e intervir em funções para rastrear a execução.

A seguir, estão os principais elementos do fluxo de trabalho que você percorrerá ao rastrear o script:

* **Calcular a nova chave de locatário**. Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
* **Verificar se a chave de locatário já existe**. O catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
* **Criar um novo banco de dados de locatário**. O banco de dados é criado copiando o banco de dados *basetenantdb* usando um modelo do Resource Manager.  O nome do novo banco de dados é baseado no nome do locatário.
* **Adicionar banco de dados ao catálogo**. O novo banco de dados de locatário é registrado como um fragmento no catálogo.
* **Inicializar o locatário no banco de dados de locatário padrão**. O banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
* **Registrar locatário no catálogo** O mapeamento entre a nova chave de locatário e o banco de dados *sequoiasoccer* é adicionado ao catálogo.
* **O nome do locatário é adicionado ao catálogo**. O nome do local é adicionado à tabela de extensão Locatários no catálogo.
* **Abrir página Eventos para o novo locatário**. A página de eventos *Sequoia Soccer* é aberta no navegador:

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote com 17 locatários. É recomendável provisionar esse lote de locatários antes de iniciar outros tutoriais do Wingtip Tickets, de forma que haja mais bancos de dados com os quais trabalhar.


1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizagem\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e altere o parâmetro *$DemoScenario* para 4:
   * **$DemoScenario** = **4**, para *Provisionar um lote de locatários em um banco de dados compartilhado*.
1. Pressione **F5** e execute o script.


### <a name="verify-the-deployed-set-of-tenants"></a>Verificar o conjunto implantado de locatários 
Neste estágio, você tem uma combinação de locatários implantados em um banco de dados compartilhado e locatários implantados em seus próprios bancos de dados. O Portal do Azure pode ser usado para inspecionar os bancos de dados criados. No [Portal do Azure](https://portal.azure.com), abra o servidor **tenants1-mt-\<USUÁRIO\>** navegando até a lista de servidores SQL.  A lista de **bancos de dados SQL** deve incluir o banco de dados **tenants1** compartilhado e os bancos de dados para os locatários que estão em seu próprio banco de dados:

   ![lista de banco de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Embora o Portal do Azure mostre os bancos de dados de locatário, ele não permite que você veja os locatários *dentro* do banco de dados compartilhado. A lista completa de locatários pode ser vista na página Hub de eventos do Wingtip Tickets e procurando no catálogo:   

**Usando a página do hub de eventos de Tíquetes Wingtip** <br>
Abra a página Hub de eventos no navegador (http:events.wingtip-mt.\<USUÁRIO\>.trafficmanager.net)  

**Usando um banco de dados do catálogo** <br>
A lista completa de locatários e seu banco de dados correspondente está disponível no catálogo. Uma exibição de SQL é fornecida no banco de dados tenantcatalog que associa o nome do locatário armazenado na tabela Locatários ao nome do banco de dados nas tabelas Gerenciamento de fragmentos. Essa exibição demonstra bem o valor de estender o metadados armazenado no catálogo.

1. Em *SSMS (SQL Server Management Studio)*, conecte-se ao servidor de locatários em **catalog-mt.\<USUÁRIO\>.database.windows.net**, com Logon: **developer**, Senha: **P@ssword1**

    ![caixa de diálogo de conexão do SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

1. No *Pesquisador de Objetos*, navegue até as exibições no banco de dados *tenantcatalog*.
1. Clique com o botão direito do mouse na exibição *TenantsExtended* e escolha **Selecionar as 1000 primeiras linhas**. Observe o mapeamento entre o nome do locatário e o banco de dados de diferentes locatários.

    ![Exibição ExtendedTenants no SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Outros padrões de provisionamento interessantes incluem:

**Pré-provisionamento de bancos de dados em pools elásticos.** O padrão de pré-provisionamento explora o fato de que, ao usar pools elásticos, a cobrança é pelo pool, não pelos bancos de dados. Assim, os bancos de dados podem ser adicionados a um pool elástico antes que eles sejam necessários sem incorrer em custo extra. Ao pré-provisionar bancos de dados em um pool e alocá-los quando necessário, o tempo gasto para provisionar um locatário em um banco de dados pode ser reduzido significativamente. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

**Provisionamento automático.** No padrão de provisionamento automático, um serviço de provisionamento dedicado é usado para provisionar servidores, pools e bancos de dados automaticamente, conforme necessário – incluindo o pré-provisionamento de bancos de dados em pools elásticos. E, se os bancos de dados forem encerrados e excluídos, as lacunas que isso cria nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento conforme desejado. Esse serviço poderá ser simples ou complexo – por exemplo, manipulação do provisionamento em várias áreas geográficas e poderá configurar a replicação geográfica para a recuperação de desastre. Com o padrão de provisionamento automático, um script ou aplicativo cliente enviaria uma solicitação de provisionamento para uma fila a ser processada pelo serviço de provisionamento e, em seguida, pesquisaria para determinar a conclusão. Se o pré-provisionamento fosse usado, as solicitações seriam manipuladas rapidamente, enquanto outro serviço gerencia o provisionamento de um banco de dados de substituição em execução em segundo plano.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Provisionar um novo locatário único em um banco de dados multilocatário e em seu próprio banco de dados
> * Provisionar um lote de locatários adicionais
> * Percorrer os detalhes do provisionamento de locatários e do registro deles no catálogo

Experimente o [Tutorial de monitoramento de desempenho](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md)
* [Como depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

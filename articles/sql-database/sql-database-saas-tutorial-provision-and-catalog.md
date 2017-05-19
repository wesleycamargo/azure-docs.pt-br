---
title: "Provisionar e catalogar novos locatários (aplicativo SaaS de exemplo usando o Banco de Dados SQL do Azure) | Microsoft Docs"
description: "Provisionar e catalogar novos locatários"
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
ms.openlocfilehash: 4eeada941f8615fa04624bc725efcb44f05d56c7
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Provisionar novos locatários e registrá-los no catálogo

Neste tutorial, você provisiona novos locatários no aplicativo SaaS WTP (Wingtip Tickets Platform). Você cria locatários, bancos de dados de locatário e registra locatários no catálogo. O *catálogo* é um banco de dados que mantém o mapeamento entre os vários locatários dos aplicativos SaaS e seus dados. Use esses scripts para explorar os padrões de provisionamento e catálogo usados e como é a implementação do registro de novos locatários no catálogo. O catálogo desempenha uma função importante no direcionamento das solicitações de aplicativo para os bancos de dados corretos.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Provisionar um novo único locatário
> * Provisionar um lote de locatários adicionais
> * Intervir nos detalhes do provisionamento de novos locatários e do registro deles no catálogo.


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo WTP foi implantado. Para implantar em menos de cinco minutos, consulte [Implantar e explorar o aplicativo SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo de SaaS

Em um aplicativo SaaS multilocatário com suporte de banco de dados, é importante saber o local em que estão armazenadas as informações de cada locatário. No padrão de catálogo de SaaS, um banco de dados de catálogo é usado para manter o mapeamento entre locatários e o local em que os respectivos dados estão armazenados. O aplicativo WTP usa uma arquitetura de banco de dados de único locatário, mas o padrão básico de armazenar o mapeamento de locatário ao banco de dados em um catálogo aplica-se tanto com o uso de um banco de dados multilocatário quanto com o uso de banco de dados de único locatário.

A cada locatário é atribuída uma chave que distingue seus dados no catálogo. No aplicativo WTP, a chave é formada por um hash do nome do locatário. Esse padrão permite que a parte do nome do locatário da URL do aplicativo seja usada para construir a chave e recuperar a conexão de um locatário específico. Outros esquemas de ID podem ser usados sem afetar o padrão geral.

O catálogo no aplicativo WTP é implementado usando a tecnologia de Gerenciamento de Fragmentos na [EDCL (Biblioteca de Cliente do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). A EDCL é responsável por criar e gerenciar um _catálogo_ com suporte de banco de dados em que um _mapa de fragmentos_ é mantido. O catálogo contém o mapeamento entre as chaves (locatários) e seus bancos de dados (fragmentos).

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis no banco de dados de catálogo, mas *não os edite*! Edite os dados de mapeamento somente com o uso de APIs da Biblioteca de Cliente do Banco de Dados Elástico. Manipular diretamente os dados de mapeamento gera o risco de corrupção do catálogo e não há suporte para isso.

O aplicativo SaaS Wingtip provisiona novos locatários copiando um banco de dados *golden*.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

Se você já criou um locatário no primeiro tutorial do WTP fique à vontade para ir direto para a próxima seção: [provisionar um lote de locatários](#provision-a-batch-of-tenants).

Execute o script *Demo-ProvisionAndCatalog* para criar um locatário rapidamente e registrá-lo no catálogo:

1. Abra o **Demo-ProvisionAndCatalog.ps1** no ISE do PowerShell e defina os seguintes valores:
   * **$TenantName** = o nome do novo local do evento (por exemplo, *Bushwillow Blues*). 
   * **$VenueType** = um dos tipos predefinidos de local: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = 1, deixe isso definido como _1_ para **Provisionar um único locatário**.

1. Pressione **F5** e execute o script.

Depois que o script for concluído, o novo locatário será provisionado e seu aplicativo *Eventos* será aberto no navegador:

![Novo locatário](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote de locatários adicionais. É recomendável que você faça isso antes de realizar os outros tutoriais do WTP.

1. Abra ...\\Módulos de aprendizado\\Utilitários\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell* e defina os seguintes valores:
   * **$DemoScenario** = **3**, Definir como **3** para **Provisionar um lote de locatários**.
1. Pressione **F5** e execute o script.

O script implanta um lote de locatários adicionais. Ele usa um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e, em seguida, delega o provisionamento de cada banco de dados a um modelo vinculado. O uso de modelos dessa maneira permite que Azure Resource Manager seja o agente do processo de provisionamento do seu script. Os modelos provisionam bancos de dados em paralelo nos locais em que isso for possível e lidam com repetições, se necessário, otimizando o processo geral. O script é idempotente, portanto, se for interrompido, execute-o novamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificar se o lote de locatários foi implantado com êxito

* Abra o servidor *tenants1* no [Portal do Azure](https://portal.azure.com) e clique em **bancos de dados SQL**:

   ![lista de banco de dados](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Passo a passo detalhado para provisionar e catalogar

Para compreender melhor como o aplicativo Wingtip implementa o provisionamento do novo locatário, execute o script *Demo-ProvisionAndCatalog* novamente e provisione mais um locatário. Desta vez, adicione um ponto de interrupção e percorra o fluxo de trabalho:

1. Abra ...\\Módulos de aprendizado\Utilitários\_Demo-ProvisionAndCatalog.ps1_ e defina o seguinte como os novos valores de locatário que não existem no catálogo atual:
   * **$TenantName** = definir como um novo nome (por exemplo, *Hackberry Hitters*).
   * **$VenueType** = usar um dos tipos predefinidos de local (por exemplo, *judo*).
   * **$DemoScenario** = 1, definir como **1** para **Provisionar um único locatário**.

1. Adicione um ponto de interrupção, colocando o cursor em qualquer local na linha a seguir: *New-Tenant `* e pressione **F9**.

   ![ponto de interrupção](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Pressione **F5** para executar o script. Quando o ponto de interrupção for alcançado, pressione **F11** para intervir. Rastreie a execução do script usando **F10** e **F11** para passar por cima ou intervir nas funções chamadas. [Dicas sobre o trabalho com scripts e a depuração de scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examinar a implementação do catálogo e o provisionamento em detalhes ao percorrer o script

Os script provisiona e cataloga novos locatários seguindo as etapas a seguir:

1. **Importar o módulo SubscriptionManagement.psm1** que contém funções para entrar no Azure e selecionar a assinatura do Azure com a qual você está trabalhando.
1. **Importar o módulo CatalogAndDatabaseManagement.psm1** que fornece um catálogo e uma abstração em nível de locatário em relação às funções de [Gerenciamento de Fragmentos](sql-database-elastic-scale-shard-map-management.md). Este é um módulo importante que encapsula a maior parte do padrão do catálogo e vale a pena explorar.
1. **Obter detalhes de configuração**. Intervenha em _Get-Configuration_ (com **F11**) e veja como a configuração do aplicativo é especificada. Os nomes de recursos e outros valores específicos do aplicativo são definidos aqui, mas não altere nenhum desses valores até que você esteja familiarizado com os scripts.
1. **Obter o objeto de catálogo**. Intervenha em *Get-Catalog* para ver como o catálogo é inicializado usando as funções de Gerenciamento de Fragmentos que são importadas do **AzureShardManagement.psm1**. O catálogo é composto dos seguintes objetos:
   * O $catalogServerFullyQualifiedName é criado usando o tronco padrão além do seu nome de Usuário: _catalog-\<usuário\>.database.windows.net_.
   * O $catalogDatabaseName é recuperado da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado no banco de dados do catálogo.
   * O objeto $shardMap é inicializado por meio do mapa do fragmentos do *tenantcatalog* no banco de dados de catálogo.
   Um objeto de catálogo é composto e retornado e usado no script de nível superior.
1. **Calcular a nova chave de locatário**. Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
1. **Verificar se a chave de locatário já existe**. O catálogo é verificado para garantir que a chave está disponível.
1. **O banco de dados do locatário é provisionado com New-TenantDatabase.** Use **F11** para intervir e ver como o banco de dados é provisionado usando um modelo do Resource Manager.
    
O nome do banco de dados é construído com base no nome do locatário para deixar claro qual fragmento pertence a qual locatário. (Outras estratégias de nomeação de banco de dados podem se facilmente usadas.)

Um modelo do Resource Manager é usado para **criar um banco de dados de locatário copiando um banco de dados *golden*** (baseTenantDB) no servidor de catálogo.  Uma abordagem alternativa seria criar um banco de dados vazio e, em seguida, inicializá-lo com a importação de um bacpac.

O modelo do Resource Manager está na pasta ...\\Módulos de aprendizado\\Comum\\: *tenantdatabasecopytemplate.json*

Depois que o banco de dados do locatário é criado, ele é, em seguida, inicializado com o nome do local (locatário) e o tipo de local. Outra inicialização também poderia ser feita aqui.

O banco de dados de locatário é registrado no catálogo com *Add-TenantDatabaseToCatalog* usando a chave do locatário. Use **F11** para ver os detalhes:

* O banco de dados de catálogo é adicionado ao mapa de fragmentos (a lista de bancos de dados conhecidos).
* O mapeamento que vincula o valor da chave ao fragmento é criado.
* Dados de metadados adicionais (o nome do local) sobre o locatário são adicionados.

Depois que o provisionamento for concluído, a execução retorna ao script original *Demo-ProvisionAndCatalog* e a página de **eventos** do novo locatário é aberta no navegador:

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Outros padrões de provisionamento não mencionados nesse tutorial incluem:

**Pré-provisionamento de bancos de dados.** Esse padrão explora o fato de que os bancos de dados em um pool elástico não adicionam custo extra (a cobrança é para o pool elástico e não para os bancos de dados) e os bancos de dados ociosos não consomem nenhum recurso. Ao pré-provisionar bancos de dados em um pool e alocá-los quando necessário, o tempo de integração do locatário poderá ser reduzido significativamente. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

**Provisionamento automático.** Nesse padrão, um serviço de provisionamento dedicado é usado para provisionar servidores, pools e bancos de dados automaticamente, conforme necessário, incluindo pré-provisionamento de bancos de dados em pools elásticos, se desejado. E se os bancos de dados forem encerrados e excluídos, as lacunas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento conforme desejado. Esse serviço pode ser simples ou complexo, por exemplo, lidando com o provisionamento em várias regiões geográficas e pode configurar a replicação geográfica automaticamente, se essa estratégia está sendo usada para a recuperação de desastres. Com o padrão de provisionamento automático, um script ou aplicativo cliente poderia enviar uma solicitação de provisionamento para uma fila para ser processada pelo serviço de provisionamento e, em seguida, pesquisaria o serviço para determinar a conclusão. Se o pré-provisionamento fosse usado, as solicitações seriam manipuladas rapidamente com o serviço gerenciando o provisionamento de um banco de dados de substituição em execução em segundo plano.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Parando a cobrança relacionada ao aplicativo SaaS Wingtip

Se você não planeja continuar com outro tutorial, é recomendável que você exclua todos os recursos para suspender a cobrança. Exclua o grupo de recursos no qual o aplicativo WTP foi implantado e todos os recursos relacionados serão excluídos.

* Navegue até o grupo de recursos do aplicativo no portal e exclua-o para interromper toda a cobrança relacionada a essa implantação do WTP.

## <a name="tips"></a>Dicas

* A EDCL também oferece recursos importantes que permitem aos aplicativos cliente se conectar e manipular o catálogo. Você também pode usar a EDCL para recuperar uma conexão ADO.NET para um determinado valor de chave, permitindo que o aplicativo se conecte ao banco de dados correto. O cliente armazena essas informações de conexão em cache para minimizar o tráfego ao banco de dados de catálogo e acelerar o aplicativo.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Provisionar um novo único locatário
> * Provisionar um lote de locatários adicionais
> * Intervir nos detalhes do provisionamento de novos locatários e do registro deles no catálogo.

[Tutorial de monitoramento de desempenho](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Biblioteca de cliente do banco de dados elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Como depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


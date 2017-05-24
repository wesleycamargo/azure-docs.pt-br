---
title: Monitorar o desempenho de um aplicativo SaaS de Banco de Dados SQL | Microsoft Docs
description: Monitorar e gerenciar o desempenho para o aplicativo WTP (Wingtip Tickets) de exemplo de Banco de Dados Azure SQL
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
ms.openlocfilehash: af9511978718af10c97bee6af3a2835c9d2c1ff4
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Monitorar o desempenho do aplicativo SaaS WTP de exemplo

Neste tutorial, são demonstrados os recursos internos de monitoramento e alerta do Banco de Dados SQL e dos pools elásticos e, em seguida, são explorados vários cenários importantes de gerenciamento de desempenho usados em aplicativos SaaS.

O aplicativo Wingtip Tickets usa um modelo de dados de único locatário, em que cada local (locatário) tem seu próprio banco de dados. Como muitos aplicativos SaaS, o padrão esperado de carga de trabalho do locatário é imprevisível e esporádico. Em outras palavras, as vendas de ingressos podem ocorrer a qualquer momento. Para tirar proveito desse padrão de uso típico do banco de dados, os bancos de dados de locatário são implantados em pools de banco de dados elástico. Os pools elásticos otimizam o custo de uma solução ao compartilhar recursos entre vários bancos de dados. Com esse tipo de padrão, é importante monitorar o uso de recursos do banco de dados e do pool para garantir que as cargas sejam razoavelmente balanceadas entre os pools. Você também precisa garantir que os bancos de dados individuais tenham recursos adequados e que os pools não atinjam seus limites de [eDTU](sql-database-what-is-a-dtu.md). Este tutorial explora maneiras para monitorar e gerenciar bancos de dados e pools e como realizar uma ação corretiva em resposta às variações na carga de trabalho.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]

> * Simular o uso em bancos de dados de locatário executando um gerador de carga fornecido
> * Monitorar os bancos de dados de locatário conforme eles respondem ao aumento de carga
> * Escalar verticalmente o pool elástico em resposta ao aumento da carga do banco de dados
> * Provisionar um segundo pool elástico para realizar o balanceamento de carga da atividade do banco de dados


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo WTP foi implantado. Para implantar em menos de cinco minutos, consulte [Implantar e explorar o aplicativo WTP SaaS](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gerenciamento de desempenho de SaaS

O gerenciamento de desempenho do banco de dados consiste na compilação e análise de dados de desempenho e, em seguida, reagir a esses dados, ajustando parâmetros para manter um tempo de resposta aceitável para o seu aplicativo. Ao hospedar vários locatários, os pools de banco de dados elástico são uma maneira econômica de fornecer e gerenciar os recursos de um grupo de bancos de dados com cargas de trabalho imprevisíveis. Com determinados padrões carga de trabalho, não mais que dois bancos de dados S3 poderiam se beneficiar sendo gerenciados em um pool. Um pool não apenas compartilha os custos dos recursos, mas também pode eliminar a necessidade de monitorar e acompanhar constantemente bancos de dados individuais.

![mídia](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

Os pools e os bancos de dados nos pools ainda precisam ser monitorados para garantir que permaneçam dentro dos intervalos de desempenho aceitáveis. Ajuste a configuração do pool para atender às necessidades da carga de trabalho agregada, garantindo que os eDTUs do pool estão adequados para a carga de trabalho geral. Ajuste os valores de eDTU mínimo por banco de dados e máximo por banco de dados com valores apropriados para os requisitos específicos do seu aplicativo.

### <a name="performance-management-strategies"></a>Estratégias de gerenciamento de desempenho

* Para evitar ter que monitorar manualmente o desempenho, é muito mais eficiente **definir alertas que são disparados se os bancos de dados ou pools se afastarem dos intervalos normais**.
* Para responder às flutuações de curto prazo no nível de desempenho agregado de um pool, o **nível de eDTU do pool pode ser aumentado ou reduzido**. Se esse flutuação ocorre de maneira regular ou previsível, **o dimensionamento do pool pode ser agendado para ocorrer automaticamente**. Por exemplo, reduzir verticalmente quando você souber que sua carga de trabalho é leve, talvez durante a noite ou durante os finais de semana.
* Para responder a flutuações de longo prazo ou alterações no número de bancos de dados, **os bancos de dados individuais podem ser movidos para outros pools**.
* Para responder a aumentos de curto prazo na carga de bancos de dados *individuais*, **bancos de dados individuais podem ser retirados de um pool e atribuídos a um nível de desempenho individual** por um período. Depois que a carga for reduzida, o banco de dados poderá ser retornado ao pool. Quando isso é conhecido de antemão, os bancos de dados podem ser movidos preventivamente para garantir que o banco de dados sempre tenha os recursos necessários e para evitar o impacto em outros bancos de dados no pool. Se esse requisito for previsível, como um local com um crescimento súbito nas vendas de ingressos para um evento popular, esse comportamento de gerenciamento poderá ser integrado ao aplicativo.

O [Portal do Azure](https://portal.azure.com) fornece monitoramento e alertas internos sobre a maioria dos recursos. Para o Banco de Dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados e pools. O monitoramento e o alerta internos são específicos de recursos, portanto é conveniente usar para pequenas quantidades de recursos, mas não é muito conveniente ao trabalhar com muitos recursos.

Para cenários de alto volume, o Log Analytics (também conhecido como OMS) pode ser usado. Esse é um serviço separado do Azure que fornece análise sobre telemetria e logs de diagnóstico emitidos, coletados em um espaço de trabalho de Log Analytics, que pode coletar a telemetria de muitos serviços e ser usado para consultar e definir alertas.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="provision-additional-tenants"></a>Provisionar locatários adicionais

Embora os pools possam ser econômicos com apenas dois bancos de dados S3, quanto mais bancos de dados estiverem no pool, mais econômico será o efeito de média. Para uma boa compreensão do funcionamento do monitoramento de desempenho e do gerenciamento em grande escala, este tutorial requer que você tenha pelo menos 20 bancos de dados implantados.

Se você já provisionou um lote de locatários em um tutorial anterior, poderá pular para a seção [Simular o uso em todos os bancos de dados de locatário](#simulate-usage-on-all-tenant-databases).

1. Abra ...\\Módulos de aprendizado\\Provisionar e Catalogar\\*Demo-PerformanceMonitoringAndManagement.ps1* no **ISE do PowerShell**. Mantenha esse script aberto pois você executará vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Provisionar um lote de locatários**
1. Pressione **F5** para executar o script.

O script implantará 17 locatários em menos de cinco minutos.

O script *New-TenantBatch* usa um conjunto aninhado ou vinculado de modelos do [Resource Manager](../azure-resource-manager/index.md) que cria um lote de locatários, que, por padrão, copia o banco de dados **baseTenantDb** no servidor de catálogo para criar os novos bancos de dados de locatário e, em seguida, registra-os no catálogo e, finalmente, inicializa-os com o nome de locatário e o tipo de local. Isso é consistente com a maneira como o aplicativo WTP provisiona um novo locatário. As alterações feitas no *baseTenantDB* são aplicadas a quaisquer novos locatários provisionados daí em diante. Consulte o [Tutorial de gerenciamento de esquema](sql-database-saas-tutorial-schema-management.md) para saber como fazer alterações de esquema em bancos de dados de locatário *existentes* (incluindo o banco de dados *golden*).

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simular diferentes padrões de uso por meio da geração de diferentes tipos de carga

O script *Demo-PerformanceMonitoringAndManagement.ps1* inicia o gerador de carga usando um dos *tipos de carga* disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTU) |
| 3 | Gerar carga com picos mais longos e mais frequentes por banco de dados|
| 4 | Gerar carga com picos maiores de DTU por banco de dados (aprox. 80 DTU)|
| 5 | Gerar uma carga normal e uma carga alta em um locatário único (aprox. 95 DTU)|
| 6 | Gerar carga desbalanceada entre vários pools|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular o uso em todos os bancos de dados de locatário

O gerador de carga aplica uma carga *sintética* somente da CPU em cada banco de dados de locatário. O gerador inicia um trabalho para cada banco de dados de locatário, que chama um procedimento armazenado que gera a carga periodicamente. Os níveis de carga (em eDTUs), a duração e os intervalos variam em todos os bancos de dados, simulando uma atividade de locatário imprevisível.

1. Defina **$DemoScenario** = **2**, *Gerar uma carga de intensidade normal*.
1. Pressione **F5** para aplicar uma carga em todos os seus bancos de dados de locatário.

Devido à natureza esporádica da carga, permita que o gerador seja executado por 10 a 20 minutos para que a atividade possa atingir um estado estável e se acomode em um bom padrão.

> [!IMPORTANT]
> O gerador de carga está sendo executado como uma série de trabalhos em sua sessão local do PowerShell. Mantenha a guia *Demo-PerformanceMonitoringAndManagement.ps1* aberta! Se você fechar a guia ou suspender seu computador, o gerador de carga será interrompido.

## <a name="monitor-resource-usage-using-the-portal"></a>Monitorar o uso de recursos usando o portal

Para monitorar o uso de recursos que resulta da carga que está sendo aplicada, abra o portal do pool que contém os bancos de dados de locatário.

1. Abra o [Portal do Azure](https://portal.azure.com) e navegue até o servidor tenants1-&lt;USER&gt;.
1. Você deve ver a lista de bancos de dados de locatário, incluindo o novo lote de bancos de dados.
1. Role para baixo e localize os pools elásticos e clique em **Pool1**. Este pool contém todos os bancos de dados de locatário criados até agora.
1. Expanda a folha do pool que é aberta e observe o gráfico de utilização do pool e o gráfico dos bancos de dados mais utilizados.

A utilização do pool é essencialmente a agregação da utilização de banco de dados para todos os bancos de dados no pool. O gráfico de utilização do banco de dados mostra os cinco bancos de dados mais ativos:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Como há outros bancos de dados no pool além dos 5 mais utilizados, a utilização do pool mostra a atividade que não está refletida no gráfico de cinco bancos de dados mais utilizados. Para ver alguns detalhes adicionais, clique em **Utilização de Recursos de Banco de Dados**:

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho para o pool

Defina um alerta no pool que é disparado em \>75% de utilização sustentada por 5 minutos, da seguinte maneira:

1. Abra *Pool1* (no servidor *tenants1-\<usuário\>*) no [Portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta** e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Forneça um nome, como **Alto DTU**, 
1. Defina os seguintes valores:
   * **Métrica = percentual de eDTU**
   * **Condição = maior que**.
   * **Limite = 75**.
   * **Período = nos últimos 30 minutos**.

   ![definir alerta](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Você pode fazer com que as notificações sejam enviadas para o email da sua conta do Azure e, opcionalmente, para outros emails (não é recomendável definir isso, a menos que você seja proprietário da assinatura que está sendo usada).

> [!NOTE]
> Como o alerta somente será disparado se o limite for excedido durante os últimos 30 minutos, o gerador de carga precisa ser executado por mais de 30 minutos para testar o alerta.


## <a name="scale-up-a-busy-pool"></a>Escalar verticalmente um pool ocupado

Se o nível de carga agregado aumentar em um pool até o seu limite e alcançar 100% de uso de eDTU, o desempenho individual do banco de dados será afetado, reduzindo potencialmente os tempos de resposta de consulta de todos os bancos de dados no pool.

A curto prazo, considere o escalar verticalmente o pool para fornecer recursos adicionais ou remover bancos de dados do pool (movê-los para outros pools ou para fora do pool em uma camada de serviço autônoma).

A longo prazo, considere a otimização de consultas ou do uso do índice para melhorar o desempenho do banco de dados. Dependendo da sensibilidade do aplicativo a problemas de desempenho, é uma melhor prática escalar verticalmente um pool antes que ele alcance 100% de uso de eDTU. Use um alerta para avisá-lo com antecedência.

Você pode simular um pool ocupado aumentando a carga produzida pelo gerador. Fazer com que os bancos de dados atinjam picos com mais frequência e por mais tempo, gera aumento da carga agregada no pool sem alterar os requisitos dos bancos de dados individuais. O portal ou o PowerShell podem ser usados para escalar verticalmente o pool facilmente. Este exercício usa o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais longos e mais frequentes por banco de dados_, para aumentar a intensidade da carga agregada no pool sem alterar a carga de pico necessária a cada banco de dados.
1. Pressione **F5** para aplicar uma carga em todos os seus bancos de dados de locatário.


1. **Abra a folha do pool** **para tenants1/Pool1**.


1. Monitore aumento no uso de DTU do pool no gráfico superior. Demora alguns minutos para que a nova carga maior faça efeito, mas você deve ver rapidamente o pool começar a atingir 100% de utilização e conforme a carga se estabiliza no novo padrão, ela rapidamente sobrecarrega o pool.


1. Para escalar verticalmente o pool, clique em **Configurar pool**
1. Ajuste o controle deslizante de **eDTU do Pool** para 100 (recomendável não ultrapassar para limitar os custos). Observe como o armazenamento agregado disponível para todos os bancos de dados no pool, indicado por **GB do Pool**, está vinculado à configuração de eDTU e também aumenta. Alterar o eDTU do pool não altera as configurações por banco de dados (que ainda é, no máximo, de 50 eDTU por banco de dados). Você pode ver as configurações por banco de dados no lado direito da folha **Configurar pool**.
1. Clique em **Salvar** para enviar a solicitação. Normalmente, a alteração levará de 3 a 5 minutos para um pool Standard.

Volte para **Pool1** > **Visão geral** para exibir os gráficos de monitoramento. Monitore o efeito do fornecimento de mais recursos ao pool (embora com uma carga aleatória e com poucos bancos de dados não seja sempre fácil visualizar conclusivamente). Enquanto estiver examinando os gráficos lembre-se que 100% no gráfico superior agora representa 100 eDTUs, enquanto que no gráfico inferior, 100% ainda representa 50 eDTUs, pois o máximo por banco de dados ainda é de 50 eDTUs.

Os bancos de dados permanecem online e totalmente disponíveis durante todo o processo. No último momento, como cada banco de dados está pronto para ser habilitado com a nova eDTU de pool, as conexões ativas serão interrompidas. O código do aplicativo sempre deve ser escrito para repetir conexões interrompidas e, assim, ser reconectado ao banco de dados no pool escalonado verticalmente.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Criar um segundo pool e efetuar balanceamento de carga de bancos de dados para lidar com o aumento de carga agregado

Como alternativa a escalar verticalmente o pool, crie um segundo pool e mova os bancos de dados para balancear a carga entre os dois pools. Para fazer isso, o novo pool deve ser criado no mesmo servidor que o primeiro.

1. Abra a **folha do servidor customers1-&lt;USER&gt;**. Se você estiver em uma folha de banco de dados ou de pool, você poderá suspender o controle básico e selecionar o nome do servidor como um atalho.
1. Clique em **+ Novo pool** para criar um pool no servidor atual
1. No modelo de novo Pool de banco de dados elástico:

    1. defina **nome = Pool2**.
    1. Deixe o tipo de preço como **Pool Standard**.
    1. Clique em **Configurar pool**,
    1. Na folha Configurar pool que é aberta, defina **eDTU do Pool = 50 DTU**.
    1. Clique no comando **Adicionar bancos de dados** para ver uma lista de bancos de dados neste servidor que não estão no pool atual.
    1. Na lista, **marque** metade dos bancos de dados (10 de 20) para movê-los para o novo pool e, em seguida, clique em **Selecionar**.
    1. Clique em **Selecionar** novamente para aceitar as alterações de configuração. Observe a estimativa de custo para um mês de uso com as opções selecionadas.
    1. Selecione **OK** para criar um novo pool com a nova configuração e para mover os bancos de dados.

A criação do pool e mover os bancos de dados para ele leva apenas alguns minutos. Cada um dos bancos de dados sendo movido permanece online e totalmente acessível até o último momento, quando todas as conexões abertas são fechadas. Quando um cliente tentar novamente a conexão, ele se conectará ao banco de dados no novo pool.

Quando o pool tiver sido criado, ele será exibido na folha do servidor customers1. Clique no nome do pool para abrir a folha do pool e monitorar o desempenho.

Você verá que a utilização de recursos do Pool1 caiu e que o Pool2 está carregado de maneira semelhante.

## <a name="manage-an-increased-load-on-a-single-database"></a>Gerenciar um aumento de carga em um banco de dados individual

Se um banco de dados individual em um pool apresentar uma alta carga constante, dependendo da configuração do pool, ele poderá ter a tendência de dominar os recursos do pool e afetar outros bancos de dados. Se for provável que a atividade continuará por algum tempo, o banco de dados poderá ser movido temporariamente para fora do pool. Isso permitirá que o banco de dados receba mais recursos que os outros no pool e o isolará dos outros bancos de dados. Este exercício simula o efeito do Contoso Concert Hall experimentando uma alta carga quando os ingressos de um concerto popular são disponibilizados para a venda.

1. No script ...\\**Demo-PerformanceManagementAndMonitoring**.ps1
1. Defina **$DemoScenario = 5, Gerar uma carga normal e uma carga alta em um locatário único (aprox. 95 DTU).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script usando **F5**.
1. **Abra a folha do pool** **para Customers1/Pool1**.
1. Examine a exibição **Monitoramento do pool elástico** na parte superior da folha e procure pelo aumento de uso de DTU do pool. Após um ou dois minutos, a carga maior deverá começar a fazer efeito e rapidamente você verá que o pool atinge 100% de utilização.
1. Monitore também a exibição **Monitoramento do banco de dados elástico** que mostra os bancos de dados mais ativos na última hora. O banco de dados contosoconcerthall logo deve aparecer como um dos 5 bancos de dados mais ativos.
1. **Clique no **gráfico** de Monitoramento do banco de dados elástico** e isso abrirá uma folha **Utilização de Recursos de Banco de Dados** na qual você pode monitorar seletivamente todos os bancos de dados. Isso permite isolar a exibição para o banco de dados contosoconcerthall.
1. Na lista de bancos de dados, **clique em contosoconcerthall** e a folha desse banco de dados será aberta.
1. Clique em **Tipo de preço (dimensionar DTUs)** no menu de contexto para abrir a folha **Configurar desempenho** na qual você pode definir um nível de desempenho isolado para o banco de dados.
1. Clique na guia **Standard** para abrir as opções de escala na camada Standard.
1. Deslize o **controle deslizante de DTU** para a direita para selecionar 100 DTUs. Observe que isso corresponde ao objetivo de serviço, **S3,** mostrado entre colchetes entre os medidores de tamanho de DTU e de Armazenamento.
1. Clique em **Aplicar** para mover o banco de dados para fora do pool e torná-lo um banco de dados S3 Standard.
1. Depois que a implantação for concluída, monitore o efeito sobre o banco de dados contosoconcerthall e sobre o pool do qual ele foi removido, nas folhas do banco de dados e do pool elástico.

Depois que a carga acima do normal do banco de dados contosoconcerthall baixar devolva-o imediatamente ao pool para reduzir seu custo. Se não estiver claro quando isso acontecerá você poderá definir um alerta no banco de dados que será disparado quando o uso de DTU cair abaixo do valor máximo por banco de dados no pool. Mover um banco de dados para um pool é descrito no exercício 5.

## <a name="other-performance-management-patterns"></a>Outros padrões de gerenciamento de desempenho

**Dimensionamento preventivo** No exercício 6, em que você explorou como dimensionar um banco de dados isolado, você sabia qual banco de dados procurar. Se a gerência do Contoso Concert Hall tivesse informado o WTP da venda iminente de ingressos, o banco de dados poderia ter sido movido para fora do pool preventivamente. Caso contrário, ele provavelmente exigiria um alerta no pool ou no banco de dados para observar o que estava acontecendo. Você não gostaria de saber sobre isso por meio dos outros locatários do pool reclamando a respeito de degradação do desempenho. E se o locatário puder prever quanto tempo ele precisará de recursos adicionais você poderá configurar um runbook de Automação do Azure para mover o banco de dados para fora do pool e, em seguida, retorná-lo em um agendamento definido.

**Dimensionamento de autoatendimento de locatário** Como o dimensionamento é uma tarefa facilmente chamada por meio da API de gerenciamento, você pode criar a capacidade de dimensionar bancos de dados de locatário em seu aplicativo voltado para o locatário e oferecê-lo como um recurso do seu serviço SaaS. Por exemplo, permita que os locatários autoadministrem o escalonamento e a redução vertical, talvez vinculado diretamente à cobrança deles!

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Escalonamento e redução vertical de um pool em um agendamento para corresponder a padrões de uso

Quando o uso agregado do locatário segue padrões de uso previsíveis, você pode usar a Automação do Azure para escalar e reduzir um pool verticalmente em um agendamento. Por exemplo, reduzir um pool verticalmente após as 18h e escalar verticalmente novamente antes das 6h em dias da semana nos quais você sabe que há uma queda nos requisitos de recursos.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Simular o uso em bancos de dados de locatário executando um gerador de carga fornecido
> * Monitorar os bancos de dados de locatário conforme eles respondem ao aumento de carga
> * Escalar verticalmente o pool elástico em resposta ao aumento da carga do banco de dados
> * Provisionar um segundo pool elástico para realizar o balanceamento de carga da atividade do banco de dados

[Tutorial para restaurar um locatário único](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Pools elásticos do SQL](sql-database-elastic-pool.md)
* [Automação do Azure](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) – Tutorial de configuração e uso do Log Analytics

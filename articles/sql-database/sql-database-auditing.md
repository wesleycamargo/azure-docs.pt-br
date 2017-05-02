---
title: "Introdução à auditoria do banco de dados SQL do Azure | Microsoft Docs"
description: "Introdução à auditoria do banco de dados SQL"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security-protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: giladm
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 20b46682471addfdaf563c55e989bb5d2271979f
ms.lasthandoff: 04/15/2017


---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria do banco de dados SQL
A Auditoria do Azure SQL Database rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta do Azure Storage.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

A auditoria permite e facilita a adoção de padrões de conformidade, mas não garante a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).

* [Visão geral da Auditoria do Azure SQL Database]
* [Configurar a auditoria do banco de dados]
* [Analisar os logs e relatórios de auditoria]

## <a id="subheading-1"></a>Visão geral da auditoria do Banco de Dados SQL do Azure
A auditoria de Banco de Dados SQL permite:

* **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
* **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
* **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Há dois **Métodos de auditoria**:

* **Auditoria de Blob** – os logs são gravados no Armazenamento de Blobs do Azure. Esse é um método de auditoria mais recente, que fornece **desempenho maior**, dá suporte à **auditoria do nível de objeto de granularidade mais alta** e é **mais econômico**. A Auditoria de Blob substituirá a Auditoria de Tabela.
* **Auditoria de Tabela (preterida)** – os logs são gravados no Armazenamento de Tabelas do Azure.

> [!IMPORTANT]
> A introdução do novo Blob de auditoria traz uma grande mudança na forma como a diretiva de auditoria de servidor está sendo herdada por banco de dados. Consulte [as diferenças de Blob/tabela na herança de diretiva de auditoria do servidor](#subheading-8) seção para obter detalhes adicionais.

Você pode configurar a auditoria para diferentes tipos de categorias de evento, conforme explicado na seção [Configurar a auditoria do banco de dados](#subheading-2).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão do servidor. Uma política de auditoria de servidor padrão se aplica a todos os bancos de dados existentes e recém-criados em um servidor.

## <a id="subheading-2"></a>Configurar a auditoria do banco de dados
A seção a seguir descreve a configuração de auditoria usando o Portal do Azure.

### <a id="subheading-2-1">Auditoria de blob</a>
1. Inicie o [Portal do Azure](https://portal.azure.com) em https://portal.azure.com.
2. Navegue até a folha de configuração do Banco de Dados SQL/SQL Server que você deseja auditar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

    <a id="auditing-screenshot"></a>
    ![Painel de navegação][1]
3. Na folha de configuração de auditoria do banco de dados, você pode marcar a caixa de seleção **Herdar configurações do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Se essa opção for marcada, você verá um link **Exibir configurações de auditoria do servidor** que permite exibir ou modificar as configurações de auditoria do servidor neste contexto.

    ![Painel de navegação][2]
4. Se preferir habilitar a auditoria de blob no nível do banco de dados (adicionalmente ou em vez da auditoria de nível de serviço), **desmarque** a opção **Herdar configurações de auditoria do servidor**, **ATIVE** a Auditoria e selecione o Tipo de Auditoria **Blob**.

   > Se a auditoria de blob do servidor estiver habilitada, a auditoria de banco de dados configurada existirá lado a lado com a auditoria de blob do servidor.  

    ![Painel de navegação][3]
5. Selecione **Detalhes de Armazenamento** para abrir a Folha de Armazenamento de Logs de Auditoria. Selecione a conta do Azure Storage em que os logs serão salvos e o período de retenção, após o qual os logs antigos serão excluídos, e clique em **OK** na parte inferior. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria.

    <a id="storage-screenshot"></a>
    ![Painel de navegação][4]
6. Se desejar personalizar os eventos auditados, você pode fazer isso por meio do PowerShell ou API REST. Consulte a seção [Automação (PowerShell/API REST)](#subheading-7) para obter mais detalhes.
7. Depois de definir as configurações de auditoria, você pode ativar o novo recurso **Detecção de Ameaças** (visualização) e configurar os emails para receber alertas de segurança. A Detecção de Ameaças permite que você receba alertas proativos em atividades anômalas de banco de dados que podem indicar possíveis ameaças de segurança. Para obter mais detalhes, consulte [Introdução à Detecção de Ameaças](sql-database-threat-detection-get-started.md) .
8. Clique em **Salvar**.

### <a id="subheading-2-2">Auditoria de Tabela</a> (preterida)

> Antes de configurar a **Auditoria de Tabela**, verifique se você está usando um [“Cliente de Versão Anterior”](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Além disso, se você tiver configurações de firewall estritas, observe que o [ponto de extremidade IP do seu banco de dados será alterado](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) quando auditoria de tabela for habilitada.


1. Inicie o [Portal do Azure](https://portal.azure.com) em https://portal.azure.com.
2. Navegue até a folha de configuração do Banco de Dados SQL/SQL Server que você deseja auditar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças** (*[confira a captura de tela na seção Auditoria de blob](#auditing-screenshot)*).
3. Na folha de configuração de auditoria do banco de dados, você pode marcar a caixa de seleção **Herdar configurações do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Se essa opção for marcada, você verá um link **Exibir configurações de auditoria do servidor** que permite exibir ou modificar as configurações de auditoria do servidor neste contexto.

    ![Painel de navegação][2]
4. Se você preferir não herdar as configurações de auditoria do servidor, **desmarque** a opção **Herdar configurações de auditoria do servidor**, **ATIVE** a Auditoria e selecione o Tipo de Auditoria **Tabela**.

    ![Painel de navegação][3-tbl]
5. Selecione **Detalhes de Armazenamento** para abrir a Folha de Armazenamento de Logs de Auditoria. Selecione a conta do Azure Storage em que os logs serão salvos e o período de retenção, após o qual os logs antigos serão excluídos. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria (*[confira a captura de tela na seção Auditoria de blob](#storage-screenshot)*).
6. Clique em **Eventos Auditados** para personalizar os eventos a auditar. Na folha **Registro em log por evento**, clique em **Sucesso** e **Falha** para registrar todos os eventos ou escolha categorias de evento individuais.

   > Também é possível realizar a personalização de eventos de auditoria por meio do PowerShell ou API REST. Consulte a seção [Automação (PowerShell/API REST)](#subheading-7) para obter mais detalhes.

    ![Painel de navegação][5]
7. Depois de definir as configurações de auditoria, você pode ativar o novo recurso **Detecção de Ameaças** (visualização) e configurar os emails para receber alertas de segurança. A Detecção de Ameaças permite que você receba alertas proativos em atividades anômalas de banco de dados que podem indicar possíveis ameaças de segurança. Para obter mais detalhes, consulte [Introdução à Detecção de Ameaças](sql-database-threat-detection-get-started.md) .
8. Clique em **Salvar**.


## <a id="subheading-8"></a>Diferenças de Blob/tabela na herança de política de auditoria do servidor

###<a name="ablob-auditinga"></a><a>Auditoria de blobs</a>

1. Se **Server Blob auditoria estiver habilitada**, ele **sempre aplica-se ao banco de dados** (ou seja, o banco de dados será auditado), independentemente de:
    - As configurações de auditoria de banco de dados.
    - Se a caixa de seleção "Herdar configurações do servidor" é verificada na folha do banco de dados.

2. Habilitar a auditoria de Blob no banco de dados, além de ativá-lo no servidor, será **não** substituir ou alterar as configurações de auditoria de Blob do servidor - ambas as auditorias continuará a existir lado a lado. Em outras palavras, o banco de dados será auditado duas vezes em paralelo (uma vez pela política de servidor e uma vez pela política de banco de dados).

    > [!NOTE]
    > Evite Habilitar auditoria de Blob de servidor e banco de dados Blob auditoria juntas, a menos que:
    > * Você precisa usar outro *conta de armazenamento* ou *o período de retenção* para um banco de dados específico.
    > * Você deseja auditar eventos diferentes tipos ou categorias de um banco de dados que estão sendo auditados para o restante dos bancos de dados neste servidor (por exemplo, se as inserções de tabela precisam ser auditado somente para um banco de dados específico).
    > <br><br>
    > Caso contrário, ele será **recomendável habilitar somente auditoria no nível de servidor Blob** e deixar a auditoria de nível de banco de dados desabilitado para todos os bancos de dados.

###<a name="atable-auditinga-deprecated"></a><a>Auditoria de tabela</a> (preterida)

Se **auditoria de tabela de nível de servidor é habilitada**, ela só se aplica ao banco de dados se a caixa de seleção "Herdar configurações do servidor" estiver marcada na folha do banco de dados (Isso é marcado por padrão para bancos de dados recém-criado e todos os existentes).

- Se a caixa de seleção for *check*, quaisquer alterações feitas nas configurações de auditoria no banco de dados **substituir** as configurações do servidor do banco de dados.

- Se a caixa de seleção for *desmarcada* e a auditoria de nível de banco de dados está desabilitada, o banco de dados não será auditado.

## <a id="subheading-3"></a>Analisar os logs e relatórios de auditoria
Os logs de auditoria são agregados na conta do Azure Storage escolhida durante a instalação.

Você pode explorar os logs de auditoria usando uma ferramenta como o [Azure Storage Explorer](http://storageexplorer.com/).

Veja abaixo informações específicas para análise de logs de auditoria de **Blob** e **Tabela**.

### <a id="subheading-3-1">Auditoria de blob</a>
Os logs da Auditoria de Blob são salvos como uma coleção de arquivos de blob em um contêiner chamado “**sqldbauditlogs**”.

Para mais detalhes sobre a hierarquia de pasta de armazenamento de logs de auditoria de blob, a convenção de nomenclatura do blob e o formato do log, consulte a [Referência do formato do log de auditoria de blob (download do arquivo doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Há vários métodos para exibir logs de auditoria de blob:

1. Por meio do [Portal do Azure](https://portal.azure.com) - abra o banco de dados relevante. Na parte superior da folha **Auditoria e detecção de ameaças de** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][10]

    Uma folha de **Registros de auditoria** será aberta, na qual é possível exibir todos os logs.

    - Você pode optar por exibir datas específicas clicando no **Filtro** na área superior da folha de registros de auditoria
    - Você pode alternar entre os registros de auditoria que foram criados pela política do servidor ou a política de banco de dados de auditoria

    ![Painel de navegação][11]
2. Baixe os arquivos de log do seu contêiner de Blob do Azure Storage por meio do portal ou usando uma ferramenta como [Azure Storage Explorer](http://storageexplorer.com/).

    Depois de baixar o arquivo localmente, você pode clicar duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.

    Métodos adicionais:

   * Você pode **baixar vários arquivos simultaneamente** por meio da Azure Storage Explorer, clique com o botão direito do mouse em uma subpasta específica (por exemplo, uma subpasta que inclui todos os arquivos de log de uma data específica) e selecione “Salvar como” para salvar em uma pasta local.

       Depois de baixar vários arquivos (ou de um dia inteiro, como descrito acima), você poderá mesclá-los localmente da seguinte maneira:

       **Abra o SSMS -> Arquivo -> Abrir -> Merge Extended Events (Mesclar Eventos Estendidos) -> Escolha todos os arquivos para mesclar**
   * Programaticamente:

     * **Biblioteca C#** do Leitor de Eventos Estendidos ([mais informações aqui](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * Consultando arquivos de eventos estendidos usando o **PowerShell** ([mais informações aqui](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

3. Nós criamos uma **aplicativo de exemplo** que é executado no Azure e utiliza APIs públicas do OMS para enviar logs de auditoria do SQL ao OMS para consumo por meio do painel do OMS ([mais informações aqui](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)).

### <a id="subheading-3-2">Auditoria de tabela</a> (preterida)
Os logs de auditoria de tabela são salvos como uma coleção de tabelas do Azure Storage com um prefixo **SQLDBAuditLogs**.

Para obter detalhes adicionais sobre o formato do log de auditoria de tabela, consulte a [Referência de formato de log de auditoria de tabela (download de arquivo doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Há vários métodos para exibir logs de auditoria de tabela:

1. Por meio do [Portal do Azure](https://portal.azure.com) - abra o banco de dados relevante. Na parte superior da folha **Auditoria e detecção de ameaças de** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][10]

    Uma folha de **Registros de auditoria** será aberta, na qual é possível exibir todos os logs.

    * Você pode optar por exibir datas específicas clicando no **Filtro** na área superior da folha de registros de auditoria
    * Você pode baixar e exibir os logs de auditoria em formato Excel clicando em **Abrir no Excel** na área superior da folha de registros de auditoria

    ![Painel de navegação][12]

2. Como alternativa, um modelo de relatório pré-configurado está disponível como uma [planilha do Excel para download](http://go.microsoft.com/fwlink/?LinkId=403540) para ajudar você a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do Power Query, que pode ser baixado [aqui](http://www.microsoft.com/download/details.aspx?id=39379).

    Você também pode importar os logs de auditoria para o modelo do Excel diretamente da sua conta do Azure Storage usando o Power Query. Você pode explorar os registros de auditoria e criar painéis e relatórios sobre os dados de log.

    ![Painel de navegação][9]

## <a id="subheading-5"></a>Práticas para uso em produção
<!--The description in this section refers to screen captures above.-->

### <a id="subheading-6">Auditoria de bancos de dados replicados geograficamente</a>
Ao usar bancos de dados replicados geograficamente, é possível configurar a auditoria no banco de dados primário, no banco de dados secundário ou ambos, dependendo do tipo de auditoria.

**Auditoria de Tabela** – você pode configurar uma política separada, no banco de dados ou no nível de serviço, para cada um dos dois bancos de dados (primário e secundário) conforme descrito na seção [Configurar a auditoria do banco de dados](#subheading-2-2).

**Auditoria de Blob** - siga estas instruções:

1. **Banco de dados primário** – ative a **Auditoria de Blob** no servidor ou no próprio banco de dados, conforme descrito na seção [Configurar a auditoria do banco de dados](#subheading-2-1).
2. **Banco de dados secundário** – a auditoria de blob pode ser ativada/desativada nas configurações de auditoria do banco de dados primário.

   * Ative a **Auditoria de Blob** no **Banco de dados primário**, conforme descrito na seção [Configurar a auditoria do banco de dados](#subheading-2-1). A Buditoria de blob precisa estar habilitada no *próprio banco de dados primário*, não no servidor.
   * Depois que a auditoria de blob estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

    > [!IMPORTANT]
    > Por padrão, as **configurações de armazenamento** para o banco de dados secundário serão idênticas às do banco de dados primário, causando um **tráfego entre regiões**. Você pode evitar isso habilitando a Auditoria de Blob no **Servidor secundário** e configurando um **armazenamento local** nas configurações de armazenamento do servidor secundário (isso substituirá a localização de armazenamento do banco de dados secundário e resultará em cada banco de dados salvando os logs de auditoria em um armazenamento local).  

<br>

### <a id="subheading-6">Regeneração de chave de armazenamento</a>
Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao atualizar suas chaves, é necessário salvar novamente a política de auditoria. O processo é o seguinte:

1. Na folha de detalhes de armazenamento, mude a **Chave de Acesso ao Armazenamento** de *Primária* para *Secundária* e clique em **OK** na parte inferior. Em seguida, clique em **SALVAR** na parte superior da folha de configuração de auditoria.

    ![Painel de navegação][6]
2. Acesse a folha de configuração de armazenamento e **regenere** a *Chave de Acesso Primária*.

    ![Painel de navegação][8]
3. Volte para a folha de configuração de auditoria, altere a **Chave de Acesso de Armazenamento** de *Secundária* para *Primária* e clique em **OK** na parte inferior. Em seguida, clique em **SALVAR** na parte superior da folha de configuração de auditoria.
4. Volte para a folha de configuração de armazenamento e **regenere** a *Chave de Acesso Secundária* (como preparação para o próximo ciclo de atualização de chaves).

## <a id="subheading-7"></a>Automação (PowerShell/API REST)
Você também pode configurar a Auditoria no Azure SQL Database usando as seguintes ferramentas de automação:

1. **Cmdlets do PowerShell**

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

   Para obter um exemplo de script, consulte [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

2. **API REST – Auditoria de Blob**

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx) (Criar ou atualizar a política de auditoria de blob do banco de dados)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx) (Criar ou atualizar uma política de auditoria de blob de servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx) (Obter a política de auditoria de blob do banco de dados)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx) (Obter a política de auditoria de blob do servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx) (Obter o resultado da operação de auditoria do blob do servidor)
3. **API REST – Auditoria de Tabela (preterida)**

   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604471.aspx) (Criar ou atualizar a política de auditoria de tabela do banco de dados)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604383.aspx) (Criar ou atualizar a política de auditoria de tabela do servidor)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604381.aspx) (Obter a política de auditoria do banco de dados)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604382.aspx) (Obter a política de auditoria do servidor)

<!--Anchors-->
[Visão geral da Auditoria do Azure SQL Database]: #subheading-1
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

[101]: https://msdn.microsoft.com/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/library/azure/mt619353(v=azure.200).aspx


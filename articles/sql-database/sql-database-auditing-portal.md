---
title: 'Portal do Azure: Gerenciar Auditoria do Banco de Dados SQL | Microsoft Docs'
description: "Configure a auditoria de Bancos de Dados SQL do Azure no Portal do Azure para rastrear eventos do banco de dados e gravá-los em um log de auditoria em sua conta de Armazenamento do Azure."
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
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: d1350081aa4f70660802c61a77250496e1e7fc2a
ms.lasthandoff: 03/10/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Configurar e gerenciar a auditoria do Banco de Dados SQL no Portal do Azure

A seção a seguir descreve como configurar e gerenciar a auditoria usando o Portal do Azure. Para configurar e gerenciar a auditoria usando o PowerShell, consulte [Configurar a auditoria com o PowerShell](sql-database-auditing-powershell.md). 

Para obter uma visão geral da auditoria, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).

## <a name="configure-blob-auditing"></a>Configurar a auditoria de blob

1. Inicie o [Portal do Azure](https://portal.azure.com) em https://portal.azure.com.
2. Navegue até a folha Configurações do Banco de Dados SQL/SQL Server que você deseja auditar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

    ![Painel de navegação](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. Na folha de Configuração de Auditoria do Banco de Dados, você pode marcar a caixa de seleção **Herdar configurações do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Se essa opção for marcada, você verá um link **Exibir configurações de auditoria do servidor** que permitirá exibir ou modificar as configurações de auditoria do servidor neste contexto.

    ![Painel de navegação][2]
4. Se preferir habilitar a auditoria de blob no nível do banco de dados (adicionalmente ou em vez da auditoria de nível de serviço), **desmarque** a opção **Herdar configurações de auditoria do servidor**, **ATIVE** a Auditoria e selecione o Tipo de Auditoria **Blob**.

    ![Painel de navegação][3]
5. Selecione **Detalhes de Armazenamento** para abrir a folha Armazenamento de Logs de Auditoria. Selecione a conta de armazenamento do Azure em que os logs são salvos e o período de retenção após o qual os logs antigos são excluídos, então clique em **OK** na parte inferior. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria.

    <a id="storage-screenshot"></a>
    ![Painel de navegação][4]
6. Se você desejar personalizar os eventos auditados, você poderá fazer isso por meio do PowerShell ou da API REST.
7. Depois de definir as configurações de auditoria, você pode ativar o novo recurso Detecção de Ameaças (visualização) e configurar os emails para receber alertas de segurança. A Detecção de Ameaças permite que você receba alertas proativos em atividades anômalas de banco de dados que podem indicar possíveis ameaças de segurança. Consulte [Detecção de ameaças](sql-database-threat-detection.md) para obter mais detalhes.
8. Clique em **Salvar**.


## <a name="table-auditing"></a>Auditoria de tabela

> [!IMPORTANT]
> Antes de configurar a **Auditoria de tabela**, verifique se você está usando um ["Cliente de Versão Anterior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Além disso, se você tiver configurações de firewall estritas, observe que o [ponto de extremidade IP do seu banco de dados é alterado](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) quando auditoria de tabela é habilitada.
>

1. Inicie o [Portal do Azure](https://portal.azure.com) em https://portal.azure.com.
2. Navegue até a folha Configurações do Banco de Dados SQL/SQL Server que você deseja auditar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças** (*[confira a captura de tela na seção Auditoria de blob](#auditing-screenshot)*).
3. Na folha de Configuração de Auditoria do Banco de Dados, você pode marcar a caixa de seleção **Herdar configurações do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Se essa opção for marcada, você verá um link **Exibir configurações de auditoria do servidor** que permitirá exibir ou modificar as configurações de auditoria do servidor neste contexto.

    ![Painel de navegação][2]
4. Se você preferir não herdar as configurações de auditoria do servidor, **desmarque** a opção **Herdar configurações de auditoria do servidor**, **ATIVE** a Auditoria e selecione o Tipo de Auditoria **Tabela**.

    ![Painel de navegação][3-tbl]
5. Selecione **Detalhes de Armazenamento** para abrir a folha Armazenamento de Logs de Auditoria. Selecione a conta de Armazenamento do Azure em que os logs serão salvos e o período de retenção após o qual os logs antigos são excluídos. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria (*[confira a captura de tela na seção Auditoria de blob](#storage-screenshot)*).
6. Clique em **Eventos Auditados** para personalizar os eventos a auditar. Na folha Registro em log por evento, clique em **Êxito** e **Falha** para registrar todos os eventos ou escolha categorias de evento individuais.

    ![Painel de navegação][5]
7. Depois de definir as configurações de auditoria, você pode ativar o novo recurso Detecção de Ameaças (visualização) e configurar os emails para receber alertas de segurança. A Detecção de Ameaças permite que você receba alertas proativos em atividades anômalas de banco de dados que podem indicar possíveis ameaças de segurança. Consulte [Detecção de ameaças](sql-database-threat-detection.md) para obter mais detalhes.
8. Clique em **Salvar**.

## <a name="auditing-geo-replicated-databases"></a>Auditoria de bancos de dados replicados geograficamente

Ao usar bancos de dados replicados geograficamente, é possível configurar a auditoria no banco de dados primário, no banco de dados secundário ou ambos, dependendo do tipo de auditoria.

**Auditoria de Tabela**: você pode configurar uma política separada, no banco de dados ou no nível de serviço, para cada um dos dois bancos de dados (primário e secundário).

**Auditoria de Blob:** siga estas instruções:

1. **Banco de dados primário:** ativar a auditoria de blob no servidor ou o banco de dados.
2. **Banco de dados secundário:** a auditoria de blob pode ser ativada/desativada nas configurações de auditoria do banco de dados primário.

   * Ative a auditoria de blob no banco de dados primário. A auditoria de blob precisa estar habilitada no *banco de dados primário*, não no servidor.
   * Depois que a auditoria de blob estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

    > [!IMPORTANT]
    > Por padrão, as configurações de armazenamento para o banco de dados secundário são idênticas às do banco de dados primário, causando um tráfego entre regiões. Você pode evitar isso habilitando a Auditoria de Blob no Servidor secundário e configurando um armazenamento local nas configurações de armazenamento do servidor secundário (isso substitui a localização de armazenamento do banco de dados secundário e resulta no salvamento dos logs de auditoria em um armazenamento local por cada banco de dados).  

## <a name="viewing-blob-auditing-logs"></a>Exibindo logs de auditoria de blob

Os logs de auditoria de blob são salvos como uma coleção de arquivos de blob em um contêiner chamado "**sqldbauditlogs**".

Para mais detalhes sobre a hierarquia de pasta de armazenamento de logs de auditoria de blob, a convenção de nomenclatura do blob e o formato do log, consulte a [Referência do formato do log de auditoria de blob (download do arquivo doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Há vários métodos para exibir logs de auditoria de blob:

* Por meio do [Portal do Azure](https://portal.azure.com) - abra o banco de dados relevante. Na parte superior da folha Auditoria e Detecção de Ameaças do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][10]

    Uma folha de Registros de auditoria será aberta, na qual será possível exibir todos os logs.

    - Você pode optar por exibir datas específicas clicando no **Filtro** na área superior da folha de registros de auditoria
    - Você pode alternar entre os registros de auditoria que foram criados pela política do servidor ou a política de banco de dados de auditoria

    ![Painel de navegação][11]
* Baixe os arquivos de log do seu contêiner do Azure Storage Blob por meio do portal ou usando uma ferramenta como o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

    Depois de baixar o arquivo localmente, você pode clicar duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.

* Métodos adicionais:

   * Você pode baixar vários arquivos simultaneamente por meio do Gerenciador de Armazenamento do Azure, clique com o botão direito do mouse em uma subpasta específica (uma subpasta que inclui todos os arquivos de log de uma data específica) e selecione **Salvar como** para salvar em uma pasta local.

       Depois de baixar vários arquivos (ou de um dia inteiro, como descrito acima), você poderá mesclá-los localmente da seguinte maneira:

       **Abra o SSMS -> Arquivo -> Abrir -> Merge Extended Events (Mesclar Eventos Estendidos) -> Escolha todos os arquivos para mesclar**
   * Programaticamente:

     * [Biblioteca C# do Leitor de Eventos Estendidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [Consultando arquivos de eventos estendidos usando o PowerShell](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * Nós criamos uma [aplicativo de exemplo](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que é executado no Azure e utiliza APIs públicas do OMS para enviar por push logs de auditoria do SQL ao OMS para consumo por meio do painel do OMS.

## <a name="viewing-table-audit-logs"></a>Exibindo logs de auditoria de tabela

Os logs de auditoria de tabela são salvos como uma coleção de tabelas do Armazenamento do Azure com um prefixo **SQLDBAuditLogs**.

Para obter detalhes adicionais sobre o formato do log de auditoria de tabela, consulte a [Referência de formato de log de auditoria de tabela (download de arquivo doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Há vários métodos para exibir logs de auditoria de tabela:

* Por meio do [Portal do Azure](https://portal.azure.com) - abra o banco de dados relevante. Na parte superior da folha Auditoria e Detecção de Ameaças do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação][10]

    Uma folha de Registros de auditoria será aberta, na qual será possível exibir todos os logs.

    * Você pode optar por exibir datas específicas clicando no **Filtro** na área superior da folha de registros de auditoria
    * Você pode baixar e exibir os logs de auditoria em formato Excel clicando em **Abrir no Excel** na área superior da folha de registros de auditoria

    ![Painel de navegação][12]

* Como alternativa, um modelo de relatório pré-configurado está disponível como uma [planilha do Excel para download](http://go.microsoft.com/fwlink/?LinkId=403540) para ajudar você a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do [Power Query(http://www.microsoft.com/download/details.aspx?id=39379).

* Você também pode importar os logs de auditoria para o modelo do Excel diretamente da sua conta do Azure Storage usando o Power Query. Você pode explorar os registros de auditoria e criar painéis e relatórios sobre os dados de log.

    ![Painel de navegação][9]

## <a name="storage-key-regeneration"></a>Regeneração de chave de armazenamento
Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao atualizar suas chaves, é necessário salvar novamente a política de auditoria. O processo é o seguinte:

1. Na folha Detalhes de armazenamento, mude a **Chave de Acesso de Armazenamento** de *Primária* para *Secundária* e clique em **OK** na parte inferior. Em seguida, clique em **SALVAR** na parte superior da folha de configuração de auditoria.

    ![Painel de navegação][6]
2. Acesse a folha Configuração de armazenamento e **regenere** a *Tecla de Acesso Primária*.

    ![Painel de navegação][8]
3. Volte para a folha Configuração de auditoria, mude a **Chave de Acesso de Armazenamento** de *Secundária* para *Primária* e clique em **OK** na parte inferior. Em seguida, clique em **SALVAR** na parte superior da folha Configuração de auditoria.
4. Volte para a folha Configuração de armazenamento e **regenere** a *Tecla de Acesso Secundária* (como preparação para o próximo ciclo de atualização de chaves).


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
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

## <a name="next-steps"></a>Próximas etapas

* Para configurar e gerenciar a auditoria usando o PowerShell, consulte [Configurar a auditoria de banco de dados usando o PowerShell](sql-database-auditing-powershell.md).
* Para obter uma visão geral da auditoria, consulte [Auditoria de banco de dados](sql-database-auditing.md).



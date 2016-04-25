<properties
	pageTitle="Introdução à auditoria do banco de dados SQL | Microsoft Azure"
	description="Introdução à auditoria do banco de dados SQL"
	services="sql-database"
	documentationCenter=""
	authors="ronitr"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="carlrabeler; ronitr"/>
 
# Introdução à auditoria do banco de dados SQL
A Auditoria do Banco de Dados SQL do Azure rastreia eventos do banco de dados e grava eventos auditados em um log de auditoria na sua conta de Armazenamento do Azure. A auditoria é disponibilizada normalmente para as camadas de serviço Basic, Standard e Premium.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

As ferramentas de auditoria permitem e facilitam a adoção de padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).

+ [Fundamentos da Auditoria do Banco de Dados SQL do Azure]
+ [Configurar a auditoria do banco de dados]
+ [Analisar os logs e relatórios de auditoria]

##<a id="subheading-1"></a>Fundamentos da Auditoria do Banco de Dados SQL do Azure

A seção a seguir descreve a configuração de auditoria usando o Portal do Azure. Você também pode [configurar a auditoria do banco de dados usando o Portal Clássico do Azure].

A auditoria de Banco de Dados SQL permite:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

> [AZURE.NOTE] Agora você pode receber alertas proativos em atividades anômalas de banco de dados que podem indicar possíveis ameaças de segurança usando o novo recurso **Detecção de Ameaças**, agora em modo de visualização. A Detecção de Ameaças pode ser ativada e configurada a partir a folha de configuração de auditoria. Para obter mais detalhes, consulte [Introdução à Detecção de Ameaças](sql-database-threat-detection-get-started.md).

Você pode configurar a auditoria para as categorias de eventos a seguir:

**SQL simples** e **SQL parametrizado** para os quais os logs de auditoria coletados são classificados como

- **Acesso a dados**
- **Alterações de esquema (DDL)**
- **Alterações de dados (DML)**
- **Contas, funções e permissões (DCL)**
- **Procedimento armazenado**, **Logon** e **Gerenciamento de transações**.

Para cada categoria de evento, as operações de auditoria de **Sucesso** e **Falha** são configuradas separadamente.

Para obter detalhes adicionais sobre as atividades e os eventos auditados, consulte a [Referência de Formato de Log de Auditoria (download do arquivo .doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Logs de auditoria são armazenados na sua conta de armazenamento do Azure. Você pode definir um período de retenção de log de auditoria, após o qual os logs antigos serão excluídos.

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão do servidor. Uma política de auditoria padrão do servidor é aplicada a todos os bancos de dados em um servidor que não contém uma política de auditoria de banco de dados substituta específica definida.

Antes de configurar a auditoria, verifique se você está usando um ["Cliente de nível inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configurar a auditoria do banco de dados

1. Inicie o [Portal do Azure](https://portal.azure.com) em https://portal.azure.com. Outra opção é iniciar o [Portal Clássico do Azure](https://manage.windowsazure.com/) em https://manage.windowsazure.com/. Consulte os detalhes abaixo.

2. Navegue até a folha de configuração do Banco de Dados SQL/SQL Server que você deseja auditar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

	![Painel de navegação][1]

3. Na folha de configuração de auditoria, escolha **ATIVAR** a auditoria.

4. Selecione **Detalhes de Armazenamento** para abrir a Folha de Armazenamento de Logs de Auditoria. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e o período de retenção. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria.

	![Painel de navegação][2]

5. Clique em **Eventos Auditados** para personalizar os eventos a auditar. Na folha **Registro em log por evento**, clique em **Sucesso** e **Falha** para registrar todos os eventos ou escolha categorias de evento individuais.


6. Você pode marcar a caixa de seleção **Herdar configurações de auditoria do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Quando você marcar essa opção, você verá um link que permite exibir ou modificar as configurações de auditoria do servidor neste contexto.

	![Painel de navegação][3]

7. Depois de definir as configurações de auditoria, você pode **ATIVAR** a Detecção de Ameaças e configurar os emails para receber alertas de segurança. Para obter mais detalhes, consulte a página [Introdução à Detecção de Ameaças](sql-database-threat-detection-get-started.md).

8. Clique em **Salvar**.



##<a id="subheading-3"></a>Analisar os logs e relatórios de auditoria

Logs de auditoria são agregados em uma coleção de tabelas de armazenamento com um prefixo **SQLDBAuditLogs** na conta de armazenamento do Azure que você escolheu durante a instalação. Você pode visualizar os arquivos de log usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/).

Um modelo de relatório pré-configurado está disponível como uma [planilha do Excel para download](http://go.microsoft.com/fwlink/?LinkId=403540) para ajudar você a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do Power Query, que pode ser baixado [aqui](http://www.microsoft.com/download/details.aspx?id=39379).

Você pode importar os logs de auditoria para o modelo do Excel diretamente da sua conta de armazenamento do Azure usando o Power Query. Você pode explorar os registros de auditoria e criar painéis e relatórios sobre os dados de log.


![Painel de navegação][4]


##<a id="subheading-4"></a>Configurar a auditoria para seu banco de dados usando o Portal Clássico do Azure

1. Inicie o [Portal Clássico do Azure](https://manage.windowsazure.com/) em https://manage.windowsazure.com/.

2. Clique no Banco de Dados SQL / SQL Server que você deseja auditar e clique na guia **AUDITORIA E SEGURANÇA**.

3. Defina a Auditoria como **HABILITADA**.

	![Painel de navegação][5]

4. Edite o **REGISTRO EM LOG POR EVENTO** conforme necessário, para personalizar quais eventos auditar.

	![Painel de navegação][6]

5. Selecione uma **CONTA DE ARMAZENAMENTO** e configure a **RETENÇÃO**.

	![Painel de navegação][7]

6. Clique em **SALVAR**.




##<a id="subheading-5">Práticas para uso em produção</a>
A descrição nesta seção refere-se às capturas de tela acima. É possível usar o [Portal do Azure](https://portal.azure.com) ou o [Portal Clássico do Azure](https://manage.windowsazure.com/).


##<a id="subheading-6"></a>Regeneração de Chave de Armazenamento

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao atualizar suas chaves, é necessário salvar novamente a política de auditoria. O processo é o seguinte:


1. Na folha de configuração de auditoria, altere **Chave de acesso de armazenamento** de *Primária* para *Secundária* e clique em **SALVAR**.

	![][8]

2. Acesse a folha de configuração de armazenamento e **regenere** a *Chave de Acesso Primária*.

3. Volte para a folha de configuração de auditoria, altere a **Chave de Acesso de Armazenamento** de *Secundária* para *Primária* e clique em **SALVAR**.

4. Volte para a interface do usuário de armazenamento e **regenere** a *Chave de Acesso Secundária* (como preparação para o próximo ciclo de atualização de chaves).
  
##<a id="subheading-7"></a>Automação
Há vários cmdlets do PowerShell que você pode usar para configurar a auditoria no Banco de Dados SQL do Azure:

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Fundamentos da Auditoria do Banco de Dados SQL do Azure]: #subheading-1
[Configurar a auditoria do banco de dados]: #subheading-2
[Analisar os logs e relatórios de auditoria]: #subheading-3
[configurar a auditoria do banco de dados usando o Portal Clássico do Azure]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=AcomDC_0413_2016-->
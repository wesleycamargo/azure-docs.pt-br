<properties
   pageTitle="Auditoria no SQL Data Warehouse do Azure | Microsoft Azure"
   description="Introdução à auditoria no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# Auditoria no Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Auditoria](sql-data-warehouse-auditing-overview.md)
- [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)

A auditoria do SQL Data Warehouse permite registrar eventos no banco de dados em um log de auditoria na sua Conta de Armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança. A auditoria do SQL Data Warehouse também se integra ao Microsoft Power BI para relatórios e análises de buscas detalhadas.

As ferramentas de auditoria permitem e facilitam a adoção de padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte a <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Central de Confiabilidade do Azure</a>.

+ [Fundamentos da Auditoria do Banco de Dados]
+ [Configurar a auditoria do banco de dados]
+ [Analisar os logs e relatórios de auditoria]

##<a id="subheading-1"></a>Fundamentos da Auditoria do Banco de Dados do SQL Data Warehouse do Azure


A auditoria do banco de dados do SQL Data Warehouse permite que você:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Você pode configurar a auditoria para as categorias de eventos a seguir:

**SQL simples** e **SQL parametrizado** para os quais os logs de auditoria coletados são classificados como

- **Acesso a dados**
- **Alterações de esquema (DDL)**
- **Alterações de dados (DML)**
- **Contas, funções e permissões (DCL)**
- **Procedimento armazenado**, **Logon** e **Gerenciamento de transações**.

Para cada categoria de evento, as operações de auditoria de **sucesso** e **falha** são configuradas separadamente.

Para obter detalhes adicionais sobre as atividades e os eventos auditados, consulte a <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referência de Formato de Log de Auditoria (download do arquivo .doc)</a>.

Logs de auditoria são armazenados na sua conta de armazenamento do Azure. Você pode definir um período de retenção para logs de auditoria.

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão do servidor. Uma política de auditoria padrão do servidor é aplicada a todos os bancos de dados em um servidor que não contém uma política de auditoria de banco de dados substituta específica definida.

Antes de configurar a auditoria, verifique se você está usando um ["Cliente de nível inferior"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurar a auditoria do banco de dados

1. Inicie o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

2. Navegue até a folha de configuração do banco de dados do SQL Data Warehouse / SQL Server que você deseja auditar. Clique no botão **Configurações** na parte superior e, na folha de Configuração, selecione **Auditoria**.

	![][1]

3. Na folha de configuração de auditoria, primeiro desmarque a caixa de seleção **Herdar as configurações de auditoria do servidor**. Isso permite que você especifique as configurações para um determinado banco de dados.

	![][2]

4. Em seguida, habilite a auditoria clicando no botão **ON**.

	![][3]

5. Na folha de configuração de auditoria, selecione **DETALHES DE ARMAZENAMENTO** para abrir a Folha de Armazenamento de Logs de Auditoria. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e o período de retenção. **Dica**: use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios pré-configurados.

	![][4]

6. Clique no botão **OK** para salvar a configuração de detalhes do armazenamento.


7. Em **REGISTRO EM LOG POR EVENTO**, clique em **SUCESSO** e **FALHA** para registrar todos os eventos ou escolha categorias de evento individuais.


8. Se você estiver configurando a Auditoria para um banco de dados, talvez seja necessário alterar a cadeia de conexão do cliente para garantir que os dados de auditoria sejam capturados corretamente. Verifique o tópico [Modify Server FDQN in the connection string](sql-data-warehouse-auditing-downlevel-clients.md) (Modificar o FQDN do servidor na cadeia de conexão) para conexões de cliente de nível anterior.

9. Clique em **OK**.


##<a id="subheading-3">Analisar os logs e relatórios de auditoria</a>

Logs de auditoria são agregados em uma coleção de tabelas de armazenamento com um prefixo **SQLDBAuditLogs** na conta de armazenamento do Azure que você escolheu durante a instalação. Você pode ver os arquivos de log usando uma ferramenta como o <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Gerenciador de Armazenamento do Azure</a>.

Um modelo de relatório do painel pré-configurado está disponível como uma planilha do <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">Excel para download</a> para ajudar você a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do Power Query, que pode ser baixado <a href="http://www.microsoft.com/download/details.aspx?id=39379">aqui</a>.

O modelo possui dados de amostra fictícios, e você pode configurar o Power Query para importar seu log de auditoria diretamente da sua conta de armazenamento do Azure.

Para obter mais instruções detalhadas sobre como trabalhar com o modelo de relatório, leia as <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Instruções (download do .doc)</a>.

![][5]


##<a id="subheading-4">Práticas para uso em produção</a>
A descrição nesta seção refere-se às capturas de tela acima. É possível usar o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou o <a href= "https://manage.windowsazure.com/" target="_bank">Portal Clássico do Azure</a>.


##<a id="subheading-5"></a>Regeneração de Chave de Armazenamento

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao atualizar suas chaves, é necessário salvar novamente a política. O processo é o seguinte:


1. Na folha de configuração de auditoria (descrita acima na seção de configuração de auditoria), altere a **Chave de Acesso de Armazenamento** de *Primária* para *Secundária* e clique em **SALVAR**. ![][4]
2. Acesse a folha de configuração de armazenamento e **regenere** a *Chave de Acesso Primária*.

3. Volte para a folha de configuração de auditoria, altere a **Chave de Acesso de Armazenamento** de *Secundária* para *Primária* e pressione **SALVAR**.

4. Volte para a interface do usuário de armazenamento e **regenere** a *Chave de Acesso Secundária* (como preparação para o próximo ciclo de atualização de chaves.

##<a id="subheading-6"></a>Automação
Há vários cmdlets do PowerShell, que você pode usar para configurar a auditoria no Banco de Dados SQL do Azure. Para acessar os cmdlets de auditoria você deve estar executando o PowerShell no modo do Gerenciador de Recursos do Azure.

> [AZURE.NOTE] O módulo do [Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/dn654592.aspx) está no modo de visualização. Ele pode não fornecer os mesmos recursos de gerenciamento que o módulo do Azure.

Quando você estiver no modo do Gerenciador de Recursos do Azure, execute `Get-Command *AzureSql*` para listar os cmdlets disponíveis.


<!--Anchors-->
[Fundamentos da Auditoria do Banco de Dados]: #subheading-1
[Configurar a auditoria do banco de dados]: #subheading-2
[Analisar os logs e relatórios de auditoria]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->
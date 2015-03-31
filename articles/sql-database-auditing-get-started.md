<properties 
	pageTitle="Introdução à auditoria do banco de dados SQL | Azure" 
	description="Introdução à auditoria do banco de dados SQL" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="jeffreyg"/>
 
# Introdução à auditoria do banco de dados SQL 
<p> A Auditoria do Banco de Dados SQL do Azure rastreia eventos do banco de dados e grava eventos auditados em um log de auditoria na sua conta de Armazenamento do Azure. A auditoria é disponibilizada normalmente para as camadas de serviço Basic, Standard e Premium.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança. 

As ferramentas de auditoria permitem e facilitam a adoção de padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre os programas Azure que dão suporte à conformidade com os padrões, consulte <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Central de confiabilidade do Azure</a>.

+ [Fundamentos da Auditoria do Banco de Dados SQL do Azure] 
+ [Configure a auditoria para seu banco de dados]
+ [Analise os logs e relatórios de auditoria]

##<a id="subheading-1"></a>Fundamentos da Auditoria do Banco de Dados SQL do Azure

A auditoria é configurada no Portal de Visualização do Azure, mas não faz diferença se o banco de dados foi criado usando o Portal do Azure ou o Portal de Visualização do Azure. A auditoria do Banco de Dados SQL permite que você:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Defina categorias de ações e eventos do banco de dados a serem registradas.
- **Relate** sobre a atividade do banco de dados. Utilize relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
- **Analise** relatórios. Encontre eventos suspeitos, atividades incomuns e tendências.

Você pode realizar a auditoria dos seguintes eventos e atividades:

- **Acesso a dados**
- **Alterações de esquema (DDL)**
- **Alterações de dados (DML)**
- **Contas, funções e permissões (DCL)**
- **Exceções de segurança**

Para mais detalhes sobre as atividades e eventos registrados, consulte a <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referência de formato de log de auditoria (download do arquivo .doc)</a>. 

Você também escolhe a conta de armazenamento onde os logs de auditoria serão salvos.

###Cadeia de conexão de segurança habilitada
Ao configurar a auditoria, o Azure oferece uma cadeia de conexão segura para o banco de dados. Somente aplicativos clientes que utilizam essa cadeia de conexão terão suas atividades e eventos registrados, por isso, você precisa atualizar os aplicativos clientes existentes para utilizar o novo formato de cadeia de caracteres.

Formato tradicional da cadeia de conexão: <*server name*>.database.windows.net

Cadeia de conexão segura: <*server name*>.database.**secure**.windows.net


##<a id="subheading-2"></a>Configure a auditoria para seu banco de dados

1. Inicie o <a href="https://portal.azure.com" target="_blank">Portal de visualização do Azure</a> em https://portal.azure.com. Como alternativa, você também pode iniciar o <a href= "https://manage.windowsazure.com/" target="_bank">Portal clássico do Azure</a> em https://manage.windowsazure.com/. Consulte os detalhes a seguir.
2. Navegue para a lâmina de configuração do banco de dados que deseja auditar. Role para baixo para a seção **Operações** e clique em **Auditoria** para habilitar a auditoria e iniciar a lâmina de configuração de auditoria.

	![][1]

3. Na folha de configuração de auditoria, selecione a conta de armazenamento do Azure onde os logs serão salvos. **Dica:** Use a mesma conta de armazenamento para todos os bancos de dados auditados para obter o máximo dos modelos de relatórios pré-configurados.

	![][2]

4. Em **Opções de Auditoria**, clique em **Todos** para registrar todos os eventos ou escolher tipos de eventos individuais.

	![][3]

5. Marque **Salvar essa configuração como padrão do servidor** se quiser que essas configurações se apliquem a todos os bancos de dados futuros no servidor e a qualquer um que ainda não tenha a auditoria configurada. Você pode substituir as configurações mais tarde para cada banco de dados seguindo essas mesmas etapas. 

6. Clique em **Mostrar cadeias de conexão de banco de dados** e depois copie ou anote a cadeia de conexão segura adequada para seu aplicativo. Use essa conexão para aplicativos clientes cuja atividade você queira auditar.

	![][5]

7. Clique em **OK**.



##<a id="subheading-3">Analise os logs e relatórios de auditoria</a>

Os logs de auditoria são agregados em uma única Tabela de Armazenamento do Azure denominada **AuditLogs** na conta de armazenamento do Azure que você escolheu durante a instalação. Você pode visualizar os arquivos de log usando uma ferramenta como o <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Gerenciador de Armazenamento do Azure</a>.

Um modelo de relatório de painel pré-configurado está disponível como uma <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">planilha de Excel para baixar</a> para ajudá-lo a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do Power Query, que pode ser baixado <a href="http://www.microsoft.com/download/details.aspx?id=39379">aqui</a>. 

O modelo possui dados de amostra fictícios, e você pode configurar o Power Query para importar seu log de auditoria diretamente da sua conta de armazenamento do Azure. 

Para obter mais instruções detalhadas sobre como trabalhar com o modelo de relatório, leia as <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Instruções (download do .doc)</a>.

![][6]


##<a id="subheading-4"></a>Configure a auditoria para seu banco de dados usando o portal clássico do azure

1. Inicie o <a href= "https://manage.windowsazure.com/" target="_bank">Portal clássico do Azure</a> em https://manage.windowsazure.com/. 
2. Clique no banco de dados que deseja auditar e depois clique na guia**Auditoria e visualização de segurança**.
3. Na seção de auditoria, clique em "Habilitar".

![][7]

4. Edite o **EVENT TYPE** conforme o necessário.

![][8]

5. Selecione uma **STORAGE ACCOUNT**.
6. Clique em **SALVAR**.
7. Clique em **Mostrar cadeia de conexão segura** para a cadeia de caracteres de conexão.


##<a id="subheading-3">Práticas para uso em produção</a>
A descrição nesta seção refere-se às capturas de tela acima. Você pode usar o <a href="https://portal.azure.com" target="_blank">Portal de visualização do Azure</a> ou <a href= "https://manage.windowsazure.com/" target="_bank">Portal clássico do Azure</a> .
 

##<a id="subheading-4"></a>Acesso habilitado para segurança

Em produção, você provavelmente precisará que todo o tráfego para todo o banco de dados de todos os aplicativos e ferramentas seja auditado. Para isso modifique o **Acesso de segurança habilitada** de *Optional* para *Required* e salve a política. Depois que *Required* tiver sido configurado, não há opção para acessar o banco de dados usando a cadeia de conexão original, mas apenas por meio da cadeia de conexão segura.


![][9]


##<a id="subheading-4"></a>Nova geração de chave de armazenamento

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. O Serviço de Auditoria não mantém suas chaves de conta de armazenamento. Ao Salvar, uma chave de Assinatura de Acesso Compartilhado (SAS) apenas de gravação é produzida para a tabela de auditoria (apenas o cliente pode ler os logs de auditoria). Para esse fim, ao atualizar suas chaves, é preciso salvar a política novamente. O processo é o seguinte:


1. Na lâmina de configuração de auditoria (descrita acima na seção de configuração de auditoria), altere **Chave de acesso de armazenamento** de *Primary* para *Secondary* e **SAVE**.
![][10]
2. Vá até a lâmina de configuração de armazenamento e **regenere** o *Primary Access Key*.

3. Volte para a lâmina de configuração de auditoria, alterne a **Chave de acesso de armazenamento** de *Secondary* para *Primary* e pressione **SAVE**.

4. Volte para o armazenamento da interface do usuário e **regenerar** a *Secondary Access Key* (como preparação para a próxima chaves ciclo de atualização.
  
##<a id="subheading-4"></a>Automação
Há vários cmdlets do PowerShell, que você pode usar para configurar a auditoria no Banco de Dados SQL do Azure. Para acessar os cmdlets de auditoria você deve estar executando o PowerShell no modo do Gerenciador de Recursos do Azure.

> [AZURE.NOTE] O módulo AzureResourceManager está atualmente em visualização. Ele pode não fornecer os mesmos recursos de gerenciamento que o módulo do Azure.

 O modo do [Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/dn654592.aspx) é acessado executando o cmdlet Switch-AzureMode (`Switch-AzureMode AzureResourceManager`). Quando você estiver no modo do Gerenciador de Recursos do Azure, execute `Get-Command *AzureSql*` para listar os cmdlets disponíveis.







<!--Anchors-->
[Fundamentos da Auditoria do Banco de Dados SQL do Azure]: #subheading-1
[Configure a auditoria para seu banco de dados]: #subheading-2
[Analise os logs e relatórios de auditoria]: #subheading-3
[Configure a auditoria para seu banco de dados usando o portal clássico do azure]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png






<!--Link references-->
[Link de 1 para outro tópico de documentação do azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Link de 2 para outro tópico de documentação do azure.microsoft.com]: ../web-sites-custom-domain-name/
[Link de 3 para outro tópico de documentação do azure.microsoft.com]: ../storage-whatis-account/


<!--HONumber=47-->

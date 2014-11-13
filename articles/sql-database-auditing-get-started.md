<properties title="Introdu&ccedil;&atilde;o &agrave; auditoria do banco de dados SQL" pageTitle="Introdu&ccedil;&atilde;o &agrave; auditoria do banco de dados SQL | Azure" description="Introdu&ccedil;&atilde;o &agrave; auditoria do banco de dados SQL" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# Introdução à auditoria do banco de dados SQL

A Auditoria do Banco de Dados SQL do Azure rastreia eventos do banco de dados e grava eventos auditados em um log de auditoria na sua conta de Armazenamento do Azure. A auditoria está disponível na visualização das camadas de serviço Basic, Standard e Premium. Para usar a auditoria, [inscreva-se na visualização][inscreva-se na visualização].

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

As ferramentas de auditoria permitem e facilitam a adoção de padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center][Azure Trust Center].

-   [Fundamentos da Auditoria do Banco de Dados SQL do Azure][Fundamentos da Auditoria do Banco de Dados SQL do Azure]
-   [Configure a auditoria para seu banco de dados][Configure a auditoria para seu banco de dados]
-   [Analise os logs e relatórios de auditoria][Analise os logs e relatórios de auditoria]

## <span id="subheading-1"></span>Fundamentos da Auditoria do Banco de Dados SQL do Azure</a>

A auditoria é configurada no Portal de Visualização do Azure, mas não faz diferença se o banco de dados foi criado usando o Portal do Azure ou o Portal de Visualização do Azure. A auditoria do Banco de Dados SQL permite que você:

-   **Retenha** uma trilha de auditoria dos eventos selecionados. Defina categorias de ações e eventos do banco de dados a serem registradas.
-   **Relate** sobre a atividade do banco de dados. Utilize relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
-   **Analise** relatórios. Encontre eventos suspeitos, atividades incomuns e tendências.

Você pode realizar a auditoria dos seguintes eventos e atividades:

-   **Acesso a dados**
-   **Alterações de esquema (DDL)**
-   **Alterações de dados (DML)**
-   **Contas, funções e permissões (DCL)**
-   **Exceções de segurança**

Para obter detalhes adicionais sobre as atividades e os eventos registrados, consulte a [Referência de Formato de Log de Auditoria (download do arquivo .doc)][Referência de Formato de Log de Auditoria (download do arquivo .doc)].

Você também escolhe a conta de armazenamento onde os logs de auditoria serão salvos.

### Cadeia de conexão segura

Ao configurar a auditoria, o Azure oferece uma cadeia de conexão segura para o banco de dados. Somente aplicativos clientes que utilizam essa cadeia de conexão terão suas atividades e eventos registrados, por isso, você precisa atualizar os aplicativos clientes existentes para utilizar o novo formato de cadeia de caracteres.

Formato tradicional da cadeia de conexão: \<*nome do servidor*\>.database.windows.net

Cadeia de conexão segura: \<*nome do servidor*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Configure a auditoria para seu banco de dados

1.  [Inscreva-se para a visualização de Auditoria][inscreva-se na visualização].
2.  Inicie o [Azure Preview Portal][Azure Preview Portal] em https://portal.azure.com.
3.  Clique no banco de dados que deseja auditar e depois clique em **Visualização da Auditoria** para ativar a visualização da auditoria e iniciar a folha de configuração de auditoria.

    ![][0]

4.  Na folha de configuração de auditoria, selecione a conta de armazenamento do Azure onde os logs serão salvos. **Dica:** Use a mesma conta de armazenamento para todos os bancos de dados auditados para obter o máximo dos modelos de relatórios pré-configurados.

    ![][1]

5.  Em **Opções de Auditoria**, clique em **Todos** para registrar todos os eventos ou escolher tipos de eventos individuais.

    ![][2]

6.  Marque **Salvar essa configuração como padrão** se quiser que essas configurações se apliquem a todos os bancos de dados futuros no servidor e a qualquer um que ainda não tenha a auditoria configurada. Você pode substituir as configurações mais tarde para cada banco de dados seguindo essas mesmas etapas.

    ![][3]

7.  Clique em **Mostrar cadeias de conexão do banco de dados** e depois copie ou anote a cadeia de conexão segura adequada para seu aplicativo. Use essa conexão para aplicativos clientes cuja atividade você queira auditar.

    ![][4]

8.  Clique em **OK**.

## <span id="subheading-3"></span>Analise os logs e relatórios de auditoria</a>

Os logs de auditoria são agregados em uma única Tabela de Armazenamento do Azure denominada **AuditLogs** na conta de armazenamento do Azure que você escolheu durante a instalação. Você pode visualizar os arquivos de log usando uma ferramenta como o [Gerenciador de Armazenamento do Azure][Gerenciador de Armazenamento do Azure].

Um modelo de relatório do painel pré-configurado está disponível como uma [planilha do Excel para download][planilha do Excel para download] para ajudar você a analisar rapidamente os dados de log. Para utilizar o modelo em seus logs de auditoria, você precisará do Excel 2013 ou mais recente e do Power Query, que pode ser baixado [aqui][aqui].

O modelo possui dados de amostra fictícios, e você pode configurar o Power Query para importar seu log de auditoria diretamente a partir da sua conta de armazenamento do Azure.

Para obter mais instruções detalhadas sobre como trabalhar com o modelo de relatório, leia as [Instruções (download do .doc)][Instruções (download do .doc)].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [inscreva-se na visualização]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure Trust Center]: http://azure.microsoft.com/pt-br/support/trust-center/compliance/
  [Fundamentos da Auditoria do Banco de Dados SQL do Azure]: #subheading-1
  [Configure a auditoria para seu banco de dados]: #subheading-2
  [Analise os logs e relatórios de auditoria]: #subheading-3
  [Referência de Formato de Log de Auditoria (download do arquivo .doc)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [Azure Preview Portal]: https://portal.azure.com
  [0]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Gerenciador de Armazenamento do Azure]: http://azurestorageexplorer.codeplex.com/
  [planilha do Excel para download]: http://go.microsoft.com/fwlink/?LinkId=403540
  [aqui]: http://www.microsoft.com/pt-br/download/details.aspx?id=39379
  [Instruções (download do .doc)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png

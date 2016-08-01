<properties
   pageTitle="Configurar um firewall do SQL Server - visão geral | Microsoft Azure"
   description="Saiba como configurar um firewall de banco de dados SQL com regras de firewall no nível de servidor e banco de dados para gerenciar o acesso."
   keywords="firewall do banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/22/2016"
   ms.author="rickbyh"/>

# Configurar regras de firewall do Banco de Dados SQL – visão geral


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall nos níveis de servidor e de banco de dados.

- **Regras de firewall no nível do servidor:** essas regras permitem que os clientes acessem todo o Azure SQL Server, ou seja, todos os bancos dentro do mesmo servidor lógico. Essas regras são armazenadas no banco de dados **mestre**. As regras de firewall no nível de servidor podem ser configuradas por meio do portal ou usando instruções Transact-SQL.
- **Regras de firewall no nível do banco de dados:** essas regras permitem que os clientes acessem bancos de dados individuais dentro de seu servidor do Banco de Dados SQL. Essas regras são criadas de acordo com o banco de dados e são armazenadas em bancos de dados individuais (incluindo o **mestre**). Essas regras podem ser úteis para restringir o acesso a determinados bancos de dados (seguros) dentro do mesmo servidor lógico. As regras de firewall no nível do banco de dados só podem ser configuradas usando instruções Transact-SQL.

**Recomendação:** a Microsoft recomenda o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de aumentar a segurança e tornar seu banco de dados mais portátil. Use regras de firewall no nível do servidor para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso e não quer gastar tempo configurando cada um individualmente.


## Visão geral do firewall

Inicialmente, todo acesso Transact-SQL ao seu Azure SQL Server é bloqueado pelo firewall. Para começar a usar o Azure SQL Server, você deve acessar o Portal do Azure e especificar uma ou mais regras de firewall no nível do servidor que permitem acesso ao seu Azure SQL Server. Use as regras de firewall para especificar quais intervalos de endereço IP da Internet têm permissão e se os aplicativos do Azure podem tentar se conectar ao seu Azure SQL Server.

No entanto, se você quiser conceder acesso de forma seletiva a apenas um dos bancos de dados em seu Azure SQL Server, você deve criar uma regra no nível de banco de dados para o banco de dados necessário com um intervalo de endereços IP que vá além do intervalo de endereços IP especificado na regra de firewall no nível de servidor, e certifique-se de que o endereço IP do cliente esteja dentro do intervalo especificado na regra no nível de banco de dados.

As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Azure SQL Server ou Banco de Dados SQL, conforme exibido no diagrama a seguir.

   ![Diagrama descrevendo a configuração de firewall.][1]

## Conectando pela Internet

Quando um computador tenta se conectar ao seu servidor de banco de dados pela Internet, o firewall verifica o endereço IP de origem da solicitação com base no conjunto completo de regras de firewall no nível do servidor e (se necessário) no nível do banco de dados:

- Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida ao seu servidor de Banco de Dados SQL do Azure.

- Se o endereço IP da solicitação não estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, ocorrerá a verificação das regras de firewall no nível do banco de dados. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do banco de dados, a conexão será concedida apenas ao banco de dados que apresenta uma regra no nível de banco de dados correspondente.

- Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em qualquer uma das regras de firewall no nível do servidor ou do banco de dados, solicitação de conexão falhará.

> [AZURE.NOTE] Para acessar o Banco de Dados SQL do Azure de seu computador local, verifique se o firewall em sua rede e computador local permite a comunicação de saída na porta TCP 1433.


## Conexão pelo Azure

Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que essas conexões são permitidas. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de Banco de Dados SQL do Azure.

> [AZURE.IMPORTANT] Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

Há duas maneiras de habilitar conexões do Azure:

- No [Portal do Microsoft Azure](https://portal.azure.com/), marque a caixa de seleção **Permitir que os serviços do Azure acessem o servidor** ao criar um novo servidor.

- No [Portal Clássico](http://go.microsoft.com/fwlink/p/?LinkID=161793), na guia **Configurar** em um servidor, na seção **Serviços Permitidos**, clique em **Sim** para **Serviços do Microsoft Azure**.

## Criando a primeira regra de firewall no nível do servidor

A primeira configuração de firewall no nível do servidor pode ser criada usando o [Portal do Azure](https://portal.azure.com/) ou com programação usando a API REST ou o Azure PowerShell. As regras de firewall no nível de servidor subsequentes podem ser criadas e gerenciados usando esses métodos, por exemplo, por meio de Transact-SQL. Para obter mais informações sobre regras de firewall no nível do servidor, consulte [Como configurar um firewall do Azure SQL Server usando o Portal do Azure](sql-database-configure-firewall-settings.md).

## Criando regras de firewall no nível do banco de dados

Depois de configurar o primeiro firewall no nível do servidor, convém restringir o acesso a determinados bancos de dados. Se você especificar um intervalo de endereços IP na regra de firewall no nível do banco de dados que está fora do intervalo especificado na regra de firewall no nível do servidor, somente os clientes com endereços IP no intervalo de nível de banco de dados poderão acessar o banco de dados. Você pode ter no máximo 128 regras de firewall no nível do banco de dados para um banco de dados. É possível criar e gerenciar regras de firewall no nível de banco de dados para bancos de dados mestre e de usuário por meio o Transact-SQL. Para obter mais informações sobre como configurar regras de firewall no nível do banco de dados, consulte [sp\_set\_database\_firewall\_rule (Bancos de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## Gerenciando programaticamente as regras de firewall

Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando Transact-SQL, API REST e Azure PowerShell. As tabelas a seguir descrevem o conjunto de comandos disponíveis para cada método.


### Transact-SQL

| Exibição do catálogo ou Procedimento armazenado | Nível | Descrição |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Servidor | Exibe as regras de firewall atuais no nível de servidor |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Servidor | Cria ou atualiza as regras de firewall no nível de servidor |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Servidor | Remove as regras de firewall no nível de servidor |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Banco de dados | Exibe as regras de firewall atuais no nível de banco de dados |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Banco de dados | Cria ou atualiza as regras de firewall no nível de banco de dados |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bancos de dados | Remove as regras de firewall no nível de banco de dados |

### API REST

| API | Nível | Descrição |
|----------------------|--------|------------------------------------------------------------------|
| [Listar regras de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Servidor | Exibe as regras de firewall atuais no nível de servidor |
| [Criar uma regra de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Servidor | Cria ou atualiza as regras de firewall no nível de servidor |
| [Definir regra de firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Servidor | Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Excluir regra de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Servidor | Remove as regras de firewall no nível de servidor |


### PowerShell do Azure

| Cmdlet | Nível | Descrição |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Servidor | Retorna as regras de firewall atuais no nível de servidor |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Servidor | Cria as regras de firewall no nível de servidor |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Servidor | Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Servidor | Remove as regras de firewall no nível de servidor |

> [AZURE.NOTE] Pode haver um atraso de até cinco minutos para as alterações nas configurações de firewall serem efetivadas.

## Solucionando problemas do firewall de banco de dados

Considere os seguintes pontos quando o acesso ao serviço de Banco de Dados SQL do Microsoft Azure não se comportar conforme o esperado:

- **Configuração de firewall local:** antes que o computador possa acessar o Banco de Dados SQL do Azure, talvez seja necessário criar uma exceção de firewall em seu computador para a porta TCP 1433. Talvez seja necessário abrir portas adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

- **NAT (Conversão de endereços de rede):** devido à NAT, o endereço IP usado pelo computador para se conectar ao Banco de Dados SQL do Azure pode ser diferente do endereço IP exibido nas configurações de IP do computador. Para exibir o endereço IP usado pelo computador para se conectar ao Azure, faça logon no Portal e navegue até a guia **Configurar** no servidor que hospeda o seu banco de dados. Na seção **Endereços IP Permitidos**, o **Atual Endereço IP do Cliente** é exibido. Clique em **Adicionar** nos **Endereços IP Permitidos** para permitir que este computador acesse o servidor.

- **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados SQL do Azure entrem em vigor.

- **O logon não está autorizado ou uma senha incorreta foi usada:** se um logon não tiver permissões no servidor do Banco de Dados SQL, ou se a senha usada estiver incorreta, a conexão com o servidor do Banco de Dados SQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente deve fornecer as credenciais de segurança necessárias. Para saber mais sobre a preparação de logons, consulte Gerenciando bancos de dados, logons e usuários no Banco de Dados SQL do Azure.

- **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

 - Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados SQL do Azure e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

 - Obtenha o endereçamento IP estático para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall.

## Próximas etapas

Para ver artigos que explicam como criar regras de firewall no nível do servidor e do banco de dados, confira:

- [Configurar regras de firewall no nível do servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)
- [Como configurar um firewall do Banco de Dados SQL do Azure usando TSQL](sql-database-configure-firewall-settings-tsql.md)
- [Como configurar um firewall do Banco de Dados SQL do Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar um banco de dados, consulte [Criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um Banco de Dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, consulte [Exemplos de código do cliente de início rápido do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender como navegar para bancos de dados, consulte [Gerenciar o acesso ao banco de dados e a segurança de logon](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## Recursos adicionais

- [Protegendo o banco de dados](sql-database-security.md)
- [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0720_2016-->
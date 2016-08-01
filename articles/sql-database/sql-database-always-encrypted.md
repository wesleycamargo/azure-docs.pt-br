<properties
	pageTitle="Sempre Criptografado – Proteger dados confidenciais no Banco de Dados SQL do Azure com criptografia de banco de dados"
	description="Proteja dados confidenciais no banco de dados SQL em minutos."
	keywords="criptografar dados, criptografia do sql, criptografia de banco de dados, dados confidenciais, sempre criptografados"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="sstein"/>

# Sempre Criptografado – Proteger dados confidenciais no Banco de Dados SQL com a criptografia de banco de dados e armazenar as chaves de criptografia no repositório de certificados do Windows

> [AZURE.SELECTOR]
- [Cofre da Chave do Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Repositório de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra como proteger dados confidenciais em um banco de dados SQL com a criptografia de banco de dados usando o [Assistente Sempre Criptografado](https://msdn.microsoft.com/library/mt459280.aspx) no [SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/hh213248.aspx) e armazenar suas chaves de criptografia no repositório de certificados do Windows.

Always Encrypted é uma nova tecnologia de criptografia de dados no Banco de Dados SQL do Azure e no SQL Server que protege dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, assim como enquanto os dados estão em uso, garantindo que os dados confidenciais nunca apareçam como texto não criptografado dentro do sistema de banco de dados. Depois de criptografar dados, somente aplicativos clientes ou servidores de aplicativo, que têm acesso às chaves, podem acessar dados de texto não criptografado. Para obter informações detalhadas, consulte [Always Encrypted (Mecanismo do Banco de Dados)](https://msdn.microsoft.com/library/mt163865.aspx).


Depois de configurar o banco de dados para usar o Always Encripted, criaremos um aplicativo cliente em C# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo e saiba como configurar o Always Encripted para um banco de dados SQL do Azure. Neste artigo, você aprenderá como realizar as seguintes tarefas:

- Usar o assistente Always Encrypted no SSMS para criar [Chaves Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - Crie uma [CMK (Chave Mestra da Coluna)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [CEK (Chave de Criptografia da Coluna)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de banco de dados e criptografar algumas colunas.
- Crie um aplicativo que insira, selecione e exiba os dados das colunas criptografadas.


## Pré-requisitos

Para este tutorial, será necessário:

- Uma conta do Azure e uma assinatura antes de começar. Se não tiver uma, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## Criar um banco de dados SQL em branco
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Novo** > **Dados + Armazenamento** > **Banco de Dados SQL**.
3. Crie um banco de dados **Em branco** chamado **Clínica** em um servidor novo ou existente. Para obter instruções detalhadas para criar um banco de dados no portal do Azure, consulte [Criar um banco de dados SQL em alguns minutos](sql-database-get-started.md).

	![criar um banco de dados em branco](./media/sql-database-always-encrypted/create-database.png)

Você precisará da cadeia de conexão mais tarde no tutorial. Portanto, após criar o banco de dados, vá para o novo banco de dados Clínica e copie a cadeia de conexão (pode-se obter a cadeia de conexão a qualquer momento, mas, como já estamos no portal, é mais fácil copiá-la agora).

1. Clique em **Bancos de dados SQL** > **Clínica** > **Mostrar cadeias de conexão do banco de dados**.
2. Copie a cadeia de conexão para **ADO.NET**:

	![copiar a cadeia de conexão](./media/sql-database-always-encrypted/connection-strings.png)


## Conectar-se ao banco de dados com o SSMS

Abra o SSMS e conecte-se ao servidor com o banco de dados Clínica.


1. Abra o SSMS (clique em **Conectar** > **Mecanismo de Banco de Dados...** para abrir a janela **Conectar ao Servidor** caso não esteja aberta).
2. Insira o nome do servidor e credenciais. O nome do servidor pode ser encontrado na folha do banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome completo do servidor, incluindo *database.windows.net*.

	![copiar a cadeia de conexão](./media/sql-database-always-encrypted/ssms-connect.png)

3. Se a janela **Nova Regra de Firewall** for aberta, entre no Azure e deixe o SSMS criar uma nova regra de firewall para você.


## Criar uma tabela

Primeiramente, criaremos uma tabela para armazenar dados de pacientes (apenas uma tabela normal inicialmente – vamos configurar a criptografia na próxima seção).

1. Expanda **Bancos de Dados**.
1. Clique com o botão direito do mouse no banco de dados **Clínica** e clique em **Nova Consulta**.
2. Cole o T-SQL (Transact-SQL) a seguir na janela de nova consulta e o **Execute**:


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Criptografar algumas colunas (configurar o Always Encrypted)

O SSMS fornece um assistente para configurar facilmente o Always Encrypted ao configurar a chave mestra da coluna (CMK), a chave de criptografia de coluna (CEK) e colunas criptografadas para você.

1. Expanda **Bancos de Dados** > **Clínica** > **Tabelas**.
2. Clique com o botão direito do mouse na tabela **Pacientes** e selecione **Criptografar Colunas...** para abrir o Assistente Sempre Criptografado:

    ![criptografar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

3. **Seleção de coluna**

    Clique em **Avançar** na página **Introdução** para abrir a página **Seleção de Coluna**, em que você seleciona quais colunas deseja criptografar, [o tipo de criptografia e qual CEK (chave de criptografia de coluna)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) será usada.

    Para cada paciente, queremos criptografar suas informações de **SSN** e **BirthDate**. A coluna SSN usará criptografia determinística, que oferece suporte a pesquisas de igualdade, junções e agrupar por. A coluna BirthDate usará criptografia aleatória, que não oferece suporte a operações.

    Selecione e defina o **Tipo de Criptografia** para a coluna SSN como **Determinístico** e a coluna BirthDate como **Aleatório**; em seguida, clique em **Avançar**.

    ![criptografar colunas](./media/sql-database-always-encrypted/column-selection.png)

4. **CMK** (Configuração da Chave Mestra)

    Na página **Configuração da Chave Mestra**, é possível configurar a CMK (chave mestra da coluna) e selecionar o provedor do repositório de chaves em que a CMK será armazenada. No momento, pode-se armazenar uma CMK no repositório de certificados do Windows, no Azure Key Vault ou em um módulo de segurança de hardware (HSM). Este tutorial mostra como armazenar suas chaves no repositório de certificados do Windows.

    Verifique se **Repositório de certificados do Windows** está selecionado e clique em **Avançar**.

    ![configuração de chave mestra](./media/sql-database-always-encrypted/master-key-configuration.png)


5. **Validação**

    Você pode criptografar as colunas agora ou salvar um script do PowerShell para execução posterior. Para este tutorial, selecione **Seguir para a conclusão agora** e clique em **Avançar**.

6. **Resumo**

    Verifique se as configurações estão corretas e clique em **Concluir** para concluir a configuração do Sempre Criptografado.


    ![resumo](./media/sql-database-always-encrypted/summary.png)


### O que exatamente o assistente acabou de fazer?

Após a conclusão do assistente, seu banco de dados está configurado para o Always Encrypted e as tarefas a seguir são concluídas:

- Criação de uma Chave Mestra da Coluna (CMK).
- Criação de uma Chave de Criptografia de Coluna (CEK).
- Configuração das colunas selecionadas para criptografia. (Atualmente, nossa tabela Pacientes ainda não tem dados, mas todos os dados existentes nas colunas selecionadas estariam criptografados agora.)

É possível verificar a criação das chaves no SSMS expandindo **Clínica** > **Segurança** > **Chaves Sempre Criptografadas**. Agora, é possível ver as chaves novas que o assistente gerou para você.


## Criar um aplicativo cliente que funcione com os dados criptografados

Agora que o Always Encrypted está configurado, vamos compilar um aplicativo que faça algumas INSERÇÕES e SELEÇÕES nas colunas criptografadas. Para executar com sucesso o aplicativo de exemplo, você deve executá-lo no mesmo computador em que executou o assistente do Always Encrypted. Para executar em outro computador, é preciso implantar os certificados do Always Encrypted no computador que executa o aplicativo cliente.

> [AZURE.IMPORTANT] Seu aplicativo deve usar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto não criptografado para o servidor com colunas do Sempre Criptografado. A passagem de valores literais sem usar objetos SqlParameter resultará em uma exceção.


1. Abra o Visual Studio e crie um novo aplicativo de console em C#. Verifique se seu projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto como **AlwaysEncryptedConsoleApp** e clique em **OK**.


	![novo aplicativo de console](./media/sql-database-always-encrypted/console-app.png)



## Modificar a cadeia de conexão para habilitar o Always Encrypted

Esta seção simplesmente explica como habilitar o Always Encrypted na sua cadeia de conexão de banco de dados. É na próxima seção **Aplicativo de console de exemplo do Sempre Criptografado** que, na verdade, você modificará o aplicativo de console que acabou de criar.


Para habilitar o Sempre Criptografado, você precisa adicionar a palavra-chave **Column Encryption Setting** na cadeia de conexão e defini-la como **Enabled**.

Isso pode ser definido diretamente na cadeia de conexão ou usando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Essa é a única alteração necessária em um aplicativo cliente específico para o Always Encrypted. Se você tiver um aplicativo existente que armazene sua cadeia de conexão externamente (ou seja, em um arquivo de configuração), é possível habilitar o Always Encrypted sem alterar nenhum código.


### Habilitar o Always Encrypted na cadeia de conexão

Adicione a palavra-chave a seguir na sua cadeia de conexão:

    Column Encryption Setting=Enabled


### Habilitar o Always Encrypted com um SqlConnectionStringBuilder

O código a seguir mostra como habilitar o Sempre Criptografado configurando o [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Aplicativo de console de exemplo do Always Encrypted

Este exemplo demonstra como:

- Modificar a cadeia de conexão para habilitar o Always Encrypted.
- Inserir dados nas colunas criptografadas.
- Selecionar um registro por filtragem para um valor específico em uma coluna criptografada.

Substitua os conteúdos do **Program.cs** pelo seguinte. Substituir a cadeia de conexão pela variável global connectionString na linha diretamente acima do método Principal com a cadeia de conexão válida do Portal do Azure. Essa é a única alteração que você precisa fazer no código.

Agora execute o aplicativo para ver o Always Encrypted em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    
    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] \([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## Verificar se os dados foram criptografados

Para uma verificação rápida de que os dados reais no servidor foram criptografados, podemos consultar facilmente os dados dos pacientes com o SSMS (usando nossa conexão atual em que a Configuração de Criptografia de Coluna ainda não está habilitada).

Execute a consulta a seguir no banco de dados Clínica:

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode-se ver que as colunas criptografadas não contêm nenhum dado de texto sem formatação.

   ![novo aplicativo de console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Para usar o SSMS para acessar os dados de texto não criptografado, podemos adicionar o parâmetro **Column Encryption Setting=enabled** à conexão.

1. No SSMS, clique com o botão direito do mouse em seu servidor no **Pesquisador de Objetos** e em **Desconectar**.
2. Clique em **Conectar** > **Mecanismo de Banco de Dados** para abrir a janela **Conectar ao Servidor** e clique em **Opções**.
3. Clique em **Parâmetros de Conexão Adicionais** e digite **Column Encryption Setting=enabled**.

	![novo aplicativo de console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Execute a consulta a seguir no banco de dados Clínica:

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora você pode ver os dados de texto sem formatação em colunas criptografadas.


	![novo aplicativo de console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Se você se conectar com o SSMS (ou qualquer cliente) em um computador diferente, ele não terá acesso às chaves de criptografia e não será capaz de descriptografar os dados.



## Próximas etapas
Depois de criar um banco de dados que usa o Always Encrypted, convém fazer o seguinte:

- Executar esse exemplo de um computador diferente. Ele não terá acesso às chaves de criptografia e, portanto, não terá acesso aos dados de texto sem formatação e não será executado com êxito.
- [Girar e limpar suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já foram criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx)
- Implantar certificados do Always Encrypted em outros computadores clientes.


## Informações relacionadas

- [Always Encrypted (Desenvolvimento Cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx)
- [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistente do Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog do Always Encrypted](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0720_2016-->
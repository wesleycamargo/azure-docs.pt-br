<properties
	pageTitle="Proteger dados confidenciais no Banco de Dados SQL com a criptografia do banco de dados | Microsoft Azure"
	description="Proteja dados confidenciais no banco de dados SQL em minutos."
	keywords="banco de dados sql, criptografia sql, criptografia do banco de dados, chave de criptografia, dados confidenciais, Always Encrypted"	
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
	ms.date="02/29/2016"
	ms.author="sstein"/>

# Proteger dados confidenciais no Banco de Dados SQL com a criptografia do banco de dados e armazenar suas chaves de criptografia no Cofre da Chave do Azure

> [AZURE.SELECTOR]
- [Cofre da Chave do Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Armazenamento de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra como proteger os dados confidenciais no banco de dados SQL com a criptografia do banco de dados usando o [Assistente Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) e armazenar suas chaves de criptografia no Cofre da Chave do Azure.

Always Encrypted é uma nova tecnologia de criptografia no Banco de Dados SQL do Azure e no SQL Server que protege dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, assim como enquanto os dados estão em uso, garantindo que os dados confidenciais nunca apareçam como texto sem formatação dentro do sistema de banco de dados. Somente aplicativos clientes ou servidores de aplicativo, que têm acesso às chaves, podem acessar dados de texto sem formatação. Para obter informações detalhadas, consulte [Always Encrypted (Mecanismo do Banco de Dados)](https://msdn.microsoft.com/library/mt163865.aspx).


Depois de configurar o banco de dados para usar o Always Encripted, criaremos um aplicativo cliente em C# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo e saiba como configurar o Always Encripted para um banco de dados SQL do Azure. Neste artigo, você aprenderá como realizar as seguintes tarefas:

- Usar o assistente Always Encrypted no SSMS para criar [Chaves Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - Crie uma [CMK (Chave Mestra da Coluna)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [CEK (Chave de Criptografia da Coluna)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de banco de dados e criptografar algumas colunas.
- Crie um aplicativo que insira, selecione e exiba os dados das colunas criptografadas.

> [AZURE.NOTE] O Always Encrypted para o Banco de Dados SQL está atualmente em visualização.


## Pré-requisitos

Para este tutorial, será necessário:

- Uma conta do Azure e uma assinatura antes de começar. Se não tiver uma, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](../powershell-install-configure.md), versão mínima 1.0.
    - Digite **(Get-Module azure -ListAvailable).Version** para ver qual versão do PowerShell você está executando.



## Habilitar seu aplicativo cliente para acessar o serviço do Banco de Dados SQL

Você deve primeiro habilitar o aplicativo cliente para acessar o serviço do Banco de Dados SQL configurando a autenticação requerida e adquirindo a *ClientId* e o *Segredo* necessários para autenticar seu aplicativo no código a seguir.

1. Abra o [Portal clássico](http://manage.windowsazure.com).
2. Escolha **Active Directory** no menu à esquerda e clique no Active Directory que seu aplicativo usará.
3. Clique em **Aplicativos** e, em seguida, clique em **ADICIONAR** (na parte inferior).
4. Digite um nome para seu aplicativo (por exemplo: *myClientApp*), escolha **APLICATIVO WEB** e clique na seta para continuar.
5. Para a URL de LOGON e o URI da ID do APLICATIVO, basta digitar uma url válida (por exemplo: **http://myClientApp*) e continuar.
6. Clique em **CONFIGURAR**.
7. Copie a **ID do CLIENTE** (posteriormente você precisará desse valor no código).
8. Na seção de chaves, defina a lista suspensa **Selecionar duração** para **1 ano** (copiaremos a chave após salvarmos abaixo).
11. Role para baixo e clique em **Adicionar aplicativo**.
12. Deixe **MOSTRAR** definido como **Aplicativos da Microsoft** e, em seguida, localize e escolha **Gerenciamento de Serviços do Windows Azure** e clique na marca de seleção para continuar.
13. Na linha **Gerenciamento de Serviços do Windows Azure...**, clique na lista suspensa **Permissões Delegadas** e selecione **Acessar Gerenciamento de Serviços do Azure**.
14. Clique em **SALVAR** (na parte inferior).
15. Após salvar, localize e copie o valor da chave na seção **chaves** (posteriormente você precisará desse valor no código). 



## Criar um Cofre da Chave do Azure para armazenar as chaves

Agora que seu aplicativo cliente está configurado e você tem a id do cliente, é hora de criar um Cofre da Chave do Azure e configurar a política de acesso para permitir que você e seu aplicativo acessem os segredos do cofre (as chaves Always Encrypted). Para usar as chaves com o Cofre da Chave do Azure, as permissões *criar*, *obter*, *listar*, *assinar*, *verificar*, *wrapKey* e *unwrapKey* são necessárias para criar uma nova chave mestra de coluna e configurar a criptografia com o SQL Server Management Studio.

Para criar rapidamente um Cofre da Chave do Azure, você pode executar o script a seguir. Para obter uma explicação detalhada sobre esses cmdlets e obter mais informações sobre como criar e configurar um Cofre da Chave do Azure, consulte [Introdução ao Cofre da Chave do Azure](../Key-Vault/key-vault-get-started.md)



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'
    

    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## Criar um banco de dados SQL em branco
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Novo** > **Dados + Armazenamento** > **Banco de Dados SQL**.
3. Crie um banco de dados **Em branco** denominado **Clínica** em um servidor novo ou existente. Para obter instruções detalhadas sobre como criar um banco de dados no portal do Azure, consulte [Criar um banco de dados SQL em minutos](sql-database-get-started.md).

	![criar um banco de dados em branco](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Você precisará da cadeia de conexão mais tarde no tutorial. Portanto, após criar o banco de dados, vá para o novo banco de dados Clínica e copie a cadeia de conexão (pode-se obter a cadeia de conexão a qualquer momento, mas, como já estamos no portal, é mais fácil copiá-la agora).

1. Clique em **Bancos de dados SQL** > **Clínica** > **Mostrar cadeias de conexão do banco de dados**.
2. Copie a cadeia de conexão para **ADO.NET**:

	![copiar a cadeia de conexão](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## Conectar-se ao banco de dados com o SSMS

Abra o SSMS e conecte-se ao servidor com o banco de dados Clínica.


1. Abra o SSMS (clique em **Conectar** > **Mecanismo do banco de dados...** para abrir a janela **Conectar ao Servidor**, caso não esteja aberta).
2. Insira o nome do servidor e credenciais. O nome do servidor pode ser encontrado na folha do banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome completo do servidor, incluindo *database.windows.net*.

	![copiar a cadeia de conexão](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

3. Se a janela **Nova Regra de Firewall** for aberta, entre no Azure e deixe o SSMS criar uma nova regra de firewall para você.


## Criar uma tabela

Primeiramente, criaremos uma tabela para armazenar os dados dos pacientes (não criptografada inicialmente – iremos configurar a criptografia na próxima seção).

1. Expanda os **Bancos de Dados**.
1. Clique com o botão direito no banco de dados **Clínica** e clique em **Nova Consulta**.
2. Cole o Transact-SQL (T-SQL) a seguir na nova janela de consulta e **Execute**:


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
2. Clique com o botão direito na tabela **Pacientes** e selecione **Criptografar Colunas...** para abrir o Assistente Always Encrypted:

    ![criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

3. **Seleção de coluna**

    Clique em **Avançar** na página **Introdução** para abrir a página **Seleção da Coluna**, onde você seleciona quais colunas deseja criptografar, [o tipo de criptografia e qual chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

    Para cada paciente, queremos criptografar as informações **SSN** e **BirthDate**. A coluna SSN usará criptografia determinística, que oferece suporte a pesquisas de igualdade, junções e agrupar por. A coluna BirthDate usará criptografia aleatória, que não oferece suporte a operações.

    Selecione e defina o **Tipo de Criptografia** da coluna SSN para **Determinístico** e a coluna BirthDate para **Aleatória**, em seguida, clique em **Avançar**.

    ![criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

4. **Configuração da Chave Mestra** (CMK)

    Na página **Configuração da Chave Mestra**, é onde você configura sua chave mestra de coluna (CMK) e seleciona o provedor de armazenamento de chaves no qual a CMK será armazenada. No momento, pode-se armazenar uma CMK no repositório de certificados do Windows, no Azure Key Vault ou em um módulo de segurança de hardware (HSM).

    Este tutorial mostra como armazenar suas chaves em um Cofre da Chave do Azure.

    1.     Selecione **Cofre da Chave do Azure**.
    1.     Selecione o cofre da chave desejado no menu suspenso.
    1.     Clique em **Próximo**.

    ![configuração de chave mestra](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


5. **Validação**

    Você pode criptografar as colunas agora ou salvar um script do PowerShell para execução posterior. Para este tutorial, selecione **Prosseguir para a conclusão agora** e clique em **Avançar**.

6. **Resumo**

    Verifique se as configurações estão corretas e clique em **Concluir** para terminar a configuração para Always Encrypted.


    ![resumo](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### O que exatamente o assistente acabou de fazer?

Após a conclusão do assistente, seu banco de dados está configurado para o Always Encrypted e as tarefas a seguir são concluídas:

- Criação de uma Chave Mestra de Coluna (CMK) e armazenamento no Cofre da Chave do Azure.
- Criação de uma Chave de Criptografia de Coluna (CEK) e armazenamento no Cofre da Chave do Azure.
- Configuração das colunas selecionadas para criptografia. (Atualmente, nossa tabela Pacientes ainda não tem dados, mas todos os dados existentes nas colunas selecionadas estariam criptografados agora.)

Você pode verificar a criação das chaves no SSMS expandindo **Clínica** > **Segurança** > **Chaves do Always Encrypted**. Agora, é possível ver as chaves novas que o assistente gerou para você.


## Criar um aplicativo cliente que funcione com os dados criptografados

Agora que o Always Encrypted está configurado, vamos compilar um aplicativo que faça algumas INSERÇÕES e SELEÇÕES nas colunas criptografadas.

> [AZURE.IMPORTANT] Seu aplicativo deve usar os objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar os dados de texto sem formatação para o servidor com as colunas do Always Encrypted. A passagem de valores literais sem usar objetos SqlParameter resultará em uma exceção.


1. Abra o Visual Studio e crie um novo aplicativo de console em C#. Verifique se seu projeto está definido para o **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto como **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.


	![novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)


3. Instale os seguintes pacotes NuGet clicando em **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

Execute estas duas linhas de código no Console do Gerenciador de Pacotes:
    
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory


   
## Modificar a cadeia de conexão para habilitar o Always Encrypted

Esta seção simplesmente explica como habilitar o Always Encrypted na sua cadeia de conexão de banco de dados. É na próxima seção do **Aplicativo de console de exemplo do Always Encrypted** que, na verdade, você modificará o aplicativo de console que acabou de criar.


Para habilitar o Always Encrypted, você precisa adicionar a palavra-chave **Column Encryption Setting** à cadeia de conexão e defini-la para **Enabled**.

Isso pode ser definido diretamente na cadeia de conexão ou usando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.



### Habilitar o Always Encrypted na cadeia de conexão

Adicione a palavra-chave a seguir na sua cadeia de conexão:

    Column Encryption Setting=Enabled


### Habilitar o Always Encrypted com um SqlConnectionStringBuilder

O código a seguir mostra como habilitar o Always Encrypted configurando o [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) para [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Aplicativo de console de exemplo do Always Encrypted

Este exemplo demonstra como:

- Modificar a cadeia de conexão para habilitar o Always Encrypted.
- Registre o Cofre da Chave do Azure como o provedor de armazenamento de chaves do aplicativo.  
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
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;
    
    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

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

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


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
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
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


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
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

   ![novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Para usar o SSMS para acessar os dados de texto sem formatação, podemos adicionar o parâmetro **Column Encryption Setting=enabled** à conexão.

1. No SSMS, clique com o botão direito no servidor em **Pesquisador de Objetos** e **Desconectar**.
2. Clique em **Conectar** > **Mecanismo do Banco de Dados** para abrir a janela **Conectar ao Servidor** e clique em **Opções**.
3. Clique em **Parâmetros Adicionais de Conexão** e digite **Column Encryption Setting=enabled**.

	![novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Execute a consulta a seguir no banco de dados Clínica:

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora você pode ver os dados de texto sem formatação em colunas criptografadas.


	![novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## Próximas etapas
Depois de criar um banco de dados que usa o Always Encrypted, convém fazer o seguinte:

- [Girar e limpar suas Chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já foram criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx)



## Informações relacionadas

- [Always Encrypted (Desenvolvimento Cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx)
- [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistente do Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog do Always Encrypted](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0302_2016-->
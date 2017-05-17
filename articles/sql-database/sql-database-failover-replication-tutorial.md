---
title: "Replicação e failover da sua solução de Banco de Dados SQL do azure | Microsoft Docs"
description: Saiba como configurar o Banco de Dados SQL do Azure e o aplicativo para o failover para um banco de dados replicado e failover de teste.
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>Replicação e failover da sua solução de Banco de Dados SQL do Azure

Neste tutorial, você irá configurar um banco de dados SQL do Azure e o aplicativo para o failover para uma região remota e, em seguida, testar o seu plano de failover. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Para concluir este tutorial, verifique se você tem:

- A versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Instalar o SSMS também instala a versão mais recente do SQLPackage, um utilitário de linha de comando que pode ser usado para automatizar uma variedade de tarefas de desenvolvimento de banco de dados. 
- Um banco de dados do Azure para migração. Este tutorial usa o banco de dados de exemplo AdventureWorksLT com um nome de * * mySampleDatabase a partir de um desses inícios rápidos:

   - [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
   - [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Criar usuários do Azure Active Directory (opcional)

Nesta etapa, você cria ou identifica os usuários do Azure Active Directory para adicionar como usuários ao seu servidor lógico do Banco de Dados SQL do Azure e ao banco de dados de exemplo.
- Se sua assinatura for parte de um ambiente corporativo do Azure Active Directory com contas de usuário existentes, identifique 3 contas de usuário para usar como o usuário administrativo do Active Directory, o usuário administrativo do aplicativo e o usuário do aplicativo para este tutorial e continue na Etapa 3: Criar usuários e logons do Banco de Dados SQL. 
- Se sua assinatura não for parte de um ambiente corporativo do Azure Active Directory ou fizer parte de um ambiente corporativo do Azure Active Directory sem nenhuma conta de usuário existente (e você tem permissão para criar novas contas de usuário do Azure Active Directory).

1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Clique em **Mais serviços** no menu esquerdo.
3. Na caixa de texto de filtro, digite **Azure** e, em seguida, selecione **Azure Active Directory**.
4. No painel **Tarefas rápidas** na página do **Azure Active Directory**, clique em **Adicionar um usuário**.
5. No formulário do **Usuário**, crie o seguinte usuário.
   - Nome: **ad-admin**
   - Nome de usuário: **ad-admin@yourdomain** (Yopu4708)
6. Selecione a caixa de texto **Mostrar senha** e anote a senha para esta conta de usuário para uso posterior.
7. Clique em **Criar**.
8. Repita as 3 etapas anteriores para criar os 2 novos usuários a seguir.
   - Nome: **app-admin**
   - Nome de usuário: **app-admin@yourdomain** (Buju4319)
   - Nome: **app-user**
   - Nome de usuário: **app-user@yourdomain** (Nonu4001).

9. Abra uma nova janela do navegador e faça logon no portal do Azure usando a conta **ad-admin** recém-criada.
10. Na página **Atualizar sua senha**, insira a senha gerada pelo sistema na caixa **Senha atual**. 
11. Nas caixas **Nova senha** e **Confirmar senha** , insira uma senha de sua escolha.
12. Clique em **Atualizar senha e entrar**.

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Configurar a integração de Banco de Dados SQL com o Azure Active Directory

1. Clique em **Mais serviços** no menu esquerdo, digite **sql** na caixa de texto de filtro e selecione **Servidores SQL**.
2. Na página **Servidores SQL** , clique no seu servidor de Banco de Dados SQL.
3. No painel do Essentials da página **Visão geral** para o seu servidor, clique em **Não configurado** em **Administrador do Active Directory**.
4. Na página **Administrador do Active Directory**, clique em **Definir administrador**.
5. Selecione a conta **ad-admin** do Azure Active Directory (ou outra conta já existente, como sua própria conta) para ser o administrador do servidor para o servidor do Banco de Dados SQL.
6. Clique em **Selecionar**.
7. Clique em **Salvar**.

## <a name="create-users-with-permissions-for-your-database"></a>Criar usuários com permissões para o seu banco de dados

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

Use o SQL Server Management Studio para conectar-se ao banco de dados e criar contas de usuário. Essas contas de usuário serão replicadas automaticamente para o servidor secundário. Talvez seja necessário configurar uma regra de firewall, se você estiver se conectando a partir de um cliente em um endereço IP para o qual você ainda não tiver configurou um firewall. Para ver as etapas, consulte [Criar um Banco de Dados SQL usando o Portal do Azure](sql-database-get-started-portal.md).

1. Abra o SQL Server Management Studio.
2. Alterar o modo de **Autenticação** para **Autenticação de senha do Active Directory**.
3. Conecte-se ao seu servidor usando a conta do administrador de servidor do Azure Active Directory recém-criada. 
4. No Pesquisador de Objetos, expanda **Bancos de dados do sistema**, clique com o botão direito do mouse em **mySampleDatabase** e clique em **Nova Consulta**.
5. Na janela de consulta, execute a consulta a seguir para criar contas de um usuário no seu banco de dados, concedendo permissões de **db_owner** para as duas contas administrativas. Substitua o espaço reservado para o nome de domínio pelo seu domínio.

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>Criar firewall no nível de banco de dados

Use o SQL Server Management Studio para criar uma regra de firewall no nível do banco de dados para o seu banco de dados. Essa regra de firewall no nível do banco de dados serão replicadas automaticamente para o servidor secundário. Para fins de teste, você pode criar uma regra de firewall para todos os endereços IP (0.0.0.0 e 255.255.255.255), pode criar uma regra de firewall para o endereço IP único com o qual você criou a regra de firewall de servidor ou pode configurar uma ou mais regras de firewall para os endereços IP dos computadores que você deseja usar para testar este tutorial.  

- Na janela de consulta aberta, substitua a consulta anterior pela consulta seguinte, substituindo os endereços IP pelos endereços IP apropriados para o seu ambiente. 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>Criar um grupo de failover 

Escolha uma região de failover, crie um servidor vazio na região e, em seguida, crie um grupo de failover entre o seu servidor existente e o novo servidor vazio.

1. Preencha as variáveis.

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. Criar um servidor de backup vazio na sua região de failover.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. Criar um grupo de failover.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. Adicionar o banco de dados ao grupo de failover

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>Adicionar servidor de backup vazio ao domínio

1. No portal do Azure, clique em **Mais serviços** no menu esquerdo, digite **sql** na caixa de texto de filtro e selecione **Servidores SQL**.
2. Na página **Servidores SQL**, clique no seu novo servidor de recuperação de desastres do Banco de Dados SQL.
3. No painel do Essentials da página **Visão geral** para o seu servidor, clique em **Não configurado** em **Administrador do Active Directory**.
4. Na página **Administrador do Active Directory**, clique em **Definir administrador**.
5. Selecione a conta **ad-admin** do Azure Active Directory (ou outra conta já existente, como sua própria conta) para ser o administrador do servidor para o novo servidor de recuperação de desastres do Banco de Dados SQL.
6. Clique em **Selecionar**.
7. Clique em **Salvar**.

## <a name="prepare-client-tier"></a>Preparar a camada do cliente

1. Criar perfil de TM com perfil de failover AWProfile.
2. Configurar monitoramento

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Implantar um aplicativo Java e conectar-se ao banco de dados

<In progress> Consulte [Conectar-se com Java](sql-database-connect-query-java.md).

<TO DO: change user to app-user>

1. Instale o java 8.
2. Instale o maven.
3. Crie um projeto maven.
4. Adicione o seguinte ao pom.xml 

   - Dependência

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - Nível de linguagem

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - Opção de build para suporte a arquivos de manifesto no jars

      ```java
      <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
      </build>
      ```
5. Adicione o seguinte ao arquivo App.java:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Salve o arquivo.

## <a name="compile-and-run"></a>Compilar e executar

1. Acesse o console e execute

   ```java
   mvn package
   ```
2. Quando terminar, execute para executar o aplicativo (ele será executado para cerca de 1 hora, a menos que seja parado manualmente):

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   A saída será parecida com esta, se for executado com êxito:

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>TUTORIAL DO BANCO DE DADOS DISTRIBUÍDO GEOGRAFICAMENTE ##
   #######################################

   1. inserir no banco de dados primário bem-sucedido, ler no secundário bem-sucedido
   2. inserir no banco de dados primário bem-sucedido, ler no secundário bem-sucedido
   3. inserir no banco de dados primário bem-sucedido, ler no secundário bem-sucedido

## <a name="perform-dr-drill"></a>Executar análise de recuperação de desastres

1. Chame o failover manual de FG usando failover forçado. Se a perda de dados durante a análise for inaceitável, remova -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    Desabilite o ponto de extremidade do banco de dados primário no perfil de TM (para acionar o failover do ponto de extremidade) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Execute o aplicativo novamente.


## <a name="relocate-application-to-primary-region"></a>Faça a realocação do aplicativo para a região primária

1.    Chame o failover manual amigável de FG. Não especifique -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    Desabilite o ponto de extremidade do banco de dados secundário (webapp2ep) no perfil de TM (para acionar o failover do ponto de extremidade) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Execute o aplicativo novamente.

## <a name="troubleshoot-failover"></a>Solucionar problemas de failover 

Descubra qual região agora é a primária para garantir que o failover ocorreu. A função mostraria se é primária ou secundária.

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>Próximas etapas 

- em breve em um cinema perto de você

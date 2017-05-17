---
title: "Implementar o aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implementar o aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: AyoOlubeko
manager: jhubbard
editor: monicar
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/08/2017
ms.author: AyoOlubek
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 80df7b504d13fe1b3be9806eb95e3980d7790970
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="implement-a-multi-tenant-saas-application-using-azure-sql-database"></a>Implementar um aplicativos SaaS multilocatário usando o Banco de Dados SQL do Azure

Um aplicativo multilocatário é um aplicativo hospedado em um ambiente de nuvem e fornece o mesmo conjunto de serviços para centenas ou milhares de locatários que não compartilham ou veem os dados uns dos outros. Um exemplo é um aplicativo SaaS que fornece serviços para locatários em um ambiente hospedado em nuvem. Esse modelo isola os dados de cada locatário e otimiza a distribuição dos recursos, reduzindo os custos. 

Este tutorial demonstra como criar um aplicativo SaaS multilocatário usando o Banco de Dados SQL do Azure.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Configurar um ambiente de banco de dados para dar suporte a um aplicativo SaaS multilocatário, usando o padrão de banco de dados por locatário
> * Criar um catálogo de locatários
> * Provisionar um banco de dados de locatário e registrá-lo no catálogo de locatários
> * Configurar um aplicativo Java de exemplo 
> * Acessar um bancos de dados de locatários simples com um aplicativo de console Java
> * Excluir um locatário

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para concluir este tutorial, verifique se você tem:
* O PowerShell instalado em seu computador, e o [SDK mais recente do Azure PowerShell](http://azure.microsoft.com/downloads/)

* A versão mais recente do [SQL Server Management Studio](http://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Instalar o SQL Server Management Studio também instala a versão mais recente do SQLPackage, um utilitário de linha de comando que pode ser usado para automatizar diversas tarefas de desenvolvimento de banco de dados.

* [JRE 8 (Java Runtime Environment)](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) e o [JDK (JAVA Development Kit) mais recente](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalados em seu computador. 

* [Apache Maven](https://maven.apache.org/download.cgi) instalado em seu computador. O Maven será usado para ajudar a gerenciar as dependências, compilar, testar e executar seu projeto Java de exemplo

## <a name="set-up-data-environment"></a>Configurar o ambiente de dados

Você provisionará um banco de dados por locatário. O modelo de banco de dados por locatário proporciona o mais alto grau de isolamento entre locatários, pouco custo de DevOps. Para otimizar o custo dos recursos de nuvem, você também provisionará os bancos de dados do locatário em um pool elástico, que permite a otimização do desempenho de preço de um grupo de bancos de dados. Para saber mais sobre outro modelos de provisionamento de banco de dados [clique aqui](sql-database-design-patterns-multi-tenancy-saas-applications.md#multitenant-data-models). 

Execute estas etapas para criar um SQL Server e um pool elástico que hospedará todos os seus bancos de dados de locatário. 

1. Crie variáveis para armazenar valores que serão usados no restante do tutorial. Modifique a variável de endereço IP para incluir seu endereço IP 
   
   ```PowerShell 
   # Set an admin login and password for your database
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   
   # Create random unique names for logical server and tenants
   $servername = "server-$(Get-Random)"
   $tenant1 = "geolamice"
   $tenant2 = "ranplex"
   
   # Store current client IP address (modify to include your IP address)
   $startIpAddress = 0.0.0.0 
   $endIpAddress = 0.0.0.1
   ```
   
2. Fazer logon no Azure e criar um SQL Server e pool elástico 
   
   ```PowerShell
   # Login to Azure 
   Login-AzureRmAccount
   
   # Create resource group 
   New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
   
   # Create logical SQL Server with firewall rules 
   New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
       -ServerName $servername `
       -Location "northcentralus" `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   
   New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
       -ServerName $servername `
       -FirewallRuleName "singleAddress" -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
   
   # Create elastic pool 
   New-AzureRmSqlElasticPool -ResourceGroupName "myResourceGroup"
       -ServerName $servername `
       -ElasticPoolName "myElasticPool" `
       -Edition "Standard" `
       -Dtu 50 `
       -DatabaseDtuMin 10 `
       -DatabaseDtuMax 20
   ```
   
## <a name="create-tenant-catalog"></a>Criar um catálogo de locatários 

Em um aplicativo SaaS multilocatário, é importante saber onde estão armazenadas as informações de um locatário. Normalmente, elas ficam armazenadas em um banco de dados de catálogo. O banco de dados de catálogo é usado como um mapeamento entre um locatário e um banco de dados no qual os dados do locatário estão armazenados.  O padrão básico se aplica se um banco de dados multilocatário ou um banco de dados de locatário único for usado.

Execute estas etapas para criar um banco de dados de catálogo para o aplicativo SaaS de exemplo.

```PowerShell
# Create empty database in pool
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "tenantCatalog" `
    -ElasticPoolName "myElasticPool"

# Create table to track mapping between tenants and their databases
$commandText = "
CREATE TABLE Tenants
(
   TenantId         INT IDENTITY PRIMARY KEY,
   TenantName       NVARCHAR(128) NOT NULL,
   TenantDatabase   NVARCHAR(128) NOT NULL
);

CREATE INDEX IX_TenantName ON Tenants (TenantName);"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant1-and-register-in-tenant-catalog"></a>Provisione o banco de dados para o 'locatário1' e registre no catálogo de locatários 
Use o Powershell para provisionar um banco de dados para um novo locatário, 'locatário1', e registre esse locatário no catálogo. 

```PowerShell
# Create empty database in pool for 'tenant1'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant1');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant1 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register 'tenant1' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant1', '$tenant1');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant2-and-register-in-tenant-catalog"></a>Provisione o banco de dados para o 'locatário2' e registre no catálogo de locatários
Use o Powershell para provisionar um banco de dados para um novo locatário, 'locatário2', e registre esse locatário no catálogo. 

```PowerShell
# Create empty database in pool for 'tenant2'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant2 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant2');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant2 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register tenant 'tenant2' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant2', '$tenant2');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="set-up-sample-java-application"></a>Configurar um aplicativo Java de exemplo 

1. Criar um projeto do Maven. Digite o seguinte em uma janela de prompt de comando:
   
   ```
   mvn archetype:generate -DgroupId=com.microsoft.sqlserver -DartifactId=mssql-jdbc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```
   
2. Adicione esta dependência, o nível da linguagem e a opção de compilação para oferecer suporte a arquivos de manifesto em jars para o arquivo pom.xml:
   
   ```XML
   <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
   </dependency>
   
   <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   
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

3. Adicione o seguinte ao arquivo App.java:

   ```java 
   package com.sqldbsamples;
   
   import java.util.Map;
   
   import java.util.HashMap;
   
   import java.io.BufferedReader;
   
   import java.io.InputStreamReader;
   
   import java.sql.Connection;
   
   import java.sql.Statement;
   
   import java.sql.PreparedStatement;
   
   import java.sql.ResultSet;
   
   import java.sql.DriverManager;
   
   public class App {
   
   private static final String SERVER_NAME = "your-server-name";
   
   private static final String CATALOG_DB_NAME = "tenantCatalog";
   
   private static final String USER = "ServerAdmin";
   
   private static final String PASSWORD = "ChangeYourAdminPassword1";
   
   private static final String CATALOG_DB_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, CATALOG_DB_NAME, USER, PASSWORD);
   
   private static final String CMD_LIST = "LIST";

   private static final String CMD_QUERY = "QUERY";

   private static final String CMD_QUIT = "QUIT";
   
   public static void main(String[] args) {
   
   System.out.println("\n############################");
   
   System.out.println("## SAAS DATABASE TUTORIAL ##");
   
   System.out.println("############################\n");
   
   System.out.println("OPTIONS");
   
   System.out.println(" " + CMD_LIST + " - list tenants");
   
   System.out.println(" " + CMD_QUERY + " <NAME> - connect and tenant query tenant <NAME>");
   
   System.out.println(" " + CMD_QUIT + " - quit the application\n");
   
   try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
   
   while(true) {
   
   String[] input = br.readLine().split("\\s");
   
   if (null != input && input.length > 0) {
   
   if (input[0].equalsIgnoreCase(CMD_LIST)) {
   
   listTenants();
   
   } else if (input[0].toLowerCase().startsWith(CMD_QUERY.toLowerCase()) && input.length == 2) {
   
   queryTenant(input[1].trim());
   
   } else if (input[0].equalsIgnoreCase(CMD_QUIT)) {
   
   break;
   
   } else {
   
   System.out.println(" -> Command not supported");
   
   }
   
   }
   
   System.out.println("");
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void listTenants() {
   
   // List all tenants that currently exist in the system
   
   String sql = "SELECT TenantName FROM Tenants";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   Statement stmt = connection.createStatement();
   
   ResultSet resultSet = stmt.executeQuery(sql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void queryTenant(String name) {
   
   // Query the data that was previously inserted into the primary database from the geo replicated database
   
   String url = null;
   
   String sql = "SELECT TenantDatabase FROM Tenants WHERE TenantName = ?";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   PreparedStatement pstmt = connection.prepareStatement(sql)) {
   
   pstmt.setString(1, name);
   
   try (ResultSet resultSet = pstmt.executeQuery()) {
   
   if (resultSet.next()) {
   
   url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, resultSet.getString(1), USER, PASSWORD);
   
   }
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   if (null != url) {
   
   String tenantSql = "SELECT TenantName FROM WhoAmI";
   
   try (Connection connection = DriverManager.getConnection(url);
   
   Statement stmt = connection.createStatement();

   ResultSet resultSet = stmt.executeQuery(tenantSql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> Entry in table WhoAmI in tenant " + name + " is: " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   } else {
   
   System.out.println(" -> Tenant " + name + " not found");
   
   }
   
   }
   
   }
   ```

4. Salve o arquivo.

5. Acesse o console de comando e execute

   ```bash
   mvn package
   ```

6. Quando terminar, execute o seguinte para executar o aplicativo 
   
   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```
   
A saída será parecida com esta, se for executado com êxito:

```
############################

## SAAS DATABASE TUTORIAL ##

############################

OPTIONS

LIST - list tenants

QUERY <NAME> - connect and tenant query tenant <NAME>

QUIT - quit the application

* List the tenants

* Query tenants you created
```

## <a name="delete-first-tenant"></a>Excluir primeiro locatário 
Use o PowerShell para excluir a entrada no banco de dados e no catálogo de locatários do primeiro locatário.

```PowerShell
# Remove 'tenant1' from catalog 
$commandText = "DELETE FROM Tenants WHERE TenantName = '$tenant1';"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Delete database 
Remove-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1
```

Tente se conectar a 'locatário1' usando o aplicativo Java. Você receberá um erro informando que o locatário não existe.

## <a name="next-steps"></a>Próximas etapas 
Neste tutorial, você aprendeu a:
> [!div class="checklist"]
> * Configurar um ambiente de banco de dados para dar suporte a um aplicativo SaaS multilocatário, usando o padrão de banco de dados por locatário
> * Criar um catálogo de locatários
> * Provisionar um banco de dados de locatário e registrá-lo no catálogo de locatários
> * Configurar um aplicativo Java de exemplo 
> * Acessar um bancos de dados de locatários simples com um aplicativo de console Java
> * Excluir um locatário

* Amostras do PowerShell para tarefas comuns, consulte [Amostras do PowerShell para o Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-powershell-samples)

* Padrões de design para aplicativos SaaS multilocatário,confira [Padrões de design](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

* Amostras de Java para tarefas comuns do Azure, consulte [Centro de Desenvolvimento do Java](https://azure.microsoft.com/develop/java/)





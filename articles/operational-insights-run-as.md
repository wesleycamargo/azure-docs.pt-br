<properties 
   pageTitle="Contas Executar como do Operations Manager para Insights Operacionais"
   description="Saiba mais sobre como configurar as contas de executar como do Operations Manager que você usa com o Insights Operacionais"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Contas Executar como do Operations Manager para Insights Operacionais

O Insights Operacionais do Microsoft Azure usa o grupo de gerenciamento e o agente do Operations Manager para coletar e enviar dados para o serviço Insights Operacionais. O Insights Operacionais é criado com base nos pacotes de gerenciamento para cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de domínio. Você precisa fornecer informações de credenciais configurando uma conta Executar como do Operations Manager.

As seções a seguir descrevem como configurar contas de executar como do Operations Manager para as seguintes cargas de trabalho:

- Avaliação do SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

## Definir a conta Executar como para avaliação do SQL

 Se você já estiver usando o pacote de gerenciamento do SQL Server, deve usar essa conta Executar como.

### Para configurar a conta Executar como do SQL no console Operações

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar como**, clique em **Perfis** e abra **Perfil Executar como de Avaliação SQL do Insights Operacionais**.

3. Na página **Contas Executar Como** e clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contém as credenciais necessárias para o SQL Server, ou clique em **Novo** para criar uma.
	>[AZURE.NOTE] O tipo de conta Executar Como deve ser Windows. A conta Executar como também deve fazer parte do grupo de administradores locais em todos os servidores Windows que hospedam instâncias do SQL Server.

5. Clique em **Salvar**.

6. Modifique e, em seguida, execute o seguinte exemplo de T-SQL em cada instância do SQL Server para conceder as permissões mínimas necessárias para a conta Executar como para realizar uma avaliação de SQL. No entanto, você não precisa fazer isso se uma conta Executar como já fizer parte da função de servidor sysadmin em instâncias do SQL Server.

---
    -- Substitua <UserName> pelo nome de usuário real que está sendo usado como conta Executar como.
    Mestre USE
    
    -- Crie o logon do usuário, comente nessa linha se o logon já tiver sido criado.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Conceder permissões ao usuário.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Adicione o usuário do banco de dados para os bancos de dados na instância do SQL Server. Isso é necessário para se conectar a bancos de dados individuais.
    -- OBSERVAÇÃO: Este comando deve ser executado sempre que novos bancos de dados forem adicionados a instâncias do SQL Server.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Para configurar a conta Executar como do SQL usando o Windows PowerShell

Abra uma janela do PowerShell e execute o script a seguir depois de atualizá-lo com as suas informações:

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Definir a conta Executar como do Virtual Machine Manager

Certifique-se de que a conta Executar como tenha privilégios para as seguintes ações:

- Para usar o módulo VMM do Windows PowerShell

- Para consultar o banco de dados do VMM

- Para administrar remotamente os agentes do VMM em execução em hosts de virtualização

Use as etapas a seguir para definir a conta ao conectar o Insights Operacionais ao Operations Manager.

### Para definir credenciais para o VMM

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar Como**, clique em **Perfis** e abra **conta Executar como do VMM do Insights Operacionais**.

3. Na página **Contas Executar Como** e clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o VMM, ou clique em **Novo** para criar uma.
	>[AZURE.NOTE] O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.


## Definir a conta Executar como do Lync Server

 A conta Executar como precisa ser um membro dos grupos de administradores locais e de segurança do Lync RTCUniversalUserAdmins.

### Para definir credenciais para uma conta do Lync

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar como**, clique em **Perfis** e abra **conta Executar como do Lync do Insights Operacionais**.

3. Na página **Contas Executar Como** e clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que seja um membro dos grupos de administradores locais e de segurança do Lync RTCUniversalUserAdmins. 
	>[AZURE.NOTE] O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.


## Definir a conta Executar como para o SharePoint


### Para definir credenciais para uma conta do SharePoint

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar Como**, clique em **Perfis** e abra **conta Executar como do SharePoint do Insights Operacionais**.

3. Na página **Contas Executar Como** e clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o SharePoint, ou clique em **Novo** para criar uma. 
	>[AZURE.NOTE] O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.



<!--HONumber=52-->
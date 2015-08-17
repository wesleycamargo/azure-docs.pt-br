<properties
   pageTitle="Considerações sobre o Operations Manager com Insights Operacionais"
   description="Se você usar o Insights Operacionais do Microsoft Azure com o Operations Manager, sua configuração utiliza uma distribuição de agentes do Operations Manager e grupos de gerenciamento para coletar e enviar dados para o serviço de informações operacionais para análise"
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
   ms.date="07/02/2015"
   ms.author="banders" />

# Considerações sobre o Operations Manager com Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Se você usar o Insights Operacionais do Microsoft Azure com o Operations Manager, sua configuração se baseia em uma distribuição de grupos de gerenciamento e agentes do Operations para coletar e enviar dados para o serviço de Insights Operacionais para análise. No entanto, se você usar agentes que se conectam diretamente ao serviço Web, você não precisa do Operations Manager. Considere os seguintes problemas ao usar o Insights Operacionais com o Operations Manager.

Além disso, você precisará especificar as credenciais Executar Como para as cargas de trabalho monitoradas por meu Operations Manager para Insights Operacionais.

## Requisitos e funções do software Insights Operacionais

O Insights Operacionais consiste em um serviço Web na nuvem e os agentes que se conectam diretamente ao serviço Web ou os agentes do Operations Manager e grupos de gerenciamento do Operations Manager que estão gerenciando computadores no seu ambiente.

Antes de selecionar os agentes do Operations Manager (para coletar dados) e grupos de gerenciamento (para gerenciar agentes e enviar dados para o serviço de informações operacionais), certifique-se de compreender o seguinte:

- O agente do Operations Manager é instalado em qualquer servidor do qual você deseja coletar e analisar dados.

- O grupo de gerenciamento do Operations Manager transfere dados dos agentes para o serviço Web de Insights Operacionais. Ele não analisa nenhum dos dados.

- O grupo de gerenciamento do Operations Manager deve ter acesso à Internet para carregar dados para o serviço Web.

- Para obter melhores resultados, não instale o servidor de gerenciamento do Operations Manager em um computador que também seja um controlador de domínio.

- Um agente do Operations Manager deve ter conectividade de rede para o grupo de gerenciamento do Operations Manager para que possa transferir dados.

O Insights Operacionais pode usar o serviço de integridade do System Center para coletar e analisar dados. O Operations Manager depende do serviço de integridade do System Center. Ao exibir os programas instalados no seu servidor, você verá o software de agente do System Center Operations Manager, especialmente em Adicionar/Remover Programas. Não os remova, pois o Insights Operacionais depende deles. Se você remover o software do agente do Operations Manager, o Insights Operacionais deixará de funcionar.

## Coexistência com o Operations Manager

Ao usar o Operations Manager, o Insights Operacionais dá suporte apenas com o agente do Operations Manager no System Center Operations Manager 2012 R2 ou no System Center Operations Manager 2012 SP1. Não há suporte para as versões anteriores do System Center Operations Manager. Como o agente do Operations Manager é usado para coletar dados, ele usa credenciais específicas (contas de ação ou contas Executar como) para dar suporte a algumas das cargas de trabalho analisadas, como o SharePoint 2012.

## Insights Operacionais e SQL Server 2012

Ao usar o Operations Manager, o serviço de integridade do System Center é executado sob a conta do Sistema Local. Em versões do SQL Server anteriores ao SQL Server 2008 R2, a conta do Sistema Local era habilitada por padrão e era um membro da função de servidor do administrador do sistema. No SQL Server 2012, o logon de Sistema Local não fazia parte da função de servidor do administrador do sistema. Como resultado, quando você usa o Insights Operacionais, ele não pode monitorar a instância do SQL Server 2012 completamente e nem todas as regras podem gerar alertas.

## Conectividade de rede interna e da Internet

Ao usar agentes que se conectam diretamente com o serviço Web, eles precisam acessar a Internet para enviar dados para o serviço Web e receber informações de configuração atualizada do serviço Web.

Ao usar o Operations Manager, o servidor de gerenciamento precisa acessar a Internet para enviar dados para o serviço Web e receber informações de configuração atualizada do serviço Web. No entanto, os seus agentes não precisa acessar a Internet. Se você tiver agentes do Operations Manager em servidores que não estejam conectados à Internet, pode usar o serviço Web se eles puderem se comunicar com um servidor de gerenciamento conectado à Internet.

## Suporte a cluster

Há suporte para o agente do Operations Manager em computadores que executam o Windows Server 2012, o Windows Server 2008 R2 ou o Windows Server 2008 que está configurado para fazer parte de um cluster de failover do Windows. Você pode exibir os clusters no portal de Insights Operacionais. Na página servidores, os clusters são identificados como TYPE=CLUSTER (em vez de TYPE=AGENT, que é a maneira como computadores físicos são identificados).

As regras de descoberta e configuração são executadas nos nós ativos e passivos do cluster, mas nenhum alerta gerado em nós passivos é ignorado. Se um nó passar de passivo para ativo, os alertas para esse nó são exibidos automaticamente, sem nenhuma intervenção humana.

Alguns alertas podem ser gerados duas vezes, dependendo da regra que gerou o alerta. Por exemplo, uma regra que detecta um driver inadequado examinando o sistema operacional gera alertas para o servidor físico em cluster.

Não há suporte para a análise de configuração de nós passivos.

O Insights Operacionais não dá suporte para agrupar ou vincular computadores executando o Windows Server que fazem parte do mesmo cluster de failover.

## Colocação em escala do ambiente do Insights Operacionais

Ao planejar sua implantação do Insights Operacionais (especialmente ao analisar o número de agentes do Operations Manager que você deseja transferir dados através de um único grupo de gerenciamento), considere a capacidade do servidor em termos de espaço de arquivo.

Considere as seguintes variáveis:

- Número de agentes por grupo de gerenciamento

- O tamanho médio dos dados transferidos do agente para o grupo de gerenciamento por dia. Por padrão, cada agente carrega arquivos CAB para o grupo de gerenciamento duas vezes por dia. O tamanho dos arquivos CAB depende da configuração do servidor (como o número de mecanismos de SQL Server e bancos de dados de números) e a integridade do servidor (como o número de alertas gerados). Na maioria dos casos, o tamanho de carregamento diário normalmente é menor que 100 KB.

- Período de arquivamento para manter os dados no grupo de gerenciamento (o padrão é de 5 dias)

Por exemplo, se você presumir um tamanho de carregamento diário de 100 KB por agente e o período de arquivamento padrão, precisará do armazenamento a seguir para o grupo de gerenciamento:

Número de agentes|Espaço estimado necessário para o grupo de gerenciamento
---|---
5|\~2,5 MB (5 agentes x 100 KB dados/dia x 5 dias = 2.500 KB)
50|\~25 MB (50 agentes x 100 KB dados/dia x 5 dias = 25.000 KB)

## Contas Executar como do Operations Manager para Insights Operacionais

O Insights Operacionais usa o grupo de gerenciamento e o agente do Operations Manager para coletar e enviar dados para o serviço Insights Operacionais. O Insights Operacionais é criado com base nos pacotes de gerenciamento para cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de domínio. Você precisa fornecer informações de credenciais configurando uma conta Executar como do Operations Manager.

As seções a seguir descrevem como configurar contas de executar como do Operations Manager para as seguintes cargas de trabalho:

- Avaliação do SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

### Definir a conta Executar como para avaliação do SQL

 Se você já estiver usando o pacote de gerenciamento do SQL Server, deve usar essa conta Executar como.

#### Para configurar a conta Executar como do SQL no console Operações

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar como**, clique em **Perfis** e abra **Perfil Executar como de Avaliação SQL do Insights Operacionais**.

3. Na página **Contas Executar Como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contém as credenciais necessárias para o SQL Server, ou clique em **Novo** para criar uma.
	>[AZURE.NOTE]O tipo de conta Executar Como deve ser Windows. A conta Executar como também deve fazer parte do grupo de administradores locais em todos os servidores Windows que hospedam instâncias do SQL Server.

5. Clique em **Salvar**.

6. Modifique e, em seguida, execute o seguinte exemplo de T-SQL em cada instância do SQL Server para conceder as permissões mínimas necessárias para a conta Executar como para realizar uma avaliação de SQL. No entanto, você não precisa fazer isso se uma conta Executar como já fizer parte da função de servidor sysadmin em instâncias do SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### Definir a conta Executar como do Virtual Machine Manager

Certifique-se de que a conta Executar como tenha privilégios para as seguintes ações:

- Para usar o módulo VMM do Windows PowerShell

- Para consultar o banco de dados do VMM

- Para administrar remotamente os agentes do VMM em execução em hosts de virtualização

Use as etapas a seguir para definir a conta ao conectar o Insights Operacionais ao Operations Manager.

#### Para definir credenciais para o VMM

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar Como**, clique em **Perfis** e abra **Conta Executar como do VMM do Insights Operacionais**.

3. Na página **Contas Executar Como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o VMM, ou clique em **Novo** para criar uma.
	>[AZURE.NOTE]O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.


### Definir a conta Executar como do Lync Server

 A conta Executar como precisa ser um membro dos grupos de administradores locais e de segurança do Lync RTCUniversalUserAdmins.

#### Para definir credenciais para uma conta do Lync

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar como**, clique em **Perfis** e abra **Conta Executar como do Lync do Insights Operacionais**.

3. Na página **Contas Executar Como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que seja um membro dos grupos de administradores locais e de segurança do Lync RTCUniversalUserAdmins.
	>[AZURE.NOTE]O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.


### Definir a conta Executar como para o SharePoint


#### Para definir credenciais para uma conta do SharePoint

1. No Operations Manager, abra o console Operações e, em seguida, clique em **Administração**.

2. Em **Configuração de Executar Como**, clique em **Perfis** e abra **Conta Executar como do SharePoint do Insights Operacionais**.

3. Na página **Contas Executar Como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o SharePoint, ou clique em **Novo** para criar uma.
	>[AZURE.NOTE]O tipo de conta Executar Como deve ser Windows.

5. Clique em **Salvar**.



## Localizações geográficas

Se você deseja analisar os dados dos servidores em diferentes localizações geográficas, considere ter um grupo de gerenciamento por local. Isso pode melhorar o desempenho de transferência de dados do agente para o grupo de gerenciamento.

<!---HONumber=August15_HO6-->
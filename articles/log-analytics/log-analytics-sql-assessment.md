<properties
	pageTitle="Otimizar seu ambiente com a solução de Avaliação de SQL da Log Analytics | Microsoft Azure"
	description="Você pode usar a solução de Avaliação de SQL para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Otimizar seu ambiente com a solução de Avaliação de SQL da Log Analytics


Você pode usar a solução de Avaliação de SQL para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo ajudará você a instalar a solução para que você possa tomar ações corretivas para potenciais problemas.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em seis áreas de foco que ajudarão a entender o risco e tomar uma ação corretiva.

As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Após terminar de adicionar a solução e a avaliação ser concluída, as informações resumidas para áreas de foco são mostradas no painel **Avaliação de SQL** para a infraestrutura no seu ambiente. As seções a seguir descrevem como usar as informações no painel **Avaliação de SQL**, em que é possível exibir e executar as ações recomendadas para sua infraestrutura de SQL Server.

![imagem do bloco de Avaliação do SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![imagem do painel de avaliação do SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Instalando e configurando a solução
A Avaliação de SQL trabalha com todas as versões do SQL Server com suporte atualmente para as edições Standard, Developer e Enterprise.

Use as informações a seguir para instalar e configurar a solução.

- A solução de Avaliação do SQL requer o .NET Framework 4 instalado em cada computador que tem um agente do OMS.
- Ao usar o agente do Operations Manager com a Avaliação do SQL, você precisará usar uma conta Executar como do Operations Manager. Consulte [Contas Executar como do Operations Manager para OMS](#operations-manager-run-as-accounts-for-oms) abaixo para obter mais informações.

    >[AZURE.NOTE] O agente MMA não dá suporte a contas Executar como do Operations Manager.

- Adicionar a solução da Avaliação do SQL ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.

>[AZURE.NOTE] Depois de adicionar a solução, o arquivo AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e, em seguida, enviados para o serviço OMS na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.

## Detalhes de coleta de dados da Avaliação de SQL

A tabela a seguir mostra os métodos de coleta de dados dos agentes, se o SCOM (Operations Manager) é necessário e com que frequência os dados são coletados por um agente.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Não](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![Não](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 dias|

## Contas Executar como do Operations Manager para OMS

O Log Analytics no OMS usa o grupo de gerenciamento e o agente do Operations Manager para coletar e enviar dados para o serviço do OMS. O OMS é criado com base nos pacotes de gerenciamento para cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de domínio. Você precisa fornecer informações de credenciais configurando uma conta Executar como do Operations Manager.

Use as informações a seguir para definir a conta Executar como do Operations Manager para Avaliação de SQL.

### Definir a conta Executar como para avaliação do SQL

 Se você já estiver usando o pacote de gerenciamento do SQL Server, deve usar essa conta Executar como.

#### Para configurar a conta Executar como do SQL no console Operações

1. No Operations Manager, abra o console Operações e clique em **Administração**.

2. Em **Configuração Executar como**, clique em **Perfis** e abra **Perfil Executar como da Avaliação SQL do OMS**.

3. Na página **Contas Executar como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o SQL Server ou clique em **Nova** para criar uma.
	>[AZURE.NOTE] O tipo de conta Executar Como deve ser Windows. A conta Executar como também deve fazer parte do grupo de administradores locais em todos os servidores Windows que hospedam instâncias do SQL Server.

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
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.


### Criar e usar um arquivo de texto IgnoreRecommendations.txt

1.	Crie um arquivo chamado IgnoreRecommendations.txt.
2.	Cole ou digite cada RecommendationId para cada recomendação que você deseja que o OMS ignore em uma linha separada, salve e feche o arquivo.
3.	Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o OMS ignore as recomendações.
    - Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *SystemDrive*: \\Arquivos de Programas\\Microsoft Monitoring Agent\\Agent
    - No servidor de gerenciamento do Operations Manager - *SystemDrive*: \\Arquivos de Programa\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Para verificar se as recomendações são ignoradas

1.	Após a execução da próxima avaliação agendada, por padrão a cada 7 dias, as recomendações especificadas são marcadas como Ignoradas e não aparecerão no painel de avaliação.
2.	É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## Perguntas frequentes sobre soluções de Avaliação de SQL

*Com que frequência uma avaliação é executada?*
- A avaliação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a avaliação é executada?*
- Não no momento.

*Se outro servidor for descoberto após ter adicionado uma solução de avaliação de SQL, ele será avaliado?*
- Sim, assim que for descoberto, ele é avaliado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da avaliação?*
- Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*
- AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*
- A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de instâncias ou bancos de dados SQL.

*Que tipo de dados é coletado?*
- Os seguintes tipos de dados são coletados:
    - WMI
    - Registro
    - Contadores de desempenho
    - Exibições de gerenciamento dinâmico SQL (DMV).

*Há uma maneira de configurar quando os dados são coletados?*
- Não no momento.

*Por que é necessário configurar uma conta Executar como?*
- Para o SQL Server, um pequeno número de consultas SQL é executado. Para elas serem executadas, uma conta Executar como com permissões VIEW SERVER STATE devem ser usadas para SQL. Além disso, para consultar WMI, são necessárias credenciais de administrador local.

*Por que apenas as 10 principais recomendações são exibidas?*
- Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se preferir ver a lista detalhada, exiba todas as recomendações usando a pesquisa de log do OMS.

*É possível ignorar uma recomendação?*
- Sim, veja a seção [Ignorar recomendações](#ignore-recommendations) acima.



## Próximas etapas

- [Pesquisar logs](log-analytics-log-searches.md) para exibir dados detalhados de Avaliação de SQL e recomendações.

<!---HONumber=AcomDC_0504_2016-->
<properties
   pageTitle="Solucionar problemas com Insights Operacionais"
   description="Saiba mais sobre a solução de problemas do Insights Operacionais"
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
   ms.date="09/10/2015"
   ms.author="banders" />

# Solucionar problemas com Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode usar as informações nas seções a seguir para ajudá-lo a solucionar problemas. Se o seu problema não estiver neste artigo, consulte o blog da equipe do [Insights Operacionais](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Solucionar problemas de permissões com a Avaliação do SQL
O Operations Management Suite (OMS) usa os grupos de gerenciamento Microsoft Monitoring Agent e Operations Manager para coletar e enviar dados para o serviço OMS. Algumas cargas de trabalho, como o SQL Server, exigem privilégios específicos à carga de trabalho para a execução de coleta de dados em um contexto de segurança diferente, como uma conta de domínio. Se o Microsoft Monitoring Agent for conectado por meio do System Center Operations Manager e se você estiver enfrentando problemas de permissões durante a coleta de dados, será necessário fornecer informações de credenciais configurando uma conta Executar como.

Se você já estiver usando o pacote de gerenciamento do SQL Server, use essa conta do Windows como a conta Executar como.

### Para configurar a conta Executar como do SQL no console Operações

1. No Operations Manager, abra o console Operações e clique em **Administração**.

2. Em **Configuração de Executar como**, clique em **Perfis** e abra **Perfil Executar como do Microsoft System Center Advisor**.

3. Na página **Contas Executar como**, clique em **Adicionar**.

4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para o SQL Server ou clique em **Nova** para criar uma.

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

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$\_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version Get-SCOMManagementPack | where {$\_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version ```

>[AZURE.NOTE]Se você estiver solucionando problemas com a solução Capacidade, consulte *quantos* pacotes de gerenciamento com “capacidade” no nome você tem: há dois pacotes de gerenciamento com o mesmo nome de exibição (mas IDs internas diferentes) que vêm do mesmo conjunto de MP; se um dos dois não for importado (geralmente por falta de dependência do VMM), o outro pacote de gerenciamento não será importado e não haverá tentativa de repetir a operação.

Você deve ver os três seguintes pacotes de gerenciamento relacionados a 'capacidade' - Pacote de inteligência de capacidade do Microsoft System Center Advisor - Pacote de inteligência de capacidade do Microsoft System Center Advisor - Dados de armazenamento de capacidade do Microsoft System Center Advisor

Se houver apenas um ou dois deles, mas não todos os três, remova-os e aguarde de 5 a 10 minutos para o Operations Manager baixá-lo e importá-lo novamente – consulte os logs de eventos quanto a erros durante esse período.

### Procedimento 2: validar se as soluções certas são baixadas para o seu Agente Direto
>[AZURE.NOTE]Se você usar somente o Operations Manager, pode ignorar esse procedimento.

No Agente Direto, você verá a política de coleta da solução sendo armazenada em cache em **C:\\Arquivos de Programas\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procedimento 3: validar se os dados estão sendo enviados para o serviço do Advisor (ou pelo menos tentando)
Dependendo de se você estiver usando agentes conectados diretamente ou o Operations Manager, você pode executar o procedimento a seguir no computador do Agente Direto ou no servidor de gerenciamento do Operations Manager:

1. - Abra o 'Monitor de Desempenho'
2. - Selecione os 'Grupos de Gerenciamento de Serviço de Integridade'
3. - Adicione todos os contadores que começam com 'HTTP'

Se a configuração estiver correta, você verá atividade para esses contadores conforme eventos e outros itens de dados (com base na solução incorporada no portal e na política de coleta de log configurada) são carregados. Os contadores não precisam necessariamente estar continuamente ocupados - se houver pouca ou nenhuma atividade, você pode não estar incorporado em muitas soluções ou não ter uma política de coleta muito simples.

### Procedimento 4: verificar se há erros no servidor de gerenciamento ou logs de eventos do Agente Direto
Como uma etapa final, se todas as opções acima falharem, mas se ainda não for possível ver os dados recebidos pelo serviço, verifique se há erros no **Visualizador de Eventos**.

Abra o **Visualizador de Eventos** –> **Aplicativos e Serviços** –> **Operations Manager** e filtre por Fontes de Evento: **Supervisor**, **Módulos de Serviço de Integridade**, **Serviço de Integridade** e **Conector de Serviço** (esse último aplica-se somente ao Agente Direto).

A maioria desses eventos seria semelhante no Operations Manager e no Agente Direto e as etapas para solução de problemas seriam semelhantes para qualquer um deles. A única parte que difere entre o Operations Manager e o Agente Direto é o processo de registro (interface gráfica do usuário no Operations Manager; combinação de chave/Id do espaço de trabalho no Agente Direto do), mas, depois do registro inicial, os certificados são trocados e usados, e tudo o mais sobre a comunicação com o serviço é igual.

Portanto, muitos desses eventos se aplicam a ambos os tipos de infraestrutura de relatório. Aqui está uma lista dos mais comuns que você pode ver.

### Eventos da origem 'Módulos de Serviço de Integridade'
##### EventID 2138
Isso significa que o proxy exige autenticação. Execute as etapas para [configurar servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2137
O Operations Manager não pode ler o certificado de autenticação. Executar novamente o assistente de registro do Supervisor corrigirá certificados/contas de executar como

##### EventID 2132
Significa **Não Autorizado**. Pode ser um problema com o certificado e/ou o registro para o serviço. Tente executar novamente o assistente de registro, o que corrigirá certificados e contas RunAs. Além disso, verifique se o proxy foi definido para permitir exclusões. Para saber mais, consulte [configurar servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2129
Isso é uma falha de conexão devido à falha na negociação SSL. Verifique se os sistemas estão configurados para usar TLS, e não SSL. Talvez existam algumas configurações de SSL estranhas nesse servidor em relação a chipers, tanto nas opções **Avançadas** do Internet Explorer, como no Registro do Windows sob a chave

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### EventID 2127
Falha ao enviar dados - recebido código de erro. Se isso ocorrer apenas eventualmente, pode ser simplesmente uma falha. Fique atento para entender com que frequência isso acontece. Se a mesma falha aparecer com muita frequência (mais ou menos a cada 10 minutos por um longo período de tempo), pode ser um problema real. Verifique sua configuração de rede, as configurações de proxy descritas acima e execute novamente o assistente de registro. Mas se a falha só acontecer esporadicamente (isto é, algumas vezes por dia), tudo deve ficar bem, uma vez que os dados serão colocados em fila e retransmitidos. Provavelmente é apenas um tempo limite da rede se essa for a frequência em que ocorre.

Alguns dos códigos de erro HTTP têm algum significado especial, ou seja:

- na PRIMEIRA vez que um direto agente ou servidor de gerenciamento MMA tenta enviar dados ao nosso serviço, ele receberá um erro 500 com um código de erro 404 interno – 404 significa não encontrado. Isso indica que a área de armazenamento que usaremos para esse novo espaço de trabalho seu ainda não está totalmente pronta – ela está sendo provisionada. Na próxima tentativa, entretanto, ele estará pronto e o fluxo de começará a funcionar (em condições normais).
- 403 pode indicar um problema de permissão/credenciais e assim por diante.

##### EventID 2128
Falha na resolução de nomes DNS. Seu servidor não conseguiu resolver nosso endereço de Internet ao qual deve enviar dados. Isso pode ser devido a configurações do resolvedor DNS em seu computador, configurações de proxy incorretas ou um problema (temporário) com o DNS no seu provedor. Como o evento anterior, dependendo de se ele ocorre constantemente ou 'de forma esporádica', pode indicar um problema real – ou não.

##### EventID 2130
Tempo limite. Como o evento anterior, dependendo de se ele ocorre constantemente ou "de vez em quando", pode ser um problema – ou não.

### Eventos da origem 'Serviço de Integridade'
##### EventID 4511
Não é possível carregar o módulo "System.PublishDataToEndPoint" – arquivo não encontrado. A inicialização de um módulo do tipo "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") falhou com o código de erro. O sistema não consegue localizar o arquivo especificado. Esse erro indica que você tem DLLs antigos em seu computador que não contêm os módulos necessários. A correção é atualizar seus Servidores de Gerenciamento com o Pacote Cumulativo de Atualizações mais recente.

##### EventID 4502
O módulo falhou. Se isso for exibido para fluxos de trabalho com nomes como **CollectInstanceSpace** ou **CollectTypeSpace**, pode significar que o servidor está com problemas para enviar alguns dados de configuração. Dependendo da frequência com que acontece - constantemente ou "de vez em quando" -, pode ser um problema ou não. Se acontecer mais que uma vez por hora, é definitivamente um problema. Se essa operação falhar apenas uma ou duas vezes por dia, não haverá problema de ela será capaz de se recuperar. Dependendo de como o módulo realmente falha (a descrição terá mais detalhes), pode ser um problema local – ou seja, para coletar ao banco de dados – ou um problema de envio para a nuvem. Verifique se a sua rede e configurações proxy e pior caso tentam reiniciar o HealthService.

##### EventID 4501
O módulo "System.PublishDataToEndPoint" falhou. Um módulo do tipo "System.PublishDataToEndPoint" relatou um erro 87L que estava sendo executado como parte da regra "Microsoft.SystemCenter.CollectAlertChangeDataToCloud", por exemplo, "Grupo de gerenciamento do Operations Manager" com id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" no grupo de gerenciamento "SCOMTEST". Você *não* verá mais isso para este fluxo de trabalho, módulo e erro exatos, era [um bug *agora corrigido*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Se isso for exibido novamente, relate por meio do canal de suporte da Microsoft preferido.


### Eventos de origem 'Conector do serviço'
##### EventID 4002
O serviço retornou o código de status HTTP 403 em resposta a uma consulta. Entre em contato com o administrador de serviços quanto à integridade do serviço. A consulta será repetida mais tarde. Você poderá obter um erro 403 durante a fase de registro inicial do agente e verá uma URL como https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest. O código de erro 403 significa 'forbidden' – isso é normalmente uma WorkspaceId ou uma chave copiada incorretamente ou o relógio não está sincronizado no computador. Tente sincronizar com uma fonte de horário confiável e use a verificação de conectividade no miniaplicativo do Painel de Controle do Microsoft Monitoring Agent para verificar se você tem a Id e a chave do espaço de trabalho certas.


### Procedimento 5: procurar os agentes para enviar seus dados e indexá-los no Portal
Verifique no Portal do OpInsights. Na página Visão Geral, navegue até o pequeno bloco **Servidores e Uso**. Isso mostrará se os grupos de gerenciamento (e seus agentes) e os agentes diretos estão reportando os dados na pesquisa de log. O número de agentes no bloco é derivado dos dados – se os computadores não relatarem por duas semanas, eles sairão do radar.

As análises o levam para a pesquisa de log e exibem o carimbo de data/hora dos últimos dados indexados para cada computador. A partir daí, você pode explorar quais são os dados. Dependendo da quantidade de coleta de dados configurada e das soluções, a agenda e a velocidade do carregamento de dados podem variar.

Essa página também apresenta informações de medição (não usa o índice de pesquisa de log, mas o sistema de cobrança, é atualizado a cada duas horas) sobre as quantidades de dados enviados ao serviço divididas por solução.

<!---HONumber=Sept15_HO3-->
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
   ms.date="07/02/2015"
   ms.author="banders" />

#Solucionar problemas com Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode usar as informações nas seções a seguir para ajudá-lo a solucionar problemas. Se o seu problema não estiver neste artigo, consulte o blog da equipe do [Insights Operacionais](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnosticar problemas de conexão para Insights Operacionais

Como o Insights Operacionais do Microsoft Azure depende de dados movidos de e para a nuvem, problemas de conexão podem ser debilitantes. Use as informações a seguir para compreender e solucionar seus problemas de conexão.


**Mensagem de erro:** a conectividade com a Internet foi verificada, mas não foi possível estabelecer conexão com o serviço do Insights Operacionais. Tente novamente mais tarde.

**Possíveis causas:** - o serviço do Insights Operacionais está em manutenção. Aguarde até que a manutenção de informações operacionais seja feita. - Sua rede bloqueou o Insights Operacionais. Entre em contato com o administrador da rede e solicite acesso a Insights Operacionais ou use outro servidor como gateway.

**Mensagem de erro:** não foi possível estabelecer conexão com a Internet. Verifique as configurações de proxy.

**Possíveis causas:** - este servidor não está conectado à Internet. Verifique o status de conectividade de Internet e conecte o servidor à Internet. -A configuração do proxy não está correta. Consulte [Configurar proxy e firewall](operational-insights-proxy-firewall.md) para obter informações sobre como definir ou alterar as configurações de proxy. - O servidor proxy exige autenticação. Consulte [Definir configurações de proxy e firewall](operational-insights-proxy-firewall.md) para saber mais sobre como configurar o Operations Manager para usar um servidor proxy.


## Solucionar problemas de descoberta do SQL Server

Se você estiver executando o Microsoft SQL Server 2008 R2 e, apesar da implantação do agente do Operations Manager, você não receber alertas para esse servidor, pode haver um problema de descoberta.

Para confirmar se essa é a fonte do seu problema, verifique os dois problemas a seguir:

- No log de eventos do Operations Manager, você verá a ID de Evento 4001. Esse evento indica que há uma classe inválida.

- No SQL Server Configuration Manager, ao exibir Serviços do SQL Server, você recebe a mensagem de erro "Falha na chamada de procedimento remoto. [0x0800706be]"

Se os dois problemas forem verdadeiros, você precisa instalar o SQL Server 2008 R2 Service Pack 2. Para baixar esse service pack, consulte [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) no Microsoft Download Center.

Depois de instalar o service pack, você deve ver dados do Insights Operacionais para o servidor dentro de 24 horas.

## Solucionar problemas de agentes ou fluxo de dados do Operations Manager para o Insights Operacionais

O seguinte conjunto de procedimentos destina-se a ser um guia para ajudar a solucionar seus agentes conectados diretamente ou implantações do Operations Manager configuradas para relatar dados ao Insights Operacionais do Azure.

### Procedimento 1: validar se os pacotes de gerenciamento certos são baixados para o seu ambiente do Operations Manager
>[AZURE.NOTE]Se você usar somente o Agente Direto, poderá pular para o próximo procedimento.

Dependendo de quais soluções (anteriormente chamadas de pacotes de inteligência) você habilitou no Portal do Insights Operacionais, você verá mais ou menos desses MPs. Pesquise a palavra-chave "Consultor" ou "Inteligência" no nome. Você pode verificar esses MPS usando o OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
```

>[AZURE.NOTE]Se você estiver solucionando problemas da solução de Capacidade, consulte *quantos* pacotes de gerenciamento com o nome contendo capacidade você tem: há dois pacotes de gerenciamento que têm o mesmo nome de exibição (mas diferentes IDs internas) que vêm do mesmo conjunto de MP; se um dos dois não for importado (geralmente por falta de dependência do VMM) outro pacote de gerenciamento não será importado e não haverá tentativa de repetir a operação.

Você deve ver os três seguintes pacotes de gerenciamento relacionados a 'capacidade' - Pacote de inteligência de capacidade do Microsoft System Center Advisor - Pacote de inteligência de capacidade do Microsoft System Center Advisor - Dados de armazenamento de capacidade do Microsoft System Center Advisor

Se houver apenas um ou dois deles, mas não todos os três, remova-os e aguarde de 5 a 10 minutos para o Operations Manager baixá-lo e importá-lo novamente – consulte os logs de eventos quanto a erros durante esse período.

### Procedimento 2: validar se as soluções certas são baixadas para o seu Agente Direto
>[AZURE.NOTE]Se você usar somente o Operations Manager, pode ignorar esse procedimento.

No Agente Direto, você verá a política de coleta da solução sendo armazenada em cache em **C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procedimento 3: validar se os dados estão sendo enviados para o serviço do Advisor (ou pelo menos tentando)
Dependendo de se você estiver usando agentes conectados diretamente ou o Operations Manager, você pode executar o procedimento a seguir no computador do Agente Direto ou no servidor de gerenciamento do Operations Manager:

1. - Abra o 'Monitor de Desempenho'
2. - Selecione os 'Grupos de Gerenciamento de Serviço de Integridade'
3. - Adicione todos os contadores que começam com 'HTTP'

Se a configuração estiver correta, você verá atividade para esses contadores conforme eventos e outros itens de dados (com base na solução incorporada no portal e na política de coleta de log configurada) são carregados. Os contadores não precisam necessariamente estar continuamente ocupados - se houver pouca ou nenhuma atividade, você pode não estar incorporado em muitas soluções ou não ter uma política de coleta muito simples.

### Procedimento 4: verificar se há erros no servidor de gerenciamento ou logs de eventos do Agente Direto
Como uma etapa final se todas as opções acima falharem, mas você ainda não visualizar nenhum dado recebido pelo serviço, verifique se você está há erros no **Visualizador de Eventos**.

Abra o **Visualizador de Eventos** –> **Aplicativos e Serviços** –> **Operations Manager** e filtre por Fontes de Eventos: **Advisor**, **Módulos de Serviço de Integridade**, **Serviço de Integridade** e **Conector de Serviço** (este último aplica-se ao Agente Direto somente).

A maioria desses eventos seria semelhante no Operations Manager e no Agente Direto e as etapas para solução de problemas seriam semelhantes para qualquer um deles. A única parte que difere entre o Operations Manager e o Agente Direto é o processo de registro (interface gráfica do usuário no Operations Manager; combinação de chave/Id do espaço de trabalho no Agente Direto do), mas, depois do registro inicial, os certificados são trocados e usados, e tudo o mais sobre a comunicação com o serviço é igual.

Portanto, muitos desses eventos se aplicam a ambos os tipos de infraestrutura de relatório. Aqui está uma lista dos mais comuns que você pode ver.

### Eventos da origem 'Módulos de Serviço de Integridade'
##### EventID 2138
Isso significa que o proxy exige autenticação. Siga as etapas para [configurar servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2137
O Operations Manager não pode ler o certificado de autenticação. Executar novamente o assistente de registro do Supervisor corrigirá certificados/contas de executar como

##### EventID 2132
Significa **Não Autorizado**. Pode ser um problema com o certificado e/ou o registro para o serviço. Tente executar novamente o assistente de registro, o que corrigirá certificados e contas RunAs. Além disso, verifique se o proxy foi definido para permitir exclusões. Para obter mais informações, consulte [configurar servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2129
Isso é uma falha de conexão devido à falha na negociação SSL. Verifique se os sistemas estão configurados para usar TLS, e não SSL. Pode haver algumas configurações de SSL estranhas nesse servidor em relação a chipers, seja nas opções **Avançadas** do Internet Explorer, seja no Registro do Windows sob a chave

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
O módulo "System.PublishDataToEndPoint" falhou. Um módulo do tipo "System.PublishDataToEndPoint" relatou um erro 87L que estava sendo executado como parte da regra "Microsoft.SystemCenter.CollectAlertChangeDataToCloud", por exemplo, "Grupo de gerenciamento do Operations Manager" com id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" no grupo de gerenciamento "SCOMTEST". Você *não* verá mais isso com este de fluxo de trabalho, módulo e erro exatos, era [um bug *agora corrigido*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Se isso for exibido novamente, relate por meio do canal de suporte da Microsoft preferido.


### Eventos de origem 'Conector do serviço'
##### EventID 4002
O serviço retornou o código de status HTTP 403 em resposta a uma consulta. Entre em contato com o administrador de serviços quanto à integridade do serviço. A consulta será repetida mais tarde. Você pode obter um 403 durante a fase de registro inicial do agente. Você verá uma URL como https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest 403 significa 'fordbidden' – isso é normalmente um WorkspaceId ou chave copiados incorretamente ou o relógio não está sincronizado no computador. Tente sincronizar com uma fonte de horário confiável e use a verificação de conectividade no miniaplicativo do Painel de Controle do Microsoft Monitoring Agent para verificar se você tem a Id e a chave do espaço de trabalho certas.


### Procedimento 5: procurar os agentes para enviar seus dados e indexá-los no Portal
Faça check-in no Portal OpInsights e, na página Visão Geral, navegue até o bloco pequeno de **Servidores e Uso**. Isso mostrará se os grupos de gerenciamento (e seus agentes) e agentes diretos estão relatando dados para a pesquisa de log. O número de agentes no bloco é derivado dos dados – se os computadores não relatarem por duas semanas, eles sairão do radar.

As análises o levam para a pesquisa de log e exibem o carimbo de data/hora dos últimos dados indexados para cada computador. A partir daí, você pode explorar quais são os dados. Dependendo da quantidade de coleta de dados configurada e das soluções, a agenda e a velocidade do carregamento de dados podem variar.

Essa página também apresenta informações de medição (não usa o índice de pesquisa de log, mas o sistema de cobrança, é atualizado a cada duas horas) sobre as quantidades de dados enviados ao serviço divididas por solução.

<!---HONumber=July15_HO4-->
<properties
	pageTitle="Segurança do Insights Operacionais"
	description="Saiba mais sobre como o Insights Operacionais protege a sua privacidade e seus dados."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# Segurança de Insights operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

A Microsoft está comprometida em proteger sua privacidade e segurança de dados, oferecendo softwares e serviços que ajudam a gerenciar a infraestrutura de TI da sua organização. Reconhecemos que quando você entrega seus dados a outros, essa confiança requer segurança rigorosa. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Segurança e proteção de dados é uma prioridade principal da Microsoft. Entre em contato conosco com quaisquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo nossas políticas de segurança nas [opções de suporte do Azure](http://azure.microsoft.com/support/options/).

Este artigo explica como os dados são coletados, processados e protegidos no Insights Operacionais do Microsoft Azure. Você pode usar qualquer um dos agentes para se conectar diretamente ao serviço Web ou você pode usar o System Center Operations Manager para coletar dados operacionais para o serviço de Insights operacionais. Os dados coletados são enviados pela Internet para o serviço de Insights operacionais, que está hospedado no Microsoft Azure.

O serviço de Insights operacionais gerencia seus dados com segurança usando os seguintes métodos:

**Diferenciação dos dados:** os dados do cliente são mantidos separados logicamente em cada componente no serviço Insights Operacionais. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço.

Cada cliente tem um blob do Azure dedicado que hospeda os dados de longo prazo. O blob é criptografado com chaves exclusivas por cliente, que são alteradas a cada 90 dias.

**Retenção de dados:** métricas agregadas para algumas das soluções (anteriormente chamadas de pacotes de inteligência), como gerenciamento de capacidade, são armazenadas em um Banco de Dados SQL hospedado pelo Microsoft Azure. Esses dados são armazenados por 390 dias. Dados indexados de pesquisa de log são armazenados e retidos de acordo com o plano de preço. Para obter mais informações consulte a [página de preços](http://azure.microsoft.com/pricing/details/operational-insights/)

**Segurança física:** o serviço Insights Operacionais é operado pelos funcionários da Microsoft e todas as atividades são registradas em log e podem ser auditadas. O serviço Insights Operacionais é executado por completo no Azure e está em conformidade com os critérios de engenharia comuns do Azure. Você pode exibir detalhes sobre a segurança física dos ativos do Azure na página 18 da [Visão geral de Segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Conformidade e certificações:** a equipe de serviço e desenvolvimento de software do Insights Operacionais trabalha ativamente com as equipes de conformidade normativa e legal da Microsoft e outros parceiros do setor para adquirir várias certificações, incluindo ISO.

Estamos atendendo atualmente aos seguintes padrões de segurança:

- Critérios de engenharia comum do Windows
- Certificação de computação confiável da Microsoft


## Segurança de fluxo de dados
O diagrama a seguir mostra o fluxo de informações de sua empresa e como elas são protegidas à medida que se movem para o serviço Insights Operacionais e por fim vistas por você no Insights Operacionais. Após o diagrama, mais informações sobre cada etapa.

![Imagem da coleta de dados e da segurança do Insights Operacionais](./media/operational-insights-security/security.png)

### 1\. Inscreva-se no Insights Operacionais e colete dados

Para sua organização enviar dados para o serviço Insights Operacionais, você deverá configurar agentes do Microsoft Monitoring conectando-se diretamente ao serviço Web ou usando um assistente de configuração no console Operações no Operations Manager. Os usuários (que podem ser você, outros usuários individuais ou um grupo de pessoas) devem criar uma ou mais contas do Insights Operacionais e registrar cada agente conectado diretamente ou seu ambiente do Operations Manager, usando uma das seguintes contas:


- [ID Organizacional](../sign-up-organization.md)

- [Conta da Microsoft - Outlook, Office Live, MSN](../sign-up-organization.md)

Uma conta do Insights Operacionais é onde os dados são coletados, agregados, analisados e apresentados. Uma conta do Insights Operacionais é usada principalmente como um meio de particionar dados, e cada conta do Insights Operacionais é exclusiva. Por exemplo, talvez você queira ter seus dados de produção gerenciados com uma conta do Insights Operacionais e seus dados de teste gerenciados com outra conta. As contas também ajudam um administrador a controlar o acesso dos usuários aos dados. Cada conta do Insights Operacionais pode ter várias contas de usuário associadas a ela, e cada conta de usuário pode ter várias contas do Insights Operacionais.

Quando o assistente de configuração estiver concluído, cada grupo de gerenciamento do Operations Manager estabelecerá uma conexão com o serviço Insights Operacionais. Então, você poderá usar o Assistente para Adicionar Computadores para escolher quais computadores do grupo de gerenciamento poderão enviar dados para o serviço.

Ambos os tipos de agentes coletam dados para o Insights Operacionais. Os tipos de dados coletados dependem dos tipos de soluções usadas. Uma solução é um conjunto de exibições predefinidas, de consultas de pesquisa de log, de regras de coleta de dados e de lógica de processamento. Somente os administradores do Insights Operacionais podem usar o Insights Operacionais para importar uma solução. Após a importação da solução, ela será movida para os servidores de gerenciamento do Operations Manager (se usado) e então para os agentes do Operations Manager escolhidos. Depois disso, os agentes coletam os dados.

A tabela a seguir lista as soluções disponíveis no Insights Operacionais e os tipos de dados coletados.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Solução</b></td>
		<td><b>Tipos de dados</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Avaliação de Configuração</td>
		<td>Dados de configuração, metadados e dados de estado</td>
    </tr>
    <tr align="left" valign="top">
		<td>Planejamento da capacidade</td>
		<td>Dados de desempenho, metadados e dados de estado</td>
    </tr>
	<tr align="left" valign="top">
		<td>Antimalware</td>
		<td>Dados de configuração, metadados e dados de estado</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Avaliação de atualização do sistema</td>
		<td>Metadados e dados de estado</td>
    </tr>
    <tr align="left" valign="top">
		<td>Gerenciamento de Log</td>
		<td>Logs de eventos definidos pelo usuário</td>
    </tr>
    <tr align="left" valign="top">
		<td>Controle de Alterações</td>
		<td>Inventário de software e metadados de serviço do Windows</td>
    </tr>
    <tr align="left" valign="top">
		<td>Avaliação do SQL e Active Directory</td>
		<td>Dados WMI, dados do registro, dados de desempenho e resultados de exibição do gerenciamento dinâmico do SQL Server</td>
    </tr>
    </tbody>
    </table>


A tabela a seguir mostra exemplos de tipos de dados:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Tipo de dados</b></td>
		<td><b>Campos</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Alerta</td>
		<td>Nome do Alerta, Descrição do Alerta, BaseManagedEntityId, ID do Problema, IsMonitorAlert, RuleId, ResolutionState, Prioridade, Gravidade, Categoria, Proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>Configuração</td>
		<td>CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>Evento</td>
		<td>EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded<p><b>Observação:</b> quando você registra eventos em log com campos personalizados no log de eventos do Windows, o Insights Operacionais os coleta. </td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadados</td>
		<td>BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>Desempenho</td>
		<td>ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>Estado</td>
		<td>StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2\. Enviar dados de agentes

Com agentes que se conectam diretamente ao serviço Web, registre-os com uma chave e uma conexão segura é estabelecida entre o agente e o serviço Insights Operacionais usando a porta 443.

Com o Operations Manager, registre uma conta com o serviço Insights Operacionais e uma conexão HTTPS segura será estabelecida entre o servidor de gerenciamento do Operations Manager e o serviço Insights Operacionais usando a porta 443. Se o Operations Manager não conseguir se comunicar com o serviço por algum motivo, os dados coletados serão armazenados em um cache temporário e o servidor de gerenciamento tentará reenviar os dados a cada oito minutos por duas horas. Os dados coletados são compactados e enviados para o serviço Insights Operacionais, ignorando os bancos de dados no local, para que ele não adicione qualquer carga a eles. Depois que os dados coletados forem enviados, eles serão removidos do cache.

### 3\. O serviço Insights Operacionais recebe e processa os dados

O serviço Insights Operacionais garante que os dados de entrada sejam de uma fonte confiável ao validar certificados e a integridade dos dados. Os dados brutos não processados, em seguida, são armazenados como um blob no [Armazenamento do Microsoft Azure](http://azure.microsoft.com/documentation/services/storage/). Cada usuário do Insights Operacionais tem um blob do Azure dedicado, que só pode ser acessado por esse usuário. Os tipos de dados armazenados dependerão dos tipos de soluções importados e usados para coletar dados.

O serviço Insights Operacionais processa os dados brutos e os dados processados agregados são armazenados em um banco de dados SQL. A comunicação entre o serviço Insights Operacionais e o banco de dados SQL se baseia na autenticação do banco de dados SQL.

### 4\. Use o Insights Operacionais para acessar os dados

Você pode entrar no Insights Operacionais usando a conta configurada anteriormente. Todo o tráfego entre o Insights Operacionais e o serviço Insights Operacionais é enviado por um canal HTTPS seguro.

<!---HONumber=July15_HO3-->
---
title: "Solução Iniciar/Parar VMs fora do horário comercial (versão prévia)| Microsoft Docs"
description: "Essa solução de gerenciamento de VM inicia e para suas máquinas virtuais do Azure Resource Manager de acordo com um agendamento, e faz o monitoramento delas proativamente no Log Analytics."
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 4424cbb83bdb31c60e15d62f9387b4050611a98d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Solução Iniciar/Parar VMs fora do horário comercial (versão prévia) na Automação do Azure

A solução Iniciar/Parar VMs fora do horário comercial inicia e para as máquinas virtuais do Azure de acordo com agendamentos definidos pelo usuário, fornece informações por meio de Log Analytics do Azure e envia emails opcionais utilizando o [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). A solução dá suporte ao Azure Resource Manager e VMs clássicas na maioria dos cenários. 

Essa solução oferece uma opção de automação descentralizada para usuários que desejam reduzir custos usando recursos de baixo custo sem servidor. Com essa solução, você pode:

* Agendar VMs para iniciar e parar.
* Agendar VMs para iniciar e parar em ordem crescente usando Marcas do Azure (incompatível com VMs clássicas).
* Parar VMs automaticamente com base no baixo uso da CPU.

## <a name="prerequisites"></a>pré-requisitos

- O runbook funciona com uma [conta Executar como do Azure](automation-offering-get-started.md#authentication-methods).  A conta Executar como é o método de autenticação preferido, pois ela usa a autenticação de certificado em vez de uma senha que poderá expirar ou ser alterada com frequência.  

- Essa solução gerencia apenas as VMs que estão na mesma assinatura que sua conta da Automação do Azure.  

- A solução pode ser implantada somente nas seguintes regiões do Azure: Sudeste da Austrália, Canadá Central, Índia Central, Leste dos EUA, Leste do Japão, Sudeste da Ásia, Sul do Reino Unido e Europa Ocidental.  
    
    > [!NOTE]
    > Os runbooks que gerenciam o agendamento de VMs podem direcionar para VMs em qualquer região.  

- Para enviar notificações por email após o encerramento dos runbooks de inicialização e parada de VMs, durante a integração no Azure Marketplace, selecione **Sim** para implantar o SendGrid. 

    > [!IMPORTANT]
    > O SendGrid é um serviço de terceiros. Para obter suporte, entre em contato com [SendGrid](https://sendgrid.com/contact/).  
    >
   
    As limitações com SendGrid são:

    * O máximo de uma conta do SendGrid por usuário por assinatura.
    * O máximo de duas contas do SendGrid por assinatura.

Se você implantou uma versão anterior dessa solução, é preciso primeiro excluí-la da sua conta antes de implantar a versão atual.  

## <a name="solution-components"></a>Componentes da solução

A solução inclui runbooks pré-configurados, agendamentos e integração com Log Analytics para que você possa personalizar a inicialização e o desligamento das máquinas virtuais para atender às suas necessidades de negócios. 

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks implantados em sua conta da Automação.  Você não deve fazer alterações no código do runbook. Em vez disso, escreva seu próprio runbook para obter novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum runbook com "child" acrescentado ao nome.
>

Todos os runbooks pai incluem o parâmetro *WhatIf*. Quando definido como **True**, o *WhatIf* é compatível com o detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro *WhatIf* e valida as VMs corretas que estão sendo direcionadas.  Um runbook só executa suas ações definidas quando o parâmetro *WhatIf* é definido como **False**. 

|**Runbook** | **Parâmetros** | **Descrição**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamado apenas por meio do runbook pai. Cria alertas por recurso para o cenário AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: Verdadeiro ou Falso <br> VMList | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> VMList: lista de VMs separadas por vírgula.  Por exemplo, *vm1,vm2,vm3*.| 
|AutoStop_Disable | Nenhum | Desabilita os alertas de AutoStop e o agendamento padrão.| 
|AutoStop_StopVM_Child | WebHookData | Chamado apenas por meio do runbook pai. As regras de alerta chamam esse runbook para parar a VM.|  
|Bootstrap_Main | Nenhum | Usado uma vez para definir configurações de inicialização, como webhookURI, que normalmente não podem ser acessadas no Azure Resource Manager. Este runbook é removido automaticamente após a implantação bem-sucedida.|  
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Chamado apenas por meio do runbook pai. Executa a parada ou o início da parada agendada.|  
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso | Isso afeta todas as VMs na assinatura. Edite o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** para executar apenas nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a variável **External_ExcludeVMNames**. O *WhatIf* se comporta da mesma forma como em outros runbooks.|  
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso | Cria marcas com o nome **SequenceStart** e **SequenceStop** em cada VM para as quais você deseja sequenciar a atividade de iniciar/parar. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem em que você deseja iniciar ou parar. O *WhatIf* se comporta da mesma forma como em outros runbooks. <br> **Observação**: as VMs devem fazer parte dos grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis da Automação do Azure. Elas devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>variáveis

A tabela a seguir lista as variáveis criadas na sua conta da Automação.  Você só deve modificar variáveis com prefixo **External**. Modificar variáveis prefixadas com **Internal** causará efeitos indesejáveis.  

|**Variable** | **Descrição**|
---------|------------|
|External_AutoStop_Condition | O operador condicional exigido para configurar a condição antes de disparar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan** e **LessThanOrEqual**.|  
|External_AutoStop_Description | O alerta para parar a VM se o percentual da CPU exceder o limite.|  
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deverá ser configurada.| 
|External_AutoStop_Threshold | O limite para a regra de alerta do Azure especificada na variável *External_AutoStop_MetricName*. Os valores percentuais podem variar de 1 a 100.|  
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo que é aplicado ao tamanho de janela selecionado para avaliar a condição. Os valores aceitáveis são **Médio**, **Mínimo**, **Máximo**, **Total** e **Último**.|  
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure analisa a métrica selecionada para disparar um alerta. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas.|  
|External_EmailFromAddress | Especifica o remetente do email.|  
|External_EmailSubject | Especifica o texto da linha de assunto do email.|  
|External_EmailToAddress | Especifica os destinatários do email. Separe os nomes usando vírgula.|  
|External_ExcludeVMNames | Insira os nomes das VMs a serem excluídas, separando-os por vírgula, sem espaços.|  
|External_IsSendEmail | Especifica a opção para enviar notificações por email após a conclusão.  Especifique **Sim** ou **Não** para enviar emails.  Esta opção deve ser **Não** se você não habilitou as notificações por email durante a implantação inicial.|  
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações de iniciar.|  
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações de parar.|  
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|  
|Internal_AutoSnooze_WebhookUri | Especifica o URI do Webhook chamado para o cenário AutoStop.|  
|Internal_AzureSubscriptionId | Especifica a ID da Assinatura do Azure.|  
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta da Automação.|  
|Internal_SendGridAccountName | Especifica o nome da conta do SendGrid.|  
|Internal_SendGridPassword | Especifica a senha da conta do SendGrid.|  

<br>

Em todos os cenários, as variáveis **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** são necessárias para direcionar VMs, com exceção da disponibilização de uma lista de VMs separadas por vírgula para o runbook **AutoStop_CreateAlert_Parent**. Em outras palavras, suas VMs devem residir em grupos de recursos de destino para que as ações iniciar e parar ocorram. A lógica funciona mais ou menos como a política do Azure, isto é, você pode direcionar a assinatura ou o grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar e parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.  É possível modificá-las ou criar suas próprias agendas personalizadas.  Por padrão, cada uma dessas é desabilitada, exceto **Scheduled_StartVM** e **Scheduled-StopVM**.

Você não deve habilitar todas os agendamentos, porque isso poderá criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja executar e modificar de acordo.  Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.   

|**Nome do agendamento** | **Frequência** | **Descrição**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, que, por sua vez, interrompe os valores baseados em VM em External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis da Automação do Azure.  Como alternativa, você pode especificar uma lista de VMs separadas por vírgula utilizando o parâmetro VMList.|  
|Scheduled_StopVM | Definida pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de *Parar* todos os dias no horário especificado.  Interrompe automaticamente todas as VMs que atendem às regras definidas por variáveis de ativo. Você deve habilitar o agendamento relacionado, **Scheduled-StartVM**.|  
|Scheduled_StartVM | Definida pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de *Iniciar* todos os dias no horário especificado.  Inicia automaticamente todas as VMs que atendem às regras definidas pelas variáveis adequadas.  Você deve habilitar o agendamento relacionado, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o runbook Scheduled_Parent com um parâmetro de *Parar* toda sexta-feira no horário especificado.  Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida pelas variáveis adequadas.  Consulte a seção de Runbooks para obter mais detalhes sobre valores de marcas e variáveis de ativos.  Você deve habilitar o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o runbook Scheduled_Parent com um parâmetro de *Parar* toda segunda-feira no horário determinado. Inicia sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida pelas variáveis adequadas.  Consulte a seção de Runbooks para obter mais detalhes sobre valores de marcas e variáveis de ativos.  Você deve habilitar o agendamento relacionado, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para adicionar a solução Iniciar/Parar VMs fora do horário comercial à sua conta de Automação e, em seguida, configure as variáveis para personalizar a solução.

1. No portal do Azure, clique em **Novo**.<br> ![portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. No painel Marketplace, digite uma palavra-chave como **Iniciar** ou **Iniciar/Parar**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Como alternativa, você pode digitar uma ou mais palavras-chave do nome completo da solução e, em seguida, pressionar Enter.  Selecione **Iniciar/Parar VMs fora do horário comercial [Visualização]** nos resultados da pesquisa.  
3. No painel **Iniciar/Parar VMs fora do horário comercial [Visualização]** da solução selecionada, analise as informações de resumo e clique em **Criar**.  
4. O painel **Adicionar Solução** é exibido. Você será solicitado a configurar a solução antes de importá-la na sua assinatura da Automação.<br><br> ![Folha Adicionar Solução do Gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  No painel **Adicionar Solução**, selecione **Espaço de Trabalho**. Selecione um espaço de trabalho do OMS que esteja vinculado à mesma assinatura do Azure em que a conta da Automação está. Se você não tiver um espaço de trabalho, selecione **Criar Novo Espaço de Trabalho**. No painel **Espaço de Trabalho do OMS**, faça o seguinte: 
   - Especifique um nome para o novo **Espaço de Trabalho do OMS**.
   - Selecione uma **Assinatura** à qual se vincular, escolhendo na lista suspensa, caso a assinatura selecionada por padrão não seja adequada.
   - Em **Grupo de Recursos**, você pode criar um novo grupo de recursos ou selecionar um existente.  
   - Selecione um **Local**.  No momento, os únicos locais disponíveis são: **Sudeste da Austrália**, **Canadá Central**, **Índia Central**, **Leste dos EUA**, **Leste do Japão**, **Sudeste da Ásia**, **Sul do Reino Unido** e **Europa Ocidental**.
   - Selecione um **tipo de preço**.  A solução oferece duas camadas: gratuita e paga do OMS.  A camada gratuita tem um limite na quantidade de dados coletados diariamente, no período de retenção e nos minutos de tempo de execução do trabalho de runbook.  A camada paga do OMS não tem um limite de quantidade de dados coletados diariamente.  

        > [!NOTE]
        > Embora a camada paga Por GB (Autônoma) seja exibida como uma opção, ela não se aplica.  Se você a selecionar e prosseguir com a criação dessa solução em sua assinatura, ocorrerá uma falha.  Essa questão será abordada quando a solução for lançada oficialmente.<br>Essa solução só usa minutos de trabalho de automação e ingestão de log.  Ela não adiciona outros nós do OMS ao seu ambiente.  

6. Depois de fornecer as informações necessárias no painel **Espaço de Trabalho do OMS**, clique em **Criar**.  Você pode acompanhar o progresso em **Notificações** no menu, que retornará o painel **Adicionar Solução** ao terminar.  
7. No painel **Adicionar Solução**, selecione **Conta de automação**.  Se você estiver criando um novo espaço de trabalho do OMS, também será necessário criar uma nova conta da Automação para ser associada com ele.  Selecione **Criar uma conta de Automação** e, no painel **Adicionar conta de Automação**, forneça o seguinte: 
  - No campo **Nome**, digite o nome da conta de Automação.

    Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do OMS selecionado. Essas opções não podem ser modificadas.  Uma conta Executar como do Azure é o método de autenticação padrão para os runbooks incluídos nesta solução.  Depois de clicar em **OK**, as opções de configuração serão validadas e a conta de Automação será criada.  Você pode acompanhar o progresso em **Notificações** no menu. 

    Caso contrário, você pode selecionar uma conta Executar como de Automação existente.  Observe que a conta selecionada não poderá já estar vinculada a outro espaço de trabalho do OMS. Se ela já estiver vinculada, você receberá uma mensagem e deverá selecionar uma conta Executar como de Automação diferente ou criar uma nova.<br><br> ![Conta de Automação já vinculada ao Espaço de Trabalho do OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por fim, no painel **Adicionar Solução**, selecione **Configuração**. O painel **Parâmetros** é exibido.<br><br> ![Painel de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Aqui, você será solicitado a:  
   - Especificar os **Nomes do ResourceGroup de destino**. Esses são os nomes de grupos de recursos que contêm VMs a serem gerenciadas por essa solução.  Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas).  O uso de um caractere curinga tem suporte para selecionar VMs em todos os grupos de recursos na assinatura.
   - Especifique a **Lista de exclusão de VM (cadeia de caracteres)**. Ela é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino.  Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas).  O uso de caracteres curingas é aceito.
   - Selecione um **Agendamento**. Este agendamento é uma data e hora recorrentes para iniciar e parar as VMs no grupo de recursos de destino.  Por padrão, o agendamento está configurado de acordo com o fuso horário UTC. A seleção de uma região diferente não está disponível.  Para configurar o agendamento de acordo com seu fuso horário específico após a configuração da solução, confira [Modificando o agendamento de inicialização e desligamento](#modifying-the-startup-and-shutdown-schedule).
   - Para receber **Notificações por Email** do SendGrid, aceite o valor padrão de **Sim** e forneça um endereço de email válido. Se você selecionar **Não** mas decidir posteriormente que deseja receber notificações por email, poderá atualizar a variável **External_EmailToAddress** com endereços de email válidos separados por vírgula e, em seguida, modificar a variável **External_IsSendEmail** com o valor **Sim**.  

9. Depois de configurar as definições iniciais necessárias para a solução, selecione **Criar**.  Depois que todas as configurações forem validadas, a solução será implantada em sua assinatura.  Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em **Notificações** no menu. 

## <a name="collection-frequency"></a>Frequência de coleta

O log do trabalho de automação e os dados de fluxo do trabalho são ingeridos no repositório do Log Analytics a cada cinco minutos.  

## <a name="using-the-solution"></a>Usando a solução

Ao adicionar a solução de gerenciamento de VM, o bloco **StartStopVMView** será adicionado ao seu painel no espaço de trabalho do Log Analytics do Portal do Azure.  Esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook da solução que foi iniciada e encerrada com êxito.<br><br> ![Bloco de exibição StartStopVM de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Na sua conta da Automação, é possível acessar e gerenciar a solução selecionando a opção **Espaço de Trabalho**. Na página do Log Analytics, selecione **Soluções** no painel esquerdo.  Na página **Soluções**, selecione a solução **Start-Stop-VM[workspace]** na lista.<br><br> ![Lista Soluções no Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

A seleção da solução exibe o painel de soluções **Start-Stop-VM [workspace]**. Aqui você pode analisar detalhes importantes, como o bloco **StartStopVM**. Como no seu espaço de trabalho do Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook da solução que foi iniciada e encerrada com êxito.<br><br> ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Daqui, você pode executar outras análises dos registros de trabalho clicando no bloco de rosca. O painel da solução mostra o histórico de trabalhos e as consultas de pesquisa de logs pré-definidas. Acesse o Portal avançado do Log Analytics para pesquisar com base em suas consultas de pesquisa.  

Todos os runbooks pai incluem o parâmetro *WhatIf*. Quando definido como **True**, ele é compatível com o detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro *WhatIf* e ele valida se as VMs corretas estão sendo direcionadas. Os runbooks executam apenas as respectivas ações definidas quando o parâmetro *WhatIf* é definido como **Falso**.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>Cenário 1: parar/iniciar VMs diariamente em uma assinatura ou grupos de recursos de destino 

Essa é a configuração padrão quando a solução é implantada pela primeira vez.  Por exemplo, você pode configurar a solução para parar todas as VMs em uma assinatura ao sair do trabalho à noite e iniciá-las pela manhã ao retornar ao escritório. Quando configurados durante a implantação, os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM** iniciam e param VMs de destino.
>[!NOTE]
>O fuso horário é o seu fuso horário atual de quando o parâmetro de hora do agendamento foi configurado. No entanto, o parâmetro é armazenado no formato UTC na Automação do Azure.  Não é necessário executar nenhuma conversão de fuso horário, pois isso acontece durante a implantação.

Para controlar quais VMs estão no escopo, configure as seguintes variáveis: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames**.  

>[!NOTE]
> O valor de **Target ResourceGroup Names** é armazenado como o valor de **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Para obter uma maior granularidade, você pode modificar cada uma das variáveis para direcionar diferentes grupos de recursos.  Para iniciar a ação, use **External_Start_ResourceGroupNames** e, para parar a ação, use **External_Stop_ResourceGroupNames**. As VMs são adicionadas automaticamente aos agendamentos de início e parada.

Para testar e validar a configuração, inicie manualmente o runbook **ScheduledStartStop_Parent** e defina o parâmetro ACTION como **iniciar** ou **parar** e o parâmetro WHATIF como **True**.<br><br> ![Configurar parâmetros para runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 Visualize a ação agendada e faça as alterações necessárias antes de implementar em VMs de produção.  Você pode executar o runbook manualmente com o parâmetro definido como **False** ou pode permitir que os agendamentos **Schedule-StartVM** e **Schedule-StopVM** da Automação sejam executados automaticamente de acordo com o agendamento prescrito.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>Cenário 2: sequenciar as VMs de início/parada em uma assinatura com o uso de marcas
Em um ambiente que inclui dois ou mais componentes em várias VMs compatíveis com cargas de trabalho distribuídas, é importante dar suporte ao sequenciamento de quais componentes são iniciados/parados em ordem.  Para fazer isso, execute as etapas a seguir:

1. Adicione uma marca **SequenceStart** e uma **SequenceStop** com um valor inteiro positivo às VMs que são direcionadas nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**.  As ações iniciar e parar são executadas em ordem crescente.  Para saber como marcar uma VM, consulte [Marcar uma Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Marcar uma Máquina Virtual do Linux no Azure](../virtual-machines/linux/tag.md).
2. Modifique os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** de acordo com a data e hora que atendem às suas exigências e habilite o agendamento.  

Para testar e validar a configuração, inicie manualmente o runbook **SequencedStartStop_Parent**. Defina o parâmetro ACTION como **iniciar** ou **parar** e o parâmetro WHATIF como **True**.<br><br> ![Configurar parâmetros para runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção.  Quando estiver pronto, execute o runbook manualmente com o parâmetro definido como **False** ou permita que os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** da Automação sejam executados automaticamente de acordo com o agendamento prescrito.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>Cenário 3: automatizar início/parada de VMs em uma assinatura com base na utilização da CPU
Essa solução pode ajudar a gerenciar o custo de execução de máquinas virtuais em sua assinatura, avaliando as VMs do Azure que não são usadas durante os períodos mais calmos, por exemplo, depois do horário comercial, e desligando-as automaticamente se a utilização do processador for menor que x%.  

Por padrão, a solução é pré-configurada para avaliar a métrica percentual da CPU para ver se a utilização média é de 5% ou menos.  Isso é controlado pelas seguintes variáveis e pode ser modificado caso os valores padrão não atendam às suas exigências:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Você pode habilitar o direcionamento da ação a uma assinatura e um grupo de recursos ou direcionar a uma lista específica de VMs, mas não ambos.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Direcione a ação de parada a uma assinatura e grupo de recursos

1. Configure as variáveis **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar as VMs de destino.  
2. Habilite e atualize a agenda **Schedule_AutoStop_CreateAlert_Parent**.
3. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro ACTION definido como **iniciar** e o parâmetro WHATIF definido como **True** para visualizar as alterações.

#### <a name="target-the-stop-action-by-vm-list"></a>Direcionar a ação de parada por lista de VMs

1. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro ACTION definido como **iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e defina o parâmetro WHATIF como **True**. Visualize as alterações.  
2. Configure o parâmetro **External_ExcludeVMNames** com uma lista de VMs separadas por vírgula (VM1,VM2,VM3).
3. Esse cenário não segue as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames**.  Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agendamento de runbooks na Automação do Azure](../automation/automation-schedules.md).

Agora que você tem um agendamento para parar VMs com base na utilização da CPU, é preciso habilitar um dos agendamento abaixo para iniciá-las.

* Direcione a ação de início por assinatura e grupo de recursos.  Consulte as etapas no [Cenário 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) para testar e habilitar agendamentos **Scheduled-StartVM**.
* Direcionar ação de início por assinatura, grupo de recursos e marca.  Consulte as etapas em [Cenário 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) para testar e habilitar agendamentos **Sequenced-StartVM**.


### <a name="configuring-email-notifications"></a>Configurando notificações por email

Para configurar notificações por email após a implantação da solução, modifique estas três variáveis:

* External_EmailFromAddress: especificar o endereço de email do remetente.
* External_EmailToAddress: especificar uma lista de endereços de email separados por vírgula (user@hotmail.com, user@outlook.com) para receber emails de notificação.
* External_IsSendEmail: definir como **Sim** para receber emails. Para desabilitar notificações por email, defina o valor como **Não**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modificando as agendas de inicialização e desligamento

O gerenciamento das agendas de inicialização e desligamento nesta solução segue as mesmas etapas descritas em [Agendando um runbook na Automação do Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Registros do Log Analytics

A Automação cria dois tipos de registros no repositório do OMS: logs de trabalho e fluxos de trabalho.

### <a name="job-logs"></a>Logs de trabalho

Propriedade | DESCRIÇÃO|
----------|----------|
Chamador |  Quem iniciou a operação.  Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automação, o valor é JobLogs.|
CorrelationId | O GUID que é a Id de correlação do trabalho de runbook.|
JobId | GUID que é a Id do trabalho de runbook.|
operationName | Especifica o tipo de operação realizada no Azure.  Para a Automação, o valor é Job.|
ResourceId | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que você pode implantar e gerenciar.  Para Automação, o valor é Automação do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
resultType | O status do trabalho de runbook.  Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>- Êxito|
resultDescription | Descreve o estado de resultado do trabalho de runbook.  Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído|
RunbookName | Especifica o nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados enviados.  Para a Automação, o valor é OpsManager|
StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Detalhado<br>- Saída<br>- Erro<br>- Aviso|
SubscriptionId | Especifica a ID da assinatura do trabalho.
Hora | Data e hora da execução do trabalho de runbook.|


### <a name="job-streams"></a>Transmissões de trabalho

Propriedade | DESCRIÇÃO|
----------|----------|
Chamador |  Quem iniciou a operação.  Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automação, o valor é JobStreams.|
JobId | GUID que é a Id do trabalho de runbook.|
operationName | Especifica o tipo de operação realizada no Azure.  Para a Automação, o valor é Job.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
ResourceId | Especifica o tipo de ID de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que você pode implantar e gerenciar.  Para Automação, o valor é Automação do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
resultType | O resultado do trabalho de runbook no momento em que o evento foi gerado. Um valor possível é:<br>- InProgress|
resultDescription | Inclui o fluxo de saída do runbook.|
RunbookName | O nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados enviados.  Para a Automação, o valor é OpsManager.|
StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado|
Hora | Data e hora da execução do trabalho de runbook.|

Quando você executa uma pesquisa de logs que retorna registros da categoria de **JobLogs** ou **JobStreams**, pode selecionar a exibição **JobLogs** ou **JobStreams**, que exibe um conjunto de blocos resumindo as atualizações retornadas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

A tabela a seguir fornece pesquisas de log de exemplo para os registros de alerta coletados por essa solução. 

Consultar | DESCRIÇÃO|
----------|----------|
Localizar trabalhos para o runbook ScheduledStartStop_Parent que foram finalizados com êxito | pesquisar Category == "JobLogs" &#124; onde ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; onde ( ResultType == "Concluído" )  &#124; resuma AggregatedValue = count() por ResultType, bin(TimeGenerated, 1h) &#124; classifique por TimeGenerated desc|
Localizar trabalhos para o runbook SequencedStartStop_Parent que foram finalizados com êxito | pesquisar Category == "JobLogs" &#124; onde ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; onde ( ResultType == "Concluído" )  &#124; resuma AggregatedValue = count() por ResultType, bin(TimeGenerated, 1h) &#124; classifique por TimeGenerated desc

## <a name="removing-the-solution"></a>Removendo a solução

Se decidir que não precisa mais usar a solução, você poderá excluí-la da conta de Automação.  A exclusão da solução remove apenas os runbooks. Ela não exclui os agendamentos ou as variáveis que foram criadas quando a solução foi adicionada.  Esses ativos deverão ser excluídos manualmente se não estiverem sendo usados com outros runbooks.  

Para excluir a solução, execute as etapas a seguir:

1.  Na sua conta de Automação, selecione **Espaço de Trabalho** no painel esquerdo.  
2.  Na página **Soluções**, selecione a solução **Start-Stop-VM[Workspace]**.  Na página **VMManagementSolution[Workspace]**, no menu, selecione **Excluir**.<br><br> ![Excluir a Solução de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Na janela **Excluir Solução**, confirme que deseja excluir a solução.
4.  Enquanto as informações são verificadas e a solução é excluída, você pode acompanhar seu progresso no menu **Notificações**.  Você é levado de volta à página **Soluções** após o início do processo de remoção da solução.  

A conta de Automação e o espaço de trabalho do Log Analytics não serão excluídos como parte desse processo.  Se você não deseja manter o espaço de trabalho do Log Analytics, é necessário excluí-lo manualmente.  Isso pode ser feito no portal do Azure:

1.    Na tela inicial do Portal do Azure, selecione **Log Analytics**.
2. No painel **Log Analytics**, selecione o espaço de trabalho.
3. Selecione **Excluir** no menu do painel de configurações do espaço de trabalho.  
      
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como criar consultas de pesquisa diferentes e examinar os logs de trabalho da Automação com o Log Analytics, confira [Pesquisas de logs no Log Analytics](../log-analytics/log-analytics-log-searches.md).
- Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md).
- Para saber mais sobre o Log Analytics e fontes de coleta de dados, confira [Coletar dados do Armazenamento do Azure na visão geral do Log Analytics](../log-analytics/log-analytics-azure-storage.md).






   


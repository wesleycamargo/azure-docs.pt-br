---
title: "Solução Iniciar/Parar VMs fora do horário comercial | Microsoft Docs"
description: "As soluções de Gerenciamento de VM iniciam e param suas Máquinas Virtuais do Azure Resource Manager com agendamento e as monitoram proativamente no Log Analytics."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solução Iniciar/Parar VMs fora do horário comercial na Automação do Azure

A solução Iniciar/Parar VMs fora do horário comercial inicia e interrompe as máquinas virtuais do Azure em agendamentos definidos pelo usuário, fornece informações por meio de Log Analytics e envia emails opcionais utilizando [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). A solução dá suporte ao Azure Resource Manager e VMs clássicas na maioria dos cenários. 

A solução oferece um recurso de automação descentralizado para clientes que desejam reduzir custos aproveitando recursos de baixo custo sem servidor. Os recursos incluem:

* Programar VMs para iniciar/parar
* Programar VMs para iniciar/parar em ordem crescente usando marcas do Azure (incompatível com VMs clássicas)
* Parar VMs automaticamente com base no baixo uso da CPU

## <a name="prerequisites"></a>Pré-requisitos

- O runbook funciona com uma [conta Executar como do Azure](automation-offering-get-started.md#authentication-methods).  A conta Executar como é o método de autenticação preferido, pois ela usa a autenticação de certificado em vez de uma senha que pode expirar ou ser alterada com frequência.  

- Essa solução só pode gerenciar VMs que estejam na mesma assinatura em que reside a conta de Automação.  

- A solução só pode ser implantada nas seguintes regiões do Azure: Sudeste da Austrália, Canadá Central, Índia Central, Leste dos EUA, Leste do Japão, Sudeste da Ásia, Sul do Reino Unido e Europa Ocidental.  
    
    > [!NOTE]
    > Os runbooks que gerenciam o agendamento de VMs podem direcionar para VMs em qualquer região.  

- Para enviar notificações por email após a conclusão dos runbooks de inicialização e parada de VMs, durante a integração no Azure Marketplace, selecione **Sim** para implantar o SendGrid. 

    > [!IMPORTANT]
    > SendGrid é um serviço de terceiros; para obter suporte com o SendGrid, entre em contato com [SendGrid](https://sendgrid.com/contact/).  
    >
   
    As limitações com o SendGrid são as seguintes:

    * Máximo de uma conta do SendGrid por usuário por assinatura
    * Máximo de duas contas do SendGrid por usuário por assinatura

Se você implantou uma versão anterior da solução, será necessário primeiro excluí-la da sua conta antes de implantar a versão atual.  

## <a name="solution-components"></a>Componentes da solução

A solução inclui runbooks pré-configurados, agendamentos e integração com Log Analytics que permitem personalizar a inicialização e o desligamento das máquinas virtuais para atender às suas necessidades de negócios. 

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks implantados em sua conta de Automação.  Não é recomendável que você faça alterações no código de runbook; em vez disso, compile seu próprio runbook para uma nova funcionalidade.

> [!NOTE]
> Não execute nenhum runbook diretamente com o nome “Filho” acrescentado ao final do nome.
>

Todo runbook pai inclui o parâmetro *WhatIf*, que, quando definido como **Verdadeiro**, dá suporte ao detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro *WhatIf* e valida as VMs corretas que estão sendo direcionadas.  Os runbooks executam apenas suas ações definidas quando o parâmetro *WhatIf* é definido como **Falso**. 

|**Runbook** | **Parâmetros** | **Descrição**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook pai somente. Cria alertas por recurso para o cenário AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: Verdadeiro ou Falso <br> VMList | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> VMList: lista de VMs separadas por vírgula.  Por exemplo, *vm1,vm2,vm3*| 
|AutoStop_Disable | nenhum | Desabilite os alertas de AutoStop e o cronograma padrão.| 
|AutoStop_StopVM_Child | WebHookData | Chamada a partir do runbook pai somente. As regras de alerta fazem chamadas a este runbook, que, por sua vez, para a VM.|  
|Bootstrap_Main | nenhum | Usado uma vez para definir configurações de inicialização, como webhookURI, que normalmente não pode ser acessada no Azure Resource Manager. Esse runbook será removido automaticamente se a implantação for concluída com êxito.|  
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Chamada a partir do runbook pai somente. Faz a execução real de parar ou iniciar nas paradas agendadas.|  
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso | Isso terá efeito em todas as VMs na assinatura, a menos que você altere **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**, que ficará restrito à execução apenas nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a variável **External_ExcludeVMNames**. WhatIf se comporta da mesma forma como em outros runbooks.|  
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso | Crie uma marca chamada **SequenceStart** e outra chamada **SequenceStop** em cada VM na qual você deseja aplicar o sequenciamento da atividade iniciar\\parar. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem em que você deseja iniciar\\parar em sentido crescente. WhatIf se comporta da mesma forma como em outros runbooks. <br> **Observação: as VMs devem fazer parte dos grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis de Automação do Azure e conter as marcas apropriadas para que as ações entrem em vigor.**|

### <a name="variables"></a>variáveis

A tabela a seguir lista as variáveis criadas em sua conta de Automação.  É recomendável que você modifique apenas variáveis prefixadas com **External**; modificar variáveis prefixadas com **Internal** provocará efeitos indesejáveis.  

|**Variable** | **Descrição**|
---------|------------|
|External_AutoStop_Condition | Este é o operador condicional exigido para configurar a condição antes de disparar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**.|  
|External_AutoStop_Description | Alerta para parar a VM se o percentual da CPU exceder o limite.|  
|External_AutoStop_MetricName | Nome da métrica de desempenho na qual a regra de alerta do Azure deverá ser configurada.| 
|External_AutoStop_Threshold | Limite para a regra de alerta do Azure especificada na variável *External_AutoStop_MetricName*. Os valores percentuais podem variar de 1 a 100.|  
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo que será aplicado ao tamanho de janela selecionado para avaliar a condição. Os valores aceitáveis são **Médio**, **Mínimo**, **Máximo**, **Total**, **Último**.|  
|External_AutoStop_TimeWindow | O tamanho da janela com base no qual o Azure analisará a métrica selecionada para disparar alertas. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de cinco minutos a seis horas.|  
|External_EmailFromAddress | Especifica o remetente do email.|  
|External_EmailSubject | Especifica o texto da linha de assunto do email.|  
|External_EmailToAddress | Especifica os destinatários do email. Separe os nomes por vírgula.|  
|External_ExcludeVMNames | Insira os nomes das VMs a serem excluídas, separando-os por vírgula, sem espaços.|  
|External_IsSendEmail | Especifica a opção para enviar notificações por email após a conclusão.  Especifique **Sim** ou **Não** para enviar emails.  Esta opção deve ser **Não** se você não criou um SendGrid durante a implantação inicial.|  
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações Iniciar.|  
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores por vírgula, direcionados a ações Iniciar.|  
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|  
|Internal_AutoSnooze_WebhookUri | Especifica o URI do Webhook chamado para o cenário AutoStop.|  
|Internal_AzureSubscriptionId | Specifies a ID da assinatura do Azure.|  
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta de automação do Azure.|  
|Internal_SendGridAccountName | Especifica o nome da conta do SendGrid.|  
|Internal_SendGridPassword | Especifica a senha da conta do SendGrid.|  

<br>

Em todos os cenários, as variáveis **External_Start_ResourceGroupNames**,  **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** são necessárias para direcionar VMs, com exceção da disponibilização de uma lista de VMs separadas por vírgula para o runbook **AutoStop_CreateAlert_Parent**. Em outras palavras, suas VMs devem residir em grupos de recursos de destino para que as ações iniciar/parar ocorram. A lógica funciona mais ou menos como a política do Azure, isto é, você pode direcionar-se à assinatura ou ao grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar/parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.  É possível modificá-las ou criar suas próprias agendas personalizadas.  Por padrão, cada uma dessas agendas é desabilitada, exceto **Scheduled_StartVM** e **Scheduled-StopVM**.

Não é recomendável habilitar todas as agendas, pois isso poderia criar uma sobreposição com base na qual a agenda executaria uma ação.  Em vez disso, seria melhor determinar quais otimizações você deseja executar e modificar de acordo.  Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.   

|**ScheduleName** | **Frequência** | **Descrição**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | É executada a cada oito horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, que, por sua vez, interromperá os valores baseados em VMs em “External_Start_ResourceGroupNames”, “External_Stop_ResourceGroupNames” e “External_ExcludeVMNames” nas variáveis de automação do Azure.  Como alternativa, você pode especificar uma lista de VMs separadas por vírgula utilizando o parâmetro “VMList”.|  
|Scheduled_StopVM | Definido pelo usuário, todos os dias | Executa o runbook Scheduled_Parent com um parâmetro de “Parar” todos os dias no horário determinado.  Interrompe automaticamente todas as VMs que atendem às regras definidas por meio de variáveis de Ativos.  Recomendamos habilitar a agenda irmã Scheduled-StartVM.|  
|Scheduled_StartVM | Definido pelo usuário, todos os dias | Executa o runbook Scheduled_Parent com um parâmetro de *Iniciar* todos os dias no horário determinado.  Inicia automaticamente todas as VMs que atendem às regras definidas e parte das variáveis apropriadas.  É recomendável habilitar a agenda irmã **Scheduled-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o runbook Scheduled_Parent com um parâmetro de *Parar* toda sexta-feira no horário determinado.  Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida e parte das variáveis apropriadas.  Consulte a seção de Runbooks para obter mais detalhes sobre valores de marcas e variáveis de ativos.  É recomendável habilitar a agenda irmã **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o runbook Scheduled_Parent com um parâmetro de *Parar* toda segunda-feira no horário determinado.  Para sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida e parte das variáveis apropriadas.  Consulte a seção de Runbooks para obter mais detalhes sobre valores de marcas e variáveis de ativos.  É recomendável habilitar a agenda irmã **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para adicionar a solução Iniciar/Parar VMs fora do horário comercial [Visualização] à sua conta de Automação e configure as variáveis para personalizar a solução.

1. No portal do Azure, clique em **Novo**.<br> ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. No painel Mercado, digite **Iniciar VM**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Iniciar/Parar VMs fora do horário comercial [Visualização]** nos resultados da pesquisa.  
3. No painel **Iniciar/Parar VMs fora do horário comercial [Visualização]** da solução selecionada, analise as informações de resumo e clique em **Criar**.  
4. O painel **Adicionar Solução** é exibido com a instrução para configurar a solução antes de importá-la para a sua assinatura de Automação.<br><br> ![Folha Adicionar Solução do Gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Na folha **Adicionar Solução**, selecione **Espaço de Trabalho** e selecione um espaço de trabalho do OMS que seja vinculado à mesma assinatura do Azure em que a conta de Automação se encontra ou crie um novo espaço de trabalho.  Caso não tenha um espaço de trabalho, você poderá selecionar **Criar Novo Espaço de Trabalho** e, no painel **Espaço de Trabalho do OMS**, fazer o seguinte: 
   - Especifique um nome para o novo **Espaço de Trabalho do OMS**.
   - Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
   - Em **Grupo de Recursos**, você pode selecionar um grupo de recursos existente ou criar um novo.  
   - Selecione um **Local**.  No momento, estes são os únicos locais disponibilizados para seleção: **Sudeste da Austrália**, **Canadá Central**, **Índia Central**, **Leste dos EUA**, **Leste do Japão**, **Sudeste da Ásia**, **Sul do Reino Unido** e **Europa Ocidental**.
   - Selecione um **tipo de preço**.  A solução é oferecida em duas camadas: camada paga OMS e gratuita.  A camada gratuita tem um limite de quantidade de dados coletados diariamente, de período de retenção e de minutos de tempo de execução do trabalho de runbook.  A camada paga do OMS não tem um limite de quantidade de dados coletados diariamente.  

        > [!NOTE]
        > Embora a camada paga Por GB (Autônoma) seja exibida como uma opção, ela não se aplica.  Se você selecioná-la e prosseguir com a criação dessa solução em sua assinatura, ocorrerá uma falha.  Essa questão será abordada quando a solução for lançada oficialmente.<br>Se você usar essa solução, ela só usará minutos de trabalho de automação e de ingestão de log.  A solução não adiciona outros nós do OMS ao seu ambiente.  

6. Depois de fornecer as informações necessárias na folha **Espaço de trabalho do OMS**, clique em **Criar**.  Enquanto as informações são verificadas e o espaço de trabalho é criado, você pode acompanhar seu progresso no menu **Notificações**.  Você será levado para a folha **Adicionar Solução**.  
7. Na folha **Adicionar Solução**, selecione **Conta de Automação**.  Se você estiver criando um novo espaço de trabalho do OMS, será necessário criar uma nova conta de Automação que será associada ao novo espaço de trabalho do OMS especificado anteriormente, incluindo a assinatura do Azure, o grupo de recursos e a região.  Selecione **Criar uma conta de Automação** e, na folha **Adicionar Conta de Automação**, forneça o seguinte: 
  - No campo **Nome**, digite o nome da conta de Automação.

    Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do OMS selecionado, e essas opções não podem ser modificadas.  Uma conta Executar como do Azure é o método de autenticação padrão para os runbooks incluídos nesta solução.  Após clicar em **OK**, as opções de configuração são validadas e a conta de Automação é criada.  Você pode acompanhar o progresso em **Notificações** no menu. 

    Caso contrário, você pode selecionar uma conta Executar como de Automação existente.  Observe que a conta selecionada não pode já estar vinculada a outro espaço de trabalho do OMS; caso contrário, uma mensagem aparecerá na folha para informá-lo.  Se já estiver vinculada, você precisará selecionar uma conta Executar como de Automação diferente ou criar uma nova.<br><br> ![Conta de Automação já vinculada ao Espaço de Trabalho do OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por fim, na folha **Adicionar Solução**, selecione **Configuração** e a folha **Parâmetros** será exibida.<br><br> ![Painel de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Na folha **Parâmetros**, você será solicitado a:  
   - Especifique os **Nomes de Grupo de Recursos de Destino**, que é um nome de grupo de recursos que contém VMs a serem gerenciadas pela solução.  Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas).  O uso de um caractere curinga tem suporte para selecionar VMs em todos os grupos de recursos na assinatura.
   - Especifique a **Lista (cadeia de caracteres) Exclusão de VMs**, que é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino.  Você pode inserir mais de um nome e separá-los por vírgula (os valores não diferenciam maiúsculas de minúsculas).  O uso de caracteres curingas é aceito.
   - Selecione uma **Agenda**, que é um conjunto de data e hora para iniciar e parar as VMs no grupo de recursos de destino.  Por padrão, o agendamento é configurado para o fuso horário UTC e a seleção de outra região não está disponível.  Se quiser configurar o agendamento para seu fuso horário específico após a configuração da solução, confira [Modificando o agendamento de inicialização e desligamento](#modifying-the-startup-and-shutdown-schedule) abaixo.
   - Para receber **Notificações por Email** do SendGrid, aceite o valor padrão de **Sim** e forneça um endereço de email válido.  Se você selecionar **Não** e decidir posteriormente que deseja receber notificações por email, será necessário reimplantar a solução no marketplace do Azure.  

10. Depois de ter concluído a configuração inicial necessária para a solução, selecione **Criar**.  Todas as configurações serão validadas e ele tentará implantar a solução em sua assinatura.  Esse processo pode levar vários segundos e você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="collection-frequency"></a>Frequência de coleta

O log de trabalho de automação e os dados de fluxo de trabalho são ingeridos no repositório do Log Analytics a cada cinco minutos.  

## <a name="using-the-solution"></a>Usando a solução

Ao adicionar a solução de Gerenciamento de VM, no seu espaço de trabalho do Log Analytics, o bloco **StartStopVM View** será adicionado ao painel.  Esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbooks para soluções iniciadas e concluídas com êxito.<br><br> ![Bloco de exibição StartStopVM de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Na sua conta de Automação, é possível acessar e gerenciar a solução selecionando a opção **Espaço de Trabalho** e, na página Log Analytics, selecione **Soluções** no painel esquerdo.  Na página **Soluções**, selecione a solução **Start-Stop-VM[Workspace]** na lista.<br><br> ![Lista Soluções no Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

A escolha da solução exibirá a folha da solução **Start-Stop-VM[Workspace]**, onde você pode examinar detalhes importantes, por exemplo, o bloco **StartStopVM**, como no espaço de trabalho do Log Analytics, que exibe a contagem e a representação gráfica dos trabalhos de runbooks para a solução iniciada e concluída com êxito.<br><br> ![Página da solução Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Aqui você pode também executar análises adicionais dos registros de trabalho clicando no bloco de rosca e, no painel de soluções, visualizar o histórico de trabalho e consultas de pesquisas de log predefinidas e alternar para o portal Log Analytics Avançado para pesquisar com base em suas consultas de pesquisa.  

Todo runbook pai inclui o parâmetro *WhatIf*, que, quando definido como **Verdadeiro**, dá suporte ao detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro *WhatIf* e valida as VMs corretas que estão sendo direcionadas.  Os runbooks executam apenas suas ações definidas quando o parâmetro *WhatIf* é definido como **Falso**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Cenário 1: parar/iniciar VMs diariamente em uma assinatura ou grupos de recursos de destino 

Essa é a configuração padrão quando a solução é implantada pela primeira vez.  Por exemplo, você pode configurar a solução para parar todas as VMs em uma assinatura à noite ao sair do trabalho e iniciá-las pela manhã ao retornar ao escritório. Quando configuradas durante a implantação, as agendas **Scheduled-StartVM" e **Scheduled-StopVM** iniciam e param VMs de destino.
>[!NOTE]
>O fuso horário é o seu fuso horário atual de quando o parâmetro de hora agendada foi configurado; no entanto, o parâmetro é armazenado no formato UTC na automação do Azure.  Não é necessário executar nenhuma conversão de fuso horário, pois isso acontece durante a implantação.

Para controlar quais VMs se encontram no escopo, configure as duas variáveis - **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames e **External_ExcludeVMNames**.  

>[!NOTE]
> O valor da variável **Target ResourceGroup Names**" é armazenado como o valor das variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Para obter uma maior granularidade, você pode modificar cada uma das variáveis para direcionar diferentes grupos de recursos.  Para iniciar a ação, use **External_Start_ResourceGroupNames** e, para parar a ação, use, em vez disso, **External_Stop_ResourceGroupNames**. As novas VMs são adicionadas automaticamente às agendas de início e parada.

Para testar e validar a configuração, inicie manualmente o runbook **ScheduledStartStop_Parent** e defina o parâmetro *ACTION* para **iniciar** ou **parar**  e o parâmetro *WhatIf* como **verdadeiro**.<br><br> ![Configurar parâmetros para runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Isso permite visualizar a ação que deveria ocorrer e fazer alterações conforme necessário antes de implementá-las nas VMs de produção.  Quando estiver familiarizado, você poderá executar o runbook manualmente com o parâmetro definido como **falso** ou permitir que o agendamento de Automação **Schedule-StartVM** e **Schedule-StopVM** seja executado automaticamente após sua agenda prescrita.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Cenário 2: sequenciar a ação parar/iniciar VMs em uma assinatura usando marcas
Em um ambiente que inclui dois ou mais componentes em várias VMs que aceitam cargas de trabalho distribuídas, é importante dar suporte ao sequenciamento de quais componentes são iniciados/interrompidos em ordem.  Para fazer isso, siga as etapas abaixo.

1. Adicione uma marca **SequenceStart** e **SequenceStop** com um valor inteiro positivo às VMs em sua assinatura que são direcionadas nas variáveis **External_Start_ResourceGroupNames**e  **External_Stop*ResourceGroupNames**.  As ações iniciar e parar serão executadas em ordem crescente.  Para saber como marcar uma VM, consulte [Marcar uma Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Marcar uma Máquina Virtual do Linux no Azure](../virtual-machines/linux/tag.md)
2. Modifique os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** para a data e hora que atendem às suas exigências e habilite a agenda.  

Para testar e validar a configuração, inicie manualmente o runbook **ScheduledStartStop_Parent** e defina o parâmetro *ACTION* como **iniciar** ou **parar**  e o parâmetro *WhatIf* como **Verdadeiro**.<br><br> ![Configurar parâmetros para runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Isso permite visualizar a ação que deveria ocorrer e fazer alterações conforme necessário antes de implementá-las nas VMs de produção.  Quando estiver familiarizado, você poderá executar o runbook manualmente com o parâmetro definido como **falso** ou permitir que o agendamento de Automação **Sequenced-StartVM** e **Sequenced-StopVM** seja executado automaticamente após sua agenda prescrita.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Cenário 3: parar/iniciar VMs automaticamente em uma assinatura com base na utilização da CPU
Para ajudar a gerenciar o custo de execução de VMs em sua assinatura, a solução avalia as VMs do Azure que não são usadas durante os períodos mais calmos, por exemplo, depois do horário comercial, e automaticamente as desliga se a utilização do processador for menor que x%.  

Por padrão, a solução é pré-configurada para avaliar a métrica percentual da CPU e se a utilização média é de 5% ou menos.  Isso é controlado pelas seguintes variáveis e pode ser modificado se seus valores padrão não atenderem às suas exigências:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Para habilitar, você só pode direcionar a ação a uma assinatura e a um grupo de recursos ou a uma lista específica de VMs, mas não a ambos.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Direcione a ação de parada a uma assinatura e grupo de recursos

1. Configure as variáveis **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar as VMs de destino.  
2. Habilite e atualize a agenda **Schedule_AutoStop_CreateAlert_Parent**.
3. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro *ACTION* definido como **iniciar** e o parâmetro *WhatIf* definido como **Verdadeiro** para visualizar as alterações.

#### <a name="target-stop-action-by-vm-list"></a>Ação de parada de destino por lista de VMs

1. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro *ACTION* definido como **iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e defina o parâmetro *WhatIf* como **Verdadeiro** para visualizar as alterações.  
2. Configure o parâmetro **External_ExcludeVMNames** com uma lista de VMs separadas por vírgula (VM1,VM2,VM3).
3. Esse cenário não segue as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames**.  Para este cenário, será necessário criar seu próprio agendamento de Automação. Para obter detalhes, consulte [Agendamento de runbooks na automação do Azure](../automation/automation-schedules.md).

Agora que você tem uma agenda para parar as VMs com base na utilização da CPU, é preciso habilitar uma das agendas abaixo para iniciá-las.

* Ação de início de destino por Assinatura e Grupo de recursos.  Consulte as etapas do [Cenário 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) para testar e habilitar a agenda **Scheduled-StartVM**.
* Ação de início de destino por Assinatura, Grupo de recursos e Marca.  Consulte as etapas do [Cenário 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) para testar e habilitar a agenda "Sequenced-StartVM".


### <a name="configuring-e-mail-notifications"></a>Configurando notificações por email

Para configurar notificações por email após a implantação da solução, você pode modificar estas três variáveis:

* External_EmailFromAddress - especifique o endereço de email do remetente
* External_EmailToAddress - uma lista de endereços de email separados por vírgula (user@hotmail.com, user@outlook.com) para receber emails de notificação
* External_IsSendEmail - Se definida como **Sim**, você receberá emails e, para desabilitar as notificações por email, defina o valor como **Não**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modificando as agendas de inicialização e desligamento

O gerenciamento das agendas de inicialização e desligamento nesta solução segue as mesmas etapas descritas em [Agendando um runbook na Automação do Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Registros do Log Analytics

A Automação cria dois tipos de registros no repositório do OMS.

### <a name="job-logs"></a>Logs de trabalho

Propriedade | Descrição|
----------|----------|
Chamador |  Quem iniciou a operação.  Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automação, o valor é JobLogs.|
CorrelationId | O GUID que é a Id de correlação do trabalho de runbook.|
JobId | GUID que é a Id do trabalho de runbook.|
operationName | Especifica o tipo de operação realizada no Azure.  Para a Automação, o valor será Trabalho.|
resourceId | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que você pode implantar e gerenciar.  Para Automação, o valor é Automação do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
resultType | O status do trabalho de runbook.  Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>- Êxito|
resultDescription | Descreve o estado de resultado do trabalho de runbook.  Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído|
RunbookName | Especifica o nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados enviados.  Em Automação, o valor será :OpsManager|
StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Detalhado<br>- Saída<br>- Erro<br>- Aviso|
SubscriptionId | Especifica a ID da assinatura do trabalho.
Hora | Data e hora da execução do trabalho de runbook.|


### <a name="job-streams"></a>Transmissões de trabalho

Propriedade | Descrição|
----------|----------|
Chamador |  Quem iniciou a operação.  Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automação, o valor é JobStreams.|
JobId | GUID que é a Id do trabalho de runbook.|
operationName | Especifica o tipo de operação realizada no Azure.  Para a Automação, o valor será Trabalho.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
resourceId | Especifica o tipo de ID de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que você pode implantar e gerenciar.  Para Automação, o valor é Automação do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automação, o valor é a conta da Automação associada ao runbook.|
resultType | O resultado do trabalho de runbook no momento em que o evento foi gerado.  Os valores possíveis são:<br>- InProgress|
resultDescription | Inclui o fluxo de saída do runbook.|
RunbookName | O nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados enviados.  Em Automação, o valor será :OpsManager|
StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado|
Hora | Data e hora da execução do trabalho de runbook.|

Quando você executa uma pesquisa de log que retorna registros de categoria de **JobLogs** ou **JobStreams**, pode selecionar a exibição **JobLogs** ou **JobStreams** que exibe um conjunto de blocos resumindo as atualizações retornadas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

A tabela a seguir fornece pesquisas de log de exemplo para os registros de alerta coletados por essa solução. 

Consultar | Descrição|
----------|----------|
Localizar trabalhos referentes ao runbook ScheduledStartStop_Parent concluídos com êxito | pesquisar Category == "JobLogs" &#124; onde ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; onde ( ResultType == "Concluído" )  &#124; resuma AggregatedValue = count() por ResultType, bin(TimeGenerated, 1h) &#124; classifique por TimeGenerated desc|
Localizar trabalhos referentes ao runbook SequencedStartStop_Parent concluídos com êxito | pesquisar Category == "JobLogs" &#124; onde ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; onde ( ResultType == "Concluído" )  &#124; resuma AggregatedValue = count() por ResultType, bin(TimeGenerated, 1h) &#124; classifique por TimeGenerated desc

## <a name="removing-the-solution"></a>Removendo a solução

Se você decidir que não precisa mais usar a solução, você pode excluí-la da conta de Automação.  Excluir a solução só removerá os runbooks, não excluirá as agendas ou variáveis que foram criadas quando a solução foi adicionada.  Esses ativos precisarão ser excluídos manualmente se não estiverem sendo usados com outros runbooks.  

Para excluir a solução, execute as etapas a seguir:

1.  Na sua conta de Automação, selecione **Espaço de Trabalho** no painel esquerdo.  
2.  Na página **Soluções**, selecione a solução **Start-Stop-VM[Workspace]**.  Na página **VMManagementSolution[Workspace]**, no menu, clique em **Excluir**.<br><br> ![Excluir a Solução de Gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Na janela **Excluir solução**, confirme que deseja excluir a solução.
4.  Enquanto as informações são verificadas e a solução é excluída, você pode acompanhar seu progresso no menu **Notificações**.  Você retornará à página **Soluções** após o processo de remoção da solução iniciar.  

A conta de Automação e o espaço de trabalho do Log Analytics não serão excluídos como parte desse processo.  Se você não deseja manter o espaço de trabalho do Log Analytics, será necessário excluí-lo manualmente.  Isso também pode ser feito no portal do Azure.   Na tela inicial do portal do Azure, selecione **Log Analytics** e, em seguida, na folha **Log Analytics**, selecione o espaço de trabalho e clique em **Excluir** no menu na folha configurações do espaço de trabalho.  
      
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como construir consultas de pesquisa diferentes e examinar os logs de trabalho de Automação com o Log Analytics, confira [Efetuar pesquisas no Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md)
- Para saber mais sobre o Log Analytics e fontes de coleta de dados, confira [Coletar dados do Armazenamento do Azure na visão geral do Log Analytics](../log-analytics/log-analytics-azure-storage.md)






   


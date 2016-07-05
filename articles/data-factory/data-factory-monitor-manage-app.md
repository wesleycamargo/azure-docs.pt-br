<properties 
	pageTitle="Monitorar e gerenciar pipelines do Azure Data Factory" 
	description="Saiba como usar o Aplicativo de Monitoramento e Gerenciamento para monitorar e gerenciar data factories e pipelines do Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/20/2016" 
	ms.author="spelluru"/>

# Monitorar e gerenciar os pipelines do Azure Data Factory usando novo Aplicativo de Monitoramento e Gerenciamento
> [AZURE.SELECTOR]
- [Usando o Portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md)

Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o **Aplicativo de Monitoramento e Gerenciamento**. Você também pode assistir ao vídeo a seguir para saber mais sobre como usar o aplicativo de gerenciamento e monitoramento.
   

> [AZURE.VIDEO azure-data-factory-monitoring-and-managing-big-data-piplines]
      
## Iniciando o Aplicativo de Monitoramento e Gerenciamento a
Para iniciar o Aplicativo de Monitoramento e Gerenciamento, clique no bloco **Monitoramento e Gerenciamento** na folha **DATA FACTORY** do seu data factory.

![Bloco Monitoramento na home page do Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Você verá que o Aplicativo de Monitoramento e Gerenciamento foi iniciado em uma guia/janela separada.

![Aplicativo de Monitoramento e Gerenciamento](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] Se você vir que o navegador da Web está bloqueado em "Autorizando...", desabilite/desmarque a configuração **Bloquear cookies de terceiros e dados de site** (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o aplicativo novamente.


Se não vir janelas de atividade na lista na parte inferior, clique no botão **Atualizar** na barra de ferramentas para atualizar a lista. Além disso, defina os valores corretos para os filtros **Hora de início** e **Hora de término**.


## Noções básicas sobre o Aplicativo de Monitoramento e Gerenciamento
Há três guias (**Gerenciador de Recursos**, **Exibições de Monitoramento** e **Alertas**) à esquerda e a primeira guia (Gerenciador de Recursos) é selecionada por padrão.

### Gerenciador de Recursos
Você vê o **modo de exibição de árvore** do Gerenciador de Recursos no painel esquerdo, **Exibição de Diagrama** na parte superior e a lista **Janelas de Atividades** na parte inferior do painel intermediário e as guias **Propriedades**/**Gerenciador de Janelas de Atividade** no painel direito.

Você pode ver todos os recursos (pipelines, conjuntos de dados, serviços vinculados) no data factory em um modo de exibição de árvore. Ao selecionar um objeto no Gerenciador de Recursos, você notará o seguinte:

- a entidade Data Factory associada está realçada na Exibição de Diagrama.
- as janelas de atividades associadas (clique [aqui](data-factory-scheduling-and-execution.md) para saber mais sobre as janelas de atividades) estão realçadas na lista Janelas de Atividades na parte inferior.  
- as propriedades do objeto selecionado na janela Propriedades no painel direito. 
- Definição de JSON do objeto selecionado, se aplicável. Por exemplo: um serviço vinculado, um conjunto de dados ou um pipeline. 

![Gerenciador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) para obter informações conceituais detalhadas sobre a janela de atividades.

### Exibição de diagrama
O Modo de exibição de diagrama de uma data factory fornece um único painel onde você pode monitorar e gerenciar a data factory e seus ativos. Ao selecionar uma entidade Data Factory (conjunto de dados/pipeline) na exibição de diagrama, você observará o seguinte:
 
- a entidade Data Factory é selecionada no modo de exibição de árvore
- as janelas de atividades associadas são destacadas na lista Janelas de Atividades.
- as propriedades do objeto selecionado na janela Propriedades

Quando o pipeline for habilitado (isto é, quando não estiver em estado pausado), será mostrado com uma linha verde, como mostrado abaixo.

![Execução do Pipeline](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Observe que há três botões de comando para o pipeline na exibição de diagrama. É possível usar o segundo botão para pausar o pipeline. Isso não encerrará as atividades atualmente em execução e as deixará continuar até a conclusão. O terceiro botão pausará o pipeline e encerrará as atividades de execução existentes. O primeiro botão retoma o pipeline, isto é, cancela a pausa. Quando o pipeline for pausado, você observará a mudança de cor do bloco do pipeline, como a seguir.

![Pausar/Retomar no bloco](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Você pode selecionar dois ou mais pipelines (usando CTRL) e usar os botões da barra de comandos para pausar/retomar vários pipelines por vez.

![Pausar/Retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

É possível ver todas as atividades no pipeline ao clicar com o botão direito do mouse no bloco do pipeline e clicar em **Abrir pipeline**.

![Abrir menu Pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na exibição aberta do pipeline, você verá todas as atividades no pipeline. Neste exemplo, há apenas uma atividade: Copiar Atividade. Para voltar para a exibição anterior, clique no nome do data factory, no menu da barra de endereços na parte superior.

![Pipeline Aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Na exibição fechada/aberta do pipeline, ao clicar em um conjunto de dados de saída e mover seu mouse sobre o conjunto de dados, você verá o pop-up Janelas de Atividades do conjunto de dados em questão.

![Pop-up Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

É possível clicar em uma janela de atividade para ver detalhes dela na janela **Propriedade** no painel direito.

![Propriedades da janela de atividades](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel direito, alterne para a guia **Gerenciador de Janelas de Atividades** para ver mais detalhes.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Você também verá as **variáveis resolvidas** para cada tentativa de execução de atividade na seção **Tentativas**.

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Alterne para a guia **Script** para ver a definição do script JSON para o objeto selecionado.

![Tabela de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Você pode ver janelas de atividades em três locais:

- Pop-up das Janelas de Atividades na exibição de diagrama (painel intermediário).
- Gerenciador de Janelas de Atividades no painel direito.
- Lista Janelas de Atividades no painel inferior.

No pop-up Janelas de Atividades e no Gerenciador de Janelas de Atividades, você pode rolar até a semana anterior e a próxima semana usando as setas para a esquerda e para a direita.

![Setas para a direita/esquerda do Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da Exibição de Diagrama, você verá os botões para Ampliar, Reduzir, Ajustar Nível de Zoom, Zoom 100%, Bloquear layout (impede a movimentação acidental de tabelas e de pipelines na exibição de diagrama). O botão Bloquear layout está ATIVADO por padrão. Você pode desativá-lo e mover entidades pelo diagrama. Ao DESATIVÁ-LO, você poderá usar o último botão para posicionar as tabelas e os pipelines automaticamente. Também é possível Ampliar/Reduzir usando o botão de rolagem do mouse.

![Comandos Zoom da Exibição de Diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### Lista Janelas de Atividades
A lista Janelas de Atividades na parte inferior do painel intermediário exibe todas as janelas de atividades do conjunto de dados que você selecionou no Gerenciador de Recursos ou na Exibição de Diagrama. Por padrão, a lista está na ordem decrescente, o que significa que você verá a janela de atividades mais recente na parte de cima.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Essa lista não é atualizada automaticamente, portanto use o botão de atualização na barra de ferramentas para atualizá-la manualmente.


As janelas de atividades podem estar em um dos seguintes status:

<table>
<tr>
	<th align="left">Status</th><th align="left">Substatus</th><th align="left">Descrição</th>
</tr>
<tr>
	<td rowspan="8">Aguardando</td><td>ScheduleTime</td><td>Não chegou a hora de a janela de atividades ser executada.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras janelas de atividades.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade é pausada e não poderá executar as janelas de atividades até ser retomada.</td>
</tr>
<tr>
<td>Retry</td><td>A execução da atividade será repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Aguardando a validação ser repetida.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validando</td><td>Validação em andamento.</td>
</tr>
<td></td>
<td>A janela de atividades está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Falha</td><td>TimedOut</td><td>A execução demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>Cancelado por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td></td><td>Falha ao gerar e/ou validar a janela de atividades.</td>
</tr>
<td>Ready</td><td></td><td>A janela de atividades está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td></td><td>A janela de atividades não foi processada.</td>
</tr>
<tr>
<td>Nenhum</td><td></td><td>Uma janela de atividades que costumava existir com um status diferente, mas que foi redefinida.</td>
</tr>
</table>


Ao clicar em uma janela de atividades na lista, você verá detalhes sobre a janela de atividades na janela **Propriedades** ou no **Gerenciador de Janelas de Atividades** à direita.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### Atualizar janelas de atividades  
Os detalhes não são atualizados automaticamente e, portanto, você precisa usar o botão **atualizar** (segundo botão) na barra de comandos para atualizar manualmente a lista de janelas de atividades.
 

### Janela Propriedades
A janela Propriedades está no painel à extrema direita do Aplicativo de Monitoramento e Gerenciamento.

![Janela Propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Ela exibe as propriedades do item selecionado no Gerenciador de Recursos (modo de exibição de árvore) (ou) na exibição de diagrama (ou) na lista de janelas de atividades.

### Gerenciador de Janelas de Atividades

A janela **Gerenciador de Janelas de Atividades** está no painel mais à direita do Aplicativo de Monitoramento e Gerenciamento. Ela exibe detalhes sobre a janela de atividades selecionada no pop-up Janelas de Atividades ou na lista Janelas de Atividades.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Você pode alternar para outra janela de atividades clicando nela na exibição de calendário na parte superior. Também é possível usar os botões **seta para a esquerda**/**seta para a direita**, na parte superior, para ver as janelas de atividades da semana anterior/próxima semana.

Você pode usar os botões da barra de ferramentas no painel inferior para **executar novamente** a janela de atividades ou para **atualizar** os detalhes no painel.

### Script 
Você pode usar a guia **Script** para exibir a definição de JSON da entidade selecionada do Data Factory (serviço vinculado, conjunto de dados e pipeline).

![Tabela de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## Usando as exibições do sistema
O Aplicativo Monitoramento e Gerenciamento inclui exibições do sistema predefinidas (**Janelas de atividades recentes**, **Janelas de atividades com falha**, **Janelas de atividades em andamento**) que permitem exibir as janelas de atividades recentes/com falha/em andamento do seu data factory.

Alterne para a guia **Exibições de Monitoramento** à esquerda ao clicar nela.

![Guia Exibições de Monitoramento](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Há três exibições do sistema compatíveis no momento. Escolha uma opção para ver as janelas de atividades recentes (ou) as janelas de atividades com falha (ou) as janelas de atividades em andamento na lista Janelas de Atividades (na parte inferior do painel intermediário).

Ao escolher a opção **Janelas de atividades recentes**, você verá todas as janelas de atividades recentes na ordem decrescente da **hora da última tentativa**.

Você pode usar a exibição **Janelas de atividades com falha** para ver todas as janelas de atividade com falha na lista. Escolha uma janela de atividades com falha na lista para ver detalhes sobre ela na janela **Propriedades** ou no **Gerenciador de Janelas de Atividades**. Você também pode baixar todos os logs de uma janela de atividades com falha.


## Classificando e filtrando janelas de atividades
Altere as configurações da **hora de início** e **hora de término** na barra de comandos para filtrar as janelas de atividades. Depois de alterar a Hora de início e a Hora de término, clique no botão ao lado da hora de término para atualizar a lista Janelas de Atividades.

![Horas de Início e Término](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Atualmente, todas as horas estão no formato UTC no Aplicativo de Monitoramento e Gerenciamento.

Na **lista Janelas de Atividades**, clique no nome de uma coluna (por exemplo: Status).

![Menu da coluna Lista de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Você pode fazer o seguinte:

- Classificar em ordem crescente.
- Classificar em ordem decrescente.
- Filtrar por um ou mais valores (Pronto, Aguardando etc...)

Ao especificar um filtro em uma coluna, você verá o botão de filtro habilitado para a coluna, indicando que os valores na coluna são valores filtrados.

![Filtrar na coluna da lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Você pode usar a mesma janela pop-up para limpar filtros. Para limpar todos os filtros da lista de janelas de atividades, clique no botão Filtrar na barra de comandos.

![Limpar todos os filtros na lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## Executando ações em lote

### Executar novamente as janelas de atividades selecionadas
Escolha uma janela de atividades, clique na seta para baixo do primeiro botão da barra de comandos e selecione **Executar novamente**/**Executar novamente com upstream no pipeline**. Quando você escolhe a opção **Executar novamente com upstream no pipeline**, todas as janelas de atividades upstream também são executadas novamente. ![Executar novamente uma janela de atividades](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também é possível escolher várias janelas de atividades na lista e executá-las novamente ao mesmo tempo. Talvez você queira filtrar as janelas de atividades com base no status (por exemplo: **Com falha**) e executar novamente as janelas de atividades com falha após corrigir o problema que faz com que as janelas de atividades falhem. Confira a seção a seguir para obter os detalhes sobre como filtrar janelas de atividades na lista.

### Pausar/retomar vários pipelines
Você pode selecionar dois ou mais pipelines (usando CTRL) e usar os botões da barra de comandos (destacados pelo retângulo vermelho na imagem a seguir) para pausá-los/retomá-los de uma vez.

![Suspender/Retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## Criando alertas 
A página Alertas permite criar um novo alerta e exibir/editar/excluir alertas existentes. Também é possível desabilitar/habilitar um alerta. Clique na guia Alertas para ver a página.

![Guia Alertas](./media/data-factory-monitor-manage-app/AlertsTab.png)

### Para criar um alerta

1. Clique em **Adicionar Alerta** para adicionar um alerta. Você verá a página Detalhes. 

	![Criar Alertas - página Detalhes](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Especifique o **nome** e a **descrição** para o alerta e clique em **Avançar**. Você verá a página **Filtros**.

	![Criar Alertas - página Filtros](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

2. Escolha o **evento**, o **status** e o **substatus** (opcional) sobre o qual deseja que o serviço Data Factory alerte você e clique em **Avançar**. Você verá a página **Destinatários**.

	![Criar Alertas - página Destinatários](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Escolha a opção **Enviar email ao administrador da assinatura** e/ou insira o **email adicional de administrador** e clique em **Concluir**. Você deverá ver o alerta na lista. 
	
	![Lista Alertas](./media/data-factory-monitor-manage-app/AlertsList.png)

Na lista Alertas, use os botões associados ao alerta para editar/excluir/desabilitar/habilitar um alerta.

### Evento/status/substatus
A tabela a seguir fornece a lista de eventos disponíveis e o status (e o substatus).

Nome do evento | Status | Substatus
-------------- | ------ | ----------
Execução de Atividade Iniciada | Iniciado | Iniciando
Execução de Atividade Concluída | Bem-sucedido | Bem-sucedido 
Execução de Atividade Concluída | Falha| Alocação de Recursos com Falha<br/>Execução com Falha<br/><br/>Tempo Limite Expirado<br/><br/>Validação com Falha<br/><br/>Abandonado<br/>
Criação de Cluster HDI sob Demanda Iniciada | Iniciado | &nbsp; |
Cluster HDI sob Demanda Criado com Êxito | Bem-sucedido | &nbsp; |
Cluster HDI sob Demanda Excluído | Bem-sucedido | &nbsp; |
### Para editar/excluir/desabilitar um alerta


![Botões Alertas](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0622_2016-->
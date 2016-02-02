<properties
	pageTitle="Meu primeiro runbook gráfico na Automação do Azure | Microsoft Azure"
	description="Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="01/18/2016"
	ms.author="bwren"/>


# O meu primeiro runbook gráfico

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)
- [PowerShell](automation-first-runbook-textual-PowerShell.md)

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automação do Azure. Começaremos com um runbook simples, que testaremos e publicaremos enquanto explicamos como acompanhar o status do trabalho do runbook. Em seguida, modificaremos o runbook para gerenciar recursos do Azure, nesse caso, iniciando uma máquina virtual do Azure. Depois, tornaremos o runbook mais robusto adicionando parâmetros de runbook e um link condicional.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte.

- do Microsoft Azure. Se ainda não tiver uma, você poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-trial/" target="_blank">[inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Conta de Automação](automation-configuring.md) para manter o runbook.
- Uma máquina virtual do Azure. Vamos parar e iniciar esse computador, portanto ele não deve ser de produção.
- [Usuário e ativo de credencial de automação do Active Directory do Azure](automation-configuring.md) para autenticar nos recursos do Azure. Esse usuário deve ter permissão para iniciar e parar a máquina virtual.

## Etapa 1: criar o novo runbook

Começaremos criando um runbook simples que exibe o texto *Olá mundo*.

1. No Portal de Visualização do Azure, abra sua conta de Automação. A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.<br> ![Controle de runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
2. Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
3. Atribua o nome *MyFirstRunbook-Graphical* ao runbook.
4. Nesse caso, criaremos um [runbook gráfico](automation-graphical-authoring-intro.md), portanto, escolha **Gráfico** para **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. Clique em **criar** para criar o runbook e abrir o editor gráfico.

## Etapa 2: adicionar atividades ao runbook

O controle de biblioteca no lado esquerdo do editor permite que você selecione atividades para adicionar ao runbook. Adicionaremos um cmdlet **Write-Output** para exibir nosso texto do runbook.

1.   No controle de biblioteca, expanda o nó **Cmdlets** e, em seguida, o **Microsoft.PowerShell.Utility**.<br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   Role até o final da lista. Clique com botão direito do mouse em **Write-Output** e clique em **Adicionar à tela**.
4.   Clique na atividade **Write-Output** na tela. Isso abre o controle de configuração que permite que você configure a atividade.
5.   O **Rótulo** usa por padrão o nome do cmdlet, mas podemos alterá-lo para algo mais amigável. Altere-o para *Write Hello World to output*.
6.   Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet. Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar que usará. Nesse caso, **Write-Output** tem apenas um conjunto de parâmetros, portanto, não é necessário selecionar um. <br> ![Propriedades de Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
7.   Selecione o parâmetro **InputObject**. Esse é o parâmetro em que especificaremos o texto para enviar ao fluxo de saída.
9.   No menu suspenso **Fonte de dados**, selecione **Expressão do PowerShell**. O menu suspenso **Fonte de dados** fornece diferentes fontes que podem ser usadas para popular um valor de parâmetro. Você pode usar a saída dessas fontes como outra atividade, um ativo de Automação ou uma expressão do PowerShell. Nesse caso, queremos apenas exibir o texto *Olá mundo*. Podemos usar uma expressão do PowerShell e especificar uma cadeia de caracteres.
10.   Na caixa **Expressão**, digite *"Olá mundo"* e, em seguida, clique em **OK** duas vezes para retornar à tela.<br> ![Expressão do PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   Salve o runbook clicando em **Salvar**.<br> ![Salvar runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Etapa 3: testar o runbook

Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.
 
2. Clique em **Painel de teste** para abrir o Painel de teste.<br> ![Painel de teste](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution) é criado e seu status é exibido no painel. O status do trabalho iniciará como *Na fila*, indicando que ele está aguardando um runbook worker na nuvem ficar disponível. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.  
4. Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, deveremos ver *Olá mundo*.<br> ![Olá mundo](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. Feche o Painel de teste para retornar à tela.


## Etapa 4: publicar e iniciar o runbook

O runbook que acabamos de criar ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **Status de Criação** de **Publicado**.
3. Role para a direita para exibir o painel de **MyFirstRunbook**. As opções na parte superior nos permitem iniciar o runbook, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP. 
4. Queremos apenas iniciar o runbook, então clique em **Iniciar** e em **Sim** quando solicitado.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. Um painel de trabalho é aberto para o trabalho de runbook que acabamos de criar. Podemos fechar esse painel, mas nesse caso, o deixaremos aberto para que possamos acompanhar o progresso do trabalho.
6.  O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que vimos quando testamos o runbook.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel **Saída** é aberto e podemos ver nosso *Olá mundo*.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  Feche o painel Saída.
9.  Clique em **Fluxos** para abrir o painel Fluxos do trabalho do runbook. Devemos ver apenas *Olá mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.<br>![Resumo do trabalho](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. Feche o painel Fluxos e o painel Trabalho para retornar ao painel MyFirstRunbook.
9.  Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez.<br> ![Trabalhos](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure. No entanto, ele não será capaz de fazer isso a menos que o autentiquemos usando as credenciais mencionadas nos [pré-requisitos](#prerequisites). Fazemos isso com o cmdlet **Set-AzureAccount**.

1.  Abra o editor gráfico clicando em **Editar** no painel MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  Não precisamos mais do **Write Hello World to output**, então clique com o botão direito do mouse nele e selecione **Excluir**.
8.  No controle de biblioteca, expanda **Cmdlets** e **Azure**. 
9.  Adicione **Add-AzureAccount** à tela.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  Selecione **Add-AzureAccount** e clique em **Parâmetros** no painel Configuração.
13.  **Add-AzureAccount** tem vários conjuntos de parâmetros, portanto, precisamos selecionar um antes que possamos fornecer valores de parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **User**.
14.  Depois de selecionar o conjunto de parâmetros, os parâmetros são exibidos no painel Configuração de Parâmetro da Atividade. Clique em **Credencial**.<br> ![Adicionar parâmetros da conta do Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  Queremos que esse cmdlet use um ativo de credencial em nossa conta de Automação, portanto, selecione **Ativo de credencial** para a **Fonte de dados**.
16.  Selecione o ativo de credencial que tem acesso para iniciar e parar uma máquina virtual no seu ambiente do Azure.<br> ![Adicionar fonte de dados da conta do Azure](media/automation-first-runbook-graphical/credential-data-source.png)
17.  Clique em **OK** duas vezes para retornar à tela.


## Etapa 6: adicionar a atividade para iniciar uma máquina virtual

Agora adicionaremos uma atividade **Start-AzureVM** para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, embutiremos esse nome no cmdlet.

1. Expanda **Cmdlets** e **Azure**.
2. Adicione **Start-AzureVM** à tela e clique e arraste-a para baixo de **Add-AzureAccount**.
11.  Passe o mouse sobre **Add-AzureAccount** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzureVM**. A seta que você acabou de criar é um *link*. O runbook iniciará com **Add-AzureAccount** e, em seguida, executará **Start-AzureVM**.<br>![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
5. Selecione **Start-AzureVM**. Clique em **Parâmetros** e em **Conjunto de Parâmetros** para exibir os conjuntos de **Start-AzureVM**. Selecione o conjunto de parâmetros **ByName**. Observe que **Name** e **ServiceName** têm pontos de exclamação ao lado deles. Isso indica que são os parâmetros obrigatórios.
7. Selecione **Name**. Use **Valor constante** para a **Fonte de dados** e digite o nome da máquina virtual a ser iniciada. Clique em **OK**.
8. Selecione **ServiceName**. Use **Valor constante** para a **Fonte de dados** e digite o nome da máquina virtual a ser iniciada. Clique em **OK**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. Clique em Painel de teste para que possamos testar o runbook.
10. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.


## Etapa 7: adicionar um parâmetro de entrada ao runbook

No momento, nosso runbook inicia a máquina virtual que especificamos no cmdlet **Start-AzureVM**, mas ele seria mais útil se pudéssemos especificar a máquina virtual quando o runbook é iniciado. Agora adicionaremos um parâmetro de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook**.
2. Clique em **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.<br> ![Entrada e Saída de runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. Especifique *VMName* para o **Nome**. Mantenha a *cadeia de caracteres* para o **Tipo**, mas altere **Obrigatório** para *Sim*. Clique em **OK**.
5. Crie um segundo parâmetro de entrada obrigatório chamado *VMServiceName* e clique em **OK** para fechar o painel **Entrada e Saída**.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/input-parameters.png) 
6. Selecione a atividade **Start-AzureVM** atividade e clique **Parâmetros**.
7. Altere a **Fonte de dados** de **Name** para **Entrada do runbook** e selecione **VMName**.<br> ![Parâmetro Name de Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png) 
8. Altere a **Fonte de dados** de **ServiceName** para **Entrada do runbook** e selecione **VMServiceName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. Salve o runbook e abra o Painel de teste. Observe que agora você pode fornecer valores para as duas variáveis de entrada que serão usadas no teste. 
11.  Feche o Painel de teste.
12.  Clique em **Publicar** para publicar a nova versão do runbook.
13.  Pare a máquina virtual que você iniciou na etapa anterior.
13.  Clique em **Iniciar** para iniciar o runbook. Digite o **VMName** e o **VMServiceName** da máquina virtual que você iniciará.<br> ![Iniciar Runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## Etapa 8: criar um link condicional

Agora modificaremos o runbook para que ele tente iniciar o runbook apenas se ele ainda não estiver iniciado. Faremos isso adicionando um cmdlet **Get-AzureVM** ao runbook que incluirá o estado atual da máquina virtual. Em seguida, adicionaremos um link condicional que executará o **Start-AzureVM** apenas se o estado de execução atual estiver parado. Se o runbook não estiver parado, exibirá uma mensagem.

1. Abra o **MyFirstRunbook** no editor gráfico.
2. Remova o link entre **Add-AzureAccount** e **Start-AzureVM** clicando nele e pressionando a tecla *Excluir*.
3. No controle de biblioteca, expanda **Cmdlets** e **Azure**.
4. Adicione **Get-AzureVM** à tela.
5. Crie um link de **Add-AzureAccount** para **Get-AzureVM**. Crie outro link de **Get-AzureVM** para **Start-AzureVM**.<br> ![Runbook com Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  Selecione **Get-AzureVM** e clique em **Parâmetros**. Selecione o conjunto de parâmetros *GetVMByServiceAndVMName*.
7.  Para **Name**, defina a **Fonte de dados** para **Entrada do runbook** e selecione **VMName**.   
7.  Para **ServiceName**, defina a **Fonte de dados** para **Entrada do runbook** e selecione **VMServiceName**.  
8.  Selecione o link entre **Get-AzureVM** e **Start-AzureVM**.
9.  No painel Configuração, altere **Aplicar condição** para **Verdadeiro**. Observe que o link muda para uma linha tracejada, indicando que a atividade de destino será executada apenas se a condição for resolvida como verdadeira.
10.  Para a **Expressão da condição**, digite *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*. **Start-AzureVM** agora será executada apenas se a máquina virtual estiver parada.<br> ![Condição do link](media/automation-first-runbook-graphical/link-condition.png) 
11.  No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
12.  Adicione **Write-Output** à tela.
13.  Crie um link de **Get-AzureVM** para **Write-Output**.
14.  Selecione o link e altere **Aplicar condição** para **Verdadeiro**.
14.  Para a **Expressão da condição**, digite *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*. **Write-Output** agora será executada apenas se a máquina virtual não estiver parada.<br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/write-output-link.png) 
15.  Selecione **Write-Output** e clique em **Parâmetros**.
16.  Para **InputObject**, altere a **Fonte de dados** para **Expressão do PowerShell** e digite a expressão *"$VMName.Name já está iniciada".*.
17.  Salve o runbook e abra o Painel de teste.
18.  Inicie o runbook com a máquina virtual parada e ela deve ser iniciada.
19.  Inicie o runbook com a máquina virtual iniciada e você deve obter a saída de que ela já está iniciada.
 

## Artigos relacionados

- [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
- [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
- [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-PowerShell.md)
 

<!---HONumber=AcomDC_0128_2016-->
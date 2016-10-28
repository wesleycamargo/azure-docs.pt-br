<properties
    pageTitle="Meu primeiro runbook gráfico na Automação do Azure | Microsoft Azure"
    description="Tutorial que orienta você pela criação, teste e publicação de um runbook gráfico simples."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="runbook, modelo de runbook, automação de runbook, runbook do azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="magoedte;bwren"/>

# O meu primeiro runbook gráfico

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

Este tutorial orienta você durante a criação de um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automação do Azure. Começaremos com um runbook simples, que testaremos e publicaremos enquanto explicamos como acompanhar o status do trabalho do runbook. Em seguida, modificaremos o runbook para gerenciar recursos do Azure, nesse caso, iniciando uma máquina virtual do Azure. Então, tornaremos o runbook mais robusto adicionando os parâmetros de runbook e links condicionais.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte.

-	Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se em uma conta gratuita](https://azure.microsoft.com/free/).
-	[Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
-	Uma máquina virtual do Azure. Vamos parar e iniciar esse computador, portanto ele não deve ser de produção.


## Etapa 1: criar o novo runbook

Começaremos criando um runbook simples que exibe o texto *Olá mundo*.

1.	No Portal do Azure, abra sua conta de Automação. A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2.	Clique no bloco **Runbooks** para abrir a lista de runbooks.<br> ![Controle de runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4.	Atribua o nome *MyFirstRunbook-Graphical* ao runbook.
5.	Neste caso, criaremos um [runbook gráfico](automation-graphical-authoring-intro.md), portanto, escolha **Gráfico** para **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.	Clique em **criar** para criar o runbook e abrir o editor gráfico.

## Etapa 2: adicionar atividades ao runbook

O controle Biblioteca no lado esquerdo do editor permite que você selecione as atividades para adicionar ao runbook. Adicionaremos um cmdlet **Write-Output** para exibir nosso o texto a partir do runbook.

1.	No controle Biblioteca, clique na caixa de texto de pesquisa e digite **Write-Output**. Os resultados da pesquisa serão exibidos abaixo. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.	Role até o final da lista. Você pode clicar com o botão direito em **Write-Output** e selecionar **Adicionar à tela** ou clicar na elipse ao lado do cmdlet, em seguida, selecionar **Adicionar à tela**.
3.	Clique na atividade **Write-Output** na tela. Isso abrirá a folha Controle da configuração que permite configurar a atividade.
4.	O **Rótulo** usa por padrão o nome do cmdlet, mas podemos alterá-lo para algo mais amigável. Altere-o para *Write Hello World to output*.
5.	Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet. Alguns cmdlets têm vários conjuntos de parâmetros e você precisa selecionar que usará. Nesse caso, **Write-Output** tem apenas um conjunto de parâmetros, portanto, não é necessário selecionar um. <br> ![Propriedades de Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.	Selecione o parâmetro **InputObject**. Esse é o parâmetro em que especificaremos o texto para enviar ao fluxo de saída.
7.	No menu suspenso **Fonte de dados**, selecione **Expressão do PowerShell**. O menu suspenso **Fonte de dados** fornece diferentes fontes que podem ser usadas para popular um valor de parâmetro. Você pode usar a saída dessas fontes como outra atividade, um ativo de Automação ou uma expressão do PowerShell. Nesse caso, queremos apenas exibir o texto *Olá mundo*. Podemos usar uma expressão do PowerShell e especificar uma cadeia de caracteres.
8.	Na caixa **Expressão**, digite *"Olá mundo"* e, em seguida, clique em **OK** duas vezes para retornar à tela.<br> ![Expressão do PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Salve o runbook clicando em **Salvar**.<br> ![Salvar runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## Etapa 3: testar o runbook

Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1.	Clique em **Painel de teste** para abrir a folha Teste.<br> ![Painel de teste](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.	Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3.	Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel. O status do trabalho será iniciado como *Na fila*, indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.
4.	Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, deveremos ver *Olá mundo*.<br> ![Olá mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
5.	Feche a folha Teste para retornar à tela.

## Etapa 4: publicar e iniciar o runbook

O runbook que acabamos de criar ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1.	Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.	Se você rolar para a esquerda para exibir o runbook na folha **Runbooks**, ele mostrará um **Status de Criação** **Publicado**.
3.	Role para a direita para exibir a folha para **MyFirstRunbook**. As opções na parte superior nos permitem iniciar o runbook, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
4.	Queremos apenas iniciar o runbook, então clique em **Iniciar** e em **Sim** quando solicitado.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.	Uma folha de trabalho será aberto para o trabalho de runbook que acabamos de criar. Podemos fechar essa folha, mas neste caso, iremos deixá-la aberta para que possamos acompanhar o progresso do trabalho.
6.	O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que vimos quando testamos o runbook.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.	Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. A folha **Saída** é aberta e podemos ver nosso *Olá Mundo* no painel.<br> ![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-output.png)
8.	Feche a folha Saída.
9.	Clique em **Todos os Logs** para abrir a folha Fluxos do trabalho do runbook. Devemos ver apenas *Olá mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.<br>![Resumo do trabalho](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10.	Feche as folhas Todos os Logs e Trabalho para retornar para a folha MyFirstRunbook.
11.	Clique em **Trabalhos** para abrir a folha Trabalhos desse runbook. Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez.<br> ![Trabalhos](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## Etapa 5: criar ativos de variável

Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure. Antes de configurarmos o runbook a ser autenticado, criaremos uma variável para conter a ID de assinatura e usá-la como referência depois que configurarmos a atividade a ser autenticada na etapa 6 abaixo. A inclusão de uma referência no contexto da assinatura facilita o seu trabalho entre as várias assinaturas. Antes de prosseguir, copie a ID de assinatura na opção Assinaturas do Painel de navegação.

1. Na folha Contas de Automação, clique no bloco **Ativos** e a folha **Ativos** será aberta.
2. Na folha Ativos, clique no bloco **Variáveis**.
3. Na folha Variáveis, clique em **Adicionar uma variável**.<br>![Variável de Automação](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Na folha Nova variável, na caixa **Nome**, digite **AzureSubscriptionId** e na caixa **Valor**, insira sua ID de Assinatura. Mantenha a *cadeia de caracteres* para o **Tipo** e o valor padrão para a **Criptografia**.
5. Clique em **Criar** para criar a variável.


## Etapa 6: adicionar a autenticação para gerenciar os recursos do Azure

Agora que temos uma variável para conter a ID de assinatura, podemos configurar nosso runbook para autenticar usando as credenciais Executar Como que são referenciadas nos [pré-requisitos](#prerequisites). Fazemos isso adicionando a conexão do Azure Executar Como **Ativos** e o cmdlet **AzureRMAccount adicionar** à tela.

1.	Abra o editor gráfico clicando em **Editar** na folha MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.	Não precisamos mais do **Write Hello World to output**, então clique com o botão direito do mouse nele e selecione **Excluir**.
3.	No controle de Biblioteca, expanda **Conexões** e adicione **AzureRunAsConnection** à tela selecionando **Adicionar à tela**.
4.	Na tela, selecione **AzureRunAsConnection** e no painel de controle da Configuração, digite **Obter Conexão Executar Como** na caixa de texto **Rótulo**. Essa é a conexão
5.	No controle Biblioteca, digite **Add-AzureRmAccount** na caixa de texto de pesquisa.
6.	Adicione **Add-AzureRmAccount** à tela.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.	Passe o mouse sobre **Obter Conexão Executar Como** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Add-AzureRmAccount**. A seta que você acabou de criar é um *link*. O runbook iniciará com **Obter Conexão Executar Como** e então execute **Add-AzureRmAccount**.<br> ![Criar link entre as atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.	Na tela, selecione **Add AzureRmAccount** e no tipo de painel de controle de configuração **Login no Azure** na caixa de texto de **Rótulo**.
9.	Clique em **Parâmetros** e a folha Configuração do Parâmetro da Atividade aparecerá.
10.  **Add-AzureRmAccount** tem vários conjuntos de parâmetros, portanto, precisamos selecionar um antes de podermos fornecer os valores do parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
11.  Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na folha Configuração do Parâmetro da Atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros da conta do Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  Na folha Valor do Parâmetro, selecione **Saída da atividade** para a **Fonte de dados**, selecione **Obter Conexão Executar Como** da lista, na caixa de texto **Caminho do Campo** escreva **ApplicationId** e então clique **OK**. Estamos especificando o nome da propriedade para o caminho de Campo porque a atividade produziu um objeto com várias propriedades.
13.  Clique em **CERTIFICATETHUMBPRINT** e na folha de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**. Selecione **Obter Conexão Executar Como** da lista, na caixa de texto **caminho de Campo** escreva **CertificateThumbrprint**, e em seguida, clique em **OK**.
14.  Clique em **SERVICEPRINCIPAL**, e na folha de Valor do Parâmetro, selecione **ConstantValue** para a **Fonte de dados**, clique na opção **True**, e em seguida, clique em **OK**.
15.  Clique em **TENANTID** e na folha de Valor do Parâmetro, selecione **Saída de atividade** para a **Fonte de dados**. Selecione **Obter Conexão Executar Como** da lista, na caixa de texto **caminho de Campo** escreva **TenantId**, e em seguida, clique em **OK** duas vezes.
16.  No controle Biblioteca, digite **Set-AzureRmContext** na caixa de texto de pesquisa.
17.	 Adicionar **Set-AzureRmContext** à tela.
18.	 Na tela, selecione **Set-AzureRmContext** e no painel de controle da Configuração, digite **Especificar ID de Assinatura** na caixa de texto **Rótulo**.
19.	 Clique em **Parâmetros** e a folha Configuração do Parâmetro da Atividade aparecerá.
20. **Set-AzureRmContext** tem vários conjuntos de parâmetros, portanto, precisamos selecionar um antes de podermos fornecer os valores do parâmetro. Clique em **Conjunto de Parâmetros** e selecione o conjunto de parâmetros **SubscriptionId**.
21.	 Depois de selecionar o conjunto de parâmetros, os parâmetros serão exibidos na folha Configuração do Parâmetro da Atividade. Clique em **SubscriptionID**
22.	 Na folha Valor do Parâmetro, selecione **Entrada do Runbook** para a **Fonte de dados**, selecione **AzureCredentialAssetName** na lista e clique em **OK** duas vezes.
23.  Passe o mouse sobre **Login no Azure** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Assinatura**.


Seu runbook deve ser semelhante ao seguinte: <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## Etapa 7: adicionar a atividade para iniciar uma máquina virtual

Agora, adicionaremos uma atividade **Start-AzureRmVM** para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, embutiremos esse nome no cmdlet.

1. No controle Biblioteca, digite **Start-AzureRm** na caixa de texto de pesquisa.
2. Adicione **Start-AzureRmVM** à tela, clique e arraste-a para baixo de **Especificar Id de Assinatura**.
3. Passe o mouse sobre **Especificar Id de Assinatura** até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzureRmVM**.
4.	Selecione **Start-AzureRmVM**. Clique em **Parâmetros** e **Conjunto de Parâmetros** para exibir os conjuntos de **Start-AzureRmVM**. Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName**. Observe que **ResourceGroupName** e **Name** têm pontos de exclamação ao lado deles. Isso indica que são os parâmetros obrigatórios. Observe também que ambos esperam valores da cadeia de caracteres.
5.	Selecione **Name**. Selecione a **Expressão do PowerShell** para a **Fonte de dados** e digite o nome da máquina virtual entre aspas duplas com a qual iniciaremos este runbook. Clique em **OK**.<br>![Valor do Parâmetro Name do Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.	Selecione **ResourceGroupName**. Use a **Expressão do PowerShell** para a **Fonte de dados** e digite o nome do grupo de recursos entre aspas duplas. Clique em **OK**.<br> ![Parâmetros do Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.	Clique em Painel de teste para que possamos testar o runbook.
9.	Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

Seu runbook deve ser semelhante ao seguinte: <br>![Configuração da autenticação do runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## Etapa 8: adicionar parâmetros de entrada adicionais ao runbook

Nosso runbook inicia atualmente a máquina virtual no grupo de recursos que especificamos no cmdlet **Start-AzureRmVM**, mas nosso runbook seria mais útil se pudéssemos especificar ambos quando o runbook é iniciado. Agora adicionaremos parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook**.
2. Clique em **Entrada e saída** e em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.<br> ![Entrada e Saída de runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Especifique *VMName* para o **Nome**. Mantenha a *cadeia de caracteres* para o **Tipo**, mas altere **Obrigatório** para *Sim*. Clique em **OK**.
4. Crie um segundo parâmetro de entrada obrigatório denominado *ResourceGroupName* e clique em **OK** para fechar o painel de **Entrada e Saída**.<br> ![Parâmetros de Entrada de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Selecione a atividade **Start-AzureRmVM** e clique em **Parâmetros**.
6. Altere a **Fonte de dados** de **Name** para a **Entrada do runbook** e selecione **VMName**.<br>
7. Altere a **Fonte de dados** de **ResourceGroupName** para a **Entrada do runbook** e selecione **ResourceGroupName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Salve o runbook e abra o Painel de teste. Observe que agora você pode fornecer valores para as duas variáveis de entrada que serão usadas no teste.
9. Feche o Painel de teste.
10.	Clique em **Publicar** para publicar a nova versão do runbook.
11.	Pare a máquina virtual que você iniciou na etapa anterior.
12.	Clique em **Iniciar** para iniciar o runbook. Digite o **NomeDaVM** e o **NomeDoGrupoDeRecursos** da máquina virtual que você iniciará.<br> ![Iniciar Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13.	Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## Etapa 9: criar um link condicional

Agora, modificaremos o runbook para que ele tente iniciar a máquina virtual apenas se ela ainda não tiver iniciado. Faremos isso adicionando um cmdlet **Get-AzureRmVM** ao runbook que obterá o status do nível da instância da máquina virtual. Em seguida, adicionaremos um módulo de código do Fluxo de Trabalho do PowerShell denominado **Obter Status** com um trecho de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado. Um link condicional do módulo **Obter Status** apenas executará **Start-AzureRmVM** se o estado de execução atual for interrompido. Por fim, enviaremos uma mensagem para informar se a VM foi iniciada com êxito ou não usando o cmdlet Write-Output do PowerShell.

1. Abra o **MyFirstRunbook** no editor gráfico.
2. Remova o link entre **Especifique a Id de Assinatura** e **Start-AzureRmVM** clicando nele e pressionando a tecla *Delete*.
3. No controle Biblioteca, digite **Get-AzureRm** na caixa de texto de pesquisa.
4. Adicione **Get-AzureRmVM** à tela.
5. Selecione **Get-AzureRmVM** e **Conjunto de Parâmetros** para exibir os conjuntos de **Get-AzureRmVM**. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**. Observe que**ResourceGroupName** e **Name** têm pontos de exclamação ao lado deles. Isso indica que são os parâmetros obrigatórios. Observe também que ambos esperam valores da cadeia de caracteres.
6. Em **Fonte de dados** para **Name**, selecione **Entrada do runbook**, em seguida, selecione **VMName**. Clique em **OK**.
7. Em **Fonte de dados** para **ResourceGroupName**, selecione **Entrada do runbook**, em seguida, selecione **ResourceGroupName**. Clique em **OK**.
8. Em **Fonte de dados** para **Status**, selecione **Valor constante**, em seguida, clique em **True**. Clique em **OK**.
9. Criar um link de **Especificar Id da Assinatura** para **Get-AzureRmVM**.
10. No controle da biblioteca, expanda **Controle do runbook** e adicione o **Código** à tela.
11. Crie um link de **Get-AzureRmVM** para **Código**.
12. Clique em **Código** e no painel de Configuração, altere o rótulo para **Obter Status**.
13. Selecione o parâmetro **Code** e a folha **Editor de Código** aparecerá.
14. No editor de código, cole o seguinte trecho de código:

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. Crie um link de **Obter Status** para **Start-AzureRmVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)
16. Selecione o link e no painel de Configuração, altere **Aplicar condição** para **Sim**. Observe que o link muda para uma linha tracejada, indicando que a atividade de destino será executada apenas se a condição for resolvida como verdadeira.
17. Para a **Expressão da condição**, digite *$ActivityOutput['Get Status'] -eq "Stopped"*. Agora, **Start-AzureRmVM** será executada apenas se a máquina virtual estiver parada.
18.	No controle de biblioteca, expanda **Cmdlets** e **Microsoft.PowerShell.Utility**.
19.	Adicione **Write-Output** à tela duas vezes.<br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. No primeiro controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Notificar VM Iniciada*.
21. Para **InputObject**, altere a **Fonte de dados** para **Expressão do PowerShell** e digite a expressão *"$VMName.Name iniciada com êxito."*.
22. No segundo controle **Write-Output**, clique em **Parâmetros** e altere o valor **Rótulo** para *Falha ao Notificar VM Iniciada*
23. Para **InputObject**, altere a **Fonte de dados** para a **Expressão do PowerShell** e digite a expressão *"$VMName não pôde iniciar."*.
24. Criar um link de **Start-AzureRmVM** para **Notificar VM Iniciada** e **Falha ao Notificar VM Iniciada**.
25. Selecione o link para **Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
26. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Agora, o controle Write-Output será executado apenas se a máquina virtual for iniciada com êxito.
27. Selecione o link para **Falha ao Notificar VM Iniciada** e altere **Aplicar condição** para **True**.
28. Para a **Expressão da condição**, digite *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Agora, o controle Write-Output será executado apenas se a máquina virtual não for iniciada com êxito.
29.	Salve o runbook e abra o Painel de teste.
30.	Inicie o runbook com a máquina virtual parada e ela deve ser iniciada.

## Próximas etapas

-	Para saber mais sobre a Criação Gráfica, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
-	Para começar a usar os runbooks do PowerShell, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
-	Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

<!---HONumber=AcomDC_0928_2016-->
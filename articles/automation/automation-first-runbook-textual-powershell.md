<properties
	pageTitle="Meu primeiro runbook do PowerShell na Automação do Azure | Microsoft Azure"
	description="Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell."
	services="automation"
	documentationCenter=""
	authors="SnehaGunda"
	manager="stevenka"
	editor="tysonn"/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="12/19/2015"
	ms.author="sngun"/>


# Meu primeiro runbook do PowerShell

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)
- [PowerShell](automation-first-runbook-textual-PowerShell.md)

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Começaremos com um runbook simples, que testaremos e publicaremos enquanto explicamos como acompanhar o status do trabalho do runbook. Em seguida, modificaremos o runbook para gerenciar recursos do Azure, nesse caso, iniciando uma máquina virtual do Azure. Depois, tornaremos o runbook mais robusto adicionando parâmetros de runbook.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte.

- do Microsoft Azure. Se ainda não tiver uma, você poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-trial/" target="_blank">[inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Conta de Automação](automation-configuring.md) para manter o runbook.
- Uma máquina virtual do Azure. Vamos parar e iniciar esse computador, portanto ele não deve ser de produção.
- [Usuário e ativo de credencial de automação do Active Directory do Azure](automation-configuring.md) para autenticar nos recursos do Azure. Esse usuário deve ter permissão para iniciar e parar a máquina virtual.

## Etapa 1: criar o novo runbook

Começaremos criando um runbook simples que exibe o texto *Olá mundo*.
 
1. No Portal de Visualização do Azure, abra sua conta de Automação. A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2. Clique no bloco **Runbooks** para abrir a lista de runbooks. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3. Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Atribua o nome *MyFirstRunbook-PowerShell* ao runbook.
5. Neste caso, vamos criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), portanto, escolha **PowerShell** como o **Tipo de runbook**. ![Tipo de runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. Clique em **criar** para criar o runbook e abrir o editor textual.

## Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este passo a passo, digitaremos diretamente no runbook.

1. Nosso runbook está vazio no momento. Digite *Write-Output "Hello World."*. ![Olá mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Salve o runbook clicando em **Salvar**. ![Botão Salvar](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## Etapa 3: testar o runbook

Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.
 
1. Clique em **Painel de teste** para abrir o painel de teste. ![Painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution) é criado e seu status é exibido. O status do trabalho iniciará como *Na fila*, indicando que ele está aguardando um runbook worker na nuvem ficar disponível. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.  
4. Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, veremos *Hello World* ![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. Feche o Painel de teste para retornar à tela.

## Etapa 4: publicar e iniciar o runbook

O runbook que acabamos de criar ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado. ![Botão Publicar](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **Status de Criação** de **Publicado**.
3. Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**. As opções na parte superior nos permitem iniciar o runbook, exibi-lo, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP. 
4. Nós só queremos iniciar o runbook, então, clique em **Iniciar** e clique em **Ok** quando a folha iniciar runbook for aberta. ![Botão Iniciar](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5. Um painel de trabalho é aberto para o trabalho de runbook que acabamos de criar. Podemos fechar esse painel, mas nesse caso, o deixaremos aberto para que possamos acompanhar o progresso do trabalho.
6. O status do trabalho é mostrado em **Resumo do Trabalho** e corresponde aos status que vimos quando testamos o runbook. ![Resumo do trabalho](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel Saída é aberto e podemos ver nosso *Hello World*. ![Saída do trabalho](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8. Feche o painel Saída.
9. Clique em **Todos os logs** para abrir o painel Fluxos do trabalho do runbook. Devemos ver apenas *Hello World* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles. ![Todos os logs](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10. Feche o painel Fluxos e o painel Trabalho para retornar ao painel MyFirstRunbook-PowerShell.
11. Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez. ![Lista de trabalhos](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12. Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure. No entanto, ele não será capaz de fazer isso a menos que o autentiquemos usando as credenciais mencionadas nos [pré-requisitos](#prerequisites). Fazemos isso com o cmdlet **Add-AzureAccount**.

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-PowerShell. ![Editar runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2. Já não precisamos da linha **Write-Output**, então vamos continuar e excluí-la.
3. No controle de biblioteca, expanda **Ativos** e **Credenciais**.
4. Clique com o botão direito do mouse na sua credencial e clique em **Adicionar à tela**. Isso adiciona uma atividade **Get-AutomationPSCredential** para sua credencial.
5. Na frente do **Get-AutomationPSCredential**, digite *$Credential =* para atribuir a credencial a uma variável. 
6. Na próxima linha, digite *Add-AzureAccount -Credential $Credential*. ![Credencial](media/automation-first-runbook-textual-powershell/automation-get-credential.png)
7. Clique no **Painel de teste** para que possamos testar o runbook.
8. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você receberá uma saída com a ID da assinatura, o tipo e os locatários da sua conta. Isso confirma que a credencial é válida.

## Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que nosso runbook está se autenticando em nossa assinatura do Azure, podemos gerenciar recursos. Adicionaremos um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, embutiremos esse nome no cmdlet.

1. Depois de *Add-AzureAccount*, digite *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'* fornecendo o nome e o nome do serviço da máquina virtual a ser iniciada. ![StartVM](media/automation-first-runbook-textual-powershell/automation-startvm.png)  
2. Salve o runbook e, para que possamos testá-lo, clique em **Painel de teste**.
3. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## Etapa 7: adicionar um parâmetro de entrada ao runbook

No momento, nosso runbook inicia a máquina virtual que codificamos no runbook, mas ele seria mais útil pudéssemos especificar a máquina virtual quando o runbook é iniciado. Agora adicionaremos parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e para *VMServiceName* ao runbook e use essas variáveis com o cmdlet **Start-AzureVM** como na imagem a seguir. ![Adicionar parâmetro](media/automation-first-runbook-textual-powershell/automation-add-parameter.png)  
2. Salve o runbook e abra o Painel de teste. Observe que agora você pode fornecer valores para as duas variáveis de entrada que serão usadas no teste. 
3. Feche o Painel de teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que você iniciou na etapa anterior.
6. Clique em **Iniciar** para iniciar o runbook. Digite o **VMName** e o **VMServiceName** da máquina virtual que você iniciará. ![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-parameter.png)  
7. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## Diferenças do fluxo de trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, os mesmos recursos e o mesmo gerenciamento dos runbooks do fluxo de trabalho do PowerShell, mas há algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados com mais rapidez em comparação aos runbooks do fluxo de trabalho do PowerShell, já que não têm a etapa de compilação.
2. Os runbooks do fluxo de trabalho do PowerShell dão suporte a pontos de verificação, ao uso de pontos de verificação e podem retomar de qualquer ponto no runbook, enquanto os runbooks do PowerShell só podem retomar desde o início.
3. Os runbooks do fluxo de trabalho do PowerShell dão suporte à execução em paralelo e serial, enquanto os runbooks do PowerShell só podem executar comandos de forma serial.
4. Em um runbook do fluxo de trabalho do PowerShell, uma atividade, um comando ou um bloco de script pode ter seu próprio espaço de execução, enquanto em um runbook do PowerShell, tudo em um script é executado em um único espaço de execução. Também há algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook nativo do PowerShell e um runbook do fluxo de trabalho do PowerShell. 


## Próximas etapas

- Para começar a usar runbooks gráficos, confira [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar a usar runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, suas vantagens e limitações, confira [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
- Para saber mais sobre o recurso de suporte de script do PowerShell, confira [Suporte a scripts nativos do PowerShell na Automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!---HONumber=AcomDC_0128_2016-->
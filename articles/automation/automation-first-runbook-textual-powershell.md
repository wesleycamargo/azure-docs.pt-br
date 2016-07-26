<properties
    pageTitle="Meu primeiro runbook do PowerShell na Automação do Azure | Microsoft Azure"
    description="Tutorial que orienta você pela criação, pelos testes e pela publicação de um runbook simples do PowerShell."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="azure powershell, tutorial de script do powershell, automação do powershell"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="magoedte;sngun"/>

# Meu primeiro runbook do PowerShell

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)  

Este tutorial orienta você durante a criação de um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automação do Azure. Começaremos com um runbook simples, que testaremos e publicaremos enquanto explicamos como acompanhar o status do trabalho do runbook. Em seguida, modificaremos o runbook para gerenciar recursos do Azure, nesse caso, iniciando uma máquina virtual do Azure. Depois, tornaremos o runbook mais robusto adicionando parâmetros de runbook.

## Pré-requisitos

Para concluir este tutorial, você precisará do seguinte.

-	do Microsoft Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se em uma conta gratuita](https://azure.microsoft.com/free/).
-	[Conta de automação](automation-security-overview.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
-	Uma máquina virtual do Azure. Vamos parar e iniciar esse computador, portanto ele não deve ser de produção.

## Etapa 1: criar o novo runbook

Começaremos criando um runbook simples que exibe o texto *Olá mundo*.

1.	No Portal do Azure, abra sua conta de Automação.  
	A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).
2.	Clique no bloco **Runbooks** para abrir a lista de runbooks.  
	![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3.	Crie um novo runbook clicando no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4.	Atribua o nome *MyFirstRunbook-PowerShell* ao runbook.
5.	Neste caso, vamos criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), portanto, escolha **PowerShell** como o **Tipo de runbook**.  
	![Tipo de runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6.	Clique em **criar** para criar o runbook e abrir o editor textual.

## Etapa 2 - adicionar código ao runbook

Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este passo a passo, digitaremos diretamente no runbook.

1.	Nosso runbook está vazio no momento. Digite *Write-Output "Olá mundo."*.  
	![Olá mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2.	Salve o runbook clicando em **Salvar**.  
	![Botão Salvar](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## Etapa 3: testar o runbook

Antes que publicamos o runbook para disponibilizá-lo na produção, queremos testá-lo para garantir que ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1.	Clique em **Painel de teste** para abrir o Painel de teste.  
	![Painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2.	Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3.	Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.  
	O status do trabalho iniciará como *Na fila*, indicando que ele está aguardando um runbook worker na nuvem ficar disponível. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.  
4.	Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, deveremos ver *Olá Mundo*.  
	![Saída do painel de teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5.	Feche o Painel de teste para retornar à tela.

## Etapa 4: publicar e iniciar o runbook

O runbook que acabamos de criar ainda está em Modo de rascunho. Precisamos publicá-lo antes que possamos executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Em nosso caso, não temos uma versão Publicada ainda porque que acabamos de criar o runbook.

1.	Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.  
	![Botão Publicar](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2.	Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **Status de Criação** de **Publicado**.
3.	Role para a direita para exibir o painel de **MyFirstRunbook-PowerShell**.  
	As opções na parte superior nos permitem iniciar o runbook, exibi-lo, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
4.	Nós só queremos iniciar o runbook, então, clique em **Iniciar** e clique em **Ok** quando a folha iniciar runbook for aberta.  
	![Botão Iniciar](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5.	Um painel de trabalho é aberto para o trabalho de runbook que acabamos de criar. Podemos fechar esse painel, mas nesse caso, o deixaremos aberto para que possamos acompanhar o progresso do trabalho.
6.	O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que vimos quando testamos o runbook.  
	![Resumo do trabalho](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7.	Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel Saída é aberto e podemos ver nosso *Olá Mundo*.  
	![Saída do trabalho](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	Feche o painel Saída.
9.	Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Devemos ver apenas *Olá Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.  
	![Todos os logs](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10.	Feche o painel Fluxos e o painel Trabalho para retornar ao painel MyFirstRunbook-PowerShell.
11.	Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Devemos ver apenas um trabalho listado, já que executamos o trabalho apenas uma vez.  
	![Lista de trabalhos](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12.	Você pode clicar neste trabalho para abrir o mesmo painel Trabalho que exibimos quando iniciamos o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## Etapa 5: adicionar autenticação para gerenciar recursos do Azure

Testamos e publicamos nosso runbook, mas até o momento ele não faz nada útil. Gostaríamos que ele gerenciasse recursos do Azure. No entanto, ele não será capaz de fazer isso a menos que o autentiquemos usando as credenciais mencionadas nos [pré-requisitos](#prerequisites). Fazemos isso com o cmdlet **Add-AzureRmAccount**.

1.	Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-PowerShell.  
	![Editar runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2.	Já não precisamos da linha **Write-Output**, então vamos continuar e excluí-la.
3.	Digite ou copie e cole o seguinte código que irá manipular a autenticação com sua conta de Automação Executar Como:

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ``` 
<br>
4.	Clique no **Painel de teste** para que possamos testar o runbook.
5.	Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá receber uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Isso confirma que a credencial é válida. <br> ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## Etapa 6: adicionar código para iniciar uma máquina virtual

Agora que nosso runbook está se autenticando em nossa assinatura do Azure, podemos gerenciar recursos. Adicionaremos um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual na sua assinatura do Azure e, por enquanto, embutiremos esse nome no cmdlet.

1.	Depois de *Add-AzureRmAccount*, digite *Start-AzureRmVM -Name 'NomeDaVM' -ResourceGroupName 'NomedoGrupodeRecursos'* fornecendo o nome e o nome do Grupo de Recursos da máquina virtual para iniciar.
    
    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
     Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
     ```
<br>
2.	Salve o runbook e, para que possamos testá-lo, clique em **Painel de teste**.
3.	Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## Etapa 7: adicionar um parâmetro de entrada ao runbook

No momento, nosso runbook inicia a máquina virtual que codificamos no runbook, mas ele seria mais útil pudéssemos especificar a máquina virtual quando o runbook é iniciado. Agora adicionaremos parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1.	Adicione parâmetros para *NomeDaVM* e *NomeDoGrupoDeRecursos* ao runbook e use essas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo abaixo.
	
    ```
    Param(
       [string]$VMName,
       [string]$ResourceGroupName
    )
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
     Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
     ```
<br>
2.	Salve o runbook e abra o Painel de teste. Observe que agora você pode fornecer valores para as duas variáveis de entrada que serão usadas no teste.
3.	Feche o Painel de teste.
4.	Clique em **Publicar** para publicar a nova versão do runbook.
5.	Pare a máquina virtual que você iniciou na etapa anterior.
6.	Clique em **Iniciar** para iniciar o runbook. Digite o **NomeDaVM** e o **NomeDoGrupoDeRecursos** da máquina virtual que você iniciará.  
	![Passar parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)  
7.	Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.

## Diferenças do fluxo de trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, os mesmos recursos e o mesmo gerenciamento dos runbooks do fluxo de trabalho do PowerShell, mas há algumas diferenças e limitações:

1.	Os runbooks do PowerShell são executados com mais rapidez em comparação aos runbooks do fluxo de trabalho do PowerShell, já que não têm a etapa de compilação.
2.	Os runbooks do fluxo de trabalho do PowerShell dão suporte a pontos de verificação, ao uso de pontos de verificação e podem retomar de qualquer ponto no runbook, enquanto os runbooks do PowerShell só podem retomar desde o início.
3.	Os runbooks do fluxo de trabalho do PowerShell dão suporte à execução em paralelo e serial, enquanto os runbooks do PowerShell só podem executar comandos de forma serial.
4.	Em um runbook do fluxo de trabalho do PowerShell, uma atividade, um comando ou um bloco de script pode ter seu próprio espaço de execução, enquanto em um runbook do PowerShell, tudo em um script é executado em um único espaço de execução. Também há algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook nativo do PowerShell e um runbook do Fluxo de Trabalho do PowerShell.

## Próximas etapas

-	Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
-	Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
-	Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
-	Para saber mais sobre o recurso de suporte a scripts do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!---HONumber=AcomDC_0720_2016-->
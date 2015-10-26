<properties 
   pageTitle="Ativos de variáveis na Automação do Azure | Microsoft Azure"
   description="Ativos variáveis são valores que estão disponíveis para todos os runbooks na Automação do Azure. Este artigo explica os detalhes das variáveis e como trabalhar com elas na criação de textos e gráficos."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="bwren" />

# Ativos variáveis na Automação do Azure

Ativos variáveis são valores que estão disponíveis para todos os runbooks na sua conta de automação. Eles podem ser criados, modificados e recuperados no portal do Azure, no Windows PowerShell e a partir de um runbook. As variáveis de automação são úteis para os seguintes cenários:

- Compartilhar um valor entre vários runbooks.

- Compartilhar um valor entre vários trabalhos do mesmo runbook.

- Gerenciar um valor do portal ou da linha de comando do Windows PowerShell usada pelos runbooks.

As variáveis de automação são mantidas para que continuem disponíveis mesmo se o runbook falhar. Isso também permite que um valor seja definido por um runbook e depois usado por outro, ou usado pelo mesmo runbook na próxima vez em que for executado.

Quando uma variável é criada, você pode definir que ele seja armazenado criptografado. Quando uma variável é criptografada, ela é armazenada com segurança na Automação do Azure e seu valor não pode ser recuperado do cmdlet [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) enviado como parte do módulo do PowerShell do Azure. A única maneira de recuperar um valor criptografado é através da atividade **Get-AutomationVariable** em um runbook.

>[AZURE.NOTE]Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## Tipos de variável

Quando você cria uma variável com o portal do Azure, deve especificar um tipo de dados na lista suspensa para que o portal possa exibir o controle adequado para a inserção do valor da variável. A variável não está restrita a esse tipo de dados, mas você deve definir a variável usando o Windows PowerShell se desejar especificar um valor de um tipo diferente. Se você especificar **Indefinido**, o valor da variável será definido como **$null** e você deverá definir o valor com o cmdlet [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) ou atividade **Set-AutomationVariable**. Você não pode criar ou alterar o valor de um tipo complexo de variável no portal, mas pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos serão retornados como um [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Você pode armazenar vários valores para uma única variável criando uma matriz ou hashtable e salvando-a na variável.

## Atividades de fluxo de trabalho e cmdlets

Os cmdlets na tabela a seguir são usados para criar e gerenciar variáveis de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo PowerShell do Azure](../powershell-install-configure.md) que está disponível para uso em runbooks de Automação.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Recupera o valor de uma variável existente.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Cria uma nova variável e define o seu valor.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Remove uma variável existente.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Define o valor de uma variável existente.|

As atividades de fluxo de trabalho na tabela a seguir são usadas para acessar variáveis de automação em um runbook. Elas só estão disponíveis para uso em um runbook e não são enviadas como parte do módulo do PowerShell do Azure.

|Atividades de fluxo de trabalho|Descrição|
|:---|:---|
|Get-AutomationVariable|Recupera o valor de uma variável existente.|
|Set-AutomationVariable|Define o valor de uma variável existente.|

>[AZURE.NOTE]Evite usar variáveis no parâmetro Name de **Get-AutomationVariable** em um runbook, pois isso pode complicar a descoberta de dependências entre runbooks e variáveis de automação no momento do design.

## Criando uma nova variável de automação

### Para criar uma nova variável com o portal do Azure

1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
1. Clique em **Adicionar Configuração** na parte inferior da tela.
1. Clique em **Adicionar Variável**.
1. Conclua o assistente e clique na caixa de seleção para salvar a nova variável.


### Para criar uma nova variável com o portal de visualização do Azure

1. Na sua conta de automação, clique na parte de **Ativos** para abrir a folha de **Ativos**.
1. Clique na parte de **Variáveis** para abrir a folha de **Variáveis**.
1. Clique em **Adicionar uma variável** na parte superior da folha.
1. Preencha o formulário e clique em **Criar** para salvar a nova variável.


### Para criar uma nova variável com o Windows PowerShell

O cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) cria uma nova variável e define o seu valor inicial. Você pode recuperar o valor usando [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx). Se o valor for um tipo simples, é retornado o mesmo tipo. Se for um tipo complexo, um **PSCustomObject** é retornado.

Os comandos de exemplo a seguir mostram como criar uma variável do tipo cadeia de caracteres e retornar o seu valor.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Os comandos de exemplo a seguir mostram como criar uma variável do tipo complexo e retornar as suas propriedades. Nesse caso, é usado um objeto máquina virtual de **Get-AzureVM**.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## Usando uma variável em um runbook

Use a atividade **Set-AutomationVariable** para definir o valor de uma variável de automação em um runbook e a **Get-AutomationVariable** para recuperá-lo. Você não deve usar os cmdlets **Set-AzureAutomationVariable** ou **Get-AzureAutomationVariable** em um runbook, já que eles são menos eficientes do que as atividades de fluxo de trabalho. Também não é possível recuperar o valor de variáveis protegidas com o **Get-AzureAutomationVariable**. A única maneira de criar uma nova variável de dentro de um runbook é usar o cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Exemplos de runbook textual

#### Definindo e recuperando um valor simples de uma variável

Os comandos de exemplo a seguir mostram como definir e recuperar uma variável em um runbook textual. Nesse exemplo, presume-se que as variáveis do tipo integer denominadas *NumberOfIterations* e *NumberOfRunnings*, além de uma variável do tipo cadeia de caracteres denominada *SampleMessage*, já foram criadas.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### Definindo e recuperando um objeto complexo em uma variável

O código de exemplo a seguir mostra como atualizar uma variável com um valor complexo em um runbook textual. Nesse exemplo, uma máquina virtual do Azure é recuperada com **Get-AzureVM** e salva em uma variável de automação existente. Como explicado em [Tipos de variáveis](#variable-types), ela é armazenada como um PSCustomObject.

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


No código a seguir, o valor é recuperado da variável e usado para iniciar a máquina virtual.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Definindo e recuperando uma coleção em uma variável

O código de exemplo a seguir mostra como usar uma variável com uma coleção de valores complexos em um runbook textual. Nesse exemplo, várias máquinas virtuais do Azure são recuperadas com **Get-AzureVM** e salvas em uma variável de automação existente. Como explicado em [Tipos de variáveis](#variable-types), elas são armazenadas como uma coleção de PSCustomObjects.

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

No código a seguir, a coleção é recuperada da variável e usada para iniciar cada máquina virtual.

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### Exemplos de runbook gráfico

Em um runbook gráfico, você adiciona o **Get-AutomationVariable** ou o **Set-AutomationVariable**clicando na variável com o botão direito do mouse no painel Biblioteca do editor gráfico e selecionando a atividade desejada.

![Adicionar variável à tela](media/automation-variables/variable-add-canvas.png)

#### Definindo valores em uma variável

A imagem a seguir mostra as atividades de exemplo para atualizar uma variável com um valor simples em um runbook gráfico. Nesse exemplo, uma única máquina virtual do Azure é recuperada com **Get-AzureVM** e o nome do computador é salvo em uma variável de automação existente com um tipo de cadeia de caracteres. Não importa se o [link é um pipeline ou uma sequência](automation-graphical-authoring-intro.md#links-and-workflow), já que esperamos somente um único objeto na saída.

![Definir variável simples](media/automation-variables/set-simple-variable.png)

A imagem a seguir mostra as atividades usadas para atualizar uma variável com um valor complexo em um runbook gráfico. A única alteração em relação ao exemplo anterior é não especificar um **Caminho de campo** para a **Saída de atividade** na atividade **Set-AutomationVariable**, de forma que o objeto seja armazenado em vez de ser apenas uma propriedade do objeto. Como explicado em [Tipos de variáveis](#variable-types), ele é armazenado como um PSCustomObject.

![Definir variável complexa](media/automation-variables/set-complex-variable.png)

A imagem a seguir mostra uma funcionalidade semelhante ao exemplo anterior, com várias máquinas virtuais salvas na variável. Um [link de sequência](automation-graphical-authoring-intro.md#links-and-workflow) deve ser usado nesse caso para que a atividade **Set-AutomationVariable** receba todo o conjunto de máquinas virtuais como uma coleção. Se um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) fosse usado, a atividade **Set-AutomationVariable** seria executada separadamente para cada objeto e somente a última máquina virtual na coleção seria salva. Como explicado em [Tipos de variáveis](#variable-types), elas são armazenadas como uma coleção de PSCustomObjects.

![Definir variável de coleção complexa](media/automation-variables/set-complex-variable-collection.png)

#### Recuperando valores de uma variável

A imagem a seguir mostra atividades de exemplo que recuperam e usam uma variável em um runbook gráfico. A primeira atividade recupera as máquinas virtuais que foram salvas na variável no exemplo anterior. O link deve ser um [pipeline](automation-graphical-authoring-intro.md#links-and-workflow) para que a atividade **Start-AzureVM** seja executada uma vez para cada objeto enviado da atividade **Get-AutomationVariable**. Isso funcionará da mesma forma, quer estejam um ou vários objetos armazenados na variável. A atividade **Start-AzureVM** usa propriedades de PSCustomObject que representam cada máquina virtual.

![Obter variável complexa](media/automation-variables/get-complex-variable.png)

A imagem a seguir mostra como filtrar os objetos que são armazenados em uma variável em um runbook gráfico. Uma [condição](automation-graphical-authoring-intro.md#links-and-workflow) é adicionada ao link no exemplo anterior para filtrar somente essas máquinas virtuais que foram interrompidas quando a variável foi definida.

![Obter variável complexa filtrada](media/automation-variables/get-complex-variable-filter.png)


## Artigos relacionados

- [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=Oct15_HO3-->
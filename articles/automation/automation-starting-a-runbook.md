<properties 
   pageTitle="Como iniciar um runbook na Automação do Azure | Microsoft Azure"
   description="Resume os métodos diferentes que podem ser usados para iniciar um runbook na Automação do Azure e fornece detalhes sobre como usar o portal do Azure e o Windows PowerShell."
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
   ms.date="01/19/2016"
   ms.author="bwren;sngun"/>

# Como iniciar um Runbook na Automação do Azure

A tabela a seguir o ajuda a determinar o método de inicialização de runbook na Automação do Azure mais adequado ao seu cenário específico. Este artigo inclui detalhes sobre como iniciar um runbook com o portal do Azure e com o Windows PowerShell. Detalhes sobre outros métodos são fornecidos em outros documentos que você pode acessar através dos links abaixo.

<table>
 <tr>
  <td>MÉTODO</td>
  <td>CARACTERÍSTICAS</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Portal do Azure</a></td>
  <td>
   <ul>
    <li>Método mais simples com interface do usuário interativa.</li>
    <li>Formulário para fornecer valores de parâmetro simples.</li>
    <li>Controle facilmente o estado do trabalho.</li>
    <li>Acesso autenticado com o logon do Azure.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Chame da linha de comando com os cmdlets do Windows PowerShell.</li>
     <li>Pode ser incluído em uma solução automatizada com várias etapas.</li>
     <li>A solicitação é autenticada com certificado ou entidade de usuário/entidade de serviço OAuth.</li>
     <li>Fornece valores de parâmetro simples e complexos.</li>
     <li>Controle o estado do trabalho.</li>
     <li>É necessário um cliente para dar suporte a cmdlets do PowerShell.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">API de Automação do Azure</a></td>
  <td>
   <ul>
    <li>Método mais flexível, mas também o mais complexo.</li>
    <li>Chame de qualquer código personalizado que possa fazer solicitações HTTP.</li>
    <li>A solicitação autenticada com certificado ou entidade de usuário/entidade de serviço OAuth.</li>
    <li>Fornece valores de parâmetro simples e complexos.</li>
    <li>Controle o estado do trabalho.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Inicie o runbook de uma solicitação HTTP única.</li>
    <li>Autenticado com token de segurança na URL.</li>
    <li>O cliente não pode substituir valores de parâmetro especificados quando o webhook foi criado. O runbook pode definir um único parâmetro que é populado com os detalhes da solicitação HTTP.</li>
    <li>Sem capacidade de rastrear o estado do trabalho através da URL do webhook.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Responder a um Alerta do Azure</a></td>
  <td>
   <ul>
    <li>Inicie um runbook em resposta a um alerta do Azure.</li>
    <li>Configure o webhook para o runbook e vincule ao alerta.</li>
    <li>Autenticado com token de segurança na URL.</li>
    <li>Atualmente, dá suporte ao alerta apenas para as Métricas.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Agenda</a></td>
  <td>
   <ul>
    <li>Inicie automaticamente o runbook em um cronograma horário, diário ou semanal.</li>
    <li>Manipule a agenda pelo portal do Azure, por cmdlets do PowerShell ou pela a API do Azure.</li>
    <li>Fornece valores de parâmetro a serem usados com a agenda.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-child-runbooks/">De outro runbook</a></td>
  <td>
   <ul>
    <li>Use um runbook como uma atividade em outro runbook</li>
    <li>É útil para funcionalidades usadas por vários runbooks.</li>
    <li>Fornece valores de parâmetro para o runbook filho e usa a saída no runbook pai.</li>
   </ul>
  </td>
 </tr>
</table>
<br>


A imagem a seguir ilustra o processo passo a passo detalhado no ciclo de vida de um runbook. Ela inclui várias maneiras de inicialização de um runbook na Automação do Azure, os componentes necessários para uma máquina local executar runbooks de Automação do Azure e as interações entre diferentes componentes. Para saber mais sobre a execução de runbooks de Automação em seu datacenter, confira [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Arquitetura do runbook](media/automation-starting-runbook/runbooks-architecture.png)

## Iniciando um runbook com o portal do Azure

1. No portal do Azure, selecione **Automação** e clique no nome de uma conta de automação.
1. Selecione a guia **Runbooks**.
1. Selecione um runbook e clique em **Iniciar**.
1. Se o runbook tiver parâmetros, você deverá fornecer valores com uma caixa de texto para cada parâmetro. Confira [Parâmetros de runbook](#Runbook-parameters) abaixo para mais detalhes sobre os parâmetros.
1. Selecione **Exibir trabalho** ao lado da mensagem **Iniciando** ou selecione a guia **Trabalhos** do runbook para exibir o seu status de trabalho.

## Iniciando um runbook com o portal de visualização do Azure

1. Na sua conta de automação, clique na parte de **Runbooks** para abrir a folha de **Runbooks**.
1. Clique em um runbook para abrir sua folha de **Runbook**.
2. Clique em **Iniciar**.
1. Se o runbook não tiver parâmetros, você será solicitado a confirmar se deseja iniciá-lo. Se o runbook tiver parâmetros, a folha **Iniciar Runbook** será aberta para que você possa fornecer os valores de parâmetro. Confira [Parâmetros de runbook](#Runbook-parameters) abaixo para mais detalhes sobre os parâmetros.
3. A folha de **Trabalho** é aberta para que você possa controlar o status do trabalho.


## Iniciando um runbook com o Windows PowerShell

Você pode usar [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) para iniciar um runbook com o Windows PowerShell. O código de exemplo a seguir inicia um runbook chamado Test-Runbook.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook retorna um objeto de trabalho que você pode usar para controlar seu status quando o runbook é iniciado. Você pode usar esse objeto de trabalho com [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para determinar o status do trabalho e [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) para obter sua saída. O código de exemplo a seguir inicia um runbook chamado Test-Runbook, aguarda até que ele seja concluído e exibe a sua saída.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Se o runbook exigir parâmetros, você deve fornecê-los como uma [hashtable](http://technet.microsoft.com/library/hh847780.aspx), em que a chave da hashtable corresponde ao nome do parâmetro e o valor é o valor do parâmetro. O exemplo a seguir mostra como iniciar um runbook com dois parâmetros de cadeia de caracteres chamados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booliano denominado Show. Para saber mais sobre parâmetros, confira [Parâmetros de runbook](#Runbook-parameters) abaixo.

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Parâmetros de runbook

Quando você inicia um runbook usando o Portal de Gerenciamento do Azure ou o Windows PowerShell, a instrução é enviada pelo serviço Web da Automação do Azure. Esse serviço não dá suporte a parâmetros com tipos de dados complexos. Se você precisa fornecer um valor para um parâmetro complexo, você deve chamá-lo embutido de outro runbook, como descrito em [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

O serviço Web da Automação do Azure fornece uma funcionalidade especial para os parâmetros usando certos tipos de dados, conforme descrito nas seções a seguir.

### Valores nomeados

Se o parâmetro é do tipo de dados [object], você pode usar o seguinte formato JSON para enviar-lhe uma lista de valores nomeados: *{"Name1":Value1, "Name2":Value2, "Name3":Value3}*. Esses valores devem ser tipos simples. O runbook receberá o parâmetro como um [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) com propriedades que correspondem a cada valor nomeado.

Considere o runbook de teste a seguir que aceita um parâmetro chamado user.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][object]$user
	   )
	    if ($user.Show) {
	        foreach ($i in 1..$user.RepeatCount) {
	            $user.FirstName
	            $user.LastName
	        }
	    } 
	}

O texto a seguir pode ser usado para o parâmetro user.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Isso resulta na saída abaixo.

	Joe
	Smith
	Joe
	Smith

### Matrizes

Se o parâmetro for uma matriz, como [array] ou [string], você pode usar o seguinte formato JSON para enviar-lhe uma lista de valores: *[Value1,Value2,Value3]*. Esses valores devem ser tipos simples.

Considere o runbook de teste a seguir que aceita um parâmetro chamado *user*.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][array]$user
	   )
	    if ($user[3]) {
	        foreach ($i in 1..$user[2]) {
	            $ user[0]
	            $ user[1]
	        }
	    } 
	}

O texto a seguir pode ser usado para o parâmetro user.

	["Joe","Smith",2,true]

Isso resulta na saída abaixo.

	Joe
	Smith
	Joe
	Smith

### Credenciais

Se o parâmetro é do tipo de dados **PSCredential**, você pode fornecer o nome de um [ativo de credenciais](automation-credentials.md) da Automação do Azure . O runbook irá recuperar as credenciais com o nome que você especificar.

Considere o runbook de teste a seguir que aceita um parâmetro chamado credential.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

O texto a seguir pode ser usado para o parâmetro user supondo que havia um ativo de credenciais chamado *Minhas credenciais*.

	My Credential

Supondo que o nome de usuário nas credenciais foi *vmonte*, isso resulta na saída abaixo.

	jsmith

## Próximas etapas

- A arquitetura do runbook no artigo atual fornece uma descrição detalhada sobre os runbooks híbridos. Para saber mais detalhes, confira [Runbooks filhos na Automação do Azure](automation-child-runbooks.md) 

<!---HONumber=AcomDC_0128_2016-->
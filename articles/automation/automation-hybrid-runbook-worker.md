<properties 
   pageTitle="Runbook Workers Híbridos da Automação do Azure"
   description="Este artigo fornece informações sobre como instalar e usar Runbook Worker Híbrido, que é um recurso da Automação do Azure que permite que você execute runbooks em máquinas no seu data center local."
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
   ms.date="05/11/2015"
   ms.author="bwren" />

# Runbook Workers Híbridos da Automação do Azure

Os Runbooks na Automação do Azure não podem acessar recursos no seu data center local, já que eles são executados na nuvem do Azure. O recurso Runbook Worker Híbrido da Automação do Azure permite executar runbooks em máquinas localizadas no seu data center para gerenciar recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores locais onde eles são executados.

Essa funcionalidade está ilustrada na imagem a seguir.

![Visão geral do Runbook Worker Híbrido](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Você pode designar um ou mais computadores no seu data center para atuar como um Runbook Worker Híbrido e executar runbooks da Automação do Azure. Cada trabalhador exige um Agente de Gerenciamento Microsoft com uma conexão ao Azure Operational Insights e ao ambiente de runbook da Automação do Azure. O Operational Insights é usado somente para instalar e manter o agente de gerenciamento e para monitorar a funcionalidade do trabalhador. A entrega de runbooks e as instruções para executá-los são desempenhadas pela Automação do Azure.

![Componentes do Runbook Worker Híbrido](./media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

Não há nenhum requisito de firewall de entrada para dar suporte a Runbook Workers Híbridos. O agente no computador local inicia todas as comunicações com a Automação do Azure na nuvem. Quando um runbook é iniciado, a Automação do Azure cria uma instrução que é recuperada pelo agente. O agente então puxa o runbook e todos os parâmetros antes de executá-lo. Ele também vai recuperar os [ativos](http://msdn.microsoft.com/library/dn939988.aspx) usados pelo runbook da Automação do Azure.

## Grupos de Runbook Worker Híbrido

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Os membros do grupo determinarão qual trabalhador atenderá a solicitação. Você não pode especificar um trabalhador específico.

## Instalando o Runbook Worker Híbrido

### Preparar o ambiente de Automação do Azure

Conclua as etapas a seguir para preparar o ambiente de Automação do Azure para Runbook Workers Híbridos.

#### 1. Criar um espaço de trabalho do Azure Operational Insights
Se você não tiver um espaço de trabalho do Operational Insights em sua conta do Azure, crie um usando as instruções em [Configurar seu espaço de trabalho do Operational Insights](../operational-insights-setup-workspace). Você pode usar um espaço de trabalho existente se já tiver um.

#### 2. Implantar Solução de automação
A Solução de automação no Operational Insights envia os componentes necessários para configurar e dar suporte ao ambiente de runbook. Siga as instruções em [Soluções do Operational Insights](../operational-insights-add-solution) para instalar o pacote de **Automação do Azure**.

### Configurar máquinas locais
Conclua as etapas a seguir para cada uma das máquinas locais que atuarão como um Runbook Worker Híbrido.


#### 1. Instale o Agente de Gerenciamento da Microsoft
O Agente de Gerenciamento da Microsoft conecta o computador ao Operational Insights e permite que ele execute a lógica das soluções. Siga as instruções em [Conectar computadores diretamente ao Operational Insights](../operational-insights-direct-agent) para instalar o agente na máquina local e conectá-la ao Operational Insights.

#### 2. Instalar o ambiente de runbook e conectá-lo à Automação do Azure
Quando você adiciona um computador ao Operational Insights, a Solução de automação envia o módulo do PowerShell **HybridRegistration** que contém o cmdlet **Add-HybridRunbookWorker**. Você pode usar esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir para importar o módulo.

	Import-Module HybridRegistration 

Se você receber uma mensagem de erro informando que o arquivo do módulo não foi encontrado, talvez seja necessário usar o comando a seguir que usa o caminho completo para o arquivo do módulo.

	Import-Module "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomationFiles\HybridRegistration\HybridRegistration.psd1"

Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a seguinte sintaxe:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>


- **Name** é o nome do Grupo de Runbook Worker Híbrido. Quando esse grupo já existe na conta de automação, o computador atual é adicionado a ele. Se ele não existir, será adicionado.
- **EndPoint** é a URL do serviço do Agente. Você pode obter isso no portal de visualização do Azure na folha **Gerenciar Chaves**.  
- **Token** é a **Chave de Acesso Primária** na folha **Gerenciar Chaves**. Você pode abrir a folha Gerenciar Chaves clicando no ícone de chave no Painel de elementos da conta de automação.<br><br>![Visão geral do Runbook Worker Híbrido](./media/automation-hybrid-runbook-worker/elements-panel-keys.png)


#### 3. Instalar módulos do PowerShell 
Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. No entanto, esses módulos não são implantados automaticamente em máquinas locais; portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure que é instalado por padrão, fornecendo acesso a cmdlets a todos os serviços e atividades do Azure da Automação do Azure.

Como a principal finalidade do recurso Runbook Worker Híbrido é gerenciar recursos locais, você provavelmente precisará instalar os módulos que dão suporte a esses recursos. Veja [Instalar Módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell.

## Iniciar runbooks no Runbook Worker Híbrido

[Como iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook) descreve métodos diferentes para se iniciar um runbook. O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido. Se um grupo for especificado, o runbook é recuperado e executado pelos trabalhadores nesse grupo. Se essa opção não for especificada, ele é executado na Automação do Azure como de costume.

Quando você inicia um runbook no portal de visualização do Azure, a opção **Executar em** fica disponível e você pode selecionar **Azure** ou **Worker Híbrido**. Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**. Você poderia usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo de Runbook Worker Híbrido chamado MyHybridGroup usando o Windows PowerShell.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](http://azure.microsoft.com/downloads) se tiver uma anterior instalada. Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do Windows PowerShell. Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador. Atualmente, você não pode iniciar um runbook em um Runbook Worker Híbrido de outro runbook, pois isso exigiria que a versão mais recente do Powershell do Azure estivesse instalada em sua Conta de automação. A versão mais recente será automaticamente atualizada na Automação do Azure e enviada aos trabalhadores em breve.


## Criando runbooks para Runbook Worker Híbrido

Não há nenhuma diferença na estrutura de runbooks executados na Automação do Azure e daqueles que executam em um Runbook Worker Híbrido. Os runbooks que você usar com cada um provavelmente serão bem diferentes uns dos outros, já que os runbooks para um Runbook Worker Híbrido normalmente gerenciam recursos locais em seu data center, enquanto runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

### Permissões de runbook

Os runbooks serão executados no contexto da conta de Sistema local no Runbook Worker Híbrido; portanto devem fornecer sua própria autenticação aos recursos que irão acessar. Eles não podem usar o mesmo [método que é normalmente usado para autenticar runbooks nos recursos do Azure](automation-configuring.md/#configuring-authentication-to-azure-resources), já que eles acessarão recursos fora do Azure.

Você pode usar ativos de [Credencial](http://msdn.microsoft.com/library/dn940015.aspx) e [Certificado](http://msdn.microsoft.com/library/dn940013.aspx) no runbook com cmdlets que permitem que você especifique as credenciais para autenticar com recursos diferentes. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

Você também pode aproveitar o [InlineScript](automation-runbook-concepts.md/#inline-script), que permitirá a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](http://technet.microsoft.com/library/jj129719.aspx).


### Criação e teste de runbooks

Você pode editar um runbook para Runbook Worker Híbrido na Automação do Azure, mas pode ter problemas se tentar testá-lo no editor. Os módulos do PowerShell que acessam os recursos locais podem não estar instalados em seu ambiente de Automação do Azure. Nesse caso, o teste falhará. Se você instalar os módulos necessários, o runbook será executado, mas não será capaz de acessar recursos locais para um teste completo.

## Relação com o Service Management Automation

O [SMA (Service Management Automation)](http://aka.ms/runbookauthor/sma) é um componente do WAP (Microsoft Azure Pack) que permite que você execute os mesmos runbooks com suporte da Automação do Azure em seu data center local. Ao contrário da Automação do Azure, o SMA exige uma instalação local que inclui o Portal de Gerenciamento do Micrososft Azure Pack e um banco de dados para manter a configuração do SMA e runbooks. A Automação do Azure fornece esses serviços na nuvem e requer apenas que você mantenha os Runbook Workers Híbridos no seu ambiente local.

Se você for um usuário existente do SMA, pode mover seus runbooks para a Automação do Azure para serem usados com o Runbook Worker Híbrido sem alterações, supondo que eles executem sua própria autenticação aos recursos, como descrito em [Criar runbooks para Runbook Worker Híbrido](#creating-runbooks-for-hybrid-runbook-worker). Os runbooks em SMA são executados no contexto da conta de serviço no servidor trabalhador que pode fornecer a autenticação para os runbooks.

Você pode usar os critérios a seguir para determinar se a Automação do Azure com Runbook Worker Híbrido ou o Service Management Automation é mais adequado às suas necessidades.

- O SMA requer uma instalação local do Microsoft Azure Pack, que tem mais recursos locais e custos mais altos de manutenção do que a Automação do Azure, que só precisa de um agente instalado nos runbook workers locais. Os agentes são gerenciados pelo Operational Insights, o que diminui os custos de manutenção. 
- A Automação do Azure armazena seus runbooks na nuvem e os entrega a Runbooks Workers Híbridos locais. Se a sua política de segurança não permite esse comportamento, você deve usar o SMA.
- O Microsoft Azure Pack é um download gratuito, enquanto a Automação do Azure pode incorrer em encargos de assinatura. Azure. Deve manter vários bancos de dados para o SMA.
- A Automação do Azure com o Runbook Worker Híbrido permite que você gerencie runbooks para recursos em nuvem e locais em um único local, em vez de gerenciar separadamente tanto a Automação do Azure quando o SMA.
- A Automação do Azure tem recursos avançados que incluem a criação gráfica e não estão disponíveis no SMA. 


## Artigos relacionados

- [Como iniciar um Runbook na Automação do Azure](../automation-starting-a-runbook)
- [Como editar um Runbook na Automação do Azure](https://msdn.microsoft.com/library/dn879137.aspx)

<!---HONumber=58--> 
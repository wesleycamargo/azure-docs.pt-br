<properties
   pageTitle="Hybrid Runbook Workers da Automação do Azure | Microsoft Azure"
   description="Este artigo fornece informações sobre como instalar e usar Runbook Worker Híbrido, que é um recurso da Automação do Azure que permite que você execute runbooks em máquinas no seu data center local."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/18/2016"
   ms.author="bwren" />

# Runbook Workers Híbridos da Automação do Azure

Os Runbooks na Automação do Azure não podem acessar recursos no seu data center local, já que eles são executados na nuvem do Azure. O recurso Runbook Worker Híbrido da Automação do Azure permite executar runbooks em máquinas localizadas no seu data center para gerenciar recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a uma ou mais máquinas locais.

Essa funcionalidade está ilustrada na imagem a seguir.

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Você pode designar um ou mais computadores no seu data center para atuar como um Runbook Worker Híbrido e executar runbooks da Automação do Azure. Cada trabalho exige o Agente de Gerenciamento da Microsoft com uma conexão ao Microsoft Operations Management Suite e o ambiente de runbook da Automação do Azure. O Operations Management Suite é usado somente para instalar e manter o agente de gerenciamento, bem como para monitorar a funcionalidade do trabalho. A entrega de runbooks e as instruções para executá-los são desempenhadas pela Automação do Azure.

![Componentes do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

>[AZURE.NOTE] O Insights Operacionais está no processo de integração ao Operations Management Suite, e você poderá ver um dos dois nomes usado no portal e na documentação.

Não há nenhum requisito de firewall de entrada para dar suporte a Runbook Workers Híbridos. O agente no computador local inicia todas as comunicações com a Automação do Azure na nuvem. Quando um runbook é iniciado, a Automação do Azure cria uma instrução que é recuperada pelo agente. O agente então puxa o runbook e todos os parâmetros antes de executá-lo. Ele também vai recuperar os [ativos](http://msdn.microsoft.com/library/dn939988.aspx) usados pelo runbook da Automação do Azure.

>[AZURE.NOTE] Atualmente, os Hybrid Runbook Workers não dão suporte às [configurações DSC](automation-dsc-overview.md).

## Grupos de Runbook Worker Híbrido

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Os membros do grupo determinarão qual trabalhador atenderá a solicitação. Você não pode especificar um trabalhador específico.

## Requisitos do Hybrid Runbook Worker

Você deve designar pelo menos um computador local para executar trabalhos de runbook híbrido. Esse computador deve ter o seguinte:

- Windows Server 2012 ou posterior
- Windows PowerShell 4.0 ou posterior
- Mínimo de dois núcleos e 4 GB de RAM

Considere as seguintes recomendações para hybrid workers:

- Designe vários hybrid workers em cada grupo para alta disponibilidade.  
- Hybrid workers podem coexistir com os servidores de runbook do Service Management Automation ou do System Center Orchestrator.
- Pense na possibilidade de usar um computador fisicamente localizado ou próximo da região da sua conta de automação, uma vez que os dados do trabalho são enviados de volta à Automação do Azure quando um trabalho é concluído.

Requisitos de firewall:

- O computador local que executa o Hybrid Runbook Worker deve ter acesso de saída a *.cloudapp.net nas portas 443, 9354 e 30000-30199.

## Instalando o Runbook Worker Híbrido
O procedimento abaixo descreve como instalar e configurar o Hybrid Runbook Worker. Execute as duas primeiras etapas uma vez para seu ambiente de Automação e depois repita as etapas restantes para cada computador de trabalho.

### 1\. Criar o espaço de trabalho do Operations Management Suite
Se você ainda não tiver um espaço de trabalho do Operations Management Suite, crie um usando as instruções em [Configurar seu espaço de trabalho](https://technet.microsoft.com/library/mt484119.aspx). Você pode usar um espaço de trabalho existente se já tiver um.

### 2\. Adicionar solução de Automação ao espaço de trabalho do Operations Management Suite
As soluções adicionam funcionalidade ao Operations Management Suite. A solução de Automação adiciona a funcionalidade da Automação do Azure, incluindo o suporte ao Hybrid Runbook Worker. Quando você adicionar a solução ao espaço de trabalho, ele enviará automaticamente os componentes de trabalho ao computador do agente que você instalará na próxima etapa.

Siga as instruções em [Para adicionar uma solução usando a Galeria de Soluções](https://technet.microsoft.com/library/mt674635.aspx) para adicionar a solução **Automação** ao espaço de trabalho do Operations Management Suite.

### 3\. Instale o Agente de Gerenciamento da Microsoft
O Agente de Gerenciamento da Microsoft conecta os computadores ao Operations Management Suite. Quando você instala o agente no computador local e o conecta ao espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Siga as instruções em [Conectar computadores diretamente ao Insights Operacionais](https://technet.microsoft.com/library/mt484108.aspx) para instalar o agente no computador local. Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente tiver se conectado com êxito ao Operations Management Suite, ele será listado na guia **Fontes Conectadas** do painel **Configurações** do Operations Management Suite. Você pode verificar se o agente baixou corretamente a solução de Automação quando ele tiver uma pasta chamada **AzureAutomationFiles** em C:\\Arquivos de Programas\\Microsoft Monitoring Agent\\Agent.

### 4\. Instalar o ambiente de runbook e conectá-lo à Automação do Azure
Quando você adiciona um agente ao Operations Management Suite, a solução de Automação envia por push o módulo **HybridRegistration** do PowerShell que contém o cmdlet **Add-HybridRunbookWorker**. Você pode usar esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1


Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a seguinte sintaxe:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Você pode obter as informações necessárias para esse cmdlet na folha **Gerenciar Chaves** no portal do Azure. Abra essa folha clicando no ícone de chave no painel Elementos da conta de automação.

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name** é o nome do Grupo de Runbook Worker Híbrido. Quando esse grupo já existe na conta de automação, o computador atual é adicionado a ele. Se ele não existir, será adicionado.
- **EndPoint** é o campo **URL** na folha **Gerenciar Chaves**.
- **Token** é a **Chave de Acesso Primária** na folha **Gerenciar Chaves**.  

Use a opção **-Verbose** com **Add-HybridRunbookWorker** para receber informações detalhadas sobre a instalação.

### 5\. Instalar módulos do PowerShell
Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. No entanto, esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure que é instalado por padrão, fornecendo acesso a cmdlets a todos os serviços e atividades do Azure da Automação do Azure.

Como a principal finalidade do recurso Runbook Worker Híbrido é gerenciar recursos locais, você provavelmente precisará instalar os módulos que dão suporte a esses recursos. Veja [Instalar Módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell.

## Removendo o Hybrid Runbook Worker

Você pode remover o Hybrid Runbook Worker de um computador local executando o cmdlet **Remove-HybridRunbookWorker** nesse computador. Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

## Iniciar runbooks no Runbook Worker Híbrido

[Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para se iniciar um runbook. O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido. Se um grupo for especificado, o runbook é recuperado e executado pelos trabalhadores nesse grupo. Se essa opção não for especificada, ele é executado na Automação do Azure como de costume.

Ao iniciar um runbook no portal do Azure, a opção **Executar em** ficará disponível e você poderá selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**. Você poderia usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo de Runbook Worker Híbrido chamado MyHybridGroup usando o Windows PowerShell.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE] O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do Windows PowerShell. Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador. Atualmente, você não pode iniciar um runbook em um Runbook Worker Híbrido de outro runbook, pois isso exigiria que a versão mais recente do Powershell do Azure estivesse instalada em sua Conta de automação. A versão mais recente será automaticamente atualizada na Automação do Azure e enviada aos trabalhadores em breve.

## Permissões de runbook

Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo [método que é normalmente usado para autenticação de runbooks nos recursos do Azure](automation-configuring.md#configuring-authentication-to-azure-resources), já que eles acessarão recursos fora do Azure. O runbook pode fornecer sua própria autenticação aos recursos locais, ou você pode especificar uma conta RunAs para fornecer um contexto de usuário a todos os runbooks.

### Autenticação de runbook

Por padrão, os runbooks serão executados no contexto da conta de Sistema local na máquina local; portanto, eles devem fornecer sua própria autenticação aos recursos que acessarão.

Você pode usar ativos de [Credencial](http://msdn.microsoft.com/library/dn940015.aspx) e [Certificado](http://msdn.microsoft.com/library/dn940013.aspx) no runbook com cmdlets que permitem a especificação das credenciais para autenticar com recursos diferentes. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

Você também pode aproveitar o [InlineScript](automation-powershell-workflow.md#inline-script), que permitirá a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### Conta RunAs

Em vez de os runbooks fornecerem sua própria autenticação aos recursos locais, você pode especificar uma conta **RunAs** para um grupo Hybrid Worker. Você especifica um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais e todos os runbooks serão executados sob essas credenciais ao serem executados em um Hybrid Runbook Worker no grupo.

O nome de usuário da credencial deve estar em um dos seguintes formatos:

- domínio\\nome de usuário 
- username@domain
- nome de usuário (para contas locais da máquina local)


Use o procedimento a seguir para especificar uma conta RunAs para um grupo do Hybrid Worker:

1. Crie um [ativo de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de Automação no Portal do Azure.
2. Escolha o bloco **Grupos Hybrid Worker** e selecione o grupo.
3. Escolha **Todas as configurações** e **Configurações do grupo Hybrid Worker**.
4. Altere **Executar como** de **Padrão** para **Personalizado**.
5. Escolha a credencial e clique em **Salvar**.


## Criando runbooks para Runbook Worker Híbrido

Não há nenhuma diferença na estrutura de runbooks executados na Automação do Azure e daqueles que executam em um Runbook Worker Híbrido. Os runbooks que você usar com cada um provavelmente serão bem diferentes uns dos outros, já que os runbooks para um Runbook Worker Híbrido normalmente gerenciam recursos locais em seu data center, enquanto runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Você pode editar um runbook para Runbook Worker Híbrido na Automação do Azure, mas pode ter problemas se tentar testá-lo no editor. Os módulos do PowerShell que acessam os recursos locais podem não estar instalados em seu ambiente de Automação do Azure. Nesse caso, o teste falhará. Se você instalar os módulos necessários, o runbook será executado, mas não será capaz de acessar recursos locais para um teste completo.

## Solucionando problemas de runbooks no Hybrid Runbook Worker

[A saída e as mensagens do runbook](automation-runbook-output-and-messages.md) são enviadas à Automação do Azure do Hybrid Workers assim como os trabalhos do runbook são executados na nuvem. Também é possível habilitar os fluxos Verbose e Progress da mesma forma que você faria para outros runbooks.

Os logs são armazenados localmente em cada hybrid worker, em C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes.

Se seus runbooks não forem concluídos com êxito e o resumo do trabalho mostrar um status de **suspenso**, leia o artigo de solução de problemas [Hybrid Runbook Worker: um trabalho de runbook termina com o status Suspenso](automation-troubleshooting-hrw-runbook-terminates-suspended.md).

## Relação com o Service Management Automation

O [Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) é um componente do Microsoft Azure Pack que permite que você execute os mesmos runbooks com suporte da Automação do Azure em seu data center local. Ao contrário da Automação do Azure, o SMA exige uma instalação local que inclui o Portal de Gerenciamento do Microsoft Azure Pack e um banco de dados para manter a configuração do SMA e runbooks. A Automação do Azure fornece esses serviços na nuvem e requer apenas que você mantenha os Runbook Workers Híbridos no seu ambiente local.

Se você for um usuário existente do SMA, pode mover seus runbooks para a Automação do Azure para serem usados com o Runbook Worker Híbrido sem alterações, supondo que eles executem sua própria autenticação aos recursos, como descrito em [Criar runbooks para Runbook Worker Híbrido](#creating-runbooks-for-hybrid-runbook-worker). Os runbooks em SMA são executados no contexto da conta de serviço no servidor trabalhador que pode fornecer a autenticação para os runbooks.

Você pode usar os critérios a seguir para determinar se a Automação do Azure com Runbook Worker Híbrido ou o Service Management Automation é mais adequado às suas necessidades.

- O SMA requer uma instalação local do Microsoft Azure Pack, que contém mais recursos locais e custos mais altos de manutenção do que a Automação do Azure, que só precisa de um agente instalado nos runbook workers locais. Os agentes são gerenciados pelo Operations Management Suite, diminuindo ainda mais os custos de manutenção.
- A Automação do Azure armazena seus runbooks na nuvem e os entrega a Runbooks Workers Híbridos locais. Se a sua política de segurança não permite esse comportamento, você deve usar o SMA.
- O Microsoft Azure Pack é um download gratuito, enquanto a Automação do Azure pode incorrer em encargos de assinatura.
- A Automação do Azure com o Runbook Worker Híbrido permite que você gerencie runbooks para recursos em nuvem e locais em um único local, em vez de gerenciar separadamente tanto a Automação do Azure quando o SMA.
- A Automação do Azure tem recursos avançados que incluem a criação gráfica e não estão disponíveis no SMA.


## Próximas etapas

- Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, confira [Iniciar um Runbook na automação do Azure](automation-starting-a-runbook.md)
- Para entender os diferentes procedimentos para trabalhar com runbooks do PowerShell e de Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto, confira [Editar um Runbook na Automação do Azure](automation-edit-textual-runbook.md)

 

<!---HONumber=AcomDC_0518_2016-->
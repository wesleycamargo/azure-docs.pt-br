<properties 
   pageTitle="Criação gráfica na Automação do Azure"
   description="A criação gráfica permite criar runbooks para a Automação do Azure sem trabalhar com código. Este artigo fornece uma introdução à criação gráfica e todos os detalhes necessários para iniciar a criação de um runbook gráfico."
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
   ms.date="07/10/2015"
   ms.author="bwren" />

# Criação gráfica na Automação do Azure

## Introdução

A criação gráfica permite criar runbooks para a Automação do Azure sem as complexidades do código subjacente do Fluxo de Trabalho do Windows PowerShell. Você adiciona atividades de uma biblioteca de cmdlets e outras atividades a uma tela e as vincula para formar um fluxo de trabalho.

Este artigo fornece uma introdução à criação gráfica e aos conceitos de que você precisa para iniciar a criação de um runbook gráfico.

## Runbooks gráficos

Todos os runbooks na Automação do Azure são Fluxos de Trabalho do Windows PowerShell. Os runbooks gráficos geram código do PowerShell que é executado por trabalhadores da Automação, mas não é possível exibi-lo nem modificá-lo diretamente. Os runbooks gráficos não podem ser convertidos em runbooks textuais, e um runbook textual existente não pode ser importado para o editor gráfico.


## Visão geral do editor gráfico

Você pode abrir o editor gráfico no portal de visualização do Azure criando ou editando um runbook gráfico.

![Espaço de trabalho gráfico](media/automation-graphical-authoring-intro/graphical-editor.png)


As seções a seguir descrevem os controles no editor gráfico.


### Tela
A Tela é onde você projeta seu runbook. Você adiciona atividades dos nós no controle de Biblioteca ao runbook e os conecta a links para definir a lógica do runbook.

### Controle de Biblioteca

O controle de Biblioteca é onde você seleciona [atividades](#activities) para adicionar a seu runbook. Você pode adicioná-las à tela, onde as conecta a outras atividades. Ela inclui quatro seções, descritas na tabela a seguir.

| Seção | Descrição |
|:---|:---|
| Cmdlets | Inclui todos os cmdlets que podem ser usados em seu runbook. Os cmdlets são organizados por módulo. Todos os módulos instalados em sua conta de automação estarão disponíveis. |
| Runbooks | Inclui os runbooks em sua conta de automação, organizados por marca. Como um runbook pode ter mais de uma marca, ele pode estar listado em várias marcas. Esses runbooks podem ser adicionados à tela para serem usados como um runbook filho. O runbook que está sendo editado no momento é exibido, mas não pode ser adicionado à tela, pois um runbook não pode chamar a si mesmo.
| Ativos | Inclui os [ativos de automação](http://msdn.microsoft.com/library/dn939988.aspx) em sua conta de automação que podem ser usados em seu runbook. Quando você adiciona um ativo a um runbook, ele adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos de variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou defini-la.
| Controle de Runbook | Inclui as atividades de controle de runbook que podem ser usadas em seu runbook atual. Uma *junção* recebe várias entradas e aguarda até que todas sejam concluídas antes de continuar o fluxo de trabalho. Um *script de fluxo de trabalho* executa uma ou mais linhas de código do Fluxo de Trabalho do PowerShell. Você pode usar essa atividade para código personalizado ou para funcionalidade que você não pode obter com outras atividades.|

### Controle de Configuração

O controle de Configuração é onde você fornece detalhes para um objeto selecionado na tela. As propriedades disponíveis nesse controle dependem do tipo de objeto selecionado. Quando você seleciona uma opção no controle de Configuração, ele abre folhas adicionais para fornecer informações adicionais.

### Controle de Teste

O controle de Teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você [testa um runbook gráfico](#graphical-runbook-procedures) interativamente.

## Procedimentos de runbook gráfico 

### Testando um runbook gráfico

Você pode testar a versão de rascunho de um runbook no portal de visualização do Azure, mantendo a versão publicada do runbook inalterada, ou pode testar um novo runbook antes que ele seja publicado. Isso permite que você verifique se o runbook está funcionando corretamente antes de substituir a versão publicada. Quando você testa um runbook, o runbook de Rascunho é executado, e as ações que ele realiza são concluídas. Nenhum histórico de trabalho é criado, mas a saída é exibida no Painel de Saída de Teste.

Abra o controle de Teste para um runbook abrindo o runbook para edição e, em seguida, clique no botão **Painel de teste**.

![Botão do painel de teste](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

O controle de Teste solicitará parâmetros de entrada, e você poderá iniciar o runbook clicando no botão **Iniciar**.

![Botões de controle de teste](media/automation-graphical-authoring-intro/runbook-test-start.png)

### Publicando um runbook gráfico

Cada runbook na Automação do Azure tem um Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho está pronta para ser disponibilizada, você a publica, o que substitui a versão Publicada pela versão de Rascunho.

Você pode publicar um runbook gráfico abrindo o runbook para edição e, em seguida, clicando no botão **Publicar**.

![Botão Publicar](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Quando um runbook ainda não foi publicado, ele tem um status de **Novo**. Quando é publicado, ele tem um status de **Publicado**. Se você editar o runbook depois que ele foi publicado, e as versões de Rascunho e Publicada forem diferentes, o runbook terá um status de **Em edição**.

![Status de runbook](media/automation-graphical-authoring-intro/runbook-statuses.png)

Você também tem a opção de reverter para a versão Publicada de um runbook. Isso descarta as alterações feitas desde que o runbook foi publicado pela última vez e substitui a versão de Rascunho do runbook pela versão Publicada.

![Botão Reverter para publicado](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## Atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook filho ou uma atividade de fluxo de trabalho. Você adiciona uma atividade ao runbook clicando com o botão direito do mouse no controle de Biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar que você desejar na tela. O local da atividade na tela não afeta a operação do runbook de forma alguma. Você pode definir o layout de seu runbook da maneira que considerar mais adequada para visualizar sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas.png)

Selecione a atividade na tela para configurar suas propriedades e parâmetros na folha Configuração. Você pode alterar o **Rótulo** da atividade para algo que a descreva a você. O cmdlet original ainda está sendo executado. Você está simplesmente alterando seu nome para exibição, que será usado no editor gráfico. O rótulo deve ser exclusivo no runbook.

### Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitarão valores para determinado cmdlet. Todos os cmdlets têm pelo menos um conjunto de parâmetros, e alguns têm vários. Se um cmdlet tem vários conjuntos de parâmetros, você deve selecionar qual deles usará para poder configurar os parâmetros. Os parâmetros que você pode configurar dependem do conjunto de parâmetros que você escolhe. Você pode alterar o conjunto de parâmetros usado por uma atividade selecionando **Conjunto de Parâmetros** e selecionando outro conjunto. Nesse caso, quaisquer valores de parâmetros que você tiver configurado serão perdidos.

No exemplo a seguir, o cmdlet Get-AzureVM tem dois conjuntos de parâmetros. Você não pode configurar valores de parâmetros até selecionar um dos conjuntos de parâmetros. O conjunto de parâmetros ListAllVMs é para retornar todas as máquinas virtuais e tem um único parâmetro opcional. O GetVMByServiceand VMName é para especificar a máquina virtual que você deseja retornar e tem um parâmetro obrigatório e dois opcionais.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/parameter-set.png)

#### Valores de parâmetros

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor será especificado. As fontes de dados que estão disponíveis para determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, Null não será uma opção disponível para um parâmetro que não permita valores nulos.

| Fonte de dados | Descrição |
|:---|:---|
|Valor Constante|Digite um valor válido para o parâmetro. Só está disponível para os seguintes tipos de dados: Int32,Int64,String,Boolean,DateTime,Switch. |
|Saída de Atividade|Saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas serão listadas. Selecione apenas a atividade para usar sua saída para o valor do parâmetro. Se a atividade produzir um objeto com várias propriedades, você poderá digitar o nome da propriedade depois de selecioná-la.|
|Parâmetro de entrada de runbook|Selecione um parâmetro de entrada de runbook como entrada para o parâmetro de atividade.|  
|Ativo de variável de automação|Selecione uma Variável de Automação como entrada.|  
|Ativo de credencial de automação|Selecione uma Credencial de Automação como entrada.|  
|Ativo de certificado de automação|Selecione um Certificado de Automação como entrada.|  
|Ativo de conexão de automação|Selecione uma Conexão de Automação como entrada.| 
|Expressão do PowerShell|Especifique a expressão simples do PowerShell. A expressão será avaliada antes da atividade, e o resultado será usado para o valor do parâmetro. Você pode usar variáveis para referir-se à saída de uma atividade ou um parâmetro de entrada de runbook.|
|Cadeia de Caracteres Vazia|Um valor de cadeia de caracteres vazia.|
|Nulo|Um valor Nulo.|
|Cancelar Seleção|Limpa qualquer valor que foi configurado anteriormente.|


#### Parâmetros adicionais opcionais

Todos os cmdlets terão a opção de fornecer parâmetros adicionais. Estes são os parâmetros comuns do PowerShell ou outros parâmetros personalizados. Será exibida uma caixa de texto em que você pode fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para usar o parâmetro comum **Verbose**, você especificaria **"-Verbose:$True"**.

### Controle de Script de Fluxo de Trabalho

Um controle de Script de Fluxo de Trabalho é uma atividade especial que aceita o código do fluxo de trabalho do PowerShell para fornecer funcionalidade caso contrário não estejam disponível. Esse não é um fluxo de trabalho completo, mas deve conter linhas válidas de código de fluxo de trabalho do PowerShell. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Qualquer saída de atividade é adicionada ao barramento de dados, a menos que não tenha um link de saída. Nesse caso, é adicionada à saída do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de runbook chamada $NumberOfDays. Em seguida, envia uma data e hora calculadas como saída a ser usada por atividades subsequentes no runbook.

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
	$DateTimeStart


## Links e fluxo de trabalho

Um **link** em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta, com a atividade de destino se iniciando após a atividade de origem ser concluída.

### Criar um link

Crie um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e solte-a.

![Criar um link](media/automation-graphical-authoring-intro/create-link.png)

Selecione o link para configurar suas propriedades na folha Configuração. Isso incluirá o tipo de link, que é descrito na tabela a seguir.

| Tipo de link | Descrição |
|:---|:---|
| Pipeline | A atividade de destino é executada uma vez para cada objeto de saída da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequência | A atividade de destino é executada apenas uma vez. Ela recebe uma matriz de objetos da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### Atividade de início

Um runbook gráfico será iniciado com todas as atividades que não têm um link de entrada. Isso geralmente será apenas uma atividade, que deve atuar como a atividade de início para o runbook. Se várias atividades não tiverem um link de entrada, o runbook começará executando-as em paralelo. Ele seguirá então os links para executar outras atividades, à medida que cada uma for concluída.

### Condições

Quando você especificar uma condição em um link, a atividade de destino será executada somente se a condição for resolvida como verdadeira. Normalmente, você usará uma variável $ActivityOutput em uma condição para recuperar a saída da atividade de origem.

Para um link de pipeline, você especifica uma condição para um único objeto, e a condição é avaliada para cada saída de objeto pela atividade de origem. A atividade de destino é então executada para cada objeto que atende à condição. Por exemplo, com uma atividade de origem de Get-AzureVM, a sintaxe a seguir pode ser usada para que um link de pipeline condicional recupere somente as máquinas virtuais que estão sendo executadas no momento.

	$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

Para um link de sequência, a condição é avaliada apenas uma vez, pois é retornada uma única matriz que contém a saída de todos os objetos da atividade de origem. Por isso, um link de sequência não pode ser usado para filtragem como um link de pipeline, mas simplesmente determinará se a próxima atividade é executada ou não. O código a seguir mostra o mesmo exemplo de avaliação da saída de Get-AzureVM para determinar as máquinas virtuais em execução. Nesse caso, o código percorrerá cada objeto na matriz e será resolvido como verdadeiro se pelo menos uma máquina virtual estiver em execução. A atividade de destino seria responsável por analisar esses dados.

	$test = $false
	$VMs = $ActivityOutput['Get-AzureVm']
	Foreach ($VM in VMs)
	{
		If ($VM.PowerState –eq 'Started')
			{
				$test = $true
			}
	}
	$test

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtradas pela condição. Se uma atividade for a origem para vários links, os dados disponíveis para atividades em cada ramificação dependerão da condição no link que se conecta a essa ramificação.

Por exemplo, a atividade de origem no runbook abaixo obtém todas as máquinas virtuais. Ela tem dois links condicionais e um link sem uma condição. O primeiro link condicional usa a expressão *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'* para filtrar somente as máquinas virtuais que estão sendo executadas no momento. O segundo usa a expressão *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Stopped'* para filtrar somente as máquinas virtuais que estão interrompidas no momento.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/conditional-links.png)

Qualquer atividade que seguir o primeiro link e usa a saída de atividade de Get-AzureVM só obterá as máquinas virtuais que foram iniciadas no momento da execução de Get-AzureVM. Qualquer atividade que seguir o segundo link só obterá as máquinas virtuais que foram interrompidas no momento da execução de Get-AzureVM. Qualquer atividade que seguir o terceiro link obterá todas as máquinas virtuais, independentemente do estado de execução.

### Junções

Uma junção é uma atividade especial que esperará até que todas as ramificações de entrada sejam concluídas. Isso permite executar várias atividades em paralelo e garantir que todas tenham sido concluídas antes de prosseguir.

Embora uma junção possa ter um número ilimitado de links de entrada, apenas um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você poderá criar a junção com vários links de pipeline de entrada e salvar o runbook, mas ele falhará ao ser executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais enquanto baixa simultaneamente patches a serem aplicados a esses computadores. Uma junção é usada para garantir que os processos sejam concluídos antes que o runbook continue.

![Junção](media/automation-graphical-authoring-intro/junction.png)

### Ciclos

Um ciclo é quando uma atividade de destino se vincula de volta à sua atividade de origem ou a outra atividade que, eventualmente, se vincula de volta à sua origem. No momento, os ciclos não são permitidos na criação de gráficos. Se seu runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Ciclo](media/automation-graphical-authoring-intro/cycle.png)

### Loops

Um loop é quando você repete uma atividade por um número especificado de vezes ou continua a repeti-la até que determinada condição seja atendida. No momento, os loops não têm suporte em runbooks gráficos.

### Compartilhando dados entre atividades

Todos os dados que são gerados por uma atividade com um link de saída são gravados no *barramento de dados* do runbook. Qualquer atividade no runbook pode usar dados no barramento de dados para popular os valores de parâmetros ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A maneira como os dados são gravados no barramento de dados depende do tipo de link da atividade. Para um **pipeline**, os dados são gerados como vários objetos. Para um link de **sequência**, os dados são gerados como uma matriz. Se houver apenas um valor, ele será gerado como uma matriz de elemento único.

Você pode acessar dados no barramento de dados usando um destes dois métodos. Primeiro, é possível usar uma fonte de dados de **Saída de Atividade** para popular um parâmetro de outra atividade. Se a saída for um objeto, você poderá especificar uma única propriedade.

![Saída de atividade](media/automation-graphical-authoring-intro/activity-output-datasource.png)

Você também pode recuperar a saída de uma atividade em uma fonte de dados de **Expressão do PowerShell** ou de uma atividade de **Script de Fluxo de Trabalho** com uma variável ActivityOutput. Se a saída for um objeto, você poderá especificar uma única propriedade. As variáveis ActivityOutput usam a sintaxe a seguir.

	$ActivityOutput['Activity Label']
	$ActivityOutput['Activity Label'].PropertyName 

### Pontos de verificação

A mesma diretriz para definir [pontos de verificação](automation-powershell-workflow/#checkpoints) em seu runbook se aplica a runbooks gráficos. Você pode adicionar uma atividade ao cmdlet Checkpoint-Workflow onde você precisa definir um ponto de verificação. Você deve então incluir um Add-AzureAccount após essa atividade, caso o runbook se inicie desse ponto de verificação em um trabalhador diferente.

## Autenticação para recursos do Azure

A maioria dos runbooks na Automação do Azure requer autenticação para recursos do Azure. O método típico usado para essa autenticação é o cmdlet Add-AzureAccount com um [ativo de credencial](http://msdn.microsoft.com/library/dn940015.aspx) que representa um usuário do Active Directory com acesso à conta do Azure. Isso é discutido em [Configurando a Automação do Azure](automation-configuring.md).

Você pode adicionar essa funcionalidade a um runbook gráfico adicionando um ativo de credencial à tela, seguido por uma atividade Add-AzureAccount. Add-AzureAccount usa a atividade de credencial para sua entrada. Isso é ilustrado no exemplo a seguir.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

Você deve se autenticar no início do runbook e após cada ponto de verificação. Isso significa adicionar uma atividade de adição Add-AzureAccount depois de qualquer atividade Checkpoint-Workflow. Você não precisa de uma atividade de credencial de adição, já que pode usar a mesma

![Saída de atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Entrada e saída de runbook

### Entrada de runbook

Um runbook pode exigir entrada de um usuário quando o usuário iniciar o runbook por meio do portal de visualização do Azure ou de outro runbook, se o atual é usado como filho. Por exemplo, se tiver um runbook que cria uma máquina virtual, talvez você precise fornecer informações como o nome da máquina virtual e outras propriedades sempre que iniciar o runbook.

Você aceita a entrada para um runbook definindo um ou mais parâmetros de entrada. Você fornece valores para esses parâmetros sempre que o runbook é iniciado. Quando você iniciar um runbook com o portal de visualização do Azure, ele solicitará que você forneça valores para cada um dos parâmetros de entrada do runbook.

Você pode acessar parâmetros de entrada para um runbook clicando no botão **Entrada e saída** na barra de ferramentas do runbook.

![Entrada e saída de runbook](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

Isso abre o controle **Entrada e saída**, em que você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir.

|Propriedade|Descrição|
|:---|:---|
| Nome | O nome exclusivo do parâmetro. Pode conter apenas caracteres alfanuméricos e não pode conter espaços. |
| Descrição | Uma descrição opcional para o parâmetro de entrada. |
| Tipo | Tipo de dados esperado para o valor do parâmetro. O portal de visualização do Azure fornecerá um controle apropriado para o tipo de dados de cada parâmetro quando a entrada for solicitada. |
| Obrigatório | Especifica se deve ser fornecido um valor para o parâmetro. O runbook não poderá ser iniciado se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Padrão | Especifica o valor que será usado para o parâmetro, se nenhum for fornecido. Pode ser Nulo ou um valor específico. |


### Saída de runbook

Os dados criados por qualquer atividade que não tenha um link de saída serão adicionados à [saída do runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). A saída é salva com o trabalho do runbook e está disponível para um runbook pai quando o runbook é usado como filho.


## Artigos relacionados

- [Aprendendo sobre o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
- [Ativos de automação](http://msdn.microsoft.com/library/azure/dn939988.aspx)
 

<!---HONumber=July15_HO3-->
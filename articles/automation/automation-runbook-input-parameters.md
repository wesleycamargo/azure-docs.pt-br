<properties 
   pageTitle="Parâmetros de entrada do Runbook"
   description="Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Este artigo descreve os diferentes cenários em que os parâmetros de entrada são usados em runbooks."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/16/2015"
   ms.author="sngun"/>

# Parâmetros de entrada do Runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Os parâmetros permitem que as ações de runbook sejam direcionadas para ambientes e cenários específicos. Neste artigo, iremos conduzi-lo pelos diferentes cenários em que os parâmetros de entrada são usados em runbooks.

## Configurando parâmetros de entrada

Parâmetros de entrada podem ser configurados no PowerShell, Fluxo de trabalho do PowerShell e runbooks Gráficos. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatório ou opcionais, e você pode atribuir um valor padrão para parâmetros opcionais. Os parâmetros de entrada configurados para um runbook podem ter valores atribuídos quando você inicia um runbook por meio de qualquer um dos métodos disponíveis, incluindo iniciar um runbook por meio de interface do usuário, webhooks, serviço Web ou como um runbook filho chamado em linha no outro runbook.

## Configurando parâmetros de entrada no PowerShell e em runbooks de Fluxo de Trabalho do PowerShell

PowerShell e [runbooks de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) na Automação do Azure oferecem suporte a parâmetros de entrada definidos usando os seguintes atributos:

| **Propriedade** | **Descrição** |
|:--- |:---|
| Tipo | Obrigatório. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .Net é válido. |
| Nome | Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado, devendo começar com uma letra. |
| Obrigatório | Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você definir isso como **$true**, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir isso como **$false**, um valor será opcional. |
| Valor padrão | Opcional. Especifica um valor que será usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para qualquer parâmetro e tornará automaticamente o parâmetro opcional, independentemente da configuração obrigatória. |

O Windows PowerShell oferece suporte a mais atributos de parâmetros de entrada que aqueles listados aqui, como validação, aliases e conjuntos de parâmetros; no entanto, a Automação do Azure atualmente tem suporte apenas para a lista acima.

Uma definição de parâmetro nos runbooks de fluxo de trabalho do PowerShell tem o seguinte formato geral, em que vários parâmetros são separados por vírgula.

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     ) 
```

>[AZURE.NOTE]Ao definir parâmetros, se você não especificar o atributo **Obrigatório**, por padrão, o parâmetro será considerado opcional. Ainda, se você definir um valor padrão para um parâmetro em runbooks de fluxo de trabalho do PowerShell, ele será tratado pelo PowerShell como um parâmetro opcional, independentemente do valor de atributo **Obrigatório**.

Por exemplo, vamos configurar os parâmetros de entrada para um runbook de Fluxo de Trabalho do PowerShell que gere os detalhes sobre máquinas virtuais – seja uma única VM ou todas as VMs dentro de um serviço. Esse runbook tem dois parâmetros: o nome da máquina virtual e o nome do serviço, como mostrado na captura de tela abaixo.

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

Nesta definição de parâmetro, os parâmetros **$VMName** e **$ServiceName** são parâmetros simples do tipo cadeia de caracteres; no entanto, runbooks do PowerShell e Fluxo de Trabalho do PowerShell têm suporte a todos os tipos simples e tipos complexos, como **objeto** ou **PSCredential**, para parâmetros de entrada.

Se seu runbook tiver um parâmetro de entrada de tipo [objeto], para passar um valor, use uma tabela de hash do PowerShell com pares (nome, valor). Por exemplo, se você tiver o seguinte parâmetro em um runbook,

     [Parameter (Mandatory = $true)]
     [object] $FullName

Poderá passar o seguinte valor para o parâmetro:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## Configurando parâmetros de entrada em runbooks Gráficos

Para configurar um runbook gráfico com parâmetros de entrada, vamos criar um [runbook Gráfico](automation-first-runbook-graphical.md) que gere os detalhes sobre as máquinas virtuais – seja uma única VM ou todas as VMs dentro de um serviço. O runbook consiste em duas atividades principais:

* [**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) para autenticar com o Azure.
* [**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) para obter todas as máquinas virtuais.

Você pode usar a atividade [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) para gerar os nomes das máquinas virtuais. A atividade **Get-AzureVM** aceitará dois parâmetros: o **nome da máquina virtual** e o **nome da conta de serviço**. Uma vez que esses parâmetros exigiriam valores diferentes sempre que você iniciasse o runbook, você pode adicionar parâmetros de entrada ao runbook. Aqui estão as etapas para adicionar parâmetros de entrada:

1. Selecione o runbook gráfico na folha **Runbooks** e então [Editar](automation-graphical-authoring-intro.md). 

2. Na folha **Editar**, clique em **Entrada e saída** para abrir a folha **Entrada e Saída**.

    ![Runbook Gráfico de automação](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)

 
3. A folha **Entrada e Saída** exibe uma lista de parâmetros de entrada definidos para o runbook. A partir dessa folha, você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro ao runbook, clique **Adicionar entrada** para abrir a folha "Parâmetro de Entrada do Runbook", na qual você pode configurar os seguintes parâmetros:

    | **Propriedade** | **Descrição** |
    |:--- |:---|
    | Nome | Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado, devendo começar com uma letra. |
    | Descrição | Opcional. Descrição da finalidade do parâmetro de entrada. |
    | Tipo | Opcional. O tipo de dados esperado para o valor do parâmetro. Tipos de parâmetro com suporte são String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se um tipo de dados não estiver selecionado, será usado o padrão String. |
    | Obrigatório | Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você escolher **sim**, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher **não**, um valor não será necessário quando o runbook for iniciado, e um valor padrão poderá ser definido. |
    | Valor Padrão | Opcional. Especifica um valor que será usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para um parâmetro que não é obrigatório. Você pode escolher **Personalizado** para definir um valor padrão. Esse valor será usado a menos que outro valor seja fornecido quando o runbook for iniciado. Escolha **Nenhum** se não quiser fornecer nenhum valor padrão. |  

    ![AddNewInput](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. Crie dois parâmetros com as seguintes propriedades que serão usadas pela atividade **Get-AzureVM**:

    * **Parâmetro1:** Nome – VMName, Tipo – String, Obrigatório – No
	
    * **Parâmetro2:** Nome – ServiceName, Tipo – String, Obrigatório – No, Valor padrão – Custom, Valor padrão personalizado – <nome do serviço padrão que contém as máquinas virtuais>

5. Depois de adicionar os parâmetros, clique em **OK**. Agora você pode exibi-los na folha **Entrada e Saída**. Clique em **OK** novamente e, em seguida, escolha **Salvar** e **Publicar** seu runbook.

## Atribuindo valores a parâmetros de entrada em runbooks

Você pode passar valores para parâmetros de entrada em runbooks nos seguintes cenários:

### Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de diversas maneiras: por meio da interface do usuário do portal do Azure, com um webhook, com os cmdlets do PowerShell, com a API REST e com o SDK. A seguir, discutiremos diferentes métodos para iniciar um runbook e atribuir parâmetros.

* **Iniciar um runbook publicado por meio do portal do Azure e atribuir parâmetros**

Quando você [iniciar o runbook](automation-starting-a-runbook#starting-a-runbook-with-the-azure-portal.md), a folha **Iniciar Runbook** será aberta, permitindo configurar valores para os parâmetros que você acabou de criar.

![Começar a usar o Portal](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

No rótulo sob a caixa de texto de entrada, você pode ver os atributos definidos para o parâmetro: obrigatório ou opcional, o tipo e qualquer valor padrão. No balão de ajuda ao lado do nome de parâmetro, você pode ver todas as informações principais necessárias para tomar decisões sobre os valores de entrada de parâmetro. Isso inclui se um parâmetro é obrigatório ou opcional, o tipo, qualquer valor padrão e outras observações úteis.

![Balão de ajuda](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE]Parâmetros do tipo String têm suporte para valores de cadeia de caracteres **vazia**. Inserir **[EmptyString]** na caixa de texto do parâmetro de entrada passará uma cadeia de caracteres vazia para o parâmetro. Ainda, parâmetros do tipo de cadeia de caracteres não oferecem suporte à passagem de valores **Nulos**. Se você não passar qualquer valor para o parâmetro de cadeia de caracteres, o PowerShell o interpretará como nulo.

* **Iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros**

    * **Cmdlets de gerenciamento de serviços do azure:** você pode iniciar um runbook de automação criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx)

    **Exemplo:**

      ``` $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```


    * **Cmdlets do Gerenciador de Recursos do Azure:** você pode iniciar um runbook de automação criado em um grupo de recursos usando [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)

    **Exemplo:**

      ``` $params = @{“VMName”=”WSVMClassic”;”ServiceName”=”WSVMClassicSG”}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE]Ao iniciar um runbook usando cmdlets do PowerShell, juntamente com os parâmetros de entradas passados, um parâmetro padrão, **MicrosoftApplicationManagementStartedBy** é criado com o valor **PowerShell**. Você pode exibir esse parâmetro na folha de detalhes do trabalho.

* **Iniciar um runbook usando o SDK e atribuir parâmetros**

    * **Método de gerenciamento de serviço do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um trecho de código C# para iniciar um runbook na sua conta de automação; você pode ver o código completo em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```      
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
            var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
            {
                Properties = new JobCreateProperties 
                {
                    Runbook = new RunbookAssociationProperty
                    {
                        Name = runbookName
                    },
                        Parameters = parameters
                }
            });
            return response.Job;
        }
    ```
      
    * **Método do Gerenciador de recursos do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um trecho de código C# para iniciar um runbook na sua conta de automação; você pode ver o código completo em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

    ```
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
           var response = AutomationClient.Jobs.Create(resourceGroup, automationAccount, new JobCreateParameters
           {
               Properties = new JobCreateProperties 
               {
                   Runbook = new RunbookAssociationProperty
                   {
                       Name = runbookName
                   },
                       Parameters = parameters
               }
           });
        return response.Job;
        }
    ```

Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook, VMName e ServiceName e seus valores e iniciar o runbook. Abaixo está o trecho de código C# para chamar o método definido acima.

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

* **Iniciar um runbook usando a API REST e atribuir parâmetros**

Um trabalho de runbook pode ser criado e iniciado com a API REST de automação do Azure usando o método **PUT** com o seguinte URI de solicitação.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

No URI de solicitação, substitua os seguintes parâmetros:
 
* **subscription-id:** a ID de sua assinatura do Azure.  
* **nome de serviço de nuvem:** nome do serviço de nuvem para o qual a solicitação deve ser enviada.  
* **nome da conta de automação:** nome da sua conta de automação hospedada dentro do serviço de nuvem especificado.  
* **id do trabalho:** o GUID para o trabalho. O GUID do PowerShell pode ser criado usando o cmdlet **[GUID]::NewGuid().ToString ()**.
	
Para passar parâmetros para o trabalho do runbook, use o corpo da solicitação, e ele obtém duas propriedades fornecidas no formato JSON:

* **Nome do Runbook** – obrigatório. Nome do runbook para o trabalho ser iniciado.  
* **Parâmetros do Runbook** – opcional. Um dicionário da lista de parâmetros no formato (nome, valor) em que o nome deve ser do tipo cadeia de caracteres e o valor pode ser qualquer valor JSON válido. 

Se você quiser iniciar o runbook **Get-AzureVMTextual** criado anteriormente com VMName e ServiceName como parâmetros, use o seguinte formato JSON para o corpo da solicitação.

```
        {
           "properties":{
           "runbook":{
               "name":"Get-AzureVMTextual"
           },
           "parameters":{
               "VMName":"WSVMClassic",
               "ServiceName":”WSCS1”
           }
          }
       }
```

Um código de status HTTP 201 é retornado se o trabalho for criado com sucesso. Para obter mais informações sobre cabeçalhos de resposta e o corpo de resposta, consulte [criar um trabalho de runbook usando a API REST](https://msdn.microsoft.com/library/azure/mt163849.aspx).

### Testar um runbook e atribuir parâmetros

Quando você [testa](automation-testing-runbook.md) a versão de rascunho do runbook usando a opção de teste, a folha **Teste** é aberta, na qual você pode configurar valores para os parâmetros que acabou de criar.

![Teste e atribuir parâmetros](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-scheduling-a-runbook.md) ao seu runbook para iniciar em um momento específico. Parâmetros de entrada são atribuídos ao criar o cronograma e o runbook usará esses valores quando for iniciado pelo agendamento. Não é possível salvar a agenda até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Não é possível salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Criar Webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Quando você executar um runbook usando o webhook, um parâmetro de entrada predefinido **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** é enviado junto com os parâmetros de entrada que você definiu. Você pode clicar para expandir o parâmetro WebhookData para obter mais detalhes.

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## Próximas etapas

- Para obter mais informações sobre a entrada e a saída do runbook, consulte [Automação do Azure: entrada do runbook, saída e runbooks aninhados](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)
- Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Iniciando um runbook](automation-starting-a-runbook.md)
- Para editar um runbook textual, consulte [Editando runbooks textuais](automation-edit-textual-runbook.md)
- Para editar um runbook gráfico, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)

<!---HONumber=AcomDC_1223_2015-->
<properties
   pageTitle="Parâmetros de entrada de runbook| Microsoft Azure"
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

Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Os parâmetros permitem que as ações de runbook sejam direcionadas para ambientes e cenários específicos. Neste artigo, vamos orientá-lo quanto aos diferentes cenários em que os parâmetros de entrada são usados em runbooks.

## Como configurar parâmetros de entrada

Parâmetros de entrada podem ser configurados no PowerShell, no Fluxo de Trabalho do PowerShell e em runbooks gráficos. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatório ou opcionais, e você pode atribuir um valor padrão para parâmetros opcionais. Você pode atribuir valores aos parâmetros de entrada para um runbook ao iniciá-lo por meio de um dos métodos disponíveis. Esses métodos incluem a inicialização de um runbook usando a interface do usuário ou um serviço Web. Você também pode iniciá-lo como um runbook filho, que é chamado de embutido em outro runbook.

## Como configurar parâmetros de entrada no PowerShell e em runbooks do Fluxo de Trabalho do PowerShell

O PowerShell e [runbooks de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md) na Automação do Azure dão suporte a parâmetros de entrada definidos usando os atributos a seguir.

| **Propriedade** | **Descrição** |
|:--- |:---|
| Tipo | Obrigatório. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome | Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado. Deve começar com uma letra. |
| Obrigatório | Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você definir isso como **$true**, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir isso como **$false**, um valor será opcional. |
| Valor padrão | Opcional. Especifica um valor que será usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para qualquer parâmetro e tornará automaticamente o parâmetro opcional, independentemente da configuração obrigatória. |

 O Windows PowerShell dá suporte a mais atributos de parâmetros de entrada do que aqueles listados aqui, como validação, aliases e conjuntos de parâmetros. No entanto, a Automação do Azure atualmente dá suporte apenas aos parâmetros de entrada listados acima.

Uma definição de parâmetro nos runbooks de Fluxo de Trabalho do PowerShell tem o formato geral a seguir, em que vários parâmetros são separados por vírgulas.

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
```

>[AZURE.NOTE]Ao definir parâmetros, se você não especificar o atributo **Mandatory**, por padrão, o parâmetro será considerado opcional. Além disso, se você definir um valor padrão para um parâmetro em runbooks de Fluxo de Trabalho do PowerShell, ele será tratado pelo PowerShell como um parâmetro opcional, independentemente do valor do atributo **Mandatory**.

Por exemplo, vamos configurar os parâmetros de entrada para um runbook do Fluxo de Trabalho do PowerShell que gera detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs em um serviço. Esse runbook tem dois parâmetros, conforme mostrado na captura de tela a seguir: o nome da máquina virtual e o nome do serviço.

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

Nessa definição de parâmetro, os parâmetros **$VMName** e **$ServiceName** são parâmetros simples do tipo cadeia de caracteres. No entanto, o PowerShell e os runbooks do Fluxo de Trabalho do PowerShell dão suporte a todos os tipos simples e complexos, como **objeto** ou **PSCredential** para parâmetros de entrada.

Se o runbook tiver um parâmetro de entrada de tipo [objeto], use uma tabela de hash do PowerShell com pares (nome, valor) para passar um valor. Por exemplo, se você tiver o seguinte parâmetro em um runbook:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Poderá passar o seguinte valor para o parâmetro:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## Como configurar parâmetros de entrada em runbooks gráficos

Para [configurar um runbook gráfico](automation-first-runbook-graphical.md) com parâmetros de entrada, vamos criar um runbook gráfico que gera detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs em um serviço. A configuração de um runbook consiste em duas atividades principais, conforme descrito abaixo.

[**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) para autenticar com o Azure.

[**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) para obter todas as máquinas virtuais.

Você pode usar a atividade [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) para gerar os nomes das máquinas virtuais. A atividade **Get-AzureVM** aceitará dois parâmetros: o **nome da máquina virtual** e o **nome da conta de serviço**. Uma vez que esses parâmetros exigiriam valores diferentes sempre que você iniciasse o runbook, você pode adicionar parâmetros de entrada ao runbook. Aqui estão as etapas para adicionar parâmetros de entrada:

1. Selecione o runbook gráfico na folha **Runbooks** e então [Editar](automation-graphical-authoring-intro.md).

2. Na folha **Editar**, clique em **Entrada e saída** para abrir a folha **Entrada e saída**.

    ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)


3. A folha **Entrada e saída** exibe uma lista de parâmetros de entrada definidos para o runbook. Nessa folha, você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro ao runbook, clique **Adicionar entrada** para abrir a folha **Parâmetro de Entrada do Runbook**. Nela, você pode configurar os seguintes parâmetros:

    | **Propriedade** | **Descrição** |
    |:--- |:---|
    | Nome | Obrigatório. O nome do parâmetro. Deve ser exclusivo no runbook e pode conter apenas letras, números ou caracteres de sublinhado. Deve começar com uma letra. |
    | Descrição | Opcional. Descrição da finalidade do parâmetro de entrada. |
    | Tipo | Opcional. O tipo de dados esperado para o valor do parâmetro. Os tipos de parâmetro com suporte são **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** e **Object**. Se um tipo de dados não estiver selecionado, será usado **String** por padrão. |
    | Obrigatório | Opcional. Especifica se deve ser fornecido um valor para o parâmetro. Se você escolher **sim**, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher **não**, um valor não será necessário quando o runbook for iniciado, e um valor padrão poderá ser definido. |
    | Valor Padrão | Opcional. Especifica um valor que será usado para o parâmetro se um valor não for transmitido ao iniciar o runbook. Um valor padrão pode ser definido para um parâmetro que não é obrigatório. Para definir um valor padrão, escolha **personalizado**. Esse valor será usado a menos que outro valor seja fornecido quando o runbook for iniciado. Escolha **Nenhum** se não quiser fornecer nenhum valor padrão. |  

    ![Como adicionar nova entrada](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. Crie dois parâmetros com as seguintes propriedades que serão usadas pela atividade **Get-AzureVM**:

    * **Parâmetro1:** Nome – VMName, Tipo – String, Obrigatório – No

    * **Parâmetro2:** Nome – VMNameServiceName, Tipo – String, Obrigatório – No, Valor padrão – Custom, Valor padrão personalizado – <Nome do serviço padrão que contém as máquinas virtuais>

5. Depois de adicionar os parâmetros, clique em **OK**. Agora você pode exibi-los na **folha Entrada e saída**. Clique em **OK** novamente e clique em **Salvar** e **Publicar** o runbook.

## Como atribuir valores a parâmetros de entrada em runbooks

Você pode passar valores para parâmetros de entrada em runbooks nos cenários a seguir

### Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de diversas maneiras: por meio da interface do usuário do portal do Azure, com um webhook, com os cmdlets do PowerShell, com a API REST ou com um SDK. A seguir, discutiremos diferentes métodos para iniciar um runbook e atribuir parâmetros.

- **Como iniciar um runbook publicado usando o portal do Azure e atribuir parâmetros**

Quando você [inicia o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), a folha **Iniciar Runbook** é aberta e você pode configurar valores para os parâmetros que acabou de criar.

![Como começar a usar o portal](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

No rótulo abaixo da caixa de entrada, você pode ver os atributos que foram definidos para o parâmetro. Os atributos incluem obrigatório ou opcional, tipo e valor padrão. No balão de ajuda ao lado do nome de parâmetro, você pode ver todas as informações principais necessárias para tomar decisões sobre os valores de entrada de parâmetro. Essas informações incluem se um parâmetro é obrigatório ou opcional. Também incluem o tipo e o valor padrão (se houver) e outras observações úteis.

![Balão de ajuda](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE]Parâmetros do tipo String dão suporte a valores de Cadeia de Caracteres **Vazia**. Se você inserir **[EmptyString]** na caixa do parâmetro de entrada, uma cadeia de caracteres vazia será passada para o parâmetro. Além disso, parâmetros do tipo de cadeia de caracteres não dão suporte à passagem de valores **Nulos**. Se você não passar um valor para o parâmetro de Cadeia de Caracteres, o PowerShell o interpretará como nulo.

- **Como iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros**

    - **Cmdlets de gerenciamento de serviços do azure:** você pode iniciar um runbook de automação criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx)

    **Exemplo:**

      ``` $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```

    - **Cmdlets do Gerenciador de Recursos do Azure:** você pode iniciar um runbook de automação criado em um grupo de recursos usando [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).


    **Exemplo:**

      ``` $params = @{“VMName”=”WSVMClassic”;”ServiceName”=”WSVMClassicSG”}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE]Quando você inicia um runbook usando cmdlets do PowerShell, um parâmetro padrão, **MicrosoftApplicationManagementStartedBy**, é criado com o valor **PowerShell**. Você pode exibir esse parâmetro na folha **Detalhes do trabalho**.

- **Como uniciar um runbook usando o SDK e atribuir parâmetros**

    - **Método de Gerenciamento de Serviço do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um trecho de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

    - **Método do Gerenciador de recursos do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um trecho de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook, **VMName** e **ServiceName**, e seus valores. Em seguida, inicie o runbook. Abaixo está o trecho de código em C# para chamar o método definido acima.

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

- **Como iniciar um runbook usando a API REST e atribuir parâmetros**

Um trabalho de runbook pode ser criado e iniciado com a API REST de Automação do Azure usando o método **PUT** com o URI de solicitação a seguir.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

No URI de solicitação, substitua os seguintes parâmetros:

* **subscription-id:** a ID de sua assinatura do Azure.  
* **cloud-service-name:** o nome do serviço de nuvem ao qual a solicitação deve ser enviada.  
* **automation-account-name:** o nome de sua conta de automação hospedada no serviço de nuvem especificado.  
* **id do trabalho:** o GUID para o trabalho. O GUID do PowerShell pode ser criado usando o cmdlet **[GUID]::NewGuid().ToString ()**.

Para passar parâmetros para o trabalho de runbook, use o corpo da solicitação. Ele usa as duas seguintes propriedades fornecidas no formato JSON:

* **Nome do Runbook** – obrigatório. O nome do runbook para que o trabalho seja iniciado.  
* **Parâmetros de Runbook** – opcional. Um dicionário da lista de parâmetros no formato (nome, valor) em que o nome deve ser do tipo cadeia de caracteres e o valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o runbook **Get-AzureVMTextual** criado anteriormente com **VMName** e **ServiceName** como parâmetros, use o formato JSON a seguir para o corpo da solicitação.

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

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, consulte o artigo sobre como [criar um trabalho de runbook usando a API REST.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### Testar um runbook e atribuir parâmetros

Quando você [testa a versão de rascunho do runbook](automation-testing-runbook.md) usando a opção de teste, é aberta a folha **Teste**, e você pode configurar valores para os parâmetros que acabou de criar.

![Como testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-scheduling-a-runbook.md) ao runbook para que ele seja iniciado em um momento específico. Você atribui parâmetros de entrada ao criar a agenda. O runbook usará esses valores quando for iniciado pela agenda. Não é possível salvar a agenda até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Não é possível salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Quando você executa um runbook usando um webhook, o parâmetro de entrada predefinido **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** é enviado junto com os parâmetros de entrada que você definiu. Você pode clicar para expandir o parâmetro **WebhookData** para obter mais detalhes.

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## Próximas etapas

- Para obter mais informações sobre a entrada e a saída do runbook, consulte [Automação do Azure: entrada do runbook, saída e runbooks aninhados](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
- Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Como iniciar um runbook](automation-starting-a-runbook.md).
- Para editar um runbook textual, consulte [Como editar runbooks textuais](automation-edit-textual-runbook.md).
- Para editar um runbook gráfico, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

<!---HONumber=AcomDC_0121_2016-->
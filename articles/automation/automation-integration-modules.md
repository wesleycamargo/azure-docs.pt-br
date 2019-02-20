---
title: Criar um módulo de integração de Automação do Azure
description: Tutorial que explica a criação, os testes e o uso de exemplo dos módulos de integração na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990335"
---
# <a name="azure-automation-integration-modules"></a>Módulos de integração de automação do Azure

O PowerShell é a tecnologia fundamental por trás da Automação do Azure. Como a Automação do Azure foi desenvolvida sobre o PowerShell, os módulos do PowerShell são fundamentais para a extensibilidade da Automação do Azure. Neste artigo, você aprenderá sobre as especificidades de uso dos módulos PowerShell da Automação do Azure, conhecidos como Módulos de Integração. Você também conhecerá as práticas recomendadas para a criação de seus próprios módulos PowerShell, de modo a garantir que eles funcionem como Módulos de Integração na Automação do Azure. 

## <a name="what-is-a-powershell-module"></a>O que é um módulo do PowerShell?

Um módulo PowerShell é um grupo de cmdlets do PowerShell, como **Get-Date** ou **Copy-Item**, que pode ser usado com:

* O console do PowerShell
* scripts
* workflows
* runbooks
* recursos DSC

Toda a funcionalidade do PowerShell é exposta por meio de cmdlets e recursos DSC. Cada cmdlet e recurso DSC tem o suporte de um módulo PowerShell, muitos deles acompanham o próprio PowerShell. Por exemplo, o cmdlet **Get-Date** faz parte do módulo PowerShell `Microsoft.PowerShell.Utility`, o cmdlet **Copy-Item** faz parte do módulo PowerShell `Microsoft.PowerShell.Management` e o recurso Package DSC faz parte do módulo PowerShell PSDesiredStateConfiguration. Esses dois módulos são fornecidos com o PowerShell. Muitos módulos PowerShell não são fornecidos como parte do PowerShell. Esses módulos são distribuídos com produtos primários ou de terceiros, ou em locais como a Galeria do PowerShell. Os módulos são úteis porque simplificam tarefas complexas por meio de funcionalidade encapsulada.  Saiba mais sobre [Módulos do PowerShell no MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>O que é um Módulo de Integração da Automação do Azure?

Um Módulo de Integração não é diferente de um módulo do PowerShell. É simplesmente um módulo do PowerShell que contém, opcionalmente, um arquivo adicional: um arquivo de metadados que especifica um tipo de conexão de Automação do Azure a ser usado com os cmdlets do módulo em runbooks. Os módulos PowerShell podem ser importados na Automação do Azure de modo a disponibilizar os cmdlets para uso em runbooks e recursos DSC para uso em configurações de DSC. Em segundo plano, a Automação do Azure armazena esses módulos e, no momento da execução do trabalho do runbook e do trabalho de compilação do DSC, os carrega nas áreas restritas da Automação do Azure, onde runbooks são executados e configurações de DSC compiladas. Todos os recursos DSC em módulos também são colocados automaticamente no servidor de pull de DSC de Automação. Eles podem ser obtidos por máquinas quando elas aplicam configurações de DSC.  

Fornecemos diversos módulos do Azure PowerShell prontos para uso na Automação do Azure. Mas você pode importar módulos PowerShell em qualquer sistema, ferramenta ou serviço ao qual queira integrá-los.

> [!NOTE]
> Determinados módulos são fornecidos como **módulos globais** no serviço de Automação. Esses módulos globais estão disponíveis quando você cria uma conta de automação, e os atualizamos às vezes, o que os envia automaticamente à sua conta de automação. Se não quiser que eles sejam atualizados automaticamente, você sempre poderá importar o mesmo módulo por conta própria, e ele terá precedência sobre a versão de módulo global desse módulo que fornecemos no serviço.

O formato no qual você importa um pacote do Módulo de Integração é um arquivo compactado com o mesmo nome que o módulo e com uma extensão .zip. Ele contém o módulo do Windows PowerShell e quaisquer arquivos de suporte, incluindo um arquivo de manifesto (.psd1), se o módulo tiver um.

Se o módulo precisar conter um tipo de conexão da Automação do Azure, também deverá conter um arquivo com o nome `<ModuleName>-Automation.json` que especifica as propriedades do tipo de conexão. Esse é um arquivo json, que é colocado dentro da pasta do módulo do seu arquivo .zip compactado. Esse arquivo contém os campos de uma **conexão** que é necessária para estabelecer conexão com o sistema ou serviço que o módulo representa. Essa configuração acaba criando um tipo de conexão na Automação do Azure. Com esse arquivo você pode definir os nomes de campo, os tipos e se os campos devem ser criptografados e/ou opcionais, para o tipo de conexão do módulo. O exemplo seguinte é um modelo no formato de arquivo json:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Se você tiver implantado o Service Management Automation e criado pacotes do Módulo de Integração para seus runbooks de automação, esse modelo não será novidade para você.

## <a name="authoring-best-practices"></a>Práticas recomendadas de criação

Embora os Módulos de Integração sejam módulos PowerShell, existem vários itens que devem ser considerados na criação de um módulo PowerShell para uso na Automação do Azure. Algumas recomendações são úteis para que seus módulos funcionem bem no Fluxo de Trabalho do PowerShell.

1. Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, você pode usar o cmdlet **Get-Help** para definir certas informações de ajuda com relação aos cmdlets para que o usuário receba ajuda sobre como usá-los. Por exemplo, veja como você pode definir uma sinopse e um URI de ajuda para um módulo do PowerShell escrito em um arquivo .psm1. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Fornecer essas informações mostra essa ajuda usando o cmdlet **Get-Help** no console do PowerShell. Essas informações também são expostas na Automação do Azure.  Por exemplo, ao inserir atividades durante a criação de runbooks. Clicar em "Exibir ajuda detalhada" abrirá o URI de ajuda em outra guia do navegador da Web que você está usando para acessar a Automação do Azure.

   ![Ajuda do Módulo de Integração](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Se o módulo for executado em um sistema remoto:

   1. Ele deverá conter um arquivo de metadados do Módulo de Integração que define as informações necessárias para se conectar a esse sistema remoto, ou seja, tipo de conexão.
   2. Cada cmdlet no módulo deve ser capaz de receber um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro.  

    Fica mais fácil usar os cmdlets no módulo na Automação do Azure se você permitir a passagem de um objeto com os campos do tipo de conexão como um parâmetro para o cmdlet. Isso permite que os usuários mapeiem parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet.

    Com base no exemplo de runbook acima, ele usa um ativo de conexão Twilio chamado CorpTwilio para acessar o Twilio e retornar todos os números de telefone na conta.  Você observou como ele está mapeando os campos da conexão para os parâmetros do cmdlet?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Uma maneira mais fácil e adequada de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet -

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Você pode habilitar um comportamento como o do exemplo anterior para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto de parâmetros **SpecifyConnectionFields** é usado para passar as propriedades do campo de conexão individualmente. **UseConnectionObject** permite que você passe a conexão diretamente. Como você pode ver, o cmdlet Send-TwilioSMS no [módulo Twilio do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) permite a passagem de qualquer uma das formas:

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Defina o tipo de saída para todos os cmdlets no módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. Isso é especialmente útil durante a criação gráfica do runbook da Automação, quando o conhecimento do tempo de design é fundamental para uma experiência de usuário facilitada com o seu módulo.

   ![Tipo de saída do runbook gráfico](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Esse comportamento é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.

   ![IntelliSense POSH](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Os cmdlets no módulo não devem usar tipos de objeto complexos para parâmetros. O Fluxo de Trabalho do PowerShell é diferente do PowerShell, pois armazena tipos complexos no formato desserializado. Os tipos primitivos permanecerão primitivos, mas tipos complexos serão convertidos em suas versões desserializadas, que são essencialmente pacotes de propriedade. Por exemplo, se você tiver usado o cmdlet **Get-Process** em um runbook (ou um Fluxo de Trabalho do PowerShell para esse fim), ele retornará um objeto do tipo [Deserialized.System.Diagnostic.Process] não o tipo [System.Diagnostic.Process] esperado. Esse tipo tem as mesmas propriedades que o tipo não desserializado, mas nenhum dos métodos. E se você tentar passar esse valor como um parâmetro para um cmdlet, em que o cmdlet espera um valor [System.Diagnostic.Process] para esse parâmetro, o seguinte erro será exibido: *Não é possível processar a transformação do argumento no parâmetro 'process'. Erro: "Não é possível converter o valor "System.Diagnostics.Process (CcmExec)" do tipo "Deserialized.System.Diagnostics.Process" para o tipo "System.Diagnostics.Process".*   Isso ocorre porque há uma incompatibilidade de tipo entre o tipo [System.Diagnostic.Process] esperado e o tipo [Deserialized.System.Diagnostic.Process] fornecido. A solução alternativa para esse problema é garantir que os cmdlets de seu módulo não usem tipos complexos para parâmetros. Está é a maneira errada de fazer isso.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    A maneira certa é incluir um primitivo que é usado internamente pelo cmdlet para capturar o objeto complexo e usá-lo. Como os cmdlets são executadas no contexto do PowerShell, não no Fluxo de Trabalho do PowerShell, dentro do cmdlet $process torna-se o tipo correto de [System.Diagnostic.Process].  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Os ativos de conexão em runbooks são tabelas de hash, que são um tipo complexo. Ainda assim, parece que essas tabelas de hash podem ser transmitidas aos cmdlets para seu parâmetro 'Connection perfeitamente, sem qualquer exceção de conversão. Tecnicamente, alguns tipos de PowerShell podem ser convertidos apropriadamente de sua forma serializada para a forma desserializada e, assim, podem ser passados para os cmdlets para os parâmetros que aceitam o tipo não desserializado. A tabela de hash é um deles. É possível implementar os tipos definidos de um autor de módulo de uma forma que também possam ser desserializados corretamente, mas há algumas compensações a serem consideradas. O tipo deve ter um construtor padrão, ter todas as suas propriedades públicas e ter um PSTypeConverter. No entanto, para os tipos já definidos que não são de propriedade do autor do módulo, não é possível "corrigi-los". Por esse motivo, é recomendável evitar tipos complexos para parâmetros. Dica de criação de runbook: Se seus cmdlets precisam usar um parâmetro de tipo complexo, a solução alternativa nos Fluxos de Trabalho do PowerShell é encapsular o cmdlet que gera o tipo complexo e o cmdlet que consome o tipo complexo na mesma atividade InlineScript. Como a InlineScript executa seu conteúdo como PowerShell e não como um Fluxo de Trabalho do PowerShell, o cmdlet que gera o tipo complexo produziria esse tipo correto, não o tipo complexo desserializado.

5. Torne todos os cmdlets no módulo cmdlets sem estado. O Fluxo de Trabalho do PowerShell executa cada cmdlet chamado no fluxo de trabalho em uma sessão diferente. Isso significa que todos os cmdlets que dependem do estado de sessão criado/modificada por outros cmdlets no mesmo módulo não funcionarão em runbooks do Fluxo de Trabalho do PowerShell.  Veja um exemplo do que não fazer:
   
    ```powershell
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```

6. O módulo deve estar totalmente contido em um pacote capacitado para Xcopy. Os módulos da Automação do Azure são distribuídos para as áreas restritas da Automação quando os runbooks precisam ser executados. Os módulos precisam funcionar independentemente do host em que estão em execução. Você deve poder compactar e mover um pacote de módulo, e este deve funcionar normalmente quando importado para outro ambiente do PowerShell do host. Para que isso aconteça, o módulo não deve depender de qualquer arquivo fora da pasta do módulo. Essa pasta é a pasta que é compactada quando o módulo é importado para a Automação do Azure. O módulo também não deve depender de qualquer configuração de registro exclusiva em um host, como as configurações definidas quando um produto é instalado. Se essa melhor prática não for seguida, o módulo não poderá ser usado na Automação do Azure.  

7. Se estiver fazendo referência aos [módulos Az do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, certifique-se de que não esteja fazendo referência também a `AzureRM`. O módulo `Az` não pode ser usado em conjunto com os módulos `AzureRM`. Há suporte para `Az` em runbooks, mas não são importados por padrão. Para saber mais sobre os módulos `Az` e considerações nas quais prestar atenção, confira [Suporte ao módulo Az na Automação do Azure](az-modules.md).

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre como criar os Módulos do PowerShell, consulte [Escrevendo um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

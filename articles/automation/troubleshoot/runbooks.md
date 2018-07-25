---
title: Solucionar erros com os livros de execução de automação do Azure
description: Saiba como solucionar problemas com runbooks de automação do Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 07/13/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 286a777e16dea72e38b316e86ba57e1811888eec
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044859"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Solucionar problemas de erros com runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erros de autenticação ao trabalhar com runbooks da Automação do Azure

### <a name="sign-in-failed"></a>Cenário: Entrar na conta do Azure falha

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com o `Add-AzureAccount` ou `Connect-AzureRmAccount` cmdlets.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Causa

Este erro ocorre se o nome do ativo da credencial não for válido ou se o nome de usuário e a senha usados para configurar o ativo da credencial de Automação não forem válidos.

#### <a name="resolution"></a>Resolução

Para determinar o que está errado, execute as seguintes etapas:  

1. Verifique se não há caracteres especiais, inclusive o caractere **@** , no nome do ativo de credencial de Automação que você está usando para se conectar ao Azure.  
2. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial da Automação do Azure no editor do ISE do PowerShell local. Para fazer isso, execute os seguintes cmdlets no ISE do PowerShell:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a autenticação falhar localmente, isso indicará que você não configurou corretamente as credenciais do Active Directory do Azure. Confira a postagem de blog [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Autenticação no Azure usando o Azure Active Directory) para configurar corretamente a conta do Azure Active Directory.  

### <a name="unable-to-find-subscription"></a> Cenário: não é possível encontrar a assinatura do Azure

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com o `Select-AzureSubscription` ou `Select-AzureRmSubscription` cmdlets.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Erro

Esse erro ocorre se o nome da assinatura não é válido ou se o usuário do Active Directory do Azure que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.

#### <a name="resolution"></a>Resolução

Para determinar se você tiver autenticada corretamente para o Azure e ter acesso à assinatura que você está tentando selecionar, execute as seguintes etapas:  

1. Certifique-se de execute o cmdlet **Add-AzureAccount** antes de executar o cmdlet **Select-AzureSubscription**.  
2. Se essa mensagem de erro ainda for exibida, modifique o código adicionando o cmdlet **Get-AzureSubscription** após o cmdlet **Add-AzureAccount** e então execute o código. Agora, verifique se a saída de Get-AzureSubscription contém os detalhes de sua assinatura.  

   * Se você não encontrar detalhes da assinatura na saída, isso indicará que a assinatura ainda não foi inicializada.  
   * Se encontrar os detalhes da assinatura na saída, confirme se está usando o nome ou a ID corretos da assinatura com o cmdlet **Select-AzureSubscription** .

### <a name="auth-failed-mfa"></a>Cenário: Autenticação do Azure falhou porque a autenticação multifator é habilitada

#### <a name="issue"></a>Problema

Você receberá o seguinte erro ao autenticar no Azure com seu nome de usuário do Azure e a senha:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Active Directory do Azure para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar no Azure.

#### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Azure Resource Manager, veja [criando entidades de serviço usando o portal do Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md) e [autenticando uma entidade de serviço com o Azure Resource Manager.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Erros comuns ao trabalhar com runbooks

### <a name="not-recognized-as-cmdlet"></a>Cenário: o runbook falha devido a um cmdlet ausente

#### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. O módulo que contém o cmdlet não é importado para a conta de automação
2. O módulo contendo o cmdlet é importado, mas está expirado

#### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido executando uma das seguintes tarefas:

Se o módulo for um módulo do Azure, consulte [Como atualizar módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md) para saber como atualizar os módulos em sua conta de automação.

Se for um módulo separado, verifique se o módulo foi importado em sua Conta de Automação.

### <a name="job-attempted-3-times"></a>Cenário: O início do trabalho de runbook foi tentado três vezes, mas falhou ao iniciar a cada vez

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. Limite de Memória. Há limites documentados sobre a quantidade de memória alocada a [Limites do serviço de automação](../../azure-subscription-service-limits.md#automation-limits) da Área Restrita, de modo que um trabalho pode falhar se estiver usando mais de 400 MB de memória.

2. Módulo Incompatível. Isso poderá ocorrer se as dependências do módulo não estiverem corretas, nesse caso, seu runbook normalmente retornará uma mensagem de “Comando não encontrado” ou “Não é possível associar o parâmetro”.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Métodos sugeridos para trabalhar dentro do limite de memória são dividir a carga de trabalho entre vários runbooks, não processar muitos dados na memória, não gravar saída desnecessária de seus runbooks nem considerar quantos pontos de verificação você grava nos runbooks de fluxo de trabalho do PowerShell.  

* Atualize os módulos do Azure seguindo as etapas [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Cenário: O Runbook falha devido a objeto desserializado

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Causa

Se seu runbook for um fluxo de trabalho do PowerShell, ele armazena objetos complexos em um formato desserializado para manter o estado de runbook se o fluxo de trabalho é suspenso.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes três soluções corrigir esse problema:

1. Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em um InlineScript.
2. Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
3. Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

### <a name="quota-exceeded"></a>Cenário: O trabalho de Runbook falhou porque excedeu a cota alocada

#### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. A cota se aplica a todos os tipos de tarefas de execução de trabalhos, como testar um trabalho, iniciar um trabalho do portal, executar um trabalho usando webhooks e agendar um trabalho para ser executado usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços para Automação, confira os [Preços de automação](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, você precisa alterar sua assinatura de gratuita camada para a camada básica. Para atualizar para o tipo Básico, execute as seguintes etapas:  

1. Entre em sua assinatura do Azure  
2. Selecione a conta de Automação que deseja atualizar  
3. Clique em **Configurações** > **Preço**.
4. Clique em **Habilitar** na parte inferior da página para atualizar sua conta para o tipo **Básico**.

### <a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

#### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Causa

Este erro é causado quando o mecanismo do PowerShell não é possível encontrar o cmdlet que você está usando em seu runbook. Isso pode ocorrer porque o módulo que contém o cmdlet está ausente da conta, há um conflito de nomes com um nome de runbook ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:  

* Verifique se você inseriu o nome do cmdlet corretamente.  
* Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet estiver presente, abra um runbook no modo de edição e a pesquisa para o cmdlet que você deseja localizar na biblioteca ou execução `Get-Command <CommandName>`. Após validar que o cmdlet está disponível para a conta e que não há conflitos de nomes com outros cmdlets ou runbooks, adicione-o à tela e verifique se está usando um parâmetro válido definido no runbook.  
* Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, você poderá resolver isso usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar **NomeDoMódulo\NomeDoCmdlet**.  
* Se você está executando o runbook local em um grupo do Hybrid Worker, certifique-se de que o módulo/cmdlet esteja instalado no computador que hospeda o Hybrid Worker.

### <a name="evicted-from-checkpoint"></a>Cenário: Um runbook de longa execução consistentemente falhará com a exceção: "o trabalho não pode continuar em execução porque ele foi removido repetidamente a partir do mesmo ponto de verificação"

#### <a name="issue"></a>Problema

Esse comportamento ocorre por design devido ao "Compartilhamento justo" monitoramento de processos de automação do Azure, que suspende automaticamente um runbook se ela é executada mais de três horas. No entanto, a mensagem de erro retornada não oferece opções sobre “o que fazer agora”.

#### <a name="cause"></a>Causa

Um runbook pode ser suspenso por vários motivos. As suspensões acontecem principalmente devido a erros. Por exemplo, uma exceção não tratada em um runbook, uma falha de rede ou uma falha no Runbook Worker que estiver executando o runbook farão com que o runbook seja suspenso e inicie do último ponto de verificação quando retomado.

#### <a name="resolution"></a>Resolução

A solução documentada para evitar esse problema é usar pontos de verificação em um fluxo de trabalho. Para saber mais, consulte [Noções Básicas dos Fluxos de Trabalho do PowerShell](../automation-powershell-workflow.md#checkpoints). Veja uma explicação mais detalhada sobre a “Fração Justa” e sobre o Ponto de Verificação neste artigo de blog: [Uso de Pontos de Verificação em Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

### <a name="long-running-runbook"></a>Cenário: falha de conclusão de um runbook de execução longa

#### <a name="issue"></a>Problema

Esse comportamento ocorre por design em áreas restritas do Azure devido ao monitoramento de "Fração Justa" de processos na Automação do Azure, que suspende automaticamente um runbook se ele é executado por mais de três horas.

#### <a name="cause"></a>Causa

O runbook foi executado acima do limite de 3 horas permitido pela fração justa em uma Área Restrita do Azure

#### <a name="resolution"></a>Resolução

A solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelo limite de runbook de 3 horas da [fração justa](../automation-runbook-execution.md#fair-share) como as áreas restritas do Azure.

## <a name="common-errors-when-importing-modules"></a>Erros comuns durante a importação de módulos

### <a name="module-fails-to-import"></a>Cenário: Falha de módulo importar ou cmdlets não pode ser executados após a importação

#### <a name="issue"></a>Problema

Falha ao importar de um módulo ou importa com êxito, mas nenhum cmdlets são extraídos.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não pode importar com êxito à automação do Azure são:

* A estrutura não corresponde à estrutura em que a Automação precisa estar.
* O módulo é dependente de outro módulo que não foi implantado para sua conta de Automação.
* O módulo não tem suas dependências na pasta.
* O `New-AzureRmAutomationModule` cmdlet está sendo usado para carregar o módulo, e você não forneceu o caminho completo de armazenamento ou não carregou o módulo usando uma URL publicamente acessível.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se o módulo segue o seguinte formato: ModuleName.Zip **->** ModuleName ou Número de versão **->** (ModuleName.psm1, ModuleName.psd1)
* Abra o arquivo .psd1 e veja se o módulo tem dependências. Se tiver, carregue esses módulos para a conta de Automação.
* Verifique se quaisquer .dlls referenciadas estão presentes na pasta do módulo.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
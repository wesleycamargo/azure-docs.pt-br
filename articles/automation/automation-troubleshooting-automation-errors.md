<properties 
   pageTitle="Dicas de solução de problemas para erros comuns na Automação do Azure | Microsoft Azure"
   description="Este artigo fornece etapas básicas de solução de problemas para corrigir erros comuns que você pode encontrar ao trabalhar com a Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" 
   tags="top-support-issue"/>
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="sngun; v-reagie"/>

# Dicas de solução de problemas para erros comuns na Automação do Azure

Este artigo explica alguns dos erros comuns que podem ocorrer ao se trabalhar com a Automação do Azure e sugere etapas de correção possíveis.

## Como solucionar problemas de erros de autenticação ao trabalhar com runbooks de Automação do Azure  

### Cenário: falha ao entrar na Conta do Azure

**Erro:** você recebe o erro "Unknown\_user\_type: tipo de usuário desconhecido" ao trabalhar com os cmdlets Add-AzureAccount ou Login-AzureRmAccount.

**Motivo do erro:** esse erro ocorrerá se o nome do ativo de credencial não for válido ou se o nome de usuário e senha que você usou para configurar o ativo de credencial de automação não forem válidos.

**Dicas de solução de problemas:** para determinar o que está errado, realize as seguintes etapas:

1. Verifique se não há caracteres especiais, inclusive o caractere **@**, no nome do ativo de credencial de Automação que você está usando para se conectar ao Azure.  

2. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial da Automação do Azure no editor do ISE do PowerShell local. Para fazer isso, execute os seguintes cmdlets no ISE do PowerShell:

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Se a autenticação falhar localmente, isso indicará que você não configurou corretamente as credenciais do Active Directory do Azure. Confira a postagem de blog [Autenticação no Azure usando o Active Directory do Azure](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para configurar corretamente a conta do Active Directory do Azure.

  <br/>
### Cenário: não é possível localizar a assinatura do Azure

**Erro:** você recebe o erro "A assinatura chamada ``<subscription name>`` não pode ser encontrada" ao trabalhar com os cmdlets Select-AzureSubscription ou Select-AzureRmSubscription.

**Motivo do erro:** esse erro ocorrerá se o nome da assinatura não for válido ou se o usuário do Active Directory do Azure que está tentando obter os detalhes da assinatura não estiver configurado como um administrador da assinatura.

**Dicas de solução de problemas:** para determinar se você foi autenticado corretamente no Azure e se tem acesso à assinatura que está tentando selecionar, execute as seguintes etapas:

1. Execute o cmdlet **Add-AzureAccount** antes de executar **Select-AzureSubscription**.  

2. Se essa mensagem de erro ainda for exibida, modifique o código adicionando o cmdlet **Get-AzureSubscription** após o cmdlet **Add-AzureAccount** e, em seguida, execute o código. Agora, verifique se a saída de Get-AzureSubscription contém os detalhes de sua assinatura.
    * Se você não encontrar detalhes da assinatura na saída, isso indicará que a assinatura ainda não foi inicializada.  
    * Se encontrar os detalhes da assinatura na saída, confirme se está usando o nome de assinatura ou a ID corretos com o cmdlet **Select-AzureSubscription**.   

  <br/>
### Cenário: falha de autenticação no Azure porque a autenticação multifator está habilitada

**Erro:** você recebe o erro "Add-AzureAccount: AADSTS50079: o registro de autenticação forte (prova) é necessário" ao fazer a autenticação no Azure com seu nome de usuário e senha do Azure.

**Motivo do erro:** se tiver a autenticação multifator em sua conta do Azure, você não poderá usar um usuário do Active Directory do Azure para se autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar no Azure.

**Dicas de solução de problemas:** para usar um certificado com os cmdlets de Gerenciamento de Serviços do Azure, confira [Como criar e adicionar um certificado para gerenciar os serviços do Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Gerenciador de Recursos do Azure, confira [como criar uma entidade de serviço usando o portal do Azure](./resource-group-create-service-principal-portal.md) e [como autenticar uma entidade de serviço com o Gerenciador de Recursos do Azure.](./resource-group-authenticate-service-principal.md)

  <br/>
## Solução de erros comuns ao trabalhar com runbooks  
### Cenário: O Runbook falha devido a objeto desserializado

**Erro:** o runbook falha com o erro "Não é possível associar o parâmetro ``<ParameterName>``. Não é possível converter o valor ``<ParameterType>`` do tipo ``<ParameterType>`` Desserializado no tipo ``<ParameterType>``".

**Motivo do erro:** se o runbook for um Fluxo de Trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para que o estado do runbook persista se o fluxo de trabalho for suspenso.

**Dicas de solução de problemas:** qualquer uma das três seguintes soluções corrigirá o problema:

1. Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em um InlineScript.  
2. Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.  

3. Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

  <br/>
### Cenário: falha no trabalho de runbook porque a cota alocada foi excedida

**Erro:** o trabalho de runbook falha com o erro "A cota do tempo de execução do trabalho total mensal foi atingida para esta assinatura".

**Motivo do erro:** esse erro ocorre quando a execução do trabalho excede a cota gratuita de 500 minutos para a conta. A cota se aplica a todos os tipos de tarefas de execução de trabalhos, como testar um trabalho, iniciar um trabalho do portal, executar um trabalho usando webhooks e agendar um trabalho para ser executado usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços para Automação, confira os [Preços de automação](https://azure.microsoft.com/pricing/details/automation/).

**Dicas de solução de problemas:** para usar mais de 500 minutos de processamento por mês, você precisará alterar a assinatura da camada Gratuita para a camada Básica. Para atualizar para o tipo Básico, execute as seguintes etapas:

1. Entre em sua assinatura do Azure  
2. Selecione a conta de Automação que deseja atualizar  
3. Clique em **Configurações** > **Tipo de preços e Uso** > **Tipo de preços**  
4. Na folha **Escolha seu tipo de preços**, selecione **Básico**    

  <br/>
### Cenário: cmdlet não reconhecido ao se executar um runbook

**Erro:** o trabalho de runbook falha com o erro "``<cmdlet name>``: o termo ``<cmdlet name>`` não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável."

**Motivo do erro:** esse erro ocorre quando o mecanismo do PowerShell não consegue localizar o cmdlet que você está usando no runbook. Isso pode ocorrer porque o módulo que contém o cmdlet está ausente da conta, há um conflito de nomes com um nome de runbook ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

**Dicas de solução de problemas:** qualquer uma das seguintes soluções corrigirá o problema:

- Verifique se você inseriu o nome do cmdlet corretamente e se o caminho do cmdlet está correto.  

- Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e pesquise o cmdlet que você deseja localizar na biblioteca ou execute **Get-Command ``<CommandName>``**. Após validar que o cmdlet está disponível para a conta e que não há conflitos de nomes com outros cmdlets ou runbooks, adicione-o à tela e verifique se está usando um parâmetro válido definido no runbook.

- Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, você poderá resolver isso usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar **ModuleName\\CmdletName**.

- Se você está executando o runbook local em um grupo do Hybrid Worker, certifique-se de que o módulo/cmdlet esteja instalado no computador que hospeda o Hybrid Worker.

  <br/>
## Solução de erros comuns durante a importação de módulos 

### Cenário: falha de importação de módulo ou os cmdlets não podem ser executados após a importação

**Erro:** ocorre falha na importação de um módulo ou a importação é bem-sucedida, mas nenhum cmdlets é extraído.

**Motivo do erro:** alguns motivos comuns pelos quais um módulo pode não realizar a importação com êxito para a Automação do Azure são:

- A estrutura não corresponde à estrutura em que a Automação precisa estar.  

- O módulo é dependente de outro módulo que não foi implantado para sua conta de Automação.

- O módulo não tem suas dependências na pasta.

- O cmdlet **New-AzureRmAutomationModule** está sendo usado para carregar o módulo, e você não forneceu o caminho de armazenamento completo ou não carregou o módulo usando uma URL acessível publicamente.

**Dicas de solução de problemas:** qualquer uma das seguintes soluções corrigirá o problema:

- Verifique se o módulo segue este formato: ModuleName.Zip **->** ModuleName ou Número de Versão **->** (ModuleName.psm1, ModuleName.psd1)

- Abra o arquivo .psd1 e veja se o módulo tem dependências. Se tiver, carregue esses módulos para a conta de Automação.

- Verifique se quaisquer .dlls referenciadas estão presentes na pasta do módulo.

  <br/>

## Solucionar erros comuns ao trabalhar com DSC (Configuração de estado desejado)  

### Cenário: o nó está com um status de falha e um erro "Não encontrado"

**Erro:** o nó apresenta um relatório de status "Falha" contendo o erro "A tentativa de obter a ação do servidor https://<url>//accounts/<account-id>Nodes(AgentId=<agent-id>)/GetDscAction falhou porque não foi possível encontrar uma configuração válida <guid>."

**Motivo do erro:** essa falha normalmente ocorre porque o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC.WebServer).

**Dicas de solução de problemas:** verifique mais uma vez se o nome de configuração do nó está sendo usado e não o nome de configuração. Você pode usar o botão "atribuir configuração de nó" na folha do nó no portal ou o cmdlet Set-AzureRMAutomationDscNode para mapear o nó até uma configuração de nó válida.

### Cenário: nenhuma configuração de nó (arquivos mof) é produzida quando uma compilação de configuração é executada

**Erro:** seu trabalho de compilação de DSC foi suspenso com o seguinte erro: "Compilação concluída com êxito, mas nenhum arquivo .mofs de configuração do nó foi gerado".

**Motivo do erro:** quando a expressão ao lado de "Nó" na configuração de DSC retornar $null, nenhuma configuração de nó será produzida.

**Dicas de solução de problemas:** verifique se a expressão ao lado de Nó não está retornando $null. Se você estiver passando ConfigurationData, certifique-se de que esteja passando os valores esperados que a configuração precisa dos dados de configuração. No exemplo, "$AllNodes." Confira https://azure.microsoft.com/pt-BR/documentation/articles/automation-dsc-compile/#configurationdata para saber mais.

### Cenário: o relatório de nó de DSC fica preso no estado "Em andamento"

**Erro:** o Agente DSC gera "Nenhuma instância foi encontrada com os valores de propriedade especificados".

**Motivo do erro:** você atualizou sua versão do WMF e corrompeu o WMI.

**Dicas de solução de problemas:** siga as instruções nesta publicação para corrigir o problema: https://msdn.microsoft.com/pt-BR/powershell/wmf/limitation_dsc

### Cenário: não é possível usar uma credencial em uma configuração de DSC 

**Erro:** seu trabalho de compilação do DSC foi suspenso com o seguinte erro: "System.InvalidOperationException erro ao processar a propriedade "Credential" DO TIPO "<some resource name>": a conversão e armazenamento de uma senha criptografada como texto sem formatação é permitida somente se PSDscAllowPlainTextPassword estiver definido como true".

**Motivo do erro:** você tentou usar uma credencial em uma configuração, mas não passou o ConfigurationData adequado para definir PSAllowPlainTextPassword como true para cada configuração de nó.

**Dicas de solução de problemas:** passe o ConfigurationData adequado para definir PSAllowPlainTextPassword como true para cada configuração de nó mencionada na configuração. Confira https://azure.microsoft.com/pt-BR/documentation/articles/automation-dsc-compile/#assets para saber mais.

  <br/>

## Próximas etapas

Se tiver seguido as etapas de solução de problemas acima e precisar de mais ajuda a qualquer momento neste artigo, você poderá:

- Obter ajuda de especialistas do Azure. Enviar seu problema aos [fóruns do Azure no MSDN ou do excedente de pilha.](https://azure.microsoft.com/support/forums/)

- Registrar um incidente de suporte do Azure. Vá até o [Site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte** em **Suporte técnico e de cobrança**.

- Postar uma Solicitação de Script no [Script Center](https://azure.microsoft.com/documentation/scripts/) se está procurando uma solução de runbook de Automação do Azure ou um módulo de integração.

- Postar comentários ou solicitações de recursos para a Automação do Azure em [User Voice](https://feedback.azure.com/forums/34192--general-feedback).

<!---HONumber=AcomDC_0218_2016-->
<properties
   pageTitle="Dicas de solução de problemas para erros comuns na Automação do Azure | Microsoft Azure"
   description="Este artigo fornece etapas básicas de solução de problemas para corrigir erros comuns que você pode encontrar ao trabalhar com a Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
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


### Cenário: não é possível localizar a assinatura do Azure

**Erro:** você recebe o erro "A assinatura chamada ``<subscription name>`` não pode ser encontrada" ao trabalhar com os cmdlets Select-AzureSubscription ou Select-AzureRmSubscription.

**Motivo do erro:** esse erro ocorrerá se o nome da assinatura não for válido ou se o usuário do Azure Active Directory que está tentando obter os detalhes da assinatura não estiver configurado como um administrador da assinatura.

**Dicas de solução de problemas:** para determinar se você foi autenticado corretamente no Azure e se tem acesso à assinatura que está tentando selecionar, execute as seguintes etapas:

1. Execute o cmdlet **Add-AzureAccount** antes de executar **Select-AzureSubscription**.  

2. Se essa mensagem de erro ainda for exibida, modifique o código ao adicionar o cmdlet **Get-AzureSubscription** após o cmdlet **Add-AzureAccount** e então execute o código. Agora, verifique se a saída de Get-AzureSubscription contém os detalhes de sua assinatura.
    * Se você não encontrar detalhes da assinatura na saída, isso indicará que a assinatura ainda não foi inicializada.  
    * Se encontrar os detalhes da assinatura na saída, confirme se está usando o nome ou a ID corretos da assinatura com o cmdlet **Select-AzureSubscription**.   


### Cenário: falha de autenticação no Azure porque a autenticação multifator está habilitada

**Erro:** você recebe o erro "Add-AzureAccount: AADSTS50079: o registro de autenticação forte (prova) é necessário" ao fazer a autenticação no Azure com seu nome de usuário e sua senha do Azure.

**Motivo do erro:** se você tiver a autenticação multifator em sua conta do Azure, não poderá usar um usuário do Azure Active Directory para se autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar no Azure.

**Dicas de solução de problemas:** para usar um certificado com os cmdlets do Gerenciamento de Serviços do Azure, veja [criação e adição de um certificado para gerenciar os serviços do Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Azure Resource Manager, veja [criação de entidades de serviço usando o portal do Azure](./resource-group-create-service-principal-portal.md) e [autenticação de uma entidade de serviço com o Azure Resource Manager.](./resource-group-authenticate-service-principal.md)


## Solução de erros comuns ao trabalhar com runbooks

### Cenário: O Runbook falha devido a objeto desserializado

**Erro:** o runbook falha com o erro "Não é possível associar o parâmetro ``<ParameterName>``. Não é possível converter o valor ``<ParameterType>`` do tipo ``<ParameterType>`` Desserializado no tipo ``<ParameterType>``".

**Motivo do erro:** se o runbook for um Fluxo de Trabalho do PowerShell, ele armazena objetos complexos em um formato desserializado para que o estado do runbook persista se o fluxo de trabalho for suspenso.

**Dicas de solução de problemas:** qualquer uma das três seguintes soluções corrigirá o problema:

1. Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em um InlineScript.  
2. Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.  

3. Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.


### Cenário: falha no trabalho de runbook porque a cota alocada foi excedida

**Erro:** falha em seu trabalho de runbook com o erro "A cota para o tempo de execução do trabalho total mensal foi atingida para esta assinatura".

**Motivo do erro:** esse erro ocorre quando a execução do trabalho excede a cota de 500 minutos gratuitos para sua conta. A cota se aplica a todos os tipos de tarefas de execução de trabalhos, como testar um trabalho, iniciar um trabalho do portal, executar um trabalho usando webhooks e agendar um trabalho para ser executado usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços da Automação, veja [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Dicas de solução de problemas:** se você quiser usar mais de 500 minutos de processamento por mês, precisará alterar sua assinatura do tipo Gratuito para o tipo Básico. Para atualizar para o tipo Básico, execute as seguintes etapas:

1. Entre em sua assinatura do Azure  
2. Selecione a conta de Automação que deseja atualizar  
3. Clique em **Configurações** > **Tipo de preços e Uso** > **Tipo de preços**  
4. Na folha **Escolha seu tipo de preços**, selecione **Básico**    


### Cenário: cmdlet não reconhecido ao se executar um runbook

**Erro:** falha no trabalho de runbook com o erro "``<cmdlet name>``: o termo ``<cmdlet name>`` não é reconhecido como o nome de um cmdlet, uma função, um arquivo de script ou um programa operável."

**Motivo do erro:** esse erro ocorre quando o mecanismo do PowerShell não consegue localizar o cmdlet que você está usando no runbook. Isso pode ocorrer porque o módulo que contém o cmdlet está ausente da conta, há um conflito de nomes com um nome de runbook ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

**Dicas de solução de problemas:** qualquer uma das soluções a seguir corrigirá o problema:

- Verifique se você inseriu o nome do cmdlet corretamente e se o caminho do cmdlet está correto.  

- Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e pesquise o cmdlet que você deseja localizar na biblioteca ou execute **Get-Command``<CommandName>``**. Após validar que o cmdlet está disponível para a conta e que não há conflitos de nomes com outros cmdlets ou runbooks, adicione-o à tela e verifique se está usando um parâmetro válido definido no runbook.

- Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, você poderá resolver isso usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar **NomeDoMódulo\\NomeDoCmdlet**.

- Se você está executando o runbook local em um grupo do Hybrid Worker, certifique-se de que o módulo/cmdlet esteja instalado no computador que hospeda o Hybrid Worker.


### Cenário: um runbook de execução longa falha consistentemente com a exceção: "O trabalho não pode continuar em execução porque foi removido repetidamente do mesmo ponto de verificação".

**Motivo do erro:** isso faz parte do comportamento do design devido ao monitoramento de "Fração Justa" de processos na Automação do Azure, que suspende automaticamente um runbook caso ele esteja sendo executado por mais de três horas. No entanto, a mensagem de erro retornada não oferece opções sobre “o que fazer agora”. Um runbook pode ser suspenso por vários motivos. As suspensões acontecem principalmente devido a erros. Por exemplo, uma exceção não tratada em um runbook, uma falha de rede ou uma falha no Runbook Worker que estiver executando o runbook farão com que o runbook seja suspenso e inicie do último ponto de verificação quando retomado.

**Dicas de solução de problemas:** a solução documentada para evitar esse problema é usar Pontos de Verificação em um fluxo de trabalho. Para saber mais, consulte [Noções Básicas dos Fluxos de Trabalho do PowerShell](automation-powershell-workflow.md#Checkpoints). Veja uma explicação mais detalhada sobre a “Fração Justa” e sobre os Pontos de Verificação neste artigo de blog: [Uso de Pontos de Verificação em Runbooks](https://azure.microsoft.com/pt-BR/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## Solução de erros comuns durante a importação de módulos

### Cenário: falha de importação de módulo ou os cmdlets não podem ser executados após a importação

**Erro:** um módulo falha ao importar ou importa com êxito, mas nenhum cmdlet é extraído.

**Motivo do erro:** alguns motivos comuns pelos quais um módulo pode não realizar a importação com êxito para a Automação do Azure são:

- A estrutura não corresponde à estrutura em que a Automação precisa estar.  

- O módulo é dependente de outro módulo que não foi implantado para sua conta de Automação.

- O módulo não tem suas dependências na pasta.

- O cmdlet **New-AzureRmAutomationModule** está sendo usado para carregar o módulo, e você não forneceu o caminho de armazenamento completo ou não carregou o módulo usando uma URL acessível publicamente.

**Dicas de solução de problemas:** qualquer uma das soluções a seguir corrigirá o problema:

- Verifique se o módulo segue este formato: NomeDoMódulo.Zip **->** NomeDoMódulo ou Número de Versão **->** (NomeDoMódulo.psm1, NomeDoMódulo.psd1)

- Abra o arquivo .psd1 e veja se o módulo tem dependências. Se tiver, carregue esses módulos para a conta de Automação.

- Verifique se quaisquer .dlls referenciadas estão presentes na pasta do módulo.


## Solucionar erros comuns ao trabalhar com DSC (Configuração de estado desejado)  

### Cenário: o nó está com um status de falha e um erro "Não encontrado"

**Erro:** o nó tem um relatório com o status **Com Falha** e com o erro "A tentativa de obter a ação do servidor https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction failed because a valid configuration ``<guid>"não foi encontrada".

**Motivo do erro:** esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de a um nome de configuração de nó (por exemplo, ABC.WebServer).

**Dicas de solução de problemas:**

- Verifique se você está atribuindo o nó com o "nome de configuração de nó" e não o "nome de configuração".  

- Você pode atribuir uma configuração de nó para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.
    - Para atribuir uma configuração de nó a um nó que usa o portal do Azure, abra a folha **Nós de DSC**, selecione um nó e clique no botão **Atribuir configuração de nó**.  
    - Para atribuir uma configuração de nó a um nó usando um cmdlet do PowerShell, use o cmdlet **Set-AzureRmAutomationDscNode**


### Cenário: nenhuma configuração de nó (arquivos mof) foi produzida quando uma compilação foi compilada

**Erro:** seu trabalho de compilação de DSC foi suspenso com o seguinte erro: "Compilação concluída com êxito, mas nenhum arquivo .mofs de configuração de nó foi gerado".

**Motivo do erro:** quando a expressão após a palavra-chave **Node** na configuração de DSC for avaliada como $null, nenhuma configuração de nó será produzida.

**Dicas de solução de problemas:** qualquer uma das soluções a seguir corrigirá o problema:

- Verifique se a expressão ao lado da palavra-chave **Node** na definição de configuração não está sendo avaliada como $null.  
- Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que esteja passando os valores esperados e que a configuração exige de [configurationData](automation-dsc-compile.md#configurationdata).


### Cenário: o relatório de nó de DSC fica preso no estado "em andamento"

**Erro:** o Agente DSC gera "Nenhuma instância encontrada com os valores de propriedade especificados".

**Motivo do erro:** você atualizou sua versão do WMF e corrompeu o WMI.

**Dicas de solução de problemas:** siga as instruções na postagem de blog [Problemas conhecidos e limitações do DSC](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) para corrigir o problema.


### Cenário: não é possível usar uma credencial em uma configuração de DSC

**Erro:** seu trabalho de compilação do DSC foi suspenso com o seguinte erro: "Erro System.InvalidOperationException ao processar a propriedade 'Credential' do tipo ``<some resource name>``: a conversão e o armazenamento de uma senha criptografada como texto sem formatação só será permitida se PSDscAllowPlainTextPassword for definido como verdadeiro".

**Motivo do erro:** você usou uma credencial em uma configuração, mas não forneceu os **ConfigurationData** adequados para definir **PSDscAllowPlainTextPassword** como verdadeira para cada configuração de nó.

**Dicas de solução de problemas:**
- Transmita os **ConfigurationData** adequados para definir **PSDscAllowPlainTextPassword** como verdadeira cada configuração de nó mencionada na configuração. Para saber mais, consulte [ativos no DSC da Automação do Azure](automation-dsc-compile.md#assets).


## Próximas etapas

Se tiver seguido as etapas de solução de problemas acima e precisar de mais ajuda a qualquer momento neste artigo, você poderá:

- Obter ajuda de especialistas do Azure. Envie seu problema para os [fóruns do Azure no MSDN ou do Stack Overflow.](https://azure.microsoft.com/support/forums/)

- Registrar um incidente de suporte do Azure. Vá até o [Site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte** em **Suporte técnico e de cobrança**.

- Publique uma Solicitação de Script no [Script Center](https://azure.microsoft.com/documentation/scripts/) se estiver procurando uma solução de runbook ou um módulo de integração da Automação do Azure.

- Poste comentários ou solicitações de recursos para a Automação do Azure no [User Voice](https://feedback.azure.com/forums/34192--general-feedback).

<!---HONumber=AcomDC_0302_2016-->
<properties
   pageTitle="Introdução ao DSC de Automação do Azure"
   description="Explicações e exemplos das tarefas mais comuns no DSC (Configuração de Estado Desejado) de Automação do Azure"
   services="automation" 
   documentationCenter="na" 
   authors="eslesar" 
   manager="dongill" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="06/06/2016"
   ms.author="magoedte;eslesar"/>
   

# Introdução ao DSC de Automação do Azure

Este tópico explica como realizar as tarefas mais comuns com o DSC (Configuração de Estado Desejado) de Automação do Azure, como criar, importar e compilar configurações, máquinas de integração para gerenciar e exibir relatórios. Para obter uma visão geral do que o DSC de Automação do Azure é, consulte [Visão geral do DSC da Automação do Azure](automation-dsc-overview.md). Para obter a documentação do DSC, consulte [Windows PowerShell Desired State Configuration Overview] \(Visão geral da configuração de estado desejado do Windows PowerShell).

Este tópico fornece um guia passo a passo para usar o DSC de Automação do Azure. Se você quiser um ambiente de exemplo que já esteja configurado sem seguir as etapas descritas neste tópico, poderá usar o modelo de ARM em https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup. Esse modelo define um ambiente completo do DSC de Automação do Azure, incluindo uma VM do Azure que é gerenciada pelo DSC de Automação do Azure.
 
## Pré-requisitos

Para concluir os exemplos neste tópico, são necessários:

- Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar como da Automação do Azure, consulte [Conta Executar como do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para encontrar instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no Portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## Criando uma configuração de DSC

Criaremos uma [configuração de DSC](https://msdn.microsoft.com/powershell/dsc/configurations) simples que garante a presença ou a ausência do recurso do Windows **Web-Server** (IIS), dependendo de como os nós são atribuídos.

1. Inicie o ISE do Windows PowerShell (ou qualquer editor de texto).

2. Digite o seguinte texto:

    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true

            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'

            }
        }
        }
    ```
3. Salve o arquivo como `TestConfig.ps1`.

Essa configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), que garante a presença ou a ausência da funcionalidade **Web-Server**.

## Importando uma configuração na Automação do Azure

Em seguida, importaremos a configuração para a conta de Automação.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **DSC Configurations** (Configurações DSC).

4. Na folha **DSC Configurations (Configurações DSC), clique em **Add a configuration** (Adicionar uma configuração).

5. Na folha **Importar Configuração**, navegue até o arquivo `TestConfig.ps1` em seu computador.
    
    ![Captura de tela da folha **Importar Configuração**](./media/automation-dsc-getting-started/AddConfig.png)
    

6. Clique em **OK**.

## Exibindo uma configuração na Automação do Azure

Depois de importar uma configuração, você pode vê-la no Portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **DSC Configurations** (Configurações DSC)

4. Na folha **DSC Configurations (Configurações DSC), clique em **TestConfig** (esse é o nome da configuração importada no procedimento anterior).

5. Na folha **TestConfig Configuration** (Configuração do TestConfig), clique em **View configuration source** (Exibir fonte da configuração).

    ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)
    
    A folha **TestConfig Configuration source** (Fonte da Configuração do TestConfig) é aberta, exibindo o código do PowerShell para a configuração.
    
## Compilando uma configuração na Automação do Azure

Antes de aplicar um estado desejado a um nó, uma configuração DSC definindo esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull do DSC de Automação. Para obter uma descrição mais detalhada da compilação de configurações no DSC de Automação do Azure, consulte [Compilando configurações no DSC de Automação do Azure](automation-dsc-compile.md). Para obter mais informações sobre a compilação de configurações, consulte [Configurações DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **DSC Configurations** (Configurações DSC)

4. Na folha **DSC Configurations (Configurações DSC), clique em **TestConfig** (o nome da configuração importada anteriormente).

5. Na folha **TestConfig Configuration** (Configuração do TestConfig), clique em **Compilar** e em **Sim**. Isso inicia um trabalho de compilação.
    
    ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig) realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)
    
> [AZURE.NOTE] Quando você compila uma configuração na Automação do Azure, ela implanta automaticamente quaisquer MOFs de configuração de nó no servidor de pull.

## Exibindo um trabalho de compilação

Depois de iniciar uma compilação, você pode vê-la no bloco **Compilation jobs** (Trabalhos de compilação) na folha **Configuração**. O bloco **Compilation jobs** (Trabalhos de compilação) mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre uma folha de trabalho de compilação, ela mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **DSC Configurations** (Configurações DSC).

4. Na folha **DSC Configurations (Configurações DSC), clique em **TestConfig** (o nome da configuração importada anteriormente).

5. No bloco **Compilation jobs** (Trabalhos de compilação) da folha **TestConfig Configuration** (Configuração do TestConfig), clique em qualquer um dos trabalhos listados. Uma folha **Trabalho de Compilação** é aberta, rotulada com a data em que o trabalho de compilação foi iniciado.

    ![Captura de tela da folha Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)
  
6. Clique em qualquer bloco na folha **Trabalho de Compilação** para ver mais detalhes sobre o trabalho.

## Exibindo configurações de nó

A conclusão com êxito de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e está pronto para ser submetido ao pull e aplicado por um ou mais nós. Você pode exibir as configurações de nó na sua conta de Automação na folha **DSC Node Configurations** (Configurações de Nó DSC). Uma configuração de nó tem um nome com o formato *ConfigurationName*.*NodeName*.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **DSC Node Configurations** (Configurações de Nó DSC).

    ![Captura de tela da folha DSC Node Configurations (Configurações de Nó DSC)](./media/automation-dsc-getting-started/NodeConfigs.png)
    
## Integrando uma VM do Azure para o gerenciamento com o DSC de Automação do Azure

Você pode usar o DSC de Automação do Azure para gerenciar VMs do Azure (Clássicas e Resource Manager), VMs locais, computadores Linux, VMs AWS e computadores físicos locais. Neste tópico, abordamos como integrar somente VMs do Azure Resource Manager. Para obter informações sobre a integração de outros tipos de computadores, consulte [Integração de computadores para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).

### Para integrar uma VM do Azure Resource Manager para o gerenciamento pelo DSC de Automação do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **Nós DSC**.

4. Na folha **Nós DSC**, clique em **Adicionar VM do Azure**.

    ![Captura de tela da folha Nós DSC realçando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)

5. NA folha **Adicionar VMs do Azure**, clique em **Selecionar máquinas virtuais para integrar**.

6. Na folha **Select VMs** (Selecionar VMs), selecione a VM que deseja integrar e clique em **OK**.

    >[AZURE.IMPORTANT] Ela deve ser uma VM do Azure Resource Manager executando o Windows Server 2008 R2 ou posterior.
    
7. Na folha **Adicionar VMs do Azure**, clique em **Configurar dados de registro**.

8. Na folha **Registro**, insira o nome da configuração de nó que você deseja aplicar à VM na caixa **Node Configuration Name** (Nome de Configuração do Nó). Isso deve corresponder exatamente ao nome de uma configuração de nó na conta de Automação. Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após integrar o nó. Marque **Reinicialize o nó, se necessário**, e clique em **OK**.
    
    ![Captura de tela da folha Registro](./media/automation-dsc-getting-started/RegisterVM.png)
    
    A configuração do nó especificada será aplicada à VM em intervalos especificados pela **Configuration Mode Frequency** (Frequência do Modo de Configuração) e a VM verificará se há atualizações para a configuração de nó em intervalos especificados pela **Refresh Frequency** (Frequência de Atualização). Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Gerenciador de Configurações Local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
    
9. Na folha **Adicionar VMs do Azure**, clique em **Criar**.

O Azure iniciará o processo de integração da VM. Quando for concluída, a VM será exibida na folha **Nós DSC** na conta de Automação.

## Exibindo a lista de nós DSC

Você pode exibir a lista de todos os computadores que foram integrados para gerenciamento na sua conta de Automação na folha **Nós DSC**.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **Nós DSC**.

## Exibindo relatórios para nós DSC

Cada vez que o DSC de Automação do Azure executa uma verificação de consistência em um nó gerenciado, o nó envia um relatório de status para o servidor de pull. Você pode exibir esses relatórios na folha do nó.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **Nós DSC**.

4. No bloco **Relatórios**, clique em qualquer um dos relatórios na lista.

    ![Captura de tela da folha Relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:
- O status do relatório, se o nó é "Compatível", a configuração está "Com Falha" ou nó "Não é Compatível" (quando o nó está no modo **applyandmonitor** e o computador não está no estado desejado).
- A hora de início para a verificação de consistência.
- O tempo de execução total para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro. 
- Todos os recursos de DSC usados na configuração e o estado de cada recurso (se o nó está no estado desejado para esse recurso), você pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
- O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **View raw report** (Exibir relatório bruto) para ver os dados reais que o nó envia ao servidor. Para obter mais informações sobre como usar esses dados, consulte [Usando um servidor de relatório de DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Pode levar algum tempo depois de um nó ser integrado até que o primeiro relatório esteja disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório após você integrar um nó.

## Reatribuindo um nó a uma configuração de nó diferente

Você pode atribuir um nó para usar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **Nós DSC**.

4. Na folha **Nós DSC**, clique no nome do nó que deseja reatribuir.

5. Na folha do nó, clique em **Assign node** (Atribuir nó).

    ![Captura de tela da folha Nó realçando o botão Assign Node (Atribuir Nó)](./media/automation-dsc-getting-started/AssignNode.png)

6. Na folha **Atribuir Configuração de Nó**, selecione a configuração de nó à qual você deseja atribuir o nó e, em seguida, clique em **OK**.

    ![Captura de tela da folha Atribuir Configuração de Nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)
    
## Cancelando o registro de um nó

Se você não desejar mais que um nó seja gerenciado pelo DSC de Automação do Azure, você poderá cancelar o registro dele.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, clique em **Todos os recursos** e, em seguida, no nome da sua conta de Automação.

3. Na folha **Conta de Automação**, clique em **Nós DSC**.

4. Na folha **Nós DSC**, clique no nome do nó que deseja reatribuir.

5. Na folha do nó, clique em **Cancelar Registro**.

    ![Captura de tela da folha Nó realçando o botão Cancelar Registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## Artigos relacionados
* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)
* [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [cmdlets da DSC de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [preço da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_0608_2016-->
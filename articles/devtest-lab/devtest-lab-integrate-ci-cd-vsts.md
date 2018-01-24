---
title: "Integrar o Azure DevTest Labs em seu pipeline de integração contínua e entrega do VSTS | Microsoft Docs"
description: "Aprenda a integrar o Azure DevTest Labs em seu pipeline de integração contínua e entrega do VSTS"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: 6c6bd4fbd89ec87cbbdbfb9ed42f86a484acf7ad
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrar o Azure DevTest Labs em seu pipeline de integração contínua e entrega do VSTS
Você pode usar a extensão *Tarefas do Azure DevTest Labs* instalada no VSTS (Visual Studio Team Services) para integrar facilmente seu pipeline de build e lançamento de CI/CD ao Azure DevTest Labs. A extensão instala três tarefas: 
* Criar uma máquina virtual
* Criar uma imagem personalizada de uma VM
* Excluir uma VM 

O processo torna fácil, por exemplo, implantar rapidamente uma “imagem dourada” para uma tarefa de teste específica e excluí-la quando o teste for concluído.

Este artigo mostra como criar e implantar uma VM, criar uma imagem personalizada e, em seguida, excluir a VM, tudo como um pipeline completo. Você normalmente executaria cada tarefa individualmente em seu próprio pipeline personalizado de build-teste-implantação.

## <a name="before-you-begin"></a>Antes de começar
Antes de ser possível integrar seu pipeline de CI/CD ao Azure DevTest Labs, você deve instalar a extensão do Visual Studio Marketplace.
1. Acesse [Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Selecione **Instalar**.
1. Conclua o assistente.

## <a name="create-a-resource-manager-template"></a>Criar um modelo do Resource Manager
Esta seção descreve como criar o modelo do Azure Resource Manager que você usa para criar uma máquina virtual do Azure sob demanda.

1. Para criar um modelo do Resource Manager em sua assinatura, conclua o procedimento em [Usar um modelo do Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Antes de gerar o modelo do Resource Manager, adicione o [artefato WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação da VM.

   O acesso do WinRM é necessário para usar tarefas de implantação, como *Cópia de arquivo do Azure* e *PowerShell em computadores de destino*.

   > [!NOTE]
   > Quando usar o WinRM com um endereço IP compartilhado, você deverá adicionar uma regra NAT para mapear uma porta externa para a porta do WinRM. Essa etapa não será necessária se você criar a VM com um endereço IP público.
   >
   >

1. Salve o modelo como um arquivo no computador. Atribua o nome de **CreateVMTemplate.json** ao arquivo.
1. Verifique o modelo no seu sistema de controle do código-fonte.
1. Abra um editor de texto e cole o seguinte script nele.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Verifique o script no seu sistema de controle do código-fonte. Atribua a ele um nome como **GetLabVMParams.ps1**.

   Quando você executar esse script no agente como parte da definição da versão e se usar as etapas da tarefa, como *Cópia de arquivo do Azure* ou *PowerShell em computadores de destino*, o script coletará os valores que você precisa para implantar seu aplicativo na VM. Normalmente, você usaria essas tarefas para implantar aplicativos em uma VM do Azure. As tarefas exigem valores como o nome do grupo de recursos da VM, o endereço IP e o FQDN (nome de domínio totalmente qualificado).

## <a name="create-a-release-definition-in-release-management"></a>Criar uma definição da versão no Release Management
Para criar uma definição da versão, faça o seguinte:

1. Na guia **Versões** do hub **Build e Versão**, selecione o botão com sinal de adição (+).
2. Na janela **Criar definição da versão**, selecione o modelo **Vazio** e selecione **Avançar**.
3. Selecione **Escolher Depois** e selecione **Criar** para criar uma nova definição da versão com um ambiente padrão e nenhum artefato vinculado.
4. Para abrir o menu de atalho, na nova definição da versão, selecione as reticências (...) ao lado do nome do ambiente e, em seguida, selecione **Configurar variáveis**. 
5. Na janela **Configurar – ambiente**, para as variáveis usadas nas tarefas de definição da versão, insira os seguintes valores:

   a. Para **vmName**, insira o nome que você atribuiu à VM quando criou o modelo do Resource Manager no Portal do Azure.

   b. Para **userName**, insira o nome de usuário que você atribuiu à VM quando criou o modelo do Resource Manager no Portal do Azure.

   c. Para **senha**, insira a senha que você atribuiu à VM quando criou o modelo do Resource Manager no Portal do Azure. Use o ícone de "cadeado" para ocultar e proteger a senha.

### <a name="create-a-vm"></a>Criar uma máquina virtual

A próxima etapa da implantação é criar a VM para usar como a "imagem dourada" para implantações subsequentes. Você cria a VM em sua instância do Azure DevTest Lab usando a tarefa que foi desenvolvida especialmente para essa finalidade. 

1. Na definição da versão, selecione **Adicionar tarefas**.
2. Na guia **Implantar**, adicione uma tarefa *Criar VM do Azure DevTest Labs*. Configure as tarefas da seguinte forma:

   > [!NOTE]
   > Para criar a VM a ser usada para implantações subsequentes, consulte [Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Para **Nome do Laboratório**, selecione o nome da instância que você criou anteriormente.

   c. Para **Nome do Modelo**, insira o caminho completo e o nome do arquivo de modelo que você salvou em seu repositório de código-fonte. Você pode usar as propriedades internas do Release Management para simplificar o caminho, por exemplo:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Para **Parâmetros de Modelo**, insira os parâmetros para as variáveis que são definidas no modelo. Use os nomes das variáveis que você definiu no ambiente, por exemplo:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Para **Variáveis de Saída – ID da VM do Laboratório**, você precisa da ID da VM recém-criada para as etapas subsequentes. Você define o nome padrão da variável de ambiente que é populada automaticamente com esta ID na seção **Variáveis de Saída**. Você pode editar a variável, se necessário, mas lembre-se de usar o nome correto nas tarefas subsequentes. A ID da VM do Laboratório é escrita no seguinte formato:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Execute o script que você criou anteriormente para coletar os detalhes da VM do DevTest Labs. 
4. Na definição da versão, selecione **Adicionar tarefas** e, na guia **Implantar**, adicione uma tarefa do *Azure PowerShell*. Configure as tarefas da seguinte forma:

   > [!NOTE]
   > Para obter os detalhes da VM do DevTest Labs, consulte [Implantar: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) e execute o script.

   a. Para **Tipo de Conexão do Azure**, selecione o **Azure Resource Manager**.

   b. Para **Assinatura do Azure RM**, selecione uma conexão na lista em **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. Para **Tipo de Script**, selecione **Arquivo de Script**.
 
   d. Para **Caminho do Script**, insira o caminho completo e o nome do script que você salvou em seu repositório de código-fonte. Você pode usar as propriedades internas do Release Management para simplificar o caminho, por exemplo:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Para **Argumentos de Script**, insira o nome da variável de ambiente que foi populada automaticamente com a ID da VM do laboratório pela tarefa anterior, por exemplo: 
      ```
      -labVmId '$(labVMId)'
      ```
    O script coleta os valores necessários e os armazena em variáveis de ambiente na definição da versão para que você possa fazer referência a eles facilmente nas etapas subsequentes.

5. Implante seu aplicativo para a nova VM do DevTest Labs. As tarefas que você normalmente usa para implantar o aplicativo são *Cópia de arquivo do Azure* e *PowerShell em computadores de destino*.
   As informações sobre a VM necessárias para os parâmetros dessas tarefas estão armazenadas em três variáveis de configuração chamadas **labVmRgName**, **labVMIpAddress** e **labVMFqdn** na definição da versão. Se você quiser apenas experimentar com a criação de uma VM do DevTest Labs e uma imagem personalizada, sem implantar um aplicativo nela, poderá ignorar essa etapa.

### <a name="create-an-image"></a>Criar uma imagem

A próxima etapa é criar uma imagem da VM recém-implantada em sua instância do Azure DevTest Labs. Você pode usar a imagem para criar cópias da VM sob demanda sempre que desejar executar uma tarefa de desenvolvimento ou executar alguns testes. 

1. Na definição da versão, selecione **Adicionar tarefas**.
2. Na guia **Implantar**, adicione uma tarefa **Criar Imagem Personalizada do Azure DevTest Labs**. Configure-o da seguinte maneira:

   > [!NOTE]
   > Para criar a imagem, consulte [Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **Assinatura do Azure RM**, na lista **Conexões de Serviço do Azure Disponíveis**, selecione uma conexão na lista ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Para **Nome do Laboratório**, selecione o nome da instância que você criou anteriormente.

   c. Para **Nome da Imagem Personalizada**, insira um nome para a imagem personalizada.

   d. (Opcional) Para **Descrição**, insira uma descrição para que seja fácil selecionar a imagem correta posteriormente.

   e. Para **VM do Laboratório de Origem – ID da VM do Laboratório de Origem**, se você alterou o nome padrão da variável de ambiente que foi populada automaticamente com a ID da VM do Laboratório por uma tarefa anterior, edite-o aqui. O valor padrão é **$(labVMId)**.

   f. Para **Variáveis de saída – ID da Imagem Personalizada**, você precisa da ID da imagem recém-criada quando deseja gerenciá-la ou excluí-la. O nome padrão da variável de ambiente que é populada automaticamente com esta ID é definido na seção **Variáveis de Saída**. Se necessário, você pode editar a variável.

### <a name="delete-the-vm"></a>Excluir a VM

A última etapa é excluir a VM que você implantou em sua instância do Azure DevTest Labs. Normalmente, você excluiria a VM após executar as tarefas de desenvolvimento ou executaria os testes necessários na VM implantada. 

1. Na definição da versão, selecione **Adicionar tarefas** e, na guia **Implantar**, adicione uma tarefa *Excluir VM do Azure DevTest Labs*. Configure-o da seguinte maneira:

      > [!NOTE]
      > Para excluir a VM, consulte [Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. Para **ID da VM do Laboratório de Origem**, se você alterou o nome padrão da variável de ambiente que foi populada automaticamente com a ID da VM do Laboratório por uma tarefa anterior, edite-o aqui. O valor padrão é **$(labVMId)**.

2. Insira um nome para a definição da versão e, em seguida, salve-a.
3. Crie uma nova versão, selecione o build mais recente e implante-o no único ambiente na definição.

Em cada etapa, atualize a exibição da instância do DevTest Labs no Portal do Azure para exibir a VM e a imagem sendo criadas e a VM sendo excluída novamente.

Agora você pode usar a imagem personalizada para criar VMs quando elas forem necessárias.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* Aprenda como [Criar ambientes de várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* Explore mais modelos do Resource Manager de início rápido para a automação do DevTest Labs do [repositório público do GitHub do DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
* Se necessário, consulte a página [Solução de problemas do VSTS](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting).
 

---
title: Tutorial – Criar um pipeline de CI/CD no Azure com o Azure DevOps Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um pipeline do Azure DevOps Services para integração e entrega contínuas que implanta um aplicativo Web do IIS em uma VM do Windows no Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d4edf0d22ce04eb2cb865d80c2b70f1bcc2169df
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301891"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>Tutorial: Criar um pipeline de integração contínua com o Azure DevOps Services e o IIS
Para automatizar as fases de compilação, teste e implantação do desenvolvimento de aplicativos, você pode usar um pipeline de CI/CD (implantação e integração contínuas). Neste tutorial, você cria um pipeline de CI/CD usando o Azure DevOps Services e uma VM (máquina virtual) do Windows no Azure que executa o IIS. Você aprenderá como:

> [!div class="checklist"]
> * Publicar um aplicativo Web ASP.NET em um projeto do Azure DevOps Services
> * Criar um pipeline de build disparado por confirmações de código
> * Instalar e configurar o IIS em uma máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implantação no Azure DevOps Services
> * Criar um pipeline de lançamento para publicar novos pacotes de implantação Web no IIS
> * Testar o pipeline de CI/CD

Este tutorial exige o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-project-in-azure-devops-services"></a>Criar um projeto no Azure DevOps Services
O Azure DevOps Services facilita a colaboração e o desenvolvimento sem manter uma solução de gerenciamento de código local. O Azure DevOps Services oferece teste de código na nuvem, build e Application Insights. Você pode escolher um repositório de controle de versão e o IDE que melhor se adapta a seu desenvolvimento de código. Para este tutorial, você pode usar uma organização gratuita para criar um aplicativo Web ASP.NET básico e o pipeline de CI/CD. Se você ainda não tiver uma organização do Azure DevOps Services, [crie uma](http://go.microsoft.com/fwlink/?LinkId=307137).

Para gerenciar o processo de confirmação do código, os pipelines de build e os pipelines de lançamento, crie um projeto no Azure DevOps Services da seguinte maneira:

1. Abra o painel do Azure DevOps Services em um navegador da Web e escolha **Novo projeto**.
2. Digite *myWebApp* como **Nome do projeto**. Deixe todos os outros valores padrão para usar o controle de versão do *Git* e o processo de item de trabalho *Agile*.
3. Escolha a opção de **Compartilhar com** *Membros da Equipe* e selecione **Criar**.
5. Quando o projeto tiver sido criado, escolha a opção de **Inicializar com um arquivo LEIAME ou gitignore** e **Inicializar**.
6. Dentro de seu novo projeto, escolha **Painéis** na parte superior e selecione **Abrir no Visual Studio**.


## <a name="create-aspnet-web-application"></a>Criar aplicativo Web ASP.NET
Na etapa anterior, você criou um projeto no Azure DevOps Services. A etapa final abre seu novo projeto no Visual Studio. Gerencie suas confirmações de código na janela **Team Explorer**. Crie uma cópia local do novo projeto e crie um aplicativo Web ASP.NET de um modelo da seguinte maneira:

1. Selecione **Clonar** para criar um repositório git local de seu projeto do Azure DevOps Services.
    
    ![Clonar o repositório do projeto do Azure DevOps Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Em **Soluções**, selecione **Novo**.

    ![Criar a solução do aplicativo Web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Selecione modelos **Web** e escolha o modelo **Aplicativo Web ASP.NET**.
    1. Insira um nome para seu aplicativo, como *myWebApp*e desmarque a caixa **Criar diretório para a solução**.
    2. Se a opção estiver disponível, desmarque a caixa para **Adicionar o Application Insights ao projeto**. O Application Insights exige que você autorize seu aplicativo Web com o Azure Application Insights. Para simplificar este tutorial, ignore esse processo.
    3. Selecione **OK**.
4. Escolha **MVC** na lista de modelos.
    1. Selecione **Alterar Autenticação**, escolha **Sem Autenticação** e selecione **OK**.
    2. Selecione **OK** para criar sua solução.
5. Na janela **Team Explorer**, escolha **Alterações**.

    ![Confirmar alterações locais no repositório git do Azure Repos](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Na caixa de texto de confirmação, digite uma mensagem como *Confirmação inicial*. Escolha **Confirmar Tudo e Sincronizar** no menu suspenso.


## <a name="create-build-pipeline"></a>Criar o pipeline de build
No Azure DevOps Services, você pode usar um pipeline de build para descrever como seu aplicativo deve ser criado. Neste tutorial, podemos criar um pipeline básico que usa nosso código-fonte, compila a solução e cria o pacote de implantação na Web que podemos usar para executar o aplicativo Web em um servidor IIS.

1. Em seu projeto do Azure DevOps Services, escolha **Build e Versão** na parte superior e selecione **Builds**.
3. Selecione **+ Nova definição**.
4. Escolha o modelo **ASP.NET (VERSÃO PRÉVIA)** e selecione **Aplicar**.
5. Deixe o padrão de todos os valores de tarefa. Em **Obter fontes**, verifique se o repositório *myWebApp* e a ramificação *mestre* estão selecionados.

    ![Criar pipeline de build no projeto do Azure DevOps Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na guia **Gatilhos**, mova o controle deslizante de **Habilitar esse disparador** para *Habilitado*.
7. Salve o pipeline de build e coloque na fila um novo build selecionando **Salvar e enfileirar** e novamente **Salvar e enfileirar**. Deixe os padrões e selecione **Enfileirar**.

Veja como a build fica agendada em um agente hospedado e começa a ser criada. A saída deverá ser semelhante ao seguinte exemplo:

![Build bem-sucedido do projeto do Azure DevOps Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Criar máquina virtual
Para oferecer uma plataforma de execução do seu aplicativo Web ASP.NET, você precisará de uma máquina virtual Windows que execute o IIS. O Azure DevOps Services usa um agente para interagir com a instância do IIS durante a confirmação do código e os disparos dos builds.

Criar uma VM do Windows Server 2016 com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. O grupo de recursos *myResourceGroupAzureDevOpsServices* e recursos de rede de suporte também são criados. Para permitir o tráfego da web, a porta TCP *80* é aberta para a máquina virtual. Quando solicitado, forneça um nome de usuário e uma senha a serem usados como credenciais de logon para a VM:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Para se conectar à sua VM, obtenha o endereço IP público com [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) da seguinte maneira:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Crie uma sessão de área de trabalho remota para sua VM:

```cmd
mstsc /v:<publicIpAddress>
```

Na VM, abra um prompt de comando do **Administrador do PowerShell**. Instale o IIS e os recursos necessários do .NET da seguinte maneira:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Criar grupo de implantação

Para enviar por push o pacote de implantação Web para o servidor IIS, defina um grupo de implantação no Azure DevOps Services. Esse grupo permite que você especifique quais servidores são o destino dos novos builds conforme você confirma o código para o Azure DevOps Services e os builds são concluídos.

1. No Azure DevOps Services, escolha **Build e Versão** e selecione **Grupos de implantação**.
2. Escolha **Adicionar Grupo de implantação**.
3. Insira um nome para o grupo, como *myIIS* e selecione **Criar**.
4. Na seção **Registrar máquinas**, verifique se *Windows* foi selecionado e marque a caixa **Usar um token de acesso pessoal no script para autenticação**.
5. Selecione **Copiar o script para área de transferência**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Adicionar VM do IIS ao grupo de implantação
Com o grupo de implantação criado, adicione cada instância do IIS ao grupo. O Azure DevOps Services gera um script que baixa e configura um agente na VM para receber os novos pacotes de implantação Web e aplicá-los ao IIS.

1. De volta à sessão **Administrador do PowerShell** na sua VM, cole e execute o script copiado do Azure DevOps Services.
2. Quando solicitado a configurar marcações para o agente, escolha *Y* e digite *Web*.
3. Quando a conta de usuário for solicitada, pressione *Retornar* para aceitar os padrões.
4. Aguarde o script terminar com uma mensagem *Serviço vstsagent.account.computername de serviço iniciado com êxito*.
5. Na página **Grupos de implantação** do menu **Compilação e Versão**, abra o grupo de implantação *myIIS*. Na guia **Máquinas**, verifique se que a sua VM está listada.

    ![VM adicionada com êxito ao grupo de implantação do Azure DevOps Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>Criar pipeline de lançamento
Para publicar seus builds, crie um pipeline de lançamento no Azure DevOps Services. Esse pipeline é disparado automaticamente pela compilação bem-sucedida do aplicativo. Escolha o grupo de implantação para enviar seu pacote de implantação Web e defina as configurações do IIS apropriadas.

1. Escolha **Compilação e Versão** e selecione **Builds**. Escolha o pipeline de build criado na etapa anterior.
2. Em **Concluído recentemente**, escolha a compilação mais recente e selecione **Versão**.
3. Escolha **Sim** para criar um pipeline de lançamento.
4. Escolha o modelo **Vazio** e selecione **Avançar**.
5. Verifique se o pipeline de build do projeto e de origem está preenchido com seu projeto.
6. Marque a caixa de seleção **Implantação contínua** e selecione **Criar**.
7. Selecione a caixa com lista suspensa próxima a **+ Adicionar tarefas** e escolha *Adicionar uma fase do grupo de implantação*.
    
    ![Adicionar tarefa ao pipeline de lançamento no Azure DevOps Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Escolha **Adicionar** ao lado de **Implantação de aplicativo Web IIS (Versão Prévia)** e selecione **Fechar**.
9. Selecione a tarefa pai **Executar no grupo de implantação**.
    1. Em **Grupo de Implantação**, selecione o grupo de implantação que você criou anteriormente, como *myIIS*.
    2. Na caixa **Marcações da máquina**, selecione **Adicionar** e escolha a marcação *Web*.
    
    ![Tarefa de grupo de implantação do pipeline de lançamento para o IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Selecione a tarefa **Implantar: implantar o aplicativo Web IIS** para definir as configurações de instância do IIS da seguinte maneira:
    1. Em **nome do site**, digite *Site Padrão*.
    2. Deixe todas as outras configurações padrão.
12. Escolha **Salvar** e selecione **OK** duas vezes.


## <a name="create-a-release-and-publish"></a>Criar uma versão e publicar
Agora você pode enviar seu pacote de implantação Web como uma nova versão. Esta etapa se comunica com o agente em cada instância que é parte do grupo de implantação, envia ao pacote de implantação Web por push e configura o IIS para executar o aplicativo Web atualizado.

1. Em seu pipeline de lançamento, selecione **+ Versão** e escolha **Criar Versão**.
2. Verifique se a compilação mais recente está selecionada na lista suspensa, juntamente com **Implantação automatizada: após a criação da versão**. Selecione **Criar**.
3. Uma pequena faixa aparece na parte superior do seu pipeline de lançamento, como *A versão 'Versão-1' foi criada*. Selecione o link da versão.
4. Abra a guia **Logs** para observar o andamento da versão.
    
    ![Versão do Azure DevOps Services bem-sucedida e envio do pacote de implantação Web por push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Após a conclusão da versão, abra um navegador da Web e digite o endereço IIP público da sua VM. Seu aplicativo Web ASP.NET está em execução.

    ![Aplicativo Web ASP.NET em execução na VM do IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testar o pipeline de CI/CD inteiro
Com o aplicativo Web em execução no IIS, experimente o pipeline de CI/CD inteiro. Depois de fazer uma alteração no Visual Studio e confirmar seu código, uma compilação será disparada e disparará uma versão do seu pacote de implantação Web atualizado para o IIS:

1. No Visual Studio, abra a janela **Gerenciador de Soluções**.
2. Navegue até e abra *myWebApp | Modos de Exibição | Página Inicial | Index. cshtml*
3. Edite a linha 6 para conter o seguinte:

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. Salve o arquivo.
5. Abra a janela **Team Explorer**, selecione o projeto *myWebApp* e escolha **Alterações**.
6. Insira uma mensagem de confirmação, como *Testando pipeline de CI/CD*, escolha **Confirmar Todos e Sincronizar** no menu suspenso.
7. No workspace do Azure DevOps Services, um novo build é disparado com a confirmação do código. 
    - Escolha **Compilação e Versão** e selecione **Builds**. 
    - Escolha seu pipeline de build e selecione o build **Na fila e em execução** para assistir ao andamento da compilação.
9. Depois que a compilação é bem-sucedida, uma nova versão é disparada.
    - Escolha **Compilação e Versão** e **Versões** para ver o pacote de implantação Web enviado por push para a VM do IIS. 
    - Selecione o ícone **Atualizar** para atualizar o status. Quando a coluna *Ambientes* mostra uma marca de seleção verde, a versão foi implantada com êxito no IIS.
11. Para ver as alterações aplicadas, atualize seu site do IIS em um navegador.

    ![Aplicativo Web ASP.NET em execução na VM do IIS pelo pipeline de CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo Web ASP.NET no Azure DevOps Services e configurou pipelines de build e de lançamento para implantar novos pacotes de implantação Web no IIS em cada confirmação de código. Você aprendeu como:

> [!div class="checklist"]
> * Publicar um aplicativo Web ASP.NET em um projeto do Azure DevOps Services
> * Criar um pipeline de build disparado por confirmações de código
> * Instalar e configurar o IIS em uma máquina virtual no Azure
> * Adicionar a instância do IIS a um grupo de implantação no Azure DevOps Services
> * Criar um pipeline de lançamento para publicar novos pacotes de implantação Web no IIS
> * Testar o pipeline de CI/CD

Avance para o próximo tutorial para aprender a instalar uma pilha do SQL&#92;IIS&#92;.NET em um par de VMs do Windows.

> [!div class="nextstepaction"]
> [Pilha do SQL&#92;IIS&#92;.NET](tutorial-iis-sql.md)

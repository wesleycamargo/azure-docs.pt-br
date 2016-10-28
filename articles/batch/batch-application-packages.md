<properties
	pageTitle="Instalação e gerenciamento fáceis de aplicativos no Lote do Azure | Microsoft Azure"
	description="Use o recurso de pacotes de aplicativos do Lote do Azure para gerenciar facilmente vários aplicativos e versões para instalação nos nós de computação do Lote."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/25/2016"
	ms.author="marsma" />

# Implantação de aplicativo nos pacotes de aplicativos do Lote do Azure

O recurso de pacotes de aplicativos do Lote do Azure fornece um fácil gerenciamento dos aplicativos de tarefa e sua implantação para os nós de computação em seu pool. Com os pacotes de aplicativos, você pode carregar e gerenciar diversas versões dos aplicativos que suas tarefas executam, incluindo seus arquivos de suporte. Você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Neste artigo, você aprenderá como carregar e gerenciar os pacotes de aplicativos usando o portal do Azure. Em seguida, aprenderá como instalá-los nos nós de computação de um pool com a biblioteca [.NET do Lote][api_net].

> [AZURE.NOTE] O recurso de pacotes de aplicativos descrito aqui substitui o recurso "Aplicativos do Lote", disponível nas versões anteriores do serviço.

## Requisitos do pacote de aplicativos

Você deve [vincular uma conta de Armazenamento do Azure](#link-a-storage-account) à sua conta do Lote para usar os pacotes de aplicativos.

O recurso de pacotes de aplicativos abordado neste artigo é compatível *somente* com os pools do Lote criados depois de 10 de março de 2016. Os pacotes de aplicativos não serão implantados nos nós de computação em pools criados antes dessa data.

Esse recurso foi introduzido na [API REST do Lote][api_rest] versão 2015-12-01.2.2 e na biblioteca [.NET do Lote][api_net] correspondente, versão 3.1.0. É recomendável sempre usar a versão da API mais recente ao trabalhar com o Lote.

> [AZURE.IMPORTANT] Atualmente, apenas os pools *CloudServiceConfiguration* dão suporte aos pacotes de aplicativos. Você pode usar pacotes de aplicativos em pools criados usando imagens VirtualMachineConfiguration. Consulte a seção [Configuração da máquina virtual](batch-linux-nodes.md#virtual-machine-configuration) de [Provisionar nós de computação do Linux nos pools do Lote do Azure](batch-linux-nodes.md) para obter mais informações sobre as duas configurações diferentes.

## Sobre aplicativos e pacotes de aplicativos

No Lote do Azure, um *aplicativo* refere-se a um conjunto de binários com versão que podem ser baixados automaticamente para os nós de computação no pool. Um *pacote de aplicativos* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicativos][1]

### Aplicativos

Um aplicativo no Lote contém um ou mais pacotes de aplicativos e especifica as opções de configuração para o aplicativo. Por exemplo, um aplicativo pode especificar a versão do pacote de aplicativos padrão para instalar nos nós de computação e se seus pacotes podem ser atualizados ou excluídos.

### Pacotes de aplicativos

Um pacote de aplicativos é um arquivo .zip contendo os binários de aplicativo e arquivos de suporte exigidos pelas suas tarefas para execução. Cada pacote de aplicativos representa uma versão específica do aplicativo.

Você pode especificar os pacotes de aplicativos no nível do pool e de tarefa. Você pode especificar um ou mais desses pacotes e (opcionalmente) uma versão quando você cria uma tarefa ou um pool.

* Os **Pacotes de aplicativos do pool** são implantados em *cada* nó no pool. Os aplicativos são implantados quando um nó ingressa em um pool e quando ele é reinicializado ou tem a imagem recriada.

    Os pacotes de aplicativos do pool são adequados quando todos os nós em um pool executam as tarefas de um trabalho. Você pode especificar um ou mais pacotes de aplicativos quando cria um pool e pode adicionar ou atualizar os pacotes de um pool existente. Se você atualizar os pacotes de aplicativos de um pool existente, deverá reiniciar os nós para instalar o novo pacote.

* Os **pacotes de aplicativos de tarefa** são implantados somente em um nó de computação programado para executar uma tarefa, logo antes de executar a linha de comando da tarefa. Se o pacote de aplicativos especificado e a versão já estiverem no nó, ele não será reimplantado e o pacote existente será usado.

    Os pacotes de aplicativos de tarefa são úteis nos ambientes de pool compartilhado, onde diferentes trabalhos são executados em um pool, e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que os nós no pool, pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, pois o aplicativo é implantado apenas para os nós que executam tarefas.

    Outros cenários que podem aproveitar os pacotes de aplicativos de tarefa são os trabalhos que usam um aplicativo muito grande, mas para um pequeno número de tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de mesclagem, onde o aplicativo pré-processamento ou mesclagem é pesado.

> [AZURE.IMPORTANT] Há restrições quanto ao número de aplicativos e pacotes de aplicativos em uma conta do Lote, bem como tamanho máximo para o pacote de aplicativos. Consulte [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre esses limites.

### Benefícios dos pacotes de aplicativos

Os pacotes de aplicativos podem simplificar o código em sua solução de Lote e reduzir a sobrecarga exigida para gerenciar os aplicativos que suas tarefas executam.

A tarefa de inicialização do pool não precisa especificar uma longa lista de arquivos de recursos individuais para instalar nos nós. Não é preciso gerenciar manualmente diversas versões dos arquivos do aplicativo no Armazenamento do Azure nem em seus nós. E você não precisa preocupar-se com a geração de [URLs SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos arquivos em sua conta de Armazenamento. O Lote funciona em segundo plano com o Armazenamento do Azure para armazenar os pacotes de aplicativos e implantá-los nos nós de computação.

## Carregar e gerenciar aplicativos

Você pode usar o [Portal do Azure][portal] ou a biblioteca [.NET de Gerenciamento do Lote](batch-management-dotnet.md) para gerenciar os pacotes de aplicativos em sua conta do Lote. Nas próximas seções, primeiro iremos vincular uma conta de Armazenamento e analisar como adicionar aplicativos e pacotes, e como gerenciá-los com o portal.

### Vincular uma conta de armazenamento

Para usar pacotes de aplicativos, em primeiro lugar, você deve vincular uma conta de armazenamento do Azure à sua conta do Lote. Se você ainda não configurou uma conta de Armazenamento para sua conta do Lote, o portal do Azure exibirá um aviso na primeira vez em que clicar no bloco **Aplicativos** na folha **Conta do Lote**.

> [AZURE.IMPORTANT] No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **Finalidade geral**, conforme descrito na etapa 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Ao vincular uma conta de Armazenamento do Azure à sua conta do Lote, você vincula *somente* uma conta de armazenamento de **Finalidade geral**.

![Aviso de nenhuma conta de armazenamento configurada no portal do Azure][9]

O serviço do Lote usa a conta de armazenamento associada para o armazenamento e a recuperação dos pacotes de aplicativos. Depois que você tiver vinculado as duas contas, o Lote poderá implantar automaticamente os pacotes armazenados na conta de armazenamento vinculada nos nós de computação. Clique em **Configurações da conta de armazenamento** na folha **Aviso** e clique em **Conta de Armazenamento** na folha **Conta de Armazenamento** para vincular uma conta de armazenamento à sua conta do Lote.

![Folha Escolher conta de armazenamento no portal do Azure][10]

Recomendamos que você crie uma conta de armazenamento para usar *especificamente* com sua conta do Lote e que a selecione aqui. Para obter detalhes sobre como criar uma conta de armazenamento, consulte "Criar uma conta de armazenamento" em [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Depois de ter criado uma conta de Armazenamento, você poderá vinculá-la à sua conta do Lote usando a folha **Conta de Armazenamento**.

> [AZURE.WARNING] Como o Lote usa o Armazenamento do Azure para armazenar seus pacotes de aplicativos, você será [cobrado normalmente][storage_pricing] pelos dados do blob de blocos. Não se esqueça de considerar o tamanho e o número de pacotes de aplicativos e, periodicamente, remova pacotes preteridos para minimizar o custo.

### Exibir aplicativos atuais

Para exibir os aplicativos em sua conta do Lote, clique no bloco **Aplicativos** na folha **Conta do Lote**.

![Bloco Aplicativos][2]

Isso abre a folha **Aplicativos**:

![Listar aplicativos][3]

A folha **Aplicativos** exibe a ID de cada aplicativo em sua conta e as seguintes propriedades:

* **Pacotes** – o número de versões associadas a este aplicativo.
* **Versão padrão** – a versão que será instalada se você não especificar uma versão ao definir o aplicativo para um pool. Essa configuração é opcional.
* **Permitir atualizações**– o valor que especifica se são permitidas as atualizações, exclusões e adições do pacote. Se isso estiver definido para **Não**, as exclusões e atualizações do pacote ficarão desabilitadas para o aplicativo. Apenas novas versões do pacote de aplicativos poderão ser adicionadas. O padrão é **Sim**.

### Exibir detalhes do aplicativo

Clique em um aplicativo na folha **Aplicativos** para abrir a folha que inclui os detalhes desse aplicativo.

![Detalhes do aplicativo][4]

Na folha de detalhes do aplicativo, você pode configurar as definições a seguir para o aplicativo.

* **Permitir atualizações** – especifique se seus pacotes de aplicativos podem ser atualizados ou excluídos. Consulte "Atualizar ou excluir um pacote de aplicativos" mais adiante neste artigo.
* **Versão padrão** -- especifique um pacote de aplicativos padrão para implantar nos nós de computação.
* **Nome de exibição** -- especifique um nome "amigável" que sua solução de Lote pode usar ao exibir informações sobre o aplicativo, como na IU de um serviço que você fornece aos clientes por meio do Lote.

### Adicionar um novo aplicativo

Para criar um novo aplicativo, adicione um pacote de aplicativos e especifique uma ID de aplicativo nova e exclusiva. O primeiro pacote de aplicativos que você adiciona com a nova ID de aplicativo também criará o novo aplicativo.

Clique em **Adicionar** na folha **Aplicativos** para abrir a folha **Novo aplicativo**.

![Folha Novo aplicativo no portal do Azure][5]

A folha **Novo aplicativo** fornece os campos a seguir para especificar as configurações do seu novo aplicativo e do pacote de aplicativos.

**ID do aplicativo**

Especifica a ID do novo aplicativo, que está sujeita às regras de validação padrão de ID do Lote do Azure:

* Pode conter qualquer combinação de caracteres alfanuméricos, incluindo hifens e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusiva na conta do Lote.
* Com preservação de letras maiúsculas e sem diferenciação entre maiúsculas e minúsculas.

**Versão**

Especifica a versão do pacote de aplicativos que você está carregando. As cadeias de caracteres da versão estão sujeitas às seguintes regras de validação:

* Podem conter qualquer combinação de caracteres alfanuméricos, incluindo hifens, sublinhados e pontos.
* Não podem conter mais de 64 caracteres.
* Devem ser exclusivas no aplicativo.
* Preservação de letras maiúsculas e sem diferenciação entre maiúsculas e minúsculas.

**Pacote de aplicativos**

Esse campo especifica o arquivo .zip que contém os binários do aplicativo e os arquivos de suporte que necessários à execução do aplicativo. Clique na caixa **Selecionar um arquivo** ou no ícone de pasta para procurar e selecionar um arquivo .zip que contém os arquivos do seu aplicativo.

Depois de ter selecionado um arquivo, clique em **OK** para começar a carregar no Armazenamento do Azure. Quando a operação de carregamento for concluída, você será notificado e a folha será fechada. Dependendo do tamanho do arquivo que você estiver carregando e da velocidade da conexão de rede, essa operação pode demorar um pouco.

> [AZURE.WARNING] Não feche a folha **Novo aplicativo** antes de a operação de carregamento estar concluída. Isso interromperá o processo de carregamento.

### Adicionar um novo pacote de aplicativos

Para adicionar uma nova versão do pacote de aplicativos a um aplicativo existente, selecione um aplicativo na folha **Aplicativos**, clique em **Pacotes** e **Adicionar** para abrir a folha **Adicionar pacote**.

![Folha Adicionar pacote de aplicativos no portal do Azure][8]

Como você pode ver, os campos correspondem aos da folha **Novo aplicativo**, mas a caixa **ID do Aplicativo** fica desabilitada. Assim como para o novo aplicativo, especifique a **Versão** do novo pacote, procure o arquivo .zip **Pacote de aplicativos** e clique em **OK** para carregar o pacote.

### Atualizar ou excluir um pacote de aplicativos

Para atualizar ou excluir um pacote de aplicativos existente, abra a folha de detalhes do aplicativo, clique em **Pacotes** para abrir a folha **Pacotes**, clique nas **reticências** na linha do pacote de aplicativos que você deseja modificar e selecione a ação que deseja executar.

![Atualizar ou excluir pacote no portal do Azure][7]

**Atualização**

Quando você clica em **Atualizar**, a folha *Atualizar pacote* é exibida. Essa folha é semelhante à folha *Novo pacote de aplicativos*. No entanto, somente o campo de seleção de pacotes está habilitado, permitindo que você especifique um novo arquivo ZIP a carregar.

![Folha Atualizar pacote no portal do Azure][11]

**Excluir**

Quando você clica em **Excluir**, é preciso confirmar a exclusão da versão do pacote e o Lote exclui o pacote do Armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração da **Versão padrão** será removida para o aplicativo.

![Excluir aplicativo][12]

## Instalar aplicativos em nós de computação

Agora que você viu como gerenciar os pacotes de aplicativos com o portal do Azure, podemos analisar como implantá-los para os nós de computação e executá-los com tarefas em Lote.

### Instalar pacotes de aplicativos do pool

Para instalar um pacote de aplicativos em todos os nós de computação em um pool, especifique uma ou mais *referências* do pacote de aplicativos para o pool. Os pacotes de aplicativo que você especifica para um pool são instalados em cada nó de computação quando esse nó se une ao pool, e quando o nó é reinicializado ou tem sua imagem refeita.

No .NET do Lote, especifique um ou mais [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] quando você criar um novo pool ou para um pool existente. A classe [ApplicationPackageReference][net_pkgref] especifica uma ID e versão do aplicativo para instalar nos nós de computação de um pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Se uma implantação do pacote de aplicativos falhar por algum motivo, o serviço de Lote marcará o nó como [inutilizável][net_nodestate] e nenhuma tarefa será agendada para a execução nesse nó. Nesse caso, você deve **reiniciar** o nó para reiniciar a implantação do pacote. A reinicialização do nó também habilitará a agendamento de tarefas novamente nele.

### Instalar pacotes de aplicativos de tarefa

Semelhante a um pool, você especifica as *referências* do pacote de aplicativos para uma tarefa. Quando uma tarefa está agendada para ser executada em um nó, o pacote é baixado e extraído um pouco antes da linha de comando da tarefa ser executada. Se o pacote especificado e a versão já estiverem instalados no nó, o pacote não será baixado e o pacote existente será usado.

Para instalar um pacote de aplicativos de tarefa, configure a propriedade [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] da tarefa:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## Executar os aplicativos instalados

Os pacotes que você especificou para uma tarefa ou um pool são baixados e extraídos para um diretório nomeado dentro do `AZ_BATCH_ROOT_DIR` do nó. O Lote também cria uma variável de ambiente que contém o caminho para o diretório nomeado. As linhas de comando da tarefa usam essa variável de ambiente ao referenciar o aplicativo no nó. A variável está no seguinte formato:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID` e `version` são os valores que correspondem à versão do aplicativo e do pacote que você especificou para a implantação. Por exemplo, se você especificou que a versão 2.7 do *blender* de aplicativos deve ser instalada, as linhas de comando da tarefa usarão essa variável de ambiente para acessar seus arquivos:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se você especificar uma versão padrão para um aplicativo, poderá omitir o sufixo da versão. Por exemplo, se você definiu "2.7" como a versão padrão do *blender* de aplicativos, suas tarefas poderão referenciar a variável de ambiente a seguir e elas executarão a versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O trecho de código a seguir mostra uma linha de comando da tarefa de exemplo que inicializa a versão padrão do *blender* de aplicativos:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consulte [Configurações do ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) na [Visão geral do recurso de Lote](batch-api-basics.md) para obter mais informações sobre as configurações do ambiente do nó de computação.

## Atualizar pacotes de aplicativos de um pool

Se um pool existente já tiver sido configurado com um pacote de aplicativos, você poderá especificar um novo pacote para o pool. Se você especificar uma nova referência de pacote para um pool, o seguinte se aplicará:

* Todos os novos nós que ingressam no pool, e os nós existentes que são reinicializados ou cujas imagens são refeitas, instalarão o pacote recentemente especificado.
* Os nós de computação que já estão no pool quando você atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicativos. Esses nós de computação devem ser reinicializados ou ter sua imagem recriada para receber o novo pacote.
* Quando um novo pacote é implantado, as variáveis de ambiente criadas refletem as novas referências do pacote de aplicativos.

Neste exemplo, o pool existente tem a versão 2.7 do aplicativo *blender* configurada como um de seus [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76b, especifique um novo [ApplicationPackageReference][net_pkgref] com a nova versão e confirme a mudança.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão foi configurada, qualquer nó *novo* que ingresse no pool terá a versão 2.76b implantada nele. Para instalar a versão 2.76b nos nós que já *estão* no pool, reinicialize-os ou refaça a imagem deles. Observe que nós reinicializados reterão os arquivos das implantações anteriores do pacote.

## Listar os aplicativos em uma conta do Lote

Você pode listar os aplicativos e seus pacotes em uma conta do Lote usando o método [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries].

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## Conclusão

Com os pacotes de aplicativos, você pode fornecer ajudar seus clientes a escolher os aplicativos para seus trabalhos e especificar a versão exata a ser usada ao processar trabalhos com o serviço habilitado para o Lote. Você também pode fornecer aos clientes a capacidade de carregar e rastrear os próprios aplicativos no serviço.

## Próximas etapas

* A [API REST do Lote][api_rest] também dá suporte para trabalhar com os pacotes de aplicativos. Por exemplo, consulte o elemento [applicationPackageReferences][rest_add_pool_with_packages] em [Adicionar um pool a uma conta][rest_add_pool] para obter informações sobre como especificar os pacotes a instalar usando a API REST. Consulte [Aplicativos][rest_applications] para obter detalhes sobre como obter as informações do aplicativo usando a API REST do Lote.

* Aprenda a [gerenciar de modo programático as contas e as cotas do Lote do Azure com o .NET de Gerenciamento do Lote](batch-management-dotnet.md). A biblioteca [.NET de Gerenciamento do Lote][api_net_mgmt] pode permitir os recursos de criação e exclusão de conta para seu aplicativo ou serviço do Lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicativos"
[2]: ./media/batch-application-packages/app_pkg_02.png "Bloco Aplicativos no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Folha Aplicativos no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Folha Detalhes do aplicativo no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Folha Novo aplicativo no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Menu suspenso Atualizar ou excluir pacotes no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Folha Novo pacote de aplicativos no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento vinculado"
[10]: ./media/batch-application-packages/app_pkg_10.png "Folha Escolher conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Folha Atualizar pacote no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Caixa de diálogo de confirmação Excluir pacote no portal do Azure"

<!---HONumber=AcomDC_0831_2016-->
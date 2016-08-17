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
	ms.date="06/30/2016"
	ms.author="marsma" />

# Implantação de aplicativo nos pacotes de aplicativos do Lote do Azure

O recurso de pacotes de aplicativos do Lote do Azure fornece gerenciamento e implantação fáceis de aplicativos nos nós de computação em seu pool. Com os pacotes de aplicativos, você pode carregar e gerenciar facilmente diversas versões dos aplicativos, incluindo binários e arquivos de suporte executados por suas tarefas. Você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Neste artigo, você aprenderá como carregar e gerenciar pacotes de aplicativos usando o portal do Azure. Em seguida, você aprenderá como instalá-los em nós de computação do pool usando a biblioteca [.NET do Lote][api_net].

> [AZURE.NOTE] O recurso de pacotes de aplicativos descrito aqui substitui o recurso "Aplicativos do Lote", disponível nas versões anteriores do serviço.

## Requisitos do pacote de aplicativos

O recurso de pacotes de aplicativos abordado neste artigo é compatível *somente* com os pools do Lote que foram criados depois de 10 de março de 2016. Os pacotes de aplicativos não serão implantados nos nós de computação em pools criados antes dessa data.

Esse recurso foi introduzido na [API REST do Lote][api_rest] versão 2015-12-01.2.2 e na biblioteca [.NET do Lote][api_net] correspondente, versão 3.1.0. É recomendável sempre usar a versão da API mais recente ao trabalhar com o Lote.

> [AZURE.IMPORTANT] Atualmente, apenas os pools *CloudServiceConfiguration* dão suporte a pacotes de aplicativos. Você pode usar pacotes de aplicativos em pools criados usando imagens VirtualMachineConfiguration. Confira a seção [Configuração de máquina virtual](batch-linux-nodes.md#virtual-machine-configuration) de [Provisionar nós de computação do Linux em pools do Lote do Azure](batch-linux-nodes.md) para saber mais sobre as duas configurações diferentes.

## Sobre aplicativos e pacotes de aplicativos

No Lote do Azure, um *aplicativo* refere-se a um conjunto de binários com versão que podem ser baixados automaticamente para os nós de computação do pool. Um *pacote de aplicativos* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicativos][1]

### Aplicativos

Um aplicativo no Lote contém um ou mais pacotes de aplicativos e especifica as opções de configuração para o aplicativo. Por exemplo, um aplicativo especifica a versão do pacote de aplicativos padrão para instalar em nós de computação e se seus pacotes podem ser atualizados ou excluídos.

### Pacotes de aplicativos

Um pacote de aplicativos é um arquivo .zip contendo os binários de aplicativo e arquivos de suporte exigidos pelas suas tarefas para execução. Cada pacote de aplicativos representa uma versão específica do aplicativo. Quando você cria um pool no serviço Lote, pode especificar um ou mais desses aplicativos e (opcionalmente) uma versão. Esses pacotes de aplicativos serão baixados automaticamente e extraídos em cada nó conforme ele ingressa no pool.

> [AZURE.IMPORTANT] Há restrições quanto ao número de aplicativos e pacotes de aplicativos em uma conta do Lote, bem como tamanho máximo para o pacote de aplicativos. Confira [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre esses limites.

### Benefícios dos pacotes de aplicativos

Os pacotes de aplicativos podem simplificar o código na sua solução de Lote, bem como reduzir a sobrecarga exigida no gerenciamento dos aplicativos executados pelas suas tarefas.

Com os pacotes de aplicativos, a tarefa inicial do pool não precisa especificar uma longa lista de arquivos de recursos individuais a serem instalados nos nós. Não é preciso gerenciar manualmente várias versões desses arquivos no Armazenamento do Azure nem em seus nós. E você não precisa se preocupar com a geração de [URLs SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos arquivos na sua conta de armazenamento do Azure.

O Lote funciona em segundo plano com o Armazenamento do Azure para armazenar e implantar pacotes de aplicativos em nós de computação para simplificar o código e a sobrecarga de gerenciamento de computadores.

## Carregar e gerenciar aplicativos

No portal do Azure, você pode adicionar, atualizar e excluir pacotes de aplicativos. Você pode configurar versões padrão para cada aplicativo.

Nas próximas seções, explicaremos primeiramente como associar uma conta de Armazenamento à sua conta do Lote e como analisar os recursos de gerenciamento de pacotes disponíveis no portal do Azure. Depois disso, você aprenderá a implantar esses pacotes nos nós de computação usando a biblioteca [.NET do Lote][api_net].

### Vincular uma conta de armazenamento

Para usar pacotes de aplicativos, em primeiro lugar, você deve vincular uma conta de armazenamento do Azure à sua conta do Lote. Se ainda não tiver configurado uma conta de armazenamento para sua conta do Lote, o portal do Azure exibirá um aviso na primeira vez que você clicar no bloco **Aplicativos** na folha **Conta do Lote**.

> [AZURE.IMPORTANT] No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **Finalidade geral**, como descrito na etapa 5, [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Ao vincular uma conta do Armazenamento do Azure à sua conta do Lote, você vincula *somente* uma conta de armazenamento de **Finalidade geral**.

![Aviso de nenhuma conta de armazenamento configurada no portal do Azure][9]

O serviço do Lote usa a conta de armazenamento associada para o armazenamento e a recuperação dos pacotes de aplicativos. Depois que você tiver vinculado as duas contas, o Lote poderá implantar automaticamente os pacotes armazenados na conta de armazenamento vinculada nos nós de computação. Clique em **Configurações da conta de armazenamento** na folha **Aviso** e clique em **Conta de Armazenamento** na folha **Conta de Armazenamento** para vincular uma conta de armazenamento à sua conta do Lote.

![Folha Escolher conta de armazenamento no portal do Azure][10]

Recomendamos que você crie uma conta de armazenamento para uso *específico* com sua conta do Lote e que a selecione aqui. Para obter detalhes sobre como criar uma conta de armazenamento, confira "Criar uma conta de armazenamento" em [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Depois de criar uma conta de Armazenamento, você poderá vinculá-la à sua conta do Lote usando a folha **Conta de Armazenamento**.

> [AZURE.WARNING] Como o Lote usa o Armazenamento do Azure para armazenar os seus pacotes de aplicativos, você será [cobrado normalmente][storage_pricing] pelos dados do blob de blocos. Não se esqueça de considerar o tamanho e o número de pacotes de aplicativos e, periodicamente, remova pacotes preteridos para minimizar o custo.

### Exibir aplicativos atuais

Para exibir os aplicativos em sua conta do Lote, clique no bloco **Aplicativos** na folha **Conta do Lote**.

![Bloco Aplicativos][2]

Isso abre a folha **Aplicativos**:

![Listar aplicativos][3]

A folha **Aplicativos** exibe a ID de cada aplicativo na sua conta e as seguintes propriedades:

* **Pacotes** – o número de versões associadas a este aplicativo.
* **Versão padrão** – a versão que será instalada se você não especificar uma versão ao definir o aplicativo para um pool. Essa configuração é opcional.
* **Permitir atualizações**– o valor que especifica se são permitidos pacotes de atualizações, exclusões e adições. Se isso estiver definido como **Não**, as exclusões e atualizações de pacote ficarão desabilitadas para o aplicativo. Apenas novas versões do pacote de aplicativos poderão ser adicionadas. O padrão é **Sim**.

### Exibir detalhes do aplicativo

Clique em um aplicativo na folha **Aplicativos** para abrir a folha que inclui os detalhes para o aplicativo.

![Detalhes do aplicativo][4]

Na folha de detalhes do aplicativo, você pode configurar as definições a seguir para o aplicativo.

* **Permitir atualizações**– especifique se os seus pacotes de aplicativos podem ser atualizados ou excluídos. Consulte "Atualizar ou excluir um pacote de aplicativos" mais adiante neste artigo.
* **Versão padrão** - especifique um pacote de aplicativos padrão para implantar nos nós de computação.
* **Nome de exibição** - especifique um nome "amigável" que sua solução de Lote pode usar ao exibir informações sobre o aplicativo, como a interface de usuário de um serviço que você fornece aos seus clientes por meio do Lote.

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

Depois que você tiver selecionado um arquivo, clique em **OK** para começar a carregar para o Armazenamento do Azure. Quando a operação de carregamento for concluída, você será notificado e a folha será fechada. Dependendo do tamanho do arquivo que você estiver carregando e da velocidade da conexão de rede, essa operação pode demorar um pouco.

> [AZURE.WARNING] Não feche a folha **Novo aplicativo** antes de a operação de carregamento estar concluída. Isso interromperá o processo de carregamento.

### Adicionar um novo pacote de aplicativos

Para adicionar uma nova versão do pacote de aplicativos a um aplicativo existente, escolha um aplicativo na folha **Aplicativos**, clique em **Pacotes** e em **Adicionar** para abrir a folha **Adicionar pacote**.

![Folha Adicionar pacote de aplicativos no portal do Azure][8]

Como você pode ver, os campos correspondem aos da folha **Novo aplicativo**, mas a caixa **ID do Aplicativo** fica desabilitada. Assim como para o novo aplicativo, especifique a **Versão** do novo pacote, procure o arquivo .zip **Pacote de aplicativos** e clique em **OK** para carregar o pacote.

### Atualizar ou excluir um pacote de aplicativos

Para atualizar ou excluir um pacote de aplicativos existente, abra a folha de detalhes do aplicativo, clique em **Pacotes** para abrir a folha **Pacotes**, clique nas **reticências** na linha do pacote de aplicativos que deseja modificar e escolha a ação que deseja executar.

![Atualizar ou excluir pacote no portal do Azure][7]

**Atualização**

Quando você clica em **Atualizar**, a folha *Atualizar pacote* é exibida. Essa folha é semelhante à folha *Novo pacote de aplicativos*. No entanto, somente o campo de seleção de pacotes está habilitado, permitindo que você especifique um novo arquivo ZIP a ser carregado.

![Folha Atualizar pacote no portal do Azure][11]

**Excluir**

Quando você clica em **Excluir**, é preciso confirmar a exclusão da versão do pacote, e o Lote exclui o pacote do Armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração da **Versão padrão** será removida para o aplicativo.

![Excluir aplicativo][12]

## Instalar aplicativos em nós de computação

Agora que já explicamos como carregar e gerenciar pacotes de aplicativos usando o portal do Azure, estamos prontos para discutir como realmente implantá-los nos nós de computação e executá-los usando as tarefas no Lote.

Para instalar um pacote de aplicativos nos nós de computação em um pool, você especifica uma ou mais *referências* do pacote de aplicativos para o pool. No .NET do Lote, adicione um ou mais .[ApplicationPackageReferences][net_cloudpool_pkgref] do [CloudPool][net_cloudpool] para um novo pool criado por você ou para um pool existente.

A classe [ApplicationPackageReference][net_pkgref] especifica uma ID e versão do aplicativo para instalação em nós de computação de um pool.

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

Os pacotes de aplicativo que você especifica para um pool são instalados em cada nó de computação quando esse nó se une ao pool, e quando o nó é reinicializado ou tem sua imagem refeita. Se uma implantação de pacote de aplicativos falhar por algum motivo, o serviço de Lote marcará o nó como [inutilizável][net_nodestate] e nenhuma tarefa será agendada para execução nesse nó. Nesse caso, você deve **reiniciar** o nó para reiniciar a implantação do pacote. A reinicialização do nó também habilitará a agendamento de tarefas novamente nele.

## Executar os aplicativos instalados

À medida que cada nó de computação ingressa em um pool, é reiniciado ou tem a imagem refeita, os pacotes que você especificou são baixados e extraídos para um diretório nomeado no `AZ_BATCH_ROOT_DIR` do nó. O Lote também cria uma variável de ambiente para ser usado pelas linhas de comando da tarefa ao chamar os binários do aplicativo. Essa variável cumpre o esquema de nomenclatura abaixo:

`AZ_BATCH_APP_PACKAGE_appid#version`

Por exemplo, se você especificar que a versão 2.7 do aplicativo *mesclador* será instalada, suas tarefas poderão acessar seus binários fazendo referência à seguinte variável de ambiente nas respectivas linhas de comando:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se o aplicativo especificar uma versão padrão, você poderá fazer referência à variável de ambiente sem o sufixo da cadeia de caracteres da versão. Por exemplo, se você tiver especificado a versão 2.7 padrão para o aplicativo *mesclador* no portal do Azure, suas tarefas poderão fazer referência à seguinte variável de ambiente:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O trecho de código a seguir mostra como uma tarefa pode ser configurada quando uma versão padrão foi especificada para o aplicativo *mesclador*.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Confira "Configurações de ambiente para tarefas" em [Visão geral dos recursos do Lote](batch-api-basics.md) para obter mais informações sobre configurações do ambiente do nó de computação.

## Atualizar pacotes de aplicativos de um pool

Se um pool existente já tiver sido configurado com um pacote de aplicativos, você poderá especificar um novo pacote para o pool. Se você especificar uma nova referência de pacote para um pool, o seguinte se aplicará:

* Todos os novos nós que ingressam no pool, e os nós existentes que são reinicializados ou cujas imagens são refeitas, instalarão o pacote recentemente especificado.
* Os nós de computação que já estão no pool quando você atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicativos. Esses nós de computação devem ser reinicializados ou ter sua imagem recriada para receber o novo pacote.
* Quando um novo pacote é implantado, as variáveis de ambiente criadas refletem as novas referências do pacote de aplicativos.

Neste exemplo, o pool existente tem a versão 2.7 do aplicativo *mesclador* configurada como um de seus [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76b, especifique um novo [ApplicationPackageReference][net_pkgref] com a nova versão e confirme a mudança.

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

* A [API REST do Lote][api_rest] também dá suporte para trabalhar com pacotes de aplicativos. Por exemplo, confira o elemento [applicationPackageReferences][rest_add_pool_with_packages] em [Adicionar um pool a uma conta][rest_add_pool] para saber mais sobre como especificar os pacotes a serem instalados usando a API REST. Confira [Aplicativos][rest_applications] para obter detalhes sobre como obter as informações do aplicativo usando a API REST do Lote.

* Aprenda a [gerenciar de modo programático as contas e cotas do Lote do Azure com o .NET do Gerenciamento do Lote](batch-management-dotnet.md). A biblioteca [.NET do Gerenciamento do Lote][api_net_mgmt] pode permitir os recursos de criação e exclusão de conta para seu aplicativo ou serviço do Lote.

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
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
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

<!---HONumber=AcomDC_0803_2016-->
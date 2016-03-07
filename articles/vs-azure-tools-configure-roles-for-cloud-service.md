<properties
   pageTitle="Configure as funções para um serviço de nuvem do Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como instalar e configurar funções para serviços de nuvem do Azure usando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/24/2016"
   ms.author="tarcher" />

# Configurar as funções para um Serviço de Nuvem do Azure com o Visual Studio

Um serviço de nuvem do Azure pode ter uma ou mais funções web ou de trabalho. Para cada função, você precisa definir como essa função é configurada e configurar como ela é executada. Para saber mais sobre as funções em serviços de nuvem, assista ao vídeo [Introdução aos Serviços de Nuvem do Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). As informações sobre seu serviço de nuvem são armazenadas nos seguintes arquivos:

- **ServiceDefinition.csdef**

    O arquivo de definição de serviço define as configurações de tempo de execução de seu serviço de nuvem, incluindo as funções que são necessárias, os pontos de extremidade e o tamanho da máquina virtual. Nenhum dos dados armazenados nesse arquivo podem ser alterados quando a função está sendo executada.

- **ServiceConfiguration.cscfg**

    O arquivo de configuração de serviço define quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. Os dados armazenados nesse arquivo podem ser alterados enquanto a função está sendo executada.

Para armazenar valores diferentes para essas configurações com relação a como sua função é executada, você pode ter várias configurações de serviço. Você pode usar uma configuração de serviço diferente para cada ambiente de implantação. Por exemplo, você pode definir sua cadeia de conexão de conta de armazenamento para usar o emulador de armazenamento local do Azure em uma configuração de serviço local e criar outra configuração de serviço para usar o armazenamento do Azure na nuvem.

Quando você cria um novo serviço de nuvem do Azure no Visual Studio, duas configurações de serviço são criadas por padrão. Essas configurações são adicionadas ao seu projeto do Azure. As configurações são nomeadas:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Configurar um serviço de nuvem do Azure

Você pode configurar um serviço de nuvem do Azure por meio do Gerenciador de Soluções no Visual Studio, conforme mostrado na ilustração a seguir.

![Configurar serviço de nuvem](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### Para configurar um serviço de nuvem do Azure

1. Para configurar cada função em seu projeto do Azure do **Gerenciador de Soluções**, abra o menu de atalho para a função no projeto do Azure e selecione **Propriedades**.

    Uma página com o nome da função é exibida no editor do Visual Studio. A página exibe os campos para a guia **Configuração**.

1. Na lista **Configuração do Serviço**, escolha o nome da configuração do serviço que você deseja editar.

    Se quiser fazer alterações em todas as configurações de serviço para essa função, você poderá escolher **Todas as Configurações**.

    >[AZURE.IMPORTANT] Se você escolher uma configuração de serviço específica, algumas propriedades ficarão desabilitadas porque elas só podem ser definidas para todas as configurações. Para editar essas propriedades, você precisa escolher Todas as Configurações.

    Dessa forma, você pode escolher uma guia para atualizar qualquer propriedade habilitada nessa exibição.

## Alterar o número de instâncias de função

Para melhorar o desempenho do serviço de nuvem, você pode alterar o número de instâncias de uma função em execução, com base no número de usuários ou na carga esperada para uma função específica. Uma máquina virtual separada é criada para cada instância de uma função quando o serviço de nuvem é executado no Azure. Isso afetará a cobrança pela implantação desse serviço de nuvem. Para obter mais informações sobre a fatura, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

### Para alterar o número de instâncias de uma função

1. Escolha a guia **Configuração**.

1. Na lista **Configuração do Serviço**, selecione a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode definir a contagem de instâncias para uma configuração de serviço específica ou para todas as configurações de serviço.

1. Na caixa de texto **Contagem de instâncias**, digite o número de instâncias que você deseja iniciar para essa função.

    >[AZURE.NOTE] Cada instância é executada em uma máquina virtual separada quando você publica seu serviço de nuvem no Azure.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações para o arquivo de configuração de serviço.

## Gerenciar cadeias de conexão para contas de armazenamento

Você pode adicionar, remover ou modificar cadeias de conexão para suas configurações de serviço. Talvez você queira cadeias de conexão diferentes para configurações de serviço diferentes. Por exemplo, você pode querer uma cadeia de conexão local para uma configuração de serviço local que tem um valor de `UseDevelopmentStorage=true`. Você também pode querer definir uma configuração de serviço de nuvem que use uma conta de armazenamento no Azure.

>[AZURE.WARNING] Quando você insere as informações de chave da conta de armazenamento do Azure para uma cadeia de conexão de conta de armazenamento, essas informações são armazenadas localmente no arquivo de configuração de serviço. No entanto, atualmente essas informações não são armazenadas como texto criptografado.

Ao usar um valor diferente para cada configuração de serviço, você não precisa usar cadeias de conexão diferentes em seu serviço de nuvem ou modificar seu código quando publicar o serviço de nuvem no Azure. Você pode usar o mesmo nome para a cadeia de conexão no seu código e o valor será diferente, com base na configuração de serviço que você selecionar quando criar seu serviço de nuvem ou quando publicá-lo.

### Para gerenciar cadeias de conexão para contas de armazenamento

1. Escolha a guia **Configurações**.

1. Na lista **Configuração do Serviço**, selecione a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode atualizar cadeias de conexão para uma configuração de serviço específica, mas se precisar adicionar ou excluir uma cadeia de conexão, você precisa selecionar Todas as Configurações.

1. Para adicionar uma cadeia de conexão, selecione o botão **Adicionar configuração**. Uma nova entrada é adicionada à lista.

1. Na caixa de texto **Nome**, digite o nome que você deseja usar para a cadeia de conexão.

1. Na lista suspensa **Tipo**, selecione **Cadeia de Conexão**.

1. Para alterar o valor da cadeia de conexão, escolha o botão de reticências (...). A caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** é exibida.

1. Para usar o emulador de conta de armazenamento local, selecione o botão de opção **Emulador de Armazenamento do Microsoft Azure** e, em seguida, selecione o botão **OK**.

1. Para usar uma conta de armazenamento no Azure, escolha o botão de opção **Sua Assinatura** e selecione a conta de armazenamento desejada.

1. Para usar credenciais personalizadas, escolha no botão opções **Credenciais Inseridas Manualmente**. Digite o nome da conta de armazenamento e a chave primária ou secundária. Para obter informações sobre como criar uma conta de armazenamento e como inserir os detalhes da conta de armazenamento na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, consulte [Preparar, Publicar ou Implantar um Aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para excluir uma cadeia de conexão, selecione a cadeia de conexão e escolha o botão **Remover Configuração**.

1. Escolha o ícone **Salvar** na barra de ferramentas para salvar essas alterações para o arquivo de configuração de serviço.

1. Para acessar a cadeia de conexão no arquivo de configuração de serviço, você precisa obter o valor da configuração. O código a seguir mostra um exemplo em que o armazenamento de blob é criado e carregados usando uma cadeia de conexão de dados `MyConnectionString` do arquivo de configuração de serviço quando um usuário escolhe **Button1** na página Default. aspx na função web do Azure para o serviço de nuvem. Adicione o seguinte usando instruções a Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Abra Default.aspx.cs no modo de exibição de Design e adicione um botão da caixa de ferramentas. Adicione o seguinte código ao `Button1_Click` método. Esse código usa `GetConfigurationSettingValue` para obter o valor do arquivo de configuração de serviço para a cadeia de conexão. Um blob será criado na conta de armazenamento que é referenciada na cadeia de conexão `MyConnectionString` e, finalmente, o programa adiciona texto ao blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Adicionar configurações personalizadas para serem usadas em seu serviço de nuvem do Azure

Configurações personalizadas no arquivo de configuração de serviço permitem que você adicione um nome e valor para uma cadeia de caracteres para uma configuração de serviço específica. Você pode optar por usar essa configuração para configurar um recurso no seu serviço de nuvem lendo o valor da configuração e usando esse valor para controlar a lógica em seu código. Você pode alterar esses valores de configuração de serviço sem precisar recompilar o pacote de serviço ou quando o serviço de nuvem estiver em execução. Seu código pode verificar se há notificações de quando uma configuração é alterada. Consulte [Evento RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Você pode adicionar, remover ou modificar configurações personalizadas para suas configurações de serviço. Talvez você queira valores diferentes para essas cadeias de caracteres para configurações de serviço diferentes.

Ao usar um valor diferente para cada configuração de serviço, você não precisa usar cadeias de caracteres diferentes em seu serviço de nuvem ou modificar seu código quando publicar o serviço de nuvem no Azure. Você pode usar o mesmo nome para a cadeia de caracteres no seu código e o valor será diferente, com base na configuração de serviço que você selecionar quando criar seu serviço de nuvem ou quando publicá-lo.

### Para adicionar configurações personalizadas para serem usadas em seu serviço de nuvem do Azure

1. Escolha a guia **Configurações**.

1. Na lista **Configuração do Serviço**, selecione a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode atualizar cadeias de caracteres para uma configuração de serviço específica, entretanto, se precisar adicionar ou excluir uma cadeia de caracteres, você deve selecionar **Todas as Configurações**.

1. Para adicionar uma cadeia de caracteres, escolha o botão **Adicionar configuração**. Uma nova entrada é adicionada à lista.

1. Na caixa de texto **Nome**, digite o nome que você deseja usar para a cadeia de caracteres.

1. Na lista suspensa **Tipo**, escolha **sequência**.

1. Para adicionar ou alterar o valor da cadeia de caracteres na caixa de texto **Valor**, digite o novo valor.

1. Para excluir uma cadeia de caracteres, selecione a cadeia de caracteres e, em seguida, escolha o botão **Remover Configuração**.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações para o arquivo de configuração de serviço.

1. Para acessar a cadeia de caracteres no arquivo de configuração de serviço, você precisa obter o valor da configuração.

    Você precisará certificar-se de que as seguintes instruções já foram adicionadas ao arquivo Default.aspx.cs, exatamente como fez no procedimento anterior.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Adicione o seguinte código ao `Button1_Click` método para acessar essa cadeia de caracteres da mesma maneira que você acessa uma cadeia de conexão. Seu código pode, então, executar alguns códigos específicos com base no valor da cadeia de configurações para o arquivo de configuração de serviço usado.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## Gerenciar o armazenamento local para cada instância de função

Você pode adicionar o armazenamento do sistema de arquivos local para cada instância de uma função. Você pode armazenar aqui dados locais que não precisam ser acessados por outras funções. Quaisquer dados que você não precisar salvar em tabela, blob ou armazenamento do Banco de Dados SQL podem ser armazenados aqui. Por exemplo, você poderia usar esse armazenamento local para armazenar em cache dados que podem precisar ser usados novamente. Esses dados armazenados não podem ser acessados por outras instâncias de uma função. Para obter mais informações sobre recursos de armazenamento local, consulte [Configurar Recursos de Armazenamento Local](cloud-services/cloud-services-configure-local-storage-resources.md).

Configurações de armazenamento locais se aplicam a todas as configurações de serviço. Você só pode adicionar, remover ou modificar o armazenamento local para todas as configurações de serviço.

### Para gerenciar o armazenamento local para cada instância de função

1. Escolha a guia **Armazenamento Local**.

1. Na lista **Configuração do Serviço**, escolha **Todas as Configurações**.

1. Para adicionar uma entrada de armazenamento local, escolha o botão **Adicionar Armazenamento Local**. Uma nova entrada é adicionada à lista.

1. Na caixa de texto **Nome**, digite o nome que você deseja usar para o armazenamento local.

1. Na caixa de texto **Tamanho**, digite o tamanho em MB que você deseja para o armazenamento local.

1. Para remover os dados nesse armazenamento local quando a máquina virtual para esta função é reciclada, selecione a caixa de seleção **Limpar reciclagem de função**.

1. Para editar uma entrada de armazenamento local existente, escolha a linha que você precisa atualizar. Em seguida, você pode editar os campos, conforme descrito nas etapas anteriores.

1. Para excluir uma entrada de armazenamento local, escolha a entrada de armazenamento na lista e escolha o botão **Remover armazenamento local**.

1. Para salvar essas alterações nos arquivos de configuração de serviço, escolha o ícone **Salvar** na barra de ferramentas.

1. Para acessar o armazenamento local que você adicionou no arquivo de configuração de serviço, você deve obter o valor da configuração de recurso local. Use as seguintes linhas de código para acessar esse valor e crie um arquivo chamado **MyStorageTest.txt** e escreva uma linha de dados de teste nesse arquivo. Você pode adicionar esse código ao `Button_Click` método que você usou nos procedimentos anteriores:

1. Você precisará se certificar de que as seguintes instruções sejam adicionadas ao arquivo Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Adicione o seguinte código ao `Button1_Click` método. Isso cria o arquivo no armazenamento local e grava dados de teste no arquivo.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Opcional) Para exibir esse arquivo que você criou quando executou seu serviço de nuvem localmente, use as seguintes etapas:

  1. Execute a função web e clique no **Button1** para certificar-se de que o código dentro de `Button1_Click` é chamado.

  1. Na área de notificação, abra o menu de atalho para o ícone do Azure e escolha **Exibir interface de emulador de computação**. A caixa de diálogo **Emulador de Computação do Azure** é exibida.

  1. Selecione a função web.

  1. Na barra de menus, escolha **Ferramentas**, **Abrir armazenamento local**. É exibida uma janela do Windows Explorer.

  1. Na barra de menus, digite **MyStorageTest.txt** na caixa de texto **pesquisa** e, em seguida, escolha **Inserir** para iniciar a pesquisa.

    O arquivo é exibido nos resultados da pesquisa.

  1. Para exibir o conteúdo do arquivo, abra o menu de atalho para o arquivo e escolha **Abrir**.

## Coletar dados de diagnóstico do serviço de nuvem

Você pode coletar dados de diagnóstico do seu serviço de nuvem do Azure. Esses dados são adicionados a uma conta de armazenamento. Talvez você queira cadeias de conexão diferentes para configurações de serviço diferentes. Por exemplo, você pode querer uma conta de armazenamento local para uma configuração de serviço local com um valor de UseDevelopmentStorage=true. Você também pode querer definir uma configuração de serviço de nuvem que use uma conta de armazenamento no Azure. Para obter mais informações sobre diagnósticos do Azure, consulte Coletar dados do log usando o Diagnóstico do Azure.

>[AZURE.NOTE] A configuração do serviço local já está definida para usar recursos locais. Se você usar a configuração do serviço de nuvem para publicar seu serviço de nuvem do Azure, a cadeia de conexão que você especificar quando publicar também será usada para a cadeia de conexão de diagnóstico, a menos que você tenha especificado uma cadeia de conexão. Se você empacotar seu serviço de nuvem usando o Visual Studio, a cadeia de conexão da configuração do serviço não será alterada.

### Para coletar dados de diagnóstico do serviço de nuvem

1. Escolha a guia **Configuração**.

1. Na lista **Configuração do Serviço** escolha a configuração do serviço que você deseja atualizar ou escolha **Todas as Configurações**.

    >[AZURE.NOTE] Você pode atualizar a conta de armazenamento para uma configuração de serviço específica, mas se quiser habilitar ou desabilitar o diagnóstico, escolha Todas as Configurações.

1. Para habilitar o diagnóstico, selecione a caixa de seleção **Habilitar diagnóstico**.

1. Para alterar o valor da conta de armazenamento, escolha o botão de reticências (...).

    A caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** é exibida.

1. Para usar uma cadeia de conexão local, escolha a opção de emulador de armazenamento do Azure e escolha o botão **OK**.

1. Para usar uma conta de armazenamento associada à sua assinatura do Azure, escolha a opção **Sua Assinatura**.

1. Para usar uma conta de armazenamento para a cadeia de conexão local, escolha a opção **Credenciais Inseridas Manualmente**.

    Para obter mais informações sobre como criar uma conta de armazenamento e como inserir os detalhes da conta de armazenamento na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, consulte [Preparar, Publicar ou Implantar um Aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Escolha a conta de armazenamento que você deseja usar no **Nome da Conta**.

    Se você estiver inserindo manualmente suas credenciais de conta de armazenamento, copie ou digite sua chave primária em **Chave de Conta**. Essa chave pode ser copiada do Portal de Gerenciamento do Azure. Para copiar essa chave, siga estas etapas da exibição **Contas de Armazenamento** no Portal de Gerenciamento do Azure:

  1. Selecione a conta de armazenamento que você deseja usar para seu serviço de nuvem.

  1. Escolha o botão **Gerenciar chaves de acesso** localizado na parte inferior da tela. A caixa de diálogo **Gerenciar chaves de acesso** é exibida.

  1. Para copiar a chave de acesso, escolha o botão **Copiar para área de transferência**. Agora você pode colar essa chave no campo **Chave de Conta**.

1. Para usar a conta de armazenamento que você fornece, como a cadeia de conexão para diagnóstico (e cache) quando você publica seu serviço de nuvem no Azure, selecione a caixa de seleção **Atualizar cadeias de conexão do armazenamento de desenvolvimento para diagnóstico e cache com credenciais de conta de armazenamento do Azure, quando publicar no Azure**.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações para o arquivo de configuração de serviço.

## Alterar o tamanho da máquina virtual usada para cada função

Você pode definir o tamanho da máquina virtual para cada função. Só é possível definir o tamanho para todas as configurações de serviço. Se você selecionar um tamanho de máquina menor, menos núcleos de CPU, memória e armazenamento em disco local serão alocados. A largura de banda alocada também será menor. Para obter mais informações sobre esses tamanhos e os recursos alocados, consulte [Tamanhos para Serviços de Nuvem](cloud-services/cloud-services-sizes-specs.md).

Os recursos necessários para cada máquina virtual no Azure afetam o custo da execução do seu serviço de nuvem no Azure. Para obter mais informações sobre cobrança do Azure, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

### Para alterar o tamanho da máquina virtual

1. Escolha a guia **Configuração**.

1. Na lista **Configuração do Serviço**, escolha **Todas as Configurações**.

1. Para selecionar o tamanho da máquina virtual para esta função, escolha o tamanho apropriado da lista **tamanho da VM**.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações para o arquivo de configuração de serviço.

## Gerenciar certificados e pontos de extremidade para suas funções

Você configura os pontos de extremidade de rede especificando o protocolo, o número da porta e, para HTTPS, as informações do certificado SSL. Versões anteriores a junho de 2012 dão suporte a HTTP, HTTPS e TCP. A versão de junho de 2012 dá suporte a esses protocolos e a UDP. Você não pode usar UDP para pontos de extremidade de entrada no emulador de computação. Você pode usar esse protocolo somente para pontos de extremidade internos.

Para melhorar a segurança do seu serviço de nuvem do Azure, você pode criar pontos de extremidade que usam o protocolo HTTPS. Por exemplo, se você tiver um serviço de nuvem que é usado por clientes para ordens de compra, é necessário certificar-se de que suas informações estejam protegidas usando SSL.

Você também pode adicionar pontos de extremidade que podem ser usados interna ou externamente. Pontos de extremidade externos são chamados de pontos de extremidade de entrada. Um ponto de extremidade de entrada permite que os usuários tenham outro ponto de acesso ao seu serviço de nuvem. Se tiver um serviço WCF, talvez você queira expor um ponto de extremidade interno para que uma função web o utilize para acessar esse serviço.

>[AZURE.IMPORTANT] Só é possível atualizar os pontos de extremidade para todas as configurações de serviço.

Se adicionar pontos de extremidade HTTPS, você precisa usar um certificado SSL. Para fazer isso, você pode associar certificados com sua função para todas as configurações de serviço e usá-los para seus pontos de extremidade.

>[AZURE.IMPORTANT] Esses certificados não são empacotados com seu serviço. Você deve carregá-los separadamente no Azure por meio do Portal de Gerenciamento de plataforma do Azure

Qualquer certificado de gerenciamento que você associar às suas configurações de serviço só se aplicará quando o serviço de nuvem estiver em execução no Azure. Quando seu serviço de nuvem é executado no ambiente de desenvolvimento local, um certificado padrão que é gerenciado pelo emulador de computação do Azure é usado.

### Para adicionar um certificado a uma função

1. Escolha a guia **Certificados**.

1. Na lista **Configuração do Serviço**, escolha **Todas as Configurações**.

    >[AZURE.NOTE] Para adicionar ou remover certificados, você deve selecionar Todas as Configurações. Se for necessário, você pode atualizar o nome e a impressão digital de uma configuração de serviço específica.

1. Para adicionar um certificado para essa função, escolha o botão **Adicionar Certificado**. Uma nova entrada é adicionada à lista.

1. Na caixa de texto **Nome**, digite o nome do certificado.

1. Na lista **Local de Armazenamento**, escolha o local para o certificado que você deseja adicionar.

1. Na lista **Armazenar Nome**, escolha o repositório que você deseja usar para selecionar o certificado.

1. Para adicionar o certificado, escolha o botão de reticências (...). A caixa de diálogo do **Windows Security** é exibida.

1. Escolha o certificado que você deseja usar na lista e, em seguida, escolha o botão **OK**.

    >[AZURE.NOTE] Quando você adiciona um certificado do repositório de certificados, todos os certificados intermediários são adicionados automaticamente às definições de configuração para você. Esses certificados intermediários também devem ser carregados no Azure para configurar corretamente o serviço para SSL.

1. Para excluir um certificado, escolha o certificado e o botão **Remover Certificado**.

1. Escolha o ícone **Salvar** na barra de ferramentas para salvar essas alterações nos arquivos de configuração de serviço.

### Para gerenciar pontos de extremidade para uma função

1. Escolha a guia **Pontos de Extremidade**.

1. Na lista **Configuração do Serviço**, escolha **Todas as Configurações**.

1. Para adicionar um ponto de extremidade, escolha o botão **Adicionar Ponto de Extremidade**. Uma nova entrada é adicionada à lista.

1. Na caixa de texto **Nome**, digite o nome que você deseja usar para este ponto de extremidade.

1. Escolha o tipo de ponto de extremidade que você precisa na lista **Tipo**.

1. Escolha o tipo de ponto de extremidade que você precisa na lista **Protocolo**.

1. Se for um ponto de extremidade de entrada, na caixa de texto **Porta Pública**, digite a porta pública a ser usada.

1. Na caixa de texto **Porta Privada**, a porta privada a ser usada.

1. Se o ponto de extremidade requer o protocolo https, na lista **nome do certificado SSL** escolha um certificado para uso.

    >[AZURE.NOTE] Esta lista mostra os certificados que você adicionou para essa função na guia **Certificados**.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações nos arquivos de configuração de serviço.

## Próximas etapas
Saiba mais sobre projetos do Azure no Visual Studio, lendo [Configurando um projeto do Azure](vs-azure-tools-configuring-an-azure-project.md). Saiba mais sobre o esquema do serviço de nuvem lendo [Referência de Esquema](https://msdn.microsoft.com/library/azure/dd179398).

<!---HONumber=AcomDC_0224_2016-->
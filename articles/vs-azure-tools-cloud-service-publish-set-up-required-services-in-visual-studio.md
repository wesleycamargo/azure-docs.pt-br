<properties 
   pageTitle="Configurando os serviços necessários para publicar um serviço de nuvem no Visual Studio"
	description="Conheça os procedimentos para configurar serviços de conta de armazenamento e nuvem e configurar seu aplicativo do Azure"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags 
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/13/2015"
	ms.author="kempb"/>

# Configurando os serviços necessários para publicar um serviço de nuvem no Visual Studio

##Visão geral

Antes de publicar um projeto de serviço de nuvem, você deve configurar os seguintes serviços:

- Um **serviço de nuvem** para executar suas funções no ambiente do Azure

- Uma **conta de armazenamento** que forneça acesso aos serviços de blob, fila e tabela.

Use os procedimentos a seguir para configurar esses serviços e configurar seu aplicativo


## Criar um Serviço de Nuvem

Para publicar um serviço de nuvem no Azure, crie primeiro um serviço de nuvem que executa suas funções no ambiente do Azure. Você pode criar um serviço de nuvem no Portal de Gerenciamento do Azure, conforme descrito [aqui](#to-create-a-cloud-service-by-using-the-azure-management-portal). Você também pode criar um serviço de nuvem no Visual Studio ao usar o assistente de publicação.

### Criar um serviço de nuvem usando o Visual Studio

1. Abra o menu de atalho do projeto Azure e clique em **Publicar**.

    ![VST\_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Se você ainda não tiver entrado, entre com seu nome de usuário e senha da conta da Microsoft ou conta organizacional associada à sua assinatura do Azure.

1. Escolha o botão **Avançar** para ir para a página **Configurações**.

    ![Configurações comuns de assistente de publicação](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Na lista **Serviços de Nuvem** escolha **Criar Novo**. A caixa de diálogo **Criar Serviços do Azure** aparece.

1. Insira o nome de seu serviço de nuvem. O nome faz parte da URL do serviço e, portanto, deve ser globalmente exclusivo. O nome não diferencia maiúsculas de minúsculas.

### Para criar um serviço de nuvem usando o Portal de Gerenciamento do Azure

1. Entre no [Portal de Gerenciamento do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.

1. (opcional) Para exibir uma lista de serviços de nuvem que você criou, escolha o link Serviços de Nuvem no lado esquerdo da página.

1. Escolha o ícone **+** no canto inferior esquerdo e escolha **Serviço de Nuvem** no menu que aparece. Outra tela com duas opções, **Criação Rápida** e **Criação Personalizada**, é exibida. Se você escolher **Criação Rápida**, poderá criar um serviço de nuvem apenas especificando a URL e a região onde ele será fisicamente hospedado. Se você escolher **Criação Personalizada**, poderá, imediatamente, publicar um serviço de nuvem, especificando um pacote (arquivo .cspkg), um arquivo de configuração (.cscfg) e um certificado. Criação Personalizada não é necessária se você pretende publicar seu serviço de nuvem usando o comando **Publicar** em um projeto do Azure. O comando **Publicar** está disponível no menu de atalho para um projeto do Azure.

1. Escolha **Criação Rápida** para publicar mais tarde seu serviço de nuvem usando o Visual Studio.

1. Especifique um nome para seu serviço de nuvem. A URL completa é exibida ao lado do nome.

1. Na lista, escolha a região onde se encontra a maioria dos usuários.

1. Na parte inferior da janela, escolha o link **Criar Serviço de Nuvem**.

## Criar uma conta de armazenamento

Uma conta de armazenamento fornece acesso aos serviços de blob, fila e tabela. Você pode criar uma conta de armazenamento usando o [Portal de Gerenciamento da Plataforma do Azure](http://go.microsoft.com/fwlink/?LinkId=253103).

### Para criar uma conta de armazenamento usando o Visual Studio

1. No Gerenciador de Soluções abra o menu de atalho para o nó **Armazenamento** e escolha **Criar Conta de Armazenamento**.

    ![Como criar uma nova conta de armazenamento do Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Selecione ou insira as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar Conta de Armazenamento**.
    - A assinatura do Azure à qual você deseja adicionar a conta de armazenamento.
    - O nome que você deseja usar para a nova conta de armazenamento.
    - A região ou o grupo de afinidade (como Oeste dos EUA ou Leste Asiático).
    - O tipo de replicação que você deseja usar para a conta de armazenamento, como redundância geográfica.

1. Quando terminar, escolha **Criar**. A nova conta de armazenamento aparece na lista **Armazenamento** no Gerenciador de Servidores.

### Para criar uma conta de armazenamento usando o Portal de Gerenciamento do Azure

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.

1. (opcional) Para exibir suas contas de armazenamento, escolha o link **Armazenamento** no painel à esquerda da página.

1. No canto inferior esquerdo da página, escolha o ícone **+**.

1. No menu que aparece, escolha **Armazenamento**, e, em seguida, escolha **Criação Rápida**.

1. Nomeie a conta de armazenamento que resulta em uma URL exclusiva.

1. Dê um nome ao serviço de nuvem. A URL completa é exibida ao lado do nome.

1. Na lista de regiões, escolha a região onde se encontra a maioria dos usuários.

1. Especifique se deseja habilitar a replicação geográfica. Se você habilitar a replicação geográfica, seus dados serão salvos em vários locais físicos para reduzir a possibilidade de perda. Esse recurso torna o armazenamento mais caro, mas você pode reduzir o custo, permitindo a localização geográfica ao criar a conta de armazenamento em vez de adicionar o recurso mais tarde. Para mais informações, consulte [Replicação geográfica](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Na parte inferior da janela, escolha o link **Criar Conta de Armazenamento**.

Depois de criar sua conta de armazenamento, você verá as URLs que pode usar para acessar recursos em cada um dos serviços de armazenamento do Azure e as chaves de acesso primária e secundária para sua conta. Você pode usar essas chaves para autenticar solicitações feitas nos serviços de armazenamento.

>[AZURE.NOTE]A chave de acesso secundária fornece o mesmo acesso à sua conta de armazenamento que a chave de acesso primária e é gerada como um backup, caso sua chave de acesso primária seja comprometida. Além disso, é recomendável que você gere novamente e regularmente suas chaves de acesso. Você pode modificar uma configuração de cadeia de conexão para usar a chave secundária enquanto gera novamente a chave primária, e então poderá modificá-la para usar a chave primária gerada enquanto gera novamente a chave secundária.

## Configurar seu aplicativo para usar os serviços fornecidos pela conta de armazenamento

Configure qualquer função que acessa os serviços de armazenamento para usar os serviços de armazenamento do Azure que você criou. Para fazer isso, você pode usar várias configurações de serviço para o seu projeto do Azure. Por padrão, duas são criadas no projeto do Azure. Usando várias configurações de serviço, você pode usar a mesma cadeia de conexão no seu código, mas ter um valor diferente para uma cadeia de conexão em cada configuração de serviço. Por exemplo, você pode usar uma configuração de serviço para executar e depurar seu aplicativo localmente usando o emulador de armazenamento do Azure e uma configuração de serviço diferente para publicar seu aplicativo no Azure. Para obter mais informações sobre a configuração de serviço, consulte [Configurando um projeto no Azure](https://msdn.microsoft.com/library/azure/ee405486.aspx).

### Para configurar seu aplicativo para usar os serviços que a conta de armazenamento fornece

1. Abra sua solução Azure no Visual Studio. No Gerenciador de Soluções, abra o menu de atalho para cada função no projeto do Azure que acessa os serviços de armazenamento e escolha **Propriedades**. Uma página com o nome da função é exibida no editor do Visual Studio. A página exibe os campos para a guia **Configuração**.

1. Nas páginas de propriedades da função, escolha **Configurações**.

1. Na lista **Configuração do Serviço**, escolha o nome da configuração do serviço que você deseja editar. Se você quiser fazer alterações em todas as configurações de serviço para essa função, poderá escolher **Todas as Configurações**. Para obter mais informações sobre como atualizar as configurações de serviço, consulte [Gerenciar cadeias de conexão para contas de armazenamento](https://msdn.microsoft.com/library/azure/8cda8963-ef0e-4f64-8d29-5eac467e5f53#ConnectionStrings).

1. Para modificar as configurações da cadeia de conexão, escolha o botão **...** ao lado da configuração. A caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** é exibida.

1. Em **Conectar usando**, escolha a opção **Sua assinatura**.

1. Na lista **Assinatura**, escolha sua assinatura. Se a lista de assinaturas não incluir a que você deseja, escolha o link **Baixar Configurações de Publicação**.

1. Na lista **Nome da conta**, escolha o nome da sua conta de armazenamento. As ferramentas do Azure obtêm credenciais da conta de armazenamento automaticamente usando o arquivo .publishsettings. Para especificar as credenciais da conta de armazenamento manualmente, escolha a opção **Credenciais inseridas manualmente** opção e continue o procedimento. Você pode obter o nome da conta de armazenamento e a chave primária no [Portal de Gerenciamento](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se você não quiser especificar as configurações da conta de armazenamento manualmente, escolha o botão **OK** para fechar a caixa de diálogo.

1. Escolha o link de credenciais **Inserir conta de armazenamento**.

1. Na caixa **Nome da conta**, insira o nome da conta de armazenamento.

    >[AZURE.NOTE]Faça logon no Portal de Gerenciamento e, em seguida, escolha o botão **Armazenamento**. O portal mostra uma lista de contas de armazenamento. Se você escolher uma conta, uma página será aberta para ela. Você pode copiar o nome da conta de armazenamento nessa página. Se estiver usando uma versão anterior do Portal de Gerenciamento, o nome da conta de armazenamento será mostrado na exibição **Contas de Armazenamento** do Portal de Gerenciamento. Para copiar esse nome, realce-o na janela **Propriedades** dessa exibição e, em seguida, escolha as teclas Ctrl-C. Para colar o nome no Visual Studio, escolha a caixa de texto **Nome da Conta** e as teclas Ctrl + V.

1. Na caixa **Chave da conta**, digite sua chave primária, ou copie e cole-a do [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=213885). Para copiar essa chave do Portal de Gerenciamento:

    1. Na parte inferior da página da conta de armazenamento apropriada, escolha o botão **Gerenciar Chaves**.

    1. Na página **Gerenciar Chaves de Acesso**, selecione o texto da chave de acesso primária e, em seguida, escolha as teclas Ctrl+C.

    1. Nas ferramentas do Azure, cole a chave na caixa **Chave da conta**.

    1. Você deve selecionar uma das opções a seguir para determinar como o serviço acessará a conta de armazenamento:
        - **Usar HTTP**. Essa é a opção padrão. Por exemplo: `http://<account name>.blob.core.windows.net`.
        - **Usar HTTPS** para uma conexão segura. Por exemplo: `https://<accountname>.blob.core.windows.net`.
        - **Especificar pontos de extremidade personalizados** para cada um dos três serviços. Em seguida, você pode digitar esses pontos de extremidade no campo para o serviço específico. 

        >[AZURE.NOTE]Se você criar pontos de extremidade personalizados, poderá criar uma cadeia de conexão mais complexa. Ao usar esse formato de cadeia de caracteres, você pode especificar pontos de extremidade de serviço de armazenamento que incluem um nome de domínio personalizado que você registrou para sua conta de armazenamento com o serviço Blob. Além disso, você pode conceder acesso apenas aos recursos de blob em um único contêiner por meio de uma assinatura de acesso compartilhado. Para obter mais informações sobre como criar pontos de extremidade, consulte [Configurar cadeias de conexão de armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/).

1. Para salvar essas alterações de cadeia de conexão, escolha o botão **OK** e, em seguida, escolha **Salvar** na barra de ferramentas. Depois de salvar essas alterações, você pode obter o valor dessa cadeia de conexão no seu código usando [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando você publica seu aplicativo no Azure, escolhe a configuração do serviço que contém a conta de armazenamento do Azure para a cadeia de conexão. Depois de publicar o aplicativo, verifique se ele funciona conforme esperado nos serviços de armazenamento do Azure

## Recursos adicionais

[Publicando em um Serviço de Nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

<!---HONumber=September15_HO1-->
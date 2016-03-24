<properties
   pageTitle="Navegando e gerenciando recursos de armazenamento com o Gerenciador de Servidores | Microsoft Azure"
   description="Navegando e gerenciando recursos de armazenamento com o Gerenciador de Servidores"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/27/2016"
   ms.author="tarcher" />

# Navegando e gerenciando recursos de armazenamento com o Gerenciador de Servidores

Se você instalou as ferramentas do Azure para Microsoft Visual Studio, pode exibir dados de blob, de fila e de tabela de suas contas de armazenamento do Azure. O nó de armazenamento do Azure no Gerenciador de Servidores mostra os dados que estão em sua conta do emulador de armazenamento local e outras contas de armazenamento do Azure.

Para exibir o Gerenciador de Servidores no Visual Studio, na barra de menus, escolha **Exibir**, **Gerenciador de Servidores**. O nó de armazenamento mostra todas as contas de armazenamento que existem em cada assinatura/certificado do Azure ao qual você está conectado. Se sua conta de armazenamento não aparecer, você pode adicioná-la seguindo as instruções [mais adiante neste tópico](#add-storage-accounts-by-using-server-explorer).

A partir do Azure SDK 2.7, você pode também usar o novo Gerenciador de nuvem para exibir e gerenciar os recursos do Azure. Consulte [Gerenciando recursos do Azure com o Gerenciador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer) para obter mais informações.


## Exibir e gerenciar recursos de armazenamento no Visual Studio

O Gerenciador de Servidores automaticamente mostra uma lista de blobs, filas e tabelas na conta do emulador de armazenamento. A conta do emulador de armazenamento está listada no Gerenciador de Servidores no nó de armazenamento, como o nó **Desenvolvimento**.

Para ver os recursos da conta do emulador de armazenamento, expanda o nó **Desenvolvimento**. Se o emulador de armazenamento não foi iniciado quando você expande o nó **Desenvolvimento**, ele será iniciado automaticamente. Isso pode levar vários minutos. Você pode continuar a trabalhar em outras áreas do Visual Studio enquanto o emulador de armazenamento é iniciado.

Para exibir recursos em uma conta de armazenamento, expanda o nó da conta de armazenamento no Gerenciador de Servidores. Os seguintes sub-nós aparecem:

- Blobs

- Filas

- Tabelas

## Trabalhar com recursos de blob

O nó de blobs exibe uma lista de contêineres para a conta de armazenamento selecionado. Contêineres de blob contêm arquivos de blob e você pode organizar esses blobs em pastas e subpastas. Consulte [Como usar o Armazenamento de Blob do .NET](..storage/storage-dotnet-how-to-use-blobs/) para obter mais informações.

### Para criar um contêiner de blob

1. Abra o menu de atalho no nó **Blobs** e escolha **Criar contêiner de Blob**.

1. Insira o nome do novo contêiner na caixa de diálogo **Criar Contêiner de Blob** e escolha **Ok**.

    ![Adicionando um novo contêiner de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744153.bmp)

    >[AZURE.NOTE] O nome do contêiner de blob deve começar com uma letra minúscula (a-z) ou um número (0-9).

### Para excluir um contêiner de blob

- Abra o menu de atalho para o contêiner de blob que você deseja remover e escolha **Excluir**.

### Para exibir uma lista de itens contidos em um contêiner de blob

- Abra o menu de atalho para um nome de contêiner de blob na lista e escolha **Exibir Contêiner de Blob**.

    Quando você exibe o conteúdo de um contêiner de blob, ele aparece em uma guia conhecida como exibição do contêiner de blob.

    ![VST\_SE\_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Você pode executar as seguintes operações com blobs usando os botões no canto superior direito da exibição do contêiner de blob:

    - Inserir um valor de filtro e aplicá-lo

    - Atualizar a lista de blobs no contêiner

    - Carregar um arquivo

    - Excluir um blob

      >[AZURE.NOTE] Excluir um arquivo de um contêiner de blob não exclui o arquivo subjacente. Apenas remove o arquivo do contêiner de blob.

    - Abrir um blob

    - Salvar um blob no computador local

### Para criar uma pasta ou subpasta em um contêiner de blob

1. Escolha o contêiner de blob no Gerenciador de Servidores. Na janela do contêiner, escolha o botão **Carregar blob**.

    ![Carregar um arquivo em uma pasta de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Na caixa de diálogo **Carregar Novo Arquivo**, escolha o botão **Procurar** para especificar o arquivo que você deseja carregar e digite o nome da pasta na caixa **Pasta (opcional)**.

    Você pode adicionar as subpastas nas pastas de contêiner, seguindo o mesmo procedimento. Se você não especificar um nome de pasta, o arquivo será carregado para o nível superior do contêiner de blob. O arquivo aparece na pasta especificada no contêiner.

    ![Pasta adicionada a um contêiner de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Clique duas vezes na pasta ou pressione ENTER para ver o conteúdo da pasta. Quando estiver na pasta do contêiner, você poderá navegar até a raiz do contêiner escolhendo o botão **Abrir Diretório Pai** (seta para cima).

### Para excluir um contêiner

 - Exclua todos os arquivos na pasta

    >[AZURE.NOTE] Como as pastas em contêineres de blob são pastas virtuais, você não pode criar uma pasta vazia nem pode excluir uma pasta para excluir o conteúdo do arquivo. Você precisa excluir todo o conteúdo de uma pasta para excluir a pasta.

### Para filtrar os blobs em um contêiner

Você pode filtrar os blobs que são exibidos especificando um prefixo comum.

Por exemplo, se você inserir o prefixo `hello` na caixa de texto do filtro e, em seguida, escolher o botão **Executar** (**!**), serão exibidos apenas os blobs que começam com 'hello'.

![VST\_SE\_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] O campo de filtro diferencia maiúsculas de minúsculas e não dá suporte a filtragem com caracteres curinga. Os blobs só podem ser filtrados pelo prefixo. O prefixo pode incluir um delimitador se você estiver usando um delimitador para organizar blobs em uma hierarquia virtual. Por exemplo, filtrar pelo prefixo HelloFabric/ retorna todos os blobs que começam com essa cadeia de caracteres.

### Para baixar os dados de blob

- No **Gerenciador de Servidores**, abra o menu de atalho de um ou mais blobs e escolha **Abrir**, ou escolha o nome do blob e o botão **Abrir**, ou clique duas vezes no nome do blob.

    O andamento do download de um blob aparece na janela **Log de atividades do Azure**.

    O blob é aberto no editor padrão desse tipo de arquivo. Se o sistema operacional reconhecer o tipo de arquivo, o arquivo é aberto em um aplicativo instalado localmente. Caso contrário, você será solicitado a escolher um aplicativo que seja apropriado para o tipo de arquivo do blob. O arquivo local criado quando você baixa um blob está marcado como somente leitura.

    Os dados do blob são armazenados em cache localmente e verificados em relação à hora da última modificação do blob no serviço Blob. Se o blob tiver sido atualizado desde que foi baixado, ele será baixado novamente; Caso contrário, o blob será carregado do disco local. Por padrão, um blob é baixado para um diretório temporário. Para baixar blobs em um diretório específico, abra o menu de atalho de nomes dos blobs selecionados e escolha **Salvar como**. Quando você salva um blob dessa maneira, não é possível abrir o arquivo de blob e o arquivo local é criado com atributos de leitura/gravação.

### Para carregar blobs

- Escolha o botão **Carregar Blob** quando o contêiner for aberto para visualização na exibição do contêiner de blob.

    Você pode escolher um ou mais arquivos para carregar e pode carregar arquivos de qualquer tipo. O **Log de atividades do Azure** mostra o andamento do carregamento. Para saber mais sobre como trabalhar com os dados de blob, consulte [Como usar o serviço de Armazenamento de Blob do Azure no .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### Para exibir os logs transferidos para os blobs

- Se você estiver usando o diagnóstico do Azure para registrar dados de seu aplicativo do Azure e tiver transferido logs para sua conta de armazenamento, verá os contêineres que foram criados pelo Azure para esses logs. Exibir esses logs no Gerenciador de Servidores é uma maneira fácil de identificar problemas com seu aplicativo, especialmente se foi implantado no Azure. Para saber mais sobre o Diagnóstico do Azure, consulte [Coletar dados do log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Para obter a URL para um blob

- Abra o menu de atalho do blob e escolha **Copiar URL**.

### Para editar um blob

- Selecione o blob e escolha o botão **Abrir Blob**.

    O arquivo é baixado para um local temporário e aberto no computador local. Você deve carregar o blob novamente depois de fazer alterações.

## Trabalhar com recursos de fila

Filas de serviços de armazenamento são hospedadas em uma conta de armazenamento do Azure e você pode usá-las para permitir que suas funções do serviço de nuvem se comuniquem entre si e com outros serviços por um mecanismo de transmissão de mensagens. Você pode acessar a fila programaticamente por meio de um serviço de nuvem e em um serviço Web para clientes externos. Você também pode acessar a fila diretamente, usando o Gerenciador de Servidores no Visual Studio.

Ao desenvolver um serviço de nuvem que usa filas, talvez você queira usar o Visual Studio para criar filas e trabalhar interativamente com elas enquanto desenvolve e testa seu código.

No Gerenciador de Servidores, você pode exibir as filas em uma conta de armazenamento, criar e excluir filas, abrir uma fila para exibir suas mensagens e adicionar mensagens a uma fila. Quando abrir uma fila para exibição, pode exibir as mensagens individuais e você pode executar as seguintes ações na fila usando os botões no canto superior esquerdo:

- Atualizar a exibição da fila

- Adicionar uma mensagem a uma fila

- Remover da fila a mensagem de nível mais alto.

- Excluir a fila inteira

A imagem a seguir mostra uma fila que contém duas mensagens.

![Exibindo uma fila](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para saber mais sobre as filas de serviço de armazenamento, consulte [Como usar o serviço de armazenamento de fila](http://go.microsoft.com/fwlink/?LinkID=264702). Para saber mais sobre o serviço Web para filas de serviços de armazenamento, consulte [Conceitos do serviço Fila](http://go.microsoft.com/fwlink/?LinkId=264788). Para saber mais sobre como enviar mensagens a uma fila de serviços de armazenamento usando o Visual Studio, consulte [Enviando mensagens para uma fila de serviços de armazenamento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Filas de serviços de armazenamento são diferentes de filas do barramento de serviço. Para obter mais informações sobre filas do barramento de serviço, consulte Filas do barramento de serviço, tópicos e assinaturas.

## Trabalhar com recursos de tabela

O serviço de armazenamento Tabela do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

### Para criar uma tabela

1. No Gerenciador de Servidores, selecione o nó **Tabelas** da conta de armazenamento e escolha **Criar Tabela**.

1. Na caixa de diálogo **Criar Tabela**, insira um nome para a tabela.

### Para exibir dados da tabela

1. No Gerenciador de Servidores, abra o nó **Azure** e clique no nó **Armazenamento**.

1. Abra o nó de conta de armazenamento no qual está interessado e abra o nó **Tabelas** para ver uma lista de tabelas para a conta de armazenamento.

1. Abra o menu de atalho para uma tabela e escolha **Exibir Tabela**.

    ![Uma tabela do Azure no Gerenciador de Soluções](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tabela é organizada por entidades (mostradas nas linhas) e propriedades (mostradas nas colunas). Por exemplo, a ilustração a seguir mostra entidades listadas no **Designer de Tabela**:

### Para editar dados da tabela

1. No **Designer de Tabela**, abra o menu de atalho de uma entidade (uma única linha) ou uma propriedade (uma única célula) e escolha **Editar**.

    ![Adicionar ou editar uma entidade de tabela](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Entidades em uma única tabela não precisam ter o mesmo conjunto de propriedades (colunas). Tenha em mente as seguintes restrições ao exibir e editar dados da tabela:
    - Você não pode exibir ou editar dados binários (tipo byte), mas pode armazená-lo em uma tabela.

    - Você não pode editar os valores **PartitionKey** ou **RowKey** valores, porque o armazenamento de tabela no Azure não dá suporte a essa operação.

    - Você não pode criar uma propriedade chamada Timestamp, os serviços de armazenamento do Azure usam uma propriedade com esse nome.

    - Se você inserir um valor DateTime, deve seguir o formato apropriado para as configurações de região e idioma do seu computador (por exemplo, MM/DD/AAAA HH:MM:SS [AM|PM] para o inglês dos EUA ).

### Para adicionar entidades

1. No **Designer de tabela**, escolha o botão **Adicionar entidade**, que está no canto superior direito da exibição de tabela.

    ![Adicionar entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Na caixa de diálogo **Adicionar entidade**, insira os valores das propriedades **PartitionKey** e **RowKey**.

    ![Adicionar caixa de diálogo de entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Insira os valores com cuidado porque você não pode alterá-los depois de fechar a caixa de diálogo, a menos que exclua a entidade e adicione novamente.

### Para filtrar entidades

Você pode personalizar o conjunto de entidades que aparecem em uma tabela, se usar o construtor de consultas.

1. Para abrir o construtor de consultas, abra uma tabela para exibir.

1. Escolha o botão mais à direita na barra de ferramentas de exibição de tabela.

    A caixa de diálogo **Construtor de Consultas** é exibida. A ilustração a seguir mostra uma consulta sendo criada no construtor de consultas.

    ![Construtor de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Após criar a consulta, feche a caixa de diálogo. O formulário de texto resultante da consulta aparece em uma caixa de texto como um filtro do WCF Data Services.

1. Para executar a consulta, escolha o ícone de triângulo verde.

    Você também pode filtrar dados de entidade que aparecem no **Designer de tabela** se inserir uma cadeia de caracteres de filtro do WCF Data Services diretamente no campo de filtro. Esse tipo de cadeia de caracteres é semelhante a uma cláusula SQL WHERE, mas é enviado ao servidor como uma solicitação HTTP. Para obter informações sobre como construir cadeias de caracteres de filtro, consulte [Construir cadeias de caracteres de filtro para o Designer de tabela](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    A ilustração a seguir mostra um exemplo de uma cadeia de caracteres de filtro válida:

    ![VST\_SE\_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### Atualizar dados de armazenamento

Quando o Gerenciador de Servidores é conectado ou obtém dados de uma conta de armazenamento, ele pode levar até um minuto para que a operação seja concluída. Se não conseguir conectar, a operação pode atingir o tempo limite. Enquanto os dados são recuperados, você pode continuar a trabalhar em outras partes do Visual Studio. Para cancelar a operação se demorar muito, escolha o botão **Parar atualização** na barra de ferramentas do Gerenciador de Servidores.

#### Para atualizar dados de contêiner de blob

- Selecione o nó **Blobs** sob **Armazenamento** e escolha o botão **atualizar** na barra de ferramentas do Gerenciador de Servidores.

- Para atualizar a lista de blobs que é exibida, escolha o botão **Execute**.

#### Para atualizar dados de tabela

- Selecione o nó **Tabelas** sob **Armazenamento** e escolha o botão **Atualizar**.

- Para atualizar a lista de entidades exibida no **Designer de tabela**, escolha o botão **Executar** no **Designer de tabela**.

#### Para atualizar os dados da fila

- Selecione o nó **Filas** e escolha o botão **Atualizar**.

#### Para atualizar todos os itens em uma conta de armazenamento

- Escolha o nome da conta e, em seguida, escolha o botão **Atualizar** na barra de ferramentas do Gerenciador de Servidores.

### Adicionar contas de armazenamento usando o Gerenciador de Servidores

Há duas maneiras de adicionar contas de armazenamento usando o Gerenciador de Servidores. Você pode criar uma nova conta de armazenamento na sua assinatura do Azure ou pode anexar uma conta de armazenamento existente.

#### Para criar uma nova conta de armazenamento usando o Gerenciador de Servidores

1. No Gerenciador de Servidores, abra o menu de atalho para o nó Armazenamento e escolha Criar Conta de Armazenamento.

    ![Criar uma nova conta de armazenamento do Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Selecione ou insira as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar Conta de Armazenamento**.

    - A assinatura do Azure à qual você deseja adicionar a conta de armazenamento.

    - O nome que você deseja usar para a nova conta de armazenamento.

    - A região ou o grupo de afinidade (como Oeste dos EUA ou Leste Asiático).

    - O tipo de replicação que você deseja usar para a conta de armazenamento, como redundância geográfica.

1. Escolha **Criar**.

    A nova conta de armazenamento aparece na lista **Armazenamento** no Gerenciador de Soluções.

#### Para anexar uma conta de armazenamento existente usando o Gerenciador de Servidores

1. No Gerenciador de Servidores, abra o menu de atalho para o nó Armazenamento do Azure e escolha **Anexar armazenamento externo**.

    ![Adicionar uma conta de armazenamento existente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Selecione ou insira as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar Conta de Armazenamento**.

    - O nome da conta de armazenamento existente que você deseja anexar. Você pode inserir um nome ou selecionar na lista.

    - A chave para a conta de armazenamento selecionado. Esse valor geralmente é fornecido para você quando seleciona uma conta de armazenamento. Se desejar que o Visual Studio se lembre da chave de conta de armazenamento, marque a caixa de chave Lembrar conta.

    - O protocolo a ser usado para se conectar à conta de armazenamento, como HTTP, HTTPS ou um ponto de extremidade personalizado. Para obter mais informações sobre pontos de extremidade personalizados, consulte [Configurar cadeias de conexão](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### Para exibir os pontos de extremidade secundários

- Se você tiver criado uma conta de armazenamento usando a opção de replicação **Redundância Geográfica com Acesso de Leitura**, pode exibir seus pontos de extremidade secundários. Abra o menu de atalho para o nome da conta e, em seguida, escolha **Propriedades**.

    ![Pontos de extremidade de armazenamento secundários](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### Para remover uma conta de armazenamento do Gerenciador de Servidores

- No Gerenciador de Servidores, abra o menu de atalho para o nome da conta e, em seguida, escolha **Excluir**. Se você excluir uma conta de armazenamento, qualquer informação de chave salva para essa conta também é removida.

    >[AZURE.NOTE] Se você excluir uma conta de armazenamento do Gerenciador de servidores, isto não afeta sua conta de armazenamento ou todos os dados que ela contém. Isto simplesmente remove a referência do Gerenciador de Servidores. Para excluir permanentemente uma conta de armazenamento, use o Portal de Gerenciamento do Azure.

## Próximas etapas

Para saber mais sobre como usar os serviços de armazenamento do Azure, consulte [Acessando os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).

<!---HONumber=AcomDC_0309_2016-->
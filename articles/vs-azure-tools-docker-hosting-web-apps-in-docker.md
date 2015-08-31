<properties
   pageTitle="Hospedando aplicativos Web no Docker | Microsoft Azure"
   description="Saiba como usar o Visual Studio para hospedar um aplicativo Web em um contêiner do Docker."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/17/2015"
   ms.author="kempb" />

# Hospedando aplicativos Web no Docker

O [Docker](https://www.docker.com/whatisdocker/) é um mecanismo de contêiner leve, semelhante em alguns pontos a uma máquina virtual, que você pode usar para hospedar aplicativos e serviços. O Visual Studio oferece suporte ao Docker no Ubuntu, no CoreOS e no Windows.

Este exemplo mostra como usar a extensão das Ferramentas para Docker do Visual Studio 2015 para publicar um aplicativo ASP.NET 5 em uma máquina virtual Ubuntu Linux (mencionada aqui como um host do Docker) no Azure com a extensão do Docker instalada, juntamente com um aplicativo Web ASP.NET 5. A mesma experiência pode ser usada para publicar em um contêiner do Windows.

É possível publicar o aplicativo em um novo host do Docker hospedado no Azure, ou em um servidor local, Hyper-V ou host Boot2Docker usando a configuração **Host Personalizado**. Depois de publicar o aplicativo em um host do Docker, você pode usar as ferramentas de linha de comando do Docker para interagir com o contêiner no qual o aplicativo foi publicado.

## Criar e publicar um novo contêiner do Docker

Nestes procedimentos, você vai criar um novo projeto de aplicativo Web ASP.NET 5, criar um host de contêiner e compilar e executar o projeto de aplicativo Web no contêiner do Docker. Para começar, baixe e instale as [Ferramentas para Docker do Visual Studio 2015](aka.ms/DockerToolsForVS).

### Adicionar um projeto de aplicativo Web ASP.NET 5

1. Crie um novo projeto de aplicativo Web ASP.NET. No menu principal, escolha **Arquivo**, **Novo Projeto**. Em **C#**, **Web**, escolha **Aplicativo Web ASP.NET**.

1. Na lista de **Modelos de Visualização do ASP.NET 5**, escolha **Site**.

1. Já que o aplicativo Web será hospedado/executado no Docker, desmarque a caixa de seleção **Host na nuvem**, se estiver marcada, e escolha o botão **OK**.

  ![][0]

  Esse é o ponto em que, normalmente, você adiciona código ao aplicativo Web para que ele faça algo útil, mas, neste exemplo, vamos deixar as configurações padrão. (Observe que você também pode optar por usar os aplicativos Web ASP.NET 5 existentes).

### Publicar o projeto

1. No menu de contexto do projeto ASP.NET, escolha **Publicar**.

1. Na seção **Selecionar um destino de publicação** da caixa de diálogo **Publicar Web**, escolha o botão **Contêineres do Docker**.

    Caso a opção Contêineres do Docker não seja exibida, verifique se instalou as Ferramentas do Visual Studio 2015 para Docker e selecionou um modelo de Site ASP.NET 5 no procedimento anterior.

    ![][1]

    A caixa de diálogo **Selecionar Máquina Virtual do Docker** é exibida. Ela permite que você especifique o host do Docker em que deseja publicar o projeto. É possível criar um novo host do Docker ou escolher uma VM existente hospedada no Azure ou em qualquer outro lugar. Mais adiante neste exemplo, criaremos um novo host do Docker no Azure.

1. Se você já estiver conectado a uma conta do Azure, pule para a etapa 5. Se você não estiver conectado a uma conta, escolha o botão **Adicionar uma conta**.

    ![][2]

1. Na caixa de diálogo **Entrar no Visual Studio**, insira a conta de email da sua assinatura no Azure e escolha o botão **Continuar**.

1. Escolha o botão **Novo** para criar uma nova VM do Docker no Azure e escolha o botão **OK**.

    ![][3]

    Observe que você também tem a opção de usar um host do Docker existente. Para isso, escolha-o na lista suspensa **Máquinas Virtuais Existentes do Docker no Azure**, em vez de escolher o botão **Novo**. Essa lista não mostra somente hosts de contêiner, mas relaciona todas as VMs no seu locatário do Azure.

    Como alternativa, você pode optar por publicar em um host do Docker personalizado. Consulte [Fornecer um host do Docker personalizado](#BKMK_CustomHost), mais adiante neste tópico, para obter mais informações.

1. Insira as informações a seguir na caixa de diálogo **Criar uma máquina virtual no Microsoft Azure**. Quando tiver terminado, escolha o botão **OK**. Isso cria uma máquina virtual Linux com uma extensão do Docker configurada.

    ![][4]

    Observe que agora você também tem a opção de criar um HOST de Contêiner do Windows usando o Windows Server 2016 Technical Preview 3 (TP3).

    ![][8]

    |Nome da Propriedade|Configuração|
    |---|---|
    |Local|Altere essa configuração para a região mais próxima da sua localidade.|
    |Nome DNS|Insira um nome exclusivo para a máquina virtual. Se o nome for aceito pelo Azure, um círculo verde com uma marca de verificação branca aparecerá à direita. Se o nome não for aceito, um círculo vermelho com um x branco será exibido. Nesse caso, insira um novo nome exclusivo.|
    |Imagem|Escolha uma imagem de SO a ser usada no host do Docker, se houver. Neste exemplo, escolha uma imagem do Ubuntu Server. (Observe que uma imagem do Windows Server agora está disponível na lista de imagens disponíveis).|
    |Nome de Usuário|Insira um nome de usuário exclusivo para a máquina virtual.|
    |Senhas|Insira uma senha para o usuário e confirme-a.|
    |Diretório de certificados |Especifica a pasta em que os certificados do Docker estão armazenados. Embora você possa criar uma nova pasta ou apontar para uma existente, é recomendável usar a pasta de certificados padrão (C:\\Usuários[*nome de usuário*]\\.docker). Caso contrário, as opções de autenticação não poderão ser recuperadas automaticamente se você reutilizar o mesmo host em outro projeto ou sistema.|

1. Escolha o botão de reticências (...) ao lado da entrada **Diretório de certificados** e crie uma nova pasta para os certificados do Docker ou navegue até uma pasta de certificados existente do Docker.

    Se os certificados do Docker necessários para a VM não forem encontrados, um ícone de exclamação aparecerá ao lado da entrada, permitindo que você saiba que os certificados exigidos não foram encontrados e que, ao continuar, todos os certificados existentes serão excluídos e depois recriados.

1. Escolha o botão **OK** para começar a criar a VM. Você receberá uma mensagem de que a máquina virtual está sendo criada no Azure.

    O Visual Studio cria um arquivo de modelo ARM (Gerenciador de Recursos do Azure), um arquivo de parâmetros e um script do PowerShell, de modo que você possa executar os comandos novamente no futuro.

    ![][7]

    O Visual Studio mostra o andamento da operação na janela **Saída**. O Visual Studio chama um script do PowerShell para implantar a VM. O script usa cmdlets do Azure PowerShell para implantar o Grupo de Recursos do Azure. Posteriormente, outro script do PowerShell emitirá comandos do Docker a serem publicados, assim como você faria se estivesse criando o host do Docker manualmente.

    O provisionamento do host do Docker pode demorar um pouco. Sendo assim, verifique o status na janela Saída para saber quando o trabalho for concluído.

1. Depois que o host do Docker estiver totalmente provisionado no Azure, você poderá verificar sua conta no portal do Azure. Você deverá ver a nova máquina virtual na categoria **Máquina Virtual** no portal do Azure.

1. Assim que o host do Docker estiver pronto, volte e publique o projeto de aplicativo Web. No menu de contexto do nó do projeto de aplicativo Web, no **Gerenciador de Soluções**, escolha **Publicar**. O Visual Studio cria um arquivo de publicação com base na VM que você criou.

1. Na guia **Conexão** da caixa de diálogo **Publicar Web**, escolha a caixa **Validar Conexão** para ter certeza de que o host do Docker está pronto. Se a conexão for válida, escolha o botão **Publicar** para publicar o aplicativo Web.

    Na primeira vez que você publicar um aplicativo em um host do Docker, levará muito tempo para baixar qualquer uma das imagens base que é mencionada no arquivo do Docker (como **FROM** *nomedaimagem*).

    Observe que o arquivo do Docker é específico do sistema operacional. Se você optar por publicar novamente em um SO diferente, será preciso renomear o arquivo do Docker para que o Visual Studio possa criar um novo padrão com base no SO de destino. Por exemplo, se você publicar primeiro em um contêiner do Linux e posteriormente decidir publicar no Windows, será preciso renomear o arquivo do Docker com um nome exclusivo, como Docker Linux. Depois, quando você publicar no Windows, o Visual Studio recriará o arquivo do Docker padrão para Windows. Posteriormente, quando for publicar novamente um deles, bastará selecionar o arquivo do Docker apropriado para o SO.

## Fornecer um host do Docker personalizado

No procedimento anterior, você teve que criar uma máquina virtual do Docker hospedada no Azure. No entanto, se houver um host do Docker em qualquer outro lugar, você poderá optar por publicar nele, em vez de no Azure.

### Como fornecer um host do Docker personalizado

1. Na caixa de diálogo **Selecionar Máquina Virtual do Docker**, marque a caixa de seleção **Host do Docker Personalizado**.

    ![][5]

1. Escolha o botão **OK**.

1. Na caixa de diálogo **Publicar Web**, adicione valores às configurações na seção **CustomDockerHost**, como a URL do servidor, o nome da imagem, o local do arquivo do Docker e números de porta do contêiner e do host.

    Na seção **Opções Avançadas do Docker**, você pode exibir ou alterar as opções Autenticação e Execução, bem como a linha de comando do Docker.

    ![][6]

1. Depois de inserir todos os valores necessários, escolha o botão **Validar Conexão** para garantir que a conexão com o host do Docker funcione corretamente.

1. Se a conexão funcionar corretamente, você poderá escolher o botão **Avançar** para ver uma lista dos componentes que serão publicados ou poderá escolher o botão **Publicar** para publicar imediatamente o projeto.

## Testar o host do Docker

Agora que o projeto foi publicado em um host do Docker no Azure, você pode testá-lo clicando em suas configurações. Como as ferramentas de linha de comando do Docker são instaladas com a extensão do Visual Studio, é possível emitir comandos ao Docker diretamente de um prompt de comando do Windows.

O procedimento a seguir é para comunicação com um host do Docker implantado no Azure.

### Como testar o host do Docker

1. Abra um prompt de comando do Windows.

1. Atribua o host do Docker e verifique as variáveis de ambiente. Para isso, insira os comandos a seguir no prompt de comando. (Substitua *NameofAzureVM* pelo nome do seu host do Docker).

    ```
    Set DOCKER_HOST=tcp://<NameofAzureVM>.cloudapp.net:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    Invocar esses comandos evita ter que adicionar `–H (Host) tcp://<NameofAzureVM>.cloudapp.net:2376` e `--TLSVERIFY` a cada comando emitido.

1. Agora você pode emitir comandos como esses para verificar se o host do Docker está presente e funcionando.

    |Linha de comando|Descrição|
    |---|---|
    |docker info|Obtenha as informações de versão do Docker.|
    |docker ps|Obtenha uma lista de contêineres em execução.|
    |docker ps –a|Obtenha uma lista de contêineres, incluindo aqueles que estejam parados.|
    |docker logs <Docker container name>|Obtenha um log do contêiner especificado.|
    |docker images|Obtenha uma lista de imagens.|

    Para obter uma lista completa de comandos do Docker, basta inserir o comando `docker` no prompt de comando. Para saber mais, consulte [Linha de comando do Docker](https://docs.docker.com/reference/commandline/cli/).

## Próximas etapas

Agora que você tem um host do Docker, é possível emitir comandos do Docker a ele. Para saber mais sobre o Docker, consulte a [Documentação do Docker](https://docs.docker.com/) e o [Tutorial online do Docker](https://www.docker.com/tryit/).

Em caso de problemas ao usar o Docker no Visual Studio, consulte [Solucionando problemas do Docker](vs-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=August15_HO8-->
<properties linkid="develop-java-how-to-hudson-ci" urlDisplayName="Hudson Continuous Integration" pageTitle="How to use Hudson with the Azure Blob service | Microsoft Azure" metaKeywords="Hudson, Azure storage, Azure Blob service, Azure storage, Azure hudson" description="Describes how to use Hudson with Azure Blob storage as a repository for build artifacts." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Hudson Continuous Integration solution" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Usando o Armazenamento do Azure com uma solução Hudson Continuous Integration

*Por [Microsoft Open Technologies Inc.][]*

As informações a seguir mostram como usar o serviço Blob do Azure como um repositório de artefatos de compilação criado por uma solução Hudson Continuous Integration (CI), ou como uma fonte de arquivos baixáveis a serem usados em um processo de compilação. Um dos cenários em que isso poderia ser útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras linguagens), as compilações estão sendo executadas com base na integração contínua e você precisa de um repositório para seus artefatos de compilação, para que possa, por exemplo, compartilhá-los com outros membros da organização, com seus clientes ou mantê-los em um arquivo. Outro cenário é quando o seu próprio trabalho de compilação requer outros arquivos, por exemplo, dependências a serem baixadas como parte da entrada da compilação.

Neste tutorial, você usará o plug-in Armazenamento do Azure para Hudson CI disponibilizado pela Microsoft Open Technologies, Inc.

## Sumário

-   [Visão geral do Hudson][]
-   [Benefícios do uso do serviço Blob][]
-   [Pré-requisitos][]
-   [Como usar o serviço Blob com o Hudson CI][]
-   [Como instalar o plug-in Armazenamento do Azure][]
-   [Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento][]
-   [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento][]
-   [Como criar uma etapa de compilação baixada do armazenamento de blob do Azure][]
-   [Componentes usados pelo serviço Blob][]

## <span id="overview"></span></a><span class="short header">Visão geral do Hudson</span>Visão geral do Hudson

O Hudson possibilita a integração contínua de um projeto de software permitindo que os desenvolvedores integrem de forma fácil as alterações de código e fazendo com que as compilações sejam produzidas automaticamente e com frequência, aumentando, assim, a produtividade dos desenvolvedores. As compilações têm uma versão e os artefatos de compilação podem ser carregados em vários repositórios. Este tópico mostra como usar o armazenamento de blobs do Azure como o repositório dos artefatos de compilação. Ele também mostra como baixar dependências no armazenamento de blob do Azure.

Mais informações sobre o Hudson podem ser encontradas em [Conheça o Hudson (a página pode estar em inglês)][].

## <span id="benefits"></span></a><span class="short header">Benefícios</span>Benefícios de uso do serviço Blob

Alguns dos benefícios de usar o serviço Blob para hospedar seus artefatos de compilação de desenvolvimento ágil incluem:

-   Alta disponibilidade de seus artefatos de compilação e/ou dependências baixáveis.
-   O desempenho quando sua solução Hudson CI carrega seus artefatos de compilação.
-   Desempenho quando seus clientes e parceiros baixam seus artefatos de compilação.
-   O controle sobre as políticas de acesso do usuário, com uma opção entre acesso anônimo, acesso compartilhado com base em expiração, acesso de assinatura, acesso particular, etc.

## <span id="prerequisites"></span></a><span class="short header">Pré-requisitos</span>Pré-requisitos

Será necessário o seguinte para usar o serviço Blob com a solução Hudson CI:

-   Uma solução Hudson Continuous Integration.

    Se não tem uma solução Hudson CI no momento, você pode executá-la usando a técnica a seguir:

    1.  Em um computador com Java, baixe o Hudson WAR de <http://hudson-ci.org/>.
    2.  Em um prompt de comando aberto para a pasta que contém o Hudson WAR, execute o Hudson WAR. Por exemplo, se você baixou a versão 3.1.2:

        `java -jar hudson-3.1.2.war`

    3.  No navegador, abra `http://localhost:8080/`. Isso abrirá o painel do Hudson.

    4.  Na primeira utilização do Hudson, conclua a configuração inicial em `http://localhost:8080/`.

    5.  Depois de concluir a configuração inicial, cancele a instância em execução do Hudson WAR, inicie o Hudson WAR novamente e reabra o painel do Hudson, `http://localhost:8080/`, que você usará para instalar e configurar o plug-in Armazenamento do Azure.

        Embora uma solução Hudson CI típica possa ser configurada para ser executada como um serviço, a execução do war do Hudson na linha de comando será suficiente para este tutorial.

-   Uma conta do Azure. Você pode criar uma conta do Azure em <http://www.windowsazure.com>.

-   Uma conta de armazenamento do Azure. Se você não tiver uma conta de armazenamento, crie uma usando as etapas em [Como criar uma conta de armazenamento (a página pode estar em inglês)][].

-   Estar familiarizado com a solução Hudson CI é recomendável, mas não é obrigatório, já que o conteúdo a seguir usará um exemplo básico para mostrar a você as etapas necessárias ao usar o serviço Blob como um repositório para os artefatos de compilação do Hudson CI.

## <span id="howtouse"></span></a><span class="short header">Como usar o serviço Blob</span>Como usar o serviço Blob com o Hudson CI

Para usar o serviço Blob com o Hudson, você deverá instalar o plug-in Armazenamento do Azure, configurá-lo para usar sua conta de armazenamento e criar uma ação de pós-compilação que carregue os artefatos de compilação para a sua conta de armazenamento. Essas etapas estão descritas nas seções a seguir.

## <span id="howtoinstall"></span></a><span class="short header">Como instalar</span>Como instalar o plug-in Armazenamento do Azure

1.  No painel do Hudson, clique em **Manage Hudson**.
2.  Na página **Manage Hudson**, clique em **Manage Plugins**.
3.  Clique na guia **Disponível**.
4.  Clique em **Others**.
5.  Na seção **Carregadores de Artefatos**, marque **Plug-in de Armazenamento do Microsoft Azure**.
6.  Clique em **Install**.
7.  Após a conclusão da instalação, reinicie o Hudson.

## <span id="howtoconfigure"></span></a><span class="short header">Como configurar</span>Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento

1.  No painel do Hudson, clique em **Manage Hudson**.
2.  Na página **Manage Hudson**, clique em **Configure System**.
3.  Na seção **Configuração da Conta de Armazenamento do Microsoft Azure**:
    1.  Insira seu nome de conta de armazenamento, que pode ser obtido no portal do Azure, <https://manage.windowsazure.com>.
    2.  Insira a chave de sua conta de armazenamento, que também pode ser obtida no portal do Azure.
    3.  Use o valor padrão para **URL de Ponto de Extremidade de Serviço Blob**, se você estiver usando a nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade, conforme especificado no portal de gerenciamento do Azure, para a sua conta de armazenamento.
    4.  Clique em **Validar credenciais de armazenamento** para validar sua conta de armazenamento.
    5.  [Opcional] Se você tiver contas de armazenamento adicionais que deseja disponibilizar para o Hudson CI, clique em **Add more Storage Accounts**.
    6.  Para salvar suas configurações, clique em **Salvar**.

## <span id="howtocreatepostbuild"></span></a><span class="short header">Como criar uma ação pós-compilação</span>Como criar uma ação pós-compilação que carrega os artefatos de compilação na sua conta de armazenamento

Para fins de instrução, primeiro será necessário criar um trabalho que crie vários arquivos e, em seguida, adicioná-lo à ação de pós-compilação para carregar os arquivos para a sua conta de armazenamento.

1.  No painel do Hudson, clique em **New Job**.
2.  Nomeie o trabalho como **MyJob**, clique em **Build a free-style software job** e em **OK**.
3.  Na seção **Compilar** da configuração do trabalho, clique em **Adicionar etapa de compilação** e selecione **Executar comando em lote do Windows**.
4.  Em **Comando**, use os seguintes comandos:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5.  Na seção **Post-build Actions** de configuração do trabalho, clique em **Upload artifacts to Microsoft Azure Blob storage**.
6.  Em **Nome de Conta de Armazenamento**, selecione a conta de armazenamento a ser usada.
7.  Em **Nome do Contêiner**, especifique o nome do contêiner. (O contêiner será criado se ele ainda não existir quando os artefatos de compilação forem carregados.) É possível usar variáveis do ambiente, portanto, para este exemplo, insira **${JOB\_NAME}** como o nome do contêiner.

    **Dica**

    Abaixo da seção **Command** em que você inseriu um script para **Execute Windows batch command**, existe um link para as variáveis de ambiente reconhecidas pelo Hudson. Clique nesse link para obter os nomes de variáveis de ambiente e as descrições. Observe que as variáveis de ambiente que contêm caracteres especiais, como a variável de ambiente **BUILD\_URL**, não são permitidas como um nome de contêiner ou um caminho virtual comum.

8.  Clique em **Tornar o novo contêiner público por padrão** para este exemplo. Se desejar usar um contêiner particular, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Isso está além do escopo deste tópico. Você pode saber mais sobre assinaturas de acesso compartilhado em [Criando uma assinatura de acesso compartilhado (a página pode estar em inglês)][].
9.  [Opcional] Clique em **Limpar contêiner antes de carregar** se quiser que o contêiner seja limpo de conteúdo antes que os artefatos de compilação sejam carregados (deixe a opção desmarcada se não quiser limpar o conteúdo do contêiner).
10. Em **Lista de Artefatos a serem carregados**, insira \*\*text/\*.txt\*\*.
11. Em **Common virtual path for uploaded artifacts**, digite **${BUILD\_ID}/${BUILD\_NUMBER}**.
12. Para salvar suas configurações, clique em **Salvar**.
13. No painel do Hudson, clique em **Build Now** para executar **MyJob**. Examine a saída do console para o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação iniciar o carregamento dos artefatos de compilação.
14. Após a conclusão bem-sucedida do trabalho, você poderá examinar os artefatos de compilação abrindo o blob público.

    1.  Faça logon no Portal de Gerenciamento do Azure <https://manage.windowsazure.com>.
    2.  Clique em **Armazenamento**.
    3.  Clique no nome da conta de armazenamento usada para o Hudson.
    4.  Clique em **Contêineres**.
    5.  Clique no contêiner chamado **myjob**, que é a versão em letra minúscula do nome do trabalho que você atribuiu ao criar o trabalho do Hudson. Os nomes de contêineres e de blob são escritos em letra minúscula (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Na lista de blobs do contêiner chamado **myjob**, você deverá visualizar **hello.txt** e **date.txt**. Copie a URL de um desses itens e abra-a em seu navegador. Você visualizará o arquivo de texto carregado como um artefato de compilação.

Somente uma ação pós-compilação que carrega artefatos no armazenamento de blob do Azure pode ser criada por trabalho. Observe que uma única ação pós-compilação para carregar artefatos no armazenamento de blob do Azure pode especificar arquivos diferentes (inclusive curingas) e caminhos a arquivos dentro da **Lista de Artefatos a serem carregados** usando um ponto e vírgula como o separador. Por exemplo, se a compilação Hudson produzir arquivos JAR e arquivos TXT na pasta **build**, e você quiser carregar ambos no armazenamento de blobs do Azure, use o seguinte no valor **List of Artifacts to upload**: **build/\*.jar;build/\*.txt**. Você também pode usar a sintaxe de dois-pontos duplos para especificar um caminho a ser usado dentro do nome do blob. Por exemplo, se você quiser que os JARs sejam carregados usando **binários** no caminho do blob e quiser que os arquivos TXT sejam carregados usando **notificações** no caminho do blob, use o seguinte para o valor **List of Artifacts to upload**: **build/\*.<jar::binaries;build/>\*.txt::notices**.

## <a name="howtocreatebuildstep"></a><span class="short header">Como criar uma etapa de compilação</span>Como criar uma etapa de compilação baixada do armazenamento de blob do Azure

As etapas a seguir mostram como configurar uma etapa de compilação para baixar itens no armazenamento de blob do Azure. Isso pode ser útil se você quiser incluir itens na compilação, por exemplo, JARs que você mantém no armazenamento de blobs do Azure.

1.  Na seção **Compilar** da configuração do trabalho, clique em **Adicionar etapa de compilação** e selecione **Baixar no armazenamento de Blob do Azure**.
2.  Em **Nome de conta de armazenamento**, selecione a conta de armazenamento a ser usada.
3.  Em **Nome do contêiner**, especifique o nome do contêiner que contém os blobs que você quer baixar. É possível usar variáveis de ambiente.
4.  Em **Nome do blob**, especifique o nome do blob. É possível usar variáveis de ambiente. Além disso, você pode usar um asterisco como um curinga depois de especificar a letra inicial do nome do blob. Por exemplo, **project\*** deve especificar todos os blobs cujos nomes começam com **project**.
5.  [Opcional] Em **Download path**, especifique o caminho na máquina Hudson, onde você quer baixar arquivos do armazenamento de blobs do Azure. Também é possível usar variáveis de ambiente. (Se você não fornecer um valor para **Caminho do download**, os arquivos no armazenamento de blob do Azure serão baixados no espaço de trabalho da tarefa.)

Se houver itens adicionais que deseja baixar do armazenamento de blobs do Azure, você poderá criar etapas de compilação adicionais.

Depois de executar uma compilação, você pode verificar a saída do console de histórico da compilação ou verificar o local de download para ver se os blobs esperados foram baixados com êxito.

## <span id="components"></span></a><span class="short header">Componentes do serviço Blob</span>Componentes usados pelo serviço Blob

Segue abaixo uma visão geral dos componentes do serviço Blob.

-   **Conta de Armazenamento**: Todo o acesso ao Armazenamento do Azure é feito por meio de uma conta de armazenamento. Este é o nível mais alto do namespace para o acesso de blobs. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 100 TB.
-   **Contêiner**: Um contêiner fornece um agrupamento de um conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.
-   **Blob**: Um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de bloco. Um único blob de bloco pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de página, um outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções gerais sobre blobs de blocos e blobs de páginas (a página pode estar em inglês)][].
-   **Formato de URL**: Os blobs são endereçáveis usando o seguinte formato de URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (O formato acima aplica-se a uma nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade localizado no portal de gerenciamento do Azure para determinar o ponto de extremidade de sua URL.

    No formato acima, `storageaccount` representa o nome de sua conta de armazenamento, `container_name` representa o nome de seu contêiner e `blob_name` representa o nome de seu blob, respectivamente. Dentro do nome do contêiner, é possível ter vários caminhos, separados por uma barra, **/**. O exemplo de nome do contêiner neste tutorial foi **MyJob** e **${BUILD\_ID}/${BUILD\_NUMBER}** foi usado para o caminho virtual comum, fazendo com que o blob tivesse uma URL no seguinte formato:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

  [Microsoft Open Technologies Inc.]: http://msopentech.com
  [Visão geral do Hudson]: #overview
  [Benefícios do uso do serviço Blob]: #benefits
  [Pré-requisitos]: #prerequisites
  [Como usar o serviço Blob com o Hudson CI]: #howtouse
  [Como instalar o plug-in Armazenamento do Azure]: #howtoinstall
  [Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento]: #howtoconfigure
  [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento]: #howtocreatepostbuild
  [Como criar uma etapa de compilação baixada do armazenamento de blob do Azure]: #howtocreatebuildstep
  [Componentes usados pelo serviço Blob]: #components
  [Conheça o Hudson (a página pode estar em inglês)]: http://wiki.eclipse.org/Hudson-ci/Meet_Hudson
  [Como criar uma conta de armazenamento (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Criando uma assinatura de acesso compartilhado (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=279889
  [Noções gerais sobre blobs de blocos e blobs de páginas (a página pode estar em inglês)]: http://msdn.microsoft.com/en-us/library/windowsazure/ee691964.aspx

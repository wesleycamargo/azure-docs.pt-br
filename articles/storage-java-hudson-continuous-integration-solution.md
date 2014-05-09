<properties linkid="develop-java-how-to-hudson-ci" urlDisplayName="Integração contínua com o Hudson" pageTitle="Como usar o Hudson com o serviço Blob do Azure | Microsoft Azure" metaKeywords="Hudson, armazenamento do Azure, serviço Blob do Azure, armazenamento do Azure, Hudson para Azure" description="Descreve como usar o Hudson com o Armazenamento do blob do Azure como um repositório para artefatos de compilação." metaCanonical="" services="storage" documentationCenter="Java" title="Usando o Armazenamento do Azure com uma solução de integração contínua Hudson" authors="waltpo" solutions="" manager="" editor="mollybos" />

#Usando o Armazenamento do Azure com uma solução de integração contínua Hudson

*Por [Microsoft Open Technologies Inc.][ms-open-tech]*

As informações a seguir mostram como usar o serviço Blob do Azure como um repositório de artefatos de compilação criados por uma solução de CI (Continuous Integration - Integração Contínua) Hudson. Um dos cenários em que isso poderia ser útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras linguagens), as compilações estão sendo executadas com base na integração contínua e você precisa de um repositório para seus artefatos de compilação, para que possa, por exemplo, compartilhá-los com outros membros da organização, com seus clientes ou mantê-los em um arquivo. 

Neste tutorial, você usará o plug-in Armazenamento do Azure para Hudson CI disponibilizado pela Microsoft Open Technologies, Inc.

## Sumário

-   [Visão geral do Hudson][]
-   [Benefícios do uso do serviço Blob][]
-   [Pré-requisitos][]
-   [Como usar o serviço Blob com o Hudson CI][]
-   [Como instalar o plug-in Armazenamento do Azure][]
-   [Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento][]
-   [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento][]
-   [Componentes usados pelo serviço Blob][]

<h2><a id="overview"></a><span class="short header">Visão geral</span>Visão geral do Hudson</h2>
O Hudson possibilita a integração contínua de um projeto de software permitindo que os desenvolvedores integrem de forma fácil as alterações de código e fazendo com que as compilações sejam produzidas automaticamente e com frequência, aumentando, assim, a produtividade dos desenvolvedores. As compilações têm uma versão e os artefatos de compilação podem ser carregados em vários repositórios. Este tópico mostra como usar o armazenamento de blobs do Azure como o repositório dos artefatos de compilação.

Mais informações sobre o Hudson podem ser encontradas em [Conheça o Hudson (a página pode estar em inglês)][].

<h2><a id="benefits"></a><span class="short header">Benefícios</span>Benefícios do uso do serviço Blob</h2>

Alguns dos benefícios de usar o serviço Blob para hospedar seus artefatos de compilação de desenvolvimento ágil incluem:

- A alta disponibilidade de seus artefatos de compilação.
- O desempenho quando sua solução Hudson CI carrega seus artefatos de compilação.
- O desempenho quando seus clientes e parceiros baixam seus artefatos de compilação.
- O controle sobre as políticas de acesso de usuário, com a opção de acesso anônimo, acesso compartilhado com base em expiração, acesso de assinatura, acesso particular, etc.

<h2><a id="prerequisites"></a><span class="short header">Pré-requisitos</span>Pré-requisitos</h2>

Será necessário o seguinte para usar o serviço Blob com a solução Hudson CI:

- Uma solução de integração contínua Hudson.

    Se não tem uma solução Hudson CI no momento, você pode executá-la usando a técnica a seguir:

    1. Em um computador habilitado para Java, baixe o WAR do Hudson de <http://hudson-ci.org/>.
    2. Em um prompt de comando aberto para a pasta que contém WAR do Hudson, execute o WAR. Por exemplo, se você baixou a versão 3.0.1:

        `java -jar hudson-3.0.1.war`

    3. Em seu navegador, abra `http://localhost:8080/`. Isso abrirá o painel do Hudson.

    4. Na primeira utilização do Hudson, conclua a configuração inicial em `http://localhost:8080/`. 

    5. Depois de concluir a configuração inicial, cancele a instância em execução do WAR do Hudson, inicie o WAR do Hudson novamente e reabra o painel do Hudson, `http://localhost:8080/`, que você usará para instalar e configurar o plug-in Armazenamento do Azure.

        Embora uma solução Hudson CI típica possa ser configurada para ser executada como um serviço, a execução do war do Hudson na linha de comando será suficiente para este tutorial.

- Uma conta do Azure. Você pode se inscrever para uma conta do Azure em <http://www.windowsazure.com>.

- Uma conta de armazenamento do Azure. Se você não tiver uma conta de armazenamento, crie uma usando as etapas em [Como criar uma conta de armazenamento (a página pode estar em inglês)][].

- Estar familiarizado com a solução Hudson CI é recomendável, mas não é obrigatório, já que o conteúdo a seguir usará um exemplo básico para mostrar a você as etapas necessárias ao usar o serviço Blob como um repositório para os artefatos de compilação do Hudson CI.

<h2><a id="howtouse"></a><span class="short header">Como usar o serviço Blob</span>Como usar o serviço Blob com o Hudson CI</h2>

Para usar o serviço Blob com o Hudson, você deverá instalar o plug-in Armazenamento do Azure, configurá-lo para usar sua conta de armazenamento e criar uma ação de pós-compilação que carregue os artefatos de compilação para a sua conta de armazenamento. Essas etapas estão descritas nas seções a seguir.

<h2><a id="howtoinstall"></a><span class="short header">Como instalar</span>Como instalar o plug-in Armazenamento do Azure</h2>

1. No painel do Hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson**, clique em **Manage Plugins**.
3. Clique na guia **Available**.
4. Clique em **Others**.
5. Na seção **Artifact Uploaders**, marque **Azure Storage plugin**.
6. Clique em **Install**.
7. Após a conclusão da instalação, reinicie o Hudson.

<h2><a id="howtoconfigure"></a><span class="short header">Como configurar</span>Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento</h2>

1. No painel do Hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson**, clique em **Configure System**.
3. Na seção **Azure Storage Account Configuration**:
    1. Digite o nome de sua conta de armazenamento, que pode ser obtido no portal do Azure, em <https://manage.windowsazure.com>.
    2. Digite a chave de sua conta de armazenamento, que também pode ser obtida no portal do Azure.
    3. Use o valor padrão para **Blob Service Endpoint URL** se você estiver usando a nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade, conforme especificado no portal de gerenciamento do Azure, para a sua conta de armazenamento. 
    4. Clique em **Validate Storage Credentials** para validar sua conta de armazenamento. 
    5. [Opcional] Se você tiver contas de armazenamento adicionais que deseja disponibilizar para o Hudson CI, clique em **Add more Storage Accounts**.
    6. Para salvar suas configurações, clique em **Save**.

<h2><a id="howtocreatepostbuild"></a><span class="short header">Como criar uma ação de pós-compilação</span>Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento</h2>

Para fins de instrução, primeiro será necessário criar um trabalho que crie vários arquivos e, em seguida, adicioná-lo à ação de pós-compilação para carregar os arquivos para a sua conta de armazenamento.

1. No painel do Hudson, clique em **New Job**.
2. Nomeie o trabalho como **MyJob**, clique em **Build a free-style software job** e em **OK**.
3. Na seção **Build** da configuração do trabalho, clique em **Add build step** e selecione **Execute Windows batch command**.
4. Em **Command**, use os seguintes comandos:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Na seção **Post-build Actions** da configuração do trabalho, clique em **Upload artifacts to Azure Blob storage**.
6. Para **Storage Account Name**, selecione a conta de armazenamento a ser usada.
7. Para **Container Name**, especifique o nome do contêiner. (O contêiner será criado se ele ainda não existir quando os artefatos de compilação forem carregados.) Como é possível usar variáveis de ambiente, para este exemplo digite **${JOB_NAME}** como o nome do contêiner.

    **Dica**
    
    Abaixo da seção **Command** em que você inseriu um script para **Execute Windows batch command**, existe um link para as variáveis de ambiente reconhecidas pelo Hudson. Clique nesse link para obter os nomes de variáveis de ambiente e as descrições. Observe que as variáveis de ambiente que contêm caracteres especiais, como a variável de ambiente **BUILD_URL**, não são permitidas como um nome de contêiner ou um caminho virtual comum.

8. Clique em **Make container public** para este exemplo. Se desejar usar um contêiner particular, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Isso está além do escopo deste tópico. Você pode saber mais sobre assinaturas de acesso compartilhado em [Criando uma assinatura de acesso compartilhado (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=279889).
9. Em **List of Artifacts to upload**, digite **text/*.txt**.
10. Em **Common virtual path for uploaded artifacts**, digite **${BUILD\_ID}/${BUILD\_NUMBER}**.
11. Para salvar suas configurações, clique em **Save**.
12. No painel do Hudson, clique em **Build Now** para executar **MyJob**. Examine a saída do console para o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação iniciar o carregamento dos artefatos de compilação.
13. Após a conclusão com êxito do trabalho, você poderá examinar os artefatos de compilação abrindo o blob público.
    1. Faça logon no portal de gerenciamento do Azure, em <https://manage.windowsazure.com>.
    2. Clique em **Armazenamento**.
    3. Clique no nome da conta de armazenamento usada para o Hudson.
    4. Clique em **Contêineres**.
    5. Clique no contêiner chamado **myjob**, que é a versão em letra minúscula do nome do trabalho que você atribuiu ao criar o trabalho do Hudson. Os nomes de contêineres e de blob são escritos em letra minúscula (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Na lista de blobs do contêiner chamado **myjob**, você deverá visualizar **hello.txt** e **date.txt**. Copie a URL de um desses itens e abra-a em seu navegador. Você visualizará o arquivo de texto carregado como um artefato de compilação.

<h2><a id="components"></a><span class="short header">Componentes do serviço Blob</span>Componentes usados pelo serviço Blob</h2>

Segue abaixo uma visão geral dos componentes do serviço Blob.

- **Conta de Armazenamento**: Todo o acesso ao Armazenamento do Azure é feito por meio de uma conta de armazenamento. Este é o nível mais alto do namespace para o acesso a blobs. Uma conta pode conter um número ilimitado de contêineres, contanto que seu tamanho total esteja abaixo de 100 TB.
- **Contêiner**: Um contêiner fornece um agrupamento de um conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.
- **Blob**: Um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de bloco. Um único blob de bloco pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de páginas, outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções gerais sobre blobs de blocos e blobs de páginas (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee691964.aspx).
- **Formato de URL**: Os blobs podem ser endereçados usando o seguinte formato de URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    O formato acima se aplica à nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade localizado no portal de gerenciamento do Azure para determinar o ponto de extremidade de sua URL.

    No formato acima, `storageaccount` representa o nome de sua conta de armazenamento, `container_name` representa o nome de seu contêiner e `blob_name` representa o nome de seu blob, respectivamente. Dentro do nome do contêiner, é possível ter vários caminhos, separados por uma barra, **/**. O exemplo de nome do contêiner neste tutorial foi **MyJob**, e **${BUILD\_ID}/${BUILD\_NUMBER}** foi usado para o caminho virtual comum, fazendo com que o blob tivesse uma URL no seguinte formato:

    `http://example.blob.core.windows.net/myjob/2013-06-06_11-56-22/1/hello.txt`

  [Visão geral do Hudson]: #overview
  [Benefícios do uso do serviço Blob]: #benefits
  [Pré-requisitos]: #prerequisites
  [Como usar o serviço Blob com o Hudson CI]: #howtouse
  [Como instalar o plug-in Armazenamento do Azure]: #howtoinstall
  [Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento]: #howtoconfigure
  [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento]: #howtocreatepostbuild
  [Componentes usados pelo serviço Blob]: #components
  [Como criar uma conta de armazenamento (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Conheça o Hudson (a página pode estar em inglês)]: http://wiki.eclipse.org/Hudson-ci/Meet_Hudson
  [ms-open-tech]: http://msopentech.com



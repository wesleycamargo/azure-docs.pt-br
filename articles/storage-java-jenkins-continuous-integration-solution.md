<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Integração Contínua Jenkins" pageTitle="Usando o Armazenamento do Azure com uma solução de Integração Contínua Jenkins | Microsoft Azure" metaKeywords="" description="Este tutorial mostra como usar o serviço de blobs do Azure como um repositório para artefatos de compilação criados por uma solução de integração contínua Jenkins." metaCanonical="" services="storage" documentationCenter="Java" title="Usando o Armazenamento do Azure com uma solução de Integração Contínua Jenkins" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

#Usando o Armazenamento do Azure com uma solução de Integração Contínua Jenkins

*Por [Microsoft Open Technologies Inc.][ms-open-tech]*

As informações a seguir mostram como usar o serviço de blobs do Azure como um repositório para artefatos de compilação criados por uma solução de Integração Contínua (CI) Jenkins. Um dos cenários em que isso poderia ser útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando o Java ou outras linguagens) e as compilações estão sendo executadas com base na integração contínua e você precisa de um repositório para seus artefatos de compilação, para que você possa, por exemplo, compartilhá-los com outros membros da organização, seus clientes ou mantê-los em um arquivo. 

Neste tutorial, você usará o Plug-in do Armazenamento do Azure para a Jenkins CI disponibilizado pela Microsoft Open Technologies, Inc.

## Sumário

-   [Visão geral da Jenkins][]
-   [Vantagens de usar o serviço de blobs][]
-   [Pré-requisitos][]
-   [Como usar o serviço de blobs com a Jenkins CI][]
-   [Como instalar o plug-in do Armazenamento do Azure][]
-   [Como configurar o plug-in do Armazenamento do Azure para usar sua conta de armazenamento][]
-   [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para sua conta de armazenamento][]
-   [Componentes usados pelo serviço de blobs][]

<h2><a name="overview"></a><span class="short header">Visão geral</span>Visão geral da Jenkins</h2>
A Jenkins permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem de forma fácil as alterações de código e fazendo com que as compilações sejam produzidas automaticamente e com frequência, aumentando, assim, a produtividade dos desenvolvedores. As compilações têm uma versão e os artefatos de compilação podem ser carregados em vários repositórios. Este tópico mostra como usar o armazenamento de blobs do Azure como o repositório dos artefatos de compilação.

Mais informações sobre a Jenkins pode ser encontrada em [Conheça a Jenkins][].

<h2><a name="benefits"></a><span class="short header">Vantagens</span>Vantagens de usar o serviço de blobs</h2>

Algumas das vantagens de usar o serviço de blobs para hospedar os artefatos de compilação do desenvolvimento ágil incluem:

- Alta disponibilidade dos seus artefatos de compilação.
- Desempenho, quando a solução Jenkins CI carrega seus artefatos de compilação.
- Desempenho, quando seus clientes e parceiros baixam seus artefatos de compilação.
- Controle sobre as políticas de acesso de usuário, com a opção de acesso anônimo, acesso de assinatura de acesso compartilhado com base em expiração, acesso particular, etc.

<h2><a name="prerequisites"></a><span class="short header">Pré-requisitos</span>Pré-requisitos</h2>

Será necessário o seguinte para usar o serviço de blobs com a solução CI Jenkins:

- Uma solução de Integração Contínua Jenkins.

    Se atualmente você não tiver uma solução Jenkins CI, você pode executá-la usando a técnica a seguir:

    1. Em um computador habilitado para Java, baixe jenkins.war de <http://jenkins-ci.org>.
    2. Em um prompt de comando aberto para a pasta que contém jenkins.war, execute:

        `java -jar jenkins.war`

    3. Em seu navegador, abra `http://localhost:8080/`. Isso abrirá o painel da Jenkins, que você usará para instalar e configurar o plug-in do Armazenamento do Azure.

        Embora uma solução Jenkins CI típica seria configurada para ser executada como um serviço, executar o Jenkins.war na linha de comando será suficiente para este tutorial.

- Uma conta do Azure. Você pode se inscrever para uma conta do Azure em <http://www.windowsazure.com>.

- Uma conta de armazenamento do Azure. Se você não tiver uma conta de armazenamento, crie uma usando as etapas em [Como criar uma Conta de armazenamento][].

- Estar familiarizado com a solução Jenkins CI é recomendável, mas não é necessário, já que o conteúdo a seguir usará um exemplo básico para mostrar as etapas necessárias ao usar o serviço de blobs como um repositório para os artefatos de compilação da Jenkins CI.

<h2><a name="howtouse"></a><span class="short header">Como usar o serviço de blobs</span>Como usar o serviço de blobs com a Jenkins CI</h2>

Para usar o serviço de blobs com a Jenkins, você precisará instalar o plug-in do Armazenamento do Azure, configurá-lo para usar sua conta de armazenamento e criar uma ação de pós-compilação que carregue os artefatos de compilação para a sua conta de armazenamento. Essas etapas são descritas nas seções a seguir.

<h2><a name="howtoinstall"></a><span class="short header">Como instalar</span>Como instalar o plug-in do Armazenamento do Azure</h2>

1. No painel da Jenkins, clique em **Gerenciar Jenkins**.
2. Na página **Gerenciar Jenkins**, clique em **Gerenciar plug-ins**.
3. Clique na guia **Disponível**.
4. Na seção **Carregadores de artefatos**, marque a opção **Plug-in do Armazenamento do Azure**.
5. Clique em **Instalar sem reinicialização** ou **Baixar agora e instalar após a reinicialização**.
6. Reinicie a Jenkins.

<h2><a name="howtoconfigure"></a><span class="short header">Como configurar</span>Como configurar o plug-in do Armazenamento do Azure para usar sua conta de armazenamento</h2>

1. No painel da Jenkins, clique em **Gerenciar Jenkins**.
2. Na página **Gerenciar Jenkins**, clique em **Configurar Sistema**.
3. Na seção **Configuração da Conta de armazenamento do Azure**:
    1. Digite o nome da sua conta de armazenamento, que pode ser obtido no portal do Azure, <https://manage.windowsazure.com>.
    2. Digite a chave da sua conta de armazenamento, que também pode ser obtida no portal do Azure.
    3. Use o valor padrão para **URL do ponto de extremidade do serviço de blobs** se você estiver usando a nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade como especificado no portal de gerenciamento do Azure para a sua conta de armazenamento. 
    4. Clique em **Validar Credenciais de armazenamento** para validar a sua conta de armazenamento. 
    5. [Opcional] Se tiver contas de armazenamento adicionais que deseje que sejam disponibilizadas para a Jenkins CI, clique em **Adicionar mais contas de armazenamento**.
    6. Para salvar suas configurações, clique em **Salvar**.

<h2><a name="howtocreatepostbuild"></a><span class="short header">Como criar uma ação de pós-compilação</span>Como criar uma ação de pós-compilação que carrega os artefatos de compilação para sua conta de armazenamento</h2>

Para fins de instrução, primeiro você precisará criar um trabalho que crie vários arquivos e, em seguida, adicione-o à ação de pós-compilação para carregar os arquivos para sua conta de armazenamento.

1. No painel da Jenkins, clique em **Novo trabalho**.
2. Nomeie o trabalho **MyJob**, clique em **Criar um projeto de software livre de estilo**e, em seguida, clique em **OK**.
3. Na seção **Criar** da configuração do trabalho, clique em **Adicionar etapa de compilação** e selecione **Executar comando em lote do Windows**.
4. Em **Comando**, use os seguintes comandos:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Nas **Ações pós-compilação** da configuração do trabalho, clique em **Adicionar ação pós-compilação** e selecione **Carregar artefatos para o Armazenamento de blobs do Azure**.
6. Para **Nome da conta de armazenamento**, selecione a conta de armazenamento que será usada.
7. Para **Nome do contêiner**, especifique o nome do contêiner. (O contêiner será criado se ele ainda não existir quando os artefatos de compilação forem carregados.) Como é possível usar variáveis de ambiente, para este exemplo digite **${JOB_NAME}** como o nome do contêiner.

    **Dica**
    
    Abaixo da seção **Comando** onde você inseriu um script para **Executar comando em lote do Windows** existe um link para as variáveis de ambiente reconhecidas pela Jenkins. Clique nesse link para saber os nomes de variáveis de ambiente e as descrições. Observe que as variáveis de ambiente que contêm caracteres especiais, como a variável de ambiente **BUILD_URL** não são permitidas como um nome de contêiner ou um caminho virtual comum.

8. Clique em **Tornar o contêiner público** para este exemplo. (Se desejar usar um contêiner particular, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Isto está além do escopo deste tópico. Você pode saber mais sobre assinaturas de acesso compartilhado em [Criando uma assinatura de acesso compartilhado](http://go.microsoft.com/fwlink/?LinkId=279889).)
9. Para a **Lista de artefatos a serem carregados**, digite **text/*.txt**
10. Para **Caminho virtual comum para os artefatos carregados**, digite **${BUILD\_ID}/${BUILD\_NUMBER}**.
11. Para salvar suas configurações, clique em **Salvar**.
12. No painel da Jenkins, clique em **Criar agora** para executar **MyJob**. Examine a saída do console para o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação iniciar o carregamento dos artefatos de compilação.
13. Após a conclusão com êxito do trabalho, você pode examinar os artefatos de compilação abrindo o blob público.
    1. Faça logon no Portal de Gerenciamento do Azure em <https://manage.windowsazure.com>.
    2. Clique em **Armazenamento**.
    3. Clique no nome da conta de armazenamento usada para a Jenkins.
    4. Clique em **Contêineres**.
    5. Clique no contêiner chamado **myjob**, que é a versão em letra minúscula do nome da tarefa atribuída ao criar o trabalho na Jenkins. Os nomes de contêineres e de blob são escritos em letra minúscula (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Na lista de blobs do contêiner chamado **myjob**, você deverá visualizar **hello.txt** e **date.txt**. Copie a URL para um desses itens e abra-a em seu navegador. Você visualizará o arquivo de texto carregado como um artefato de compilação.

<h2><a name="components"></a><span class="short header">Componentes do serviço de blobs</span>Componentes usados pelo serviço de blobs</h2>

Segue abaixo uma visão geral dos componentes do serviço de blobs:

- **Conta de Armazenamento**: Todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Este é o nível mais alto do namespace para o acesso de blobs. Uma conta pode conter um número ilimitado de contêineres, contanto que seu tamanho total esteja abaixo de 100 TB.
- **Contêiner**: Um contêiner fornece um agrupamento de um conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.
- **Blob**: Um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de página e de bloco. A maioria dos arquivos são blobs de bloco. Um único blob de bloco pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de página, um outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções gerais sobre Blobs de bloco e Blobs de página](http://msdn.microsoft.com/pt-br/library/windowsazure/ee691964.aspx).
- **Formato de URL:**: Os blobs são endereçáveis usando o seguinte formato de URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (O formato acima se aplica a nuvem pública do Azure. Se estiver usando uma nuvem do Azure diferente, use o ponto de extremidade localizado no portal de gerenciamento do Azure para determinar o ponto de extremidade de sua URL.)

    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_nome` representa o nome do seu contêiner e `blob_nome` representa o nome do blob, respectivamente. Dentro do nome do contêiner, é possível ter vários caminhos, separados por uma barra invertida **/**. O exemplo de nome do contêiner neste tutorial foi **MyJob** e **${BUILD\_ID}/${BUILD\_NUMBER}** foi usado para o caminho virtual comum, fazendo com que o blob tivesse uma URL no seguinte formato:

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`

  [Visão geral da Jenkins]: #overview
  [Vantagens de usar o serviço de blobs]: #benefits
  [Pré-requisitos]: #prerequisites
  [Como usar o serviço de blobs com a Jenkins CI]: #howtouse
  [Como instalar o plug-in do Armazenamento do Azure]: #howtoinstall
  [Como configurar o plug-in do Armazenamento do Azure para usar sua conta de armazenamento]: #howtoconfigure
  [Como criar uma ação de pós-compilação que carrega os artefatos de compilação para sua conta de armazenamento]: #howtocreatepostbuild
  [Componentes usados pelo serviço de blobs]: #components
  [Como criar uma conta de armazenamento]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Conheça a Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
  [ms-open-tech]: http://msopentech.com


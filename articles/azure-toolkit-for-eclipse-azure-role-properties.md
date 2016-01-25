<properties
    pageTitle="Propriedades da função do Azure"
    description="Saiba como usar o Kit de Ferramentas do Azure para Eclipse para definir as configurações de função do Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="01/09/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# Propriedades da função do Azure #

É possível definir várias configurações para sua função do Azure dentro do Kit de Ferramentas do Azure para Eclipse.

## Configuração das propriedades da função do Azure ##

A configuração das propriedades de função do Azure é feita por meio de caixas de diálogo de propriedade para sua função de trabalho. Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse e selecione o submenu **Azure**. (Se você não vir a função no Gerenciador de Projeto do Eclipse, expanda seu projeto do Azure no Gerenciador de Projeto.)

![][ic789599]

Este tópico descreve as diversas propriedades que podem ser definidas nas caixas de diálogo **Propriedades**. Observe que muitas propriedades são preenchidas automaticamente quando você cria um novo projeto de implantação do Azure.

As páginas de propriedade a seguir estão disponíveis para funções do Azure.

* [Propriedades de máquina virtual](#virtual_machine_properties)
* [Propriedades de caching](#caching_properties)
* [Propriedades de certificados](#certificates_properties)
* [Propriedades de componentes](#components_properties)
* [Propriedades de depuração](#debugging_properties)
* [Propriedades de pontos de extremidade](#endpoints_properties)
* [Propriedades de variáveis do ambiente](#environment_variables_properties)
* [Propriedades de balanceamento de carga/afinidade (também conhecidas como "sessões temporárias")](#session_affinity_properties)
* [Propriedades de armazenamento local](#local_storage_properties)
* [Propriedades de configuração do servidor](#server_configuration_properties)
* [Propriedades de descarregamento de SSL](#ssl_offloading_properties)
	
<a name="virtual_machine_properties"></a>
### Propriedades de máquina virtual ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Propriedades**, e você terá a capacidade de alterar o tamanho da máquina virtual e também de alterar o número de instâncias, conforme mostra a imagem a seguir.

![][ic719499]

>[AZURE.NOTE]Somente Windows: quando você define o número de instâncias como um valor superior a um e também configura um servidor de aplicativos, o kit de ferramentas permite que apenas uma instância da função seja executada no emulador, independentemente dessa configuração. Isso serve para evitar conflitos de associação de porta entre as instâncias de servidor diferentes (por exemplo, todas tentando associar-se à porta 8080) quando executam no mesmo computador. A configuração de contagem de instância desejada é preservada, mas entra em vigor somente quando você implanta na nuvem.

<a name="caching_properties"></a>
### Propriedades de caching ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Caching**. Nessa caixa de diálogo, você pode habilitar caches nomeados e colocalizados compatíveis com o memcache, permitindo que você acelere seus aplicativos Web.

![][ic719483]

Dentro da página de propriedades de **Caching**, você pode especificar configurações globais para as seguintes opções:

* se o cache colocalizado está habilitado.
* o tamanho do cache como uma porcentagem da memória.
* o nome da conta de armazenamento para salvar o estado do cache quando seu aplicativo é executado como um serviço de nuvem, ou nenhum se você não quiser salvar o estado do cache. (O nome da conta de armazenamento não é usado quando você executa o aplicativo no emulador de computação.) Se você definir o nome da conta de armazenamento como **(automático)** (padrão), sua configuração de cache usará automaticamente a mesma conta de armazenamento selecionada por você na caixa de diálogo **Publicar no Azure**.

>[AZURE.NOTE]A configuração **(automático)** terá o efeito desejado somente se você publicar sua implantação usando o assistente de publicação do kit de ferramentas do Eclipse. Se, em vez disso, você publicar o arquivo .cspkg manualmente usando um mecanismo externo, como o [Portal de Gerenciamento do Azure][], a implantação não funcionará corretamente.

A caixa de diálogo a seguir mostra as propriedades de um cache.

![][ic719501]

* **Nome:** o nome do cache colocalizado.
* **Número da porta:** o número da porta a ser usado para o cache.
* **Política de expiração:** um dos valores a seguir, que especifica quando uma chave no cache expira.
    * **Absoluto:** a chave expira quando o tempo especificado por **Minutos ativa** for atingido.
    * **Nunca Expira:** a chave não tem um tempo de expiração.
    * **Janela Deslizante:** a chave expira se não for acessada durante o período especificado por **Minutos ativa**; cada vez que ela é acessada, o relógio de expiração reinicia.
* **Minutos ativa:** o número máximo de minutos de vida de uma chave memcached, sujeita à política de expiração.
* **Alta disponibilidade com backups replicados em diferentes instâncias de função:** se esta opção estiver habilitada, ajuda a fornecer alta disponibilidade utilizando backups replicados em diferentes instâncias de função. Observe que pelo menos duas instâncias de função devem estar em vigor em sua implantação para que esse recurso funcione.

Para adicionar um novo cache, clique no botão **Adicionar** na página de propriedades de **Caching** e uma caixa de diálogo **Configurar Cache Nomeado** será aberta. Forneça valores para as propriedades descritas acima.

Para modificar um cache nomeado, selecione-o e clique no botão **Editar** na página de propriedades de **Caching**. Uma caixa de diálogo será exibida permitindo a modificação das propriedades do cache. Pressione **OK** para salvar os valores do cache.

Para excluir um cache, escolha o cache, clique no botão **Remover** na página de propriedades de **Caching** e clique em **Sim** para confirmar a exclusão.

Para saber mais sobre como usar o caching, consulte [Como usar caching colocalizado][].

<a name="certificates_properties"></a>
### Propriedades de certificados ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Certificados**.

![][ic710964]

Na caixa de diálogo, você pode adicionar ou remover certificados referenciados pelo seu projeto do Eclipse. Observe que os certificados listados aqui não são armazenados automaticamente em qualquer repositório de chaves Java e, portanto, não ficam automaticamente disponíveis para qualquer uso de um aplicativo Java. Eles são registrados no Azure apenas para que possam ser pré-carregados no repositório de certificados do Windows nas máquinas virtuais que executam sua implantação e, subsequentemente, usados por outros softwares do Windows. Atualmente, o único recurso do kit de ferramentas que usa os certificados referenciados dessa maneira na caixa de diálogo **Certificados** é o [Descarregamento de SSL][], devido à sua dependência dos IIS (Serviços de informações da Internet) e do ARR (Roteamento de solicitação de aplicativo), que exige o certificado apropriado para ser disponibilizado dessa maneira.

Ao implantar seu projeto no Azure usando o assistente de publicação, você recebe uma solicitação para apontar para os arquivos PFX (Troca de informações pessoais) que correspondem a esses certificados, junto com suas senhas, a fim de carregá-los automaticamente no serviço do Azure, mas apenas se eles não tiverem sido carregados anteriormente.

<a name="components_properties"></a>
### Propriedades de componentes ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Componentes**. Nessa caixa de diálogo, você pode adicionar, modificar ou remover os componentes de sua função, bem como alterar a ordem de processamento.

![][ic719502]

O recurso de componentes permite que você adicione dependências ao seu projeto de implantação do Azure, como projetos de aplicativos Java, arquivos especiais e instruções de linha de comando executáveis necessárias para sua implantação.

Para cada componente, você pode especificar:

* A etapa a ser executada ao importar o componente no projeto de implantação do Azure durante sua criação.
* A etapa a ser executada ao implantar esse componente na nuvem do Azure.

>[AZURE.NOTE]Ao especificar arquivos de componentes ou linhas de comando, lembre-se de que sua implantação será publicada em uma máquina virtual do Windows, portanto suas etapas personalizadas devem ser válidas para um sistema operacional Windows.

Os componentes têm as seguintes propriedades:

* **Importar:** método que indica como o componente será importado no projeto durante sua criação. Ele pode assumir um dos seguintes valores:
    * **copy:** o componente é copiado do caminho local especificado pela propriedade **De** para o diretório **approot** da função.
    * **EAR:** o componente é um EAR(arquivo morto corporativo Java) importado de um projeto de aplicativo corporativo no caminho local especificado pela propriedade **De**. (Isso é detectado automaticamente pelo kit de ferramentas com base na natureza do projeto nesse local).
    * **JAR:** o componente é um JAR (arquivo morto Java) importado de um projeto Java no caminho local especificado pela propriedade **De**. (Isso é detectado automaticamente pelo kit de ferramentas com base na natureza do projeto nesse local).
    * **nenhum:** nenhuma ação é executada para importar o componente. Isso é aplicável quando o componente é considerado presente no diretório **approot** da função, ou quando o componente é simplesmente uma instrução executável de linha de comando executável, conforme especificado na propriedade **Como** quando o método de **Implantação** é **exec**.
    * **WAR:** o componente é um WAR (arquivo morto de aplicativo Web Java) importado de um Projeto Web Dinâmico no caminho local especificado pela propriedade **De**. (Isso é detectado automaticamente pelo kit de ferramentas com base na natureza do projeto nesse local).
    * **zip:** o componente é um arquivo zip importado pela compactação do diretório ou arquivo especificado pela propriedade **De**.
* **De:** caminho de origem no computador local até a pasta ou arquivo que representa os itens a serem importados em sua implantação. É possível usar variáveis de ambiente do Windows nessa propriedade. Todos os componentes passíveis de importação serão importados no diretório **approot** da função durante a criação do projeto.
	
	Observe que você pode implantar um componente a partir de um download durante a implantação na nuvem (não no emulador de computação). Confira as informações relacionadas abaixo sobre como adicionar um componente.
	
* **Como:** nome do arquivo no qual o componente será importado no diretório **approot** da função e, por fim, implantado na nuvem do Azure. Deixe essa propriedade em branco para manter o mesmo nome que está na máquina local. (Para componentes executáveis, ou seja, aqueles cujo método de **Implantação** está definido como **exec**, isso pode ser uma instrução de linha de comando aleatória do Windows.)

	>[AZURE.IMPORTANT]Se você usar caracteres de espaço para esse valor, eles serão tratados de maneira diferente dependendo do método de implantação. Se o método de implantação for **exec**, os espaços serão interpretados como separadores de argumentos de linha de comando e não como parte do nome do arquivo. Para todos os outros métodos de implantação, os espaços serão interpretados como parte do nome do arquivo.
	
* **Implantação:** método que indica a ação aplicada ao componente no início da implantação. Ele pode assumir um dos seguintes valores:
    * **copy:** o componente é copiado no caminho de destino especificado pela propriedade **Para**.
    * **exec:** o componente é uma instrução de linha de comando executável do Windows que é executada no contexto do caminho especificado pela propriedade **Para** no início da implantação.
    * **none:** nenhuma ação é aplicada ao componente quando a implantação começa.
    * **zip:** o componente é descompactado no caminho de destino especificado pela propriedade **Para**. Esse método fica disponível apenas quando a propriedade **Importar** é **zip**.
* **Para:** caminho de destino na máquina virtual onde o componente será implantado. É possível usar variáveis de ambiente do Windows nessa propriedade, e os caminhos de arquivo são relativos a **approot**.
	
Para adicionar um novo componente, clique no botão **Adicionar** na página de propriedades **Componentes**, e uma caixa de diálogo **Componente da Função do Azure** será exibida. Forneça valores para as propriedades descritas acima.

Veja a seguir um exemplo de como adicionar um novo componente WAR.

![][ic719503]

Ao implantar na nuvem (não no emulador de computação), se você quiser implantar o componente de um download, certifique-se de que a opção **Na nuvem, em vez de incluir no pacote, implantar de** esteja marcada. Se você quiser baixar de sua conta de armazenamento do Azure, selecione a conta de armazenamento na lista suspensa **Conta de armazenamento** (clique no link **Contas** para modificar o conteúdo da lista) e isso preencherá parcialmente o campo **URL**. Em seguida, preencha o restante da URL. Se você não quiser usar o armazenamento do Azure, selecione **(nenhum)** na lista suspensa **Conta de armazenamento** e digite a URL até seu componente no campo **URL**. Especifique um dos seguintes métodos:

* **copy:** o componente baixado é copiado no caminho de destino especificado pelo caminho **To Directory**.
* **same:** o mesmo método usado para **Implantar do download** e para **Implantar do pacote**.
* **zip:** o componente baixado é descompactado no caminho de destino especificado pelo caminho **To Directory**.

Para modificar um componente, selecione o componente e clique no botão **Editar** na página de propriedades de **Componentes**. Uma caixa de diálogo será exibida permitindo a modificação das propriedades do componente. Pressione **OK** para salvar os valores do componente.

Para excluir um componente, escolha o componente, clique no botão **Remover** na página de propriedades de **Componentes** e clique em **Sim** para confirmar a exclusão.

Os componentes são processados na ordem listada. Use os botões **Mover para Cima** e **Mover para Baixo** para organizar a ordem.

>[AZURE.NOTE]O recurso de configuração do servidor também depende dos componentes. Esses componentes não podem ser removidos ou editados sem a remoção da configuração do servidor correspondente. Você será questionado sobre isso ao tentar fazer alterações nesses componentes.

<a name="debugging_properties"></a>
### Propriedades de depuração ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Depuração**. Nessa caixa de diálogo, você pode habilitar ou desabilitar a depuração remota, bem como criar configurações de depuração, conforme mostra a imagem a seguir.

![][ic719504]

Para obter outras informações sobre depuração, confira [Depuração de aplicativos do Azure no Eclipse][].

<a name="endpoints_properties"></a>
### Propriedades de pontos de extremidade ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Pontos de extremidade**. Nessa caixa de diálogo, você pode criar um ponto de extremidade, bem como editar ou remover um ponto de extremidade, conforme mostra a imagem a seguir.

![][ic719505]

Para adicionar um ponto de extremidade, clique no botão **Adicionar** na página de propriedades de **Pontos de extremidade**, e uma caixa de diálogo **Adicionar ponto de extremidade** será exibida.

![][ic710897]

Insira um nome para o ponto de extremidade, selecione o tipo (**Entrada**, **Interno** ou **Entrada de instância**) e especifique as portas pública e privada. Pressione **OK** para salvar os novos valores de ponto de extremidade.

Dependendo do tipo de ponto de extremidade, você pode usar os intervalos de porta da seguinte maneira:

* Para um ponto de extremidade de instância de entrada, a porta pública pode ser um intervalo de portas (por exemplo **2000 a 2010**) e a porta privada ser um valor fixo.
* Para um ponto de extremidade interno, a porta pública não é usada, e a porta privada pode ser um intervalo, pode ser deixada em branco ou ser definida como um asterisco, a fim de indicar que ela será definida automaticamente pelo Azure.
* Para pontos de extremidade de entrada, a porta pública pode ser apenas um valor fixo e a porta privada pode ser um valor fixo, pode ser deixada em branco ou ser definida como um asterisco, a fim de indicar que ela será definida automaticamente pelo Azure.

Se você quiser usar um único número de porta em vez de um intervalo, deixe a caixa de texto do final do intervalo em branco.

Para as portas definidas como automáticas, se você precisar determinar a porta que será realmente usada durante a execução, seu aplicativo poderá usar a API de Tempo de Execução do Serviço do Azure, documentada no resumo de pacote [com.microsoft.windowsazure.serviceruntime][].

Para ver como os pontos de extremidade de entrada de instância podem ser usados para ajudar na depuração de uma implantação com várias instâncias, consulte [Depuração de uma instância de função específica em uma implantação com várias instâncias][].

Para modificar um ponto de extremidade, selecione o ponto de extremidade e clique no botão **Editar** na página de propriedades de **Pontos de extremidade**. Uma caixa de diálogo será aberta permitindo que você modifique o nome, tipo e as portas públicas e privadas do ponto de extremidade. Pressione **OK** para salvar os novos valores de ponto de extremidade modificados.

Para excluir um ponto de extremidade, escolha o ponto de extremidade, clique no botão **Remover** na página de propriedades de **Ponto de extremidade** e clique em **Sim** para confirmar a exclusão.

Para configurar corretamente alguns dos recursos (como Caching, Depuração Remota, Afinidade de Sessão ou Descarregamento de SSL) habilitados pelo usuário em uma função, o kit de ferramentas pode configurar automaticamente os pontos de extremidade especiais que serão listados juntamente com os pontos de extremidade definidos pelo usuário. O kit de ferramentas impede que o usuário edite ou exclua esses pontos de extremidade gerados automaticamente, desde que o recurso associado esteja habilitado.

<a name="environment_variables_properties"></a>
### Propriedades de variáveis do ambiente ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Variáveis do Ambiente**. Nessa caixa de diálogo, você pode criar uma variável do ambiente, bem como modificar ou remover uma variável do ambiente, conforme mostra a imagem a seguir.

![][ic719506]

As variáveis do ambiente ficam disponíveis para o seu script de inicialização quando a função é iniciada.

>[AZURE.NOTE]Ao especificar variáveis do ambiente, lembre-se de que sua implantação será publicada em uma máquina virtual do Windows, portanto as variáveis do ambiente devem ser válidas para um sistema operacional Windows.

Como exemplo de uma variável de ambiente disponibilizada quando a função é iniciada, crie uma nova variável do ambiente clicando no botão **Adicionar**. Veja a seguir uma variável do ambiente chamada **MyRoleVersion** sendo criada e recebendo o valor **1.0**.

![][ic659268]

Dentro do código jsp, você pode exibir o valor usando o método `System.getenv`:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Quando o aplicativo é executado está é a saída:

![][ic552233]

Para modificar uma variável do ambiente, selecione a variável do ambiente e clique no botão **Editar** na página de propriedades de **Variáveis do Ambiente**. Uma caixa de diálogo será exibida permitindo a modificação das variáveis do ambiente. Pressione **OK** para salvar os da variável do ambiente.

Para excluir uma variável do ambiente, selecione a variável do ambiente e clique no botão **Remover** na página de propriedades de **Variáveis do Ambiente** e clique em **Sim** para confirmar a exclusão.

Para configurar corretamente alguns dos recursos (como Configuração do Servidor, Depuração Remota ou Armazenamento Local) habilitados pelo usuário em uma função, o kit de ferramentas pode configurar automaticamente as variáveis do ambiente especiais que serão listadas juntamente com as variáveis do ambiente definidas pelo usuário. O kit de ferramentas impede que o usuário edite ou exclua essas variáveis do ambiente geradas automaticamente, desde que o recurso associado esteja habilitado.

<a name="session_affinity_properties"></a>
### Propriedades de balanceamento de carga/afinidade (também conhecidas como "sessões temporárias") ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Balanceamento de Carga**. Nessa caixa de diálogo, você pode habilitar ou desabilitar a afinidade da sessão, conforme mostra a imagem a seguir.

![][ic719492]

Para obter informações relacionadas, confira [Afinidade da sessão][]. Além disso, observe o comportamento desse recurso no contexto de descarregamento de SSL, conforme descrito em [Descarregamento de SSL][].

<a name="local_storage_properties"></a>
### Propriedades de armazenamento local ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Armazenamento Local**. Nessa caixa de diálogo, você pode criar, modificar ou remover o armazenamento local temporário da máquina virtual que está executando o aplicativo. É possível definir valores específicos para o tamanho do armazenamento local, bem como se o conteúdo é preservado quando a função é reciclada, conforme mostra a imagem a seguir.

![][ic719508]

Como opção, você também pode especificar uma variável do ambiente que corresponde ao armazenamento local.

Por padrão, tudo o que você implanta no Azure é colocado (e descompactado) na pasta **approot** da instância de função. Embora a maioria das implantações simples caibam nesse local mesmo após descompactá-las, o espaço alocado para o diretório **approot** é limitado e não é bem definido (menos de 1 GB é uma regra prática razoável). Portanto, para garantir que o Azure reserva espaço em disco suficiente para implantações maiores que talvez não caibam na pasta **approot**, você deve configurar um recurso de armazenamento local usando a caixa de diálogo **Armazenamento Local**. Para conhecer uma maneira fácil de fazer isso, consulte [Deploying Large Deployments][].

Você pode facilmente fazer referência ao recurso de armazenamento nos scripts de inicialização (por exemplo, o **startup.cmd**) usando a variável de ambiente associada automaticamente pelo kit de ferramentas do Eclipse com o recurso, conforme exibido na caixa de diálogo **Armazenamento Local**. Essa variável de ambiente contem o caminho completo até o recurso local configurado no momento de execução de seu script de inicialização.

Para modificar um recurso de armazenamento local, selecione o recurso de armazenamento local e clique no botão **Editar** na página de propriedades de **Armazenamento Local**. Uma caixa de diálogo será exibida permitindo a modificação das propriedades de recurso de armazenamento local. Pressione **OK** para salvar os valores de recursos de armazenamento local.

Para excluir um recurso de armazenamento local, selecione o recurso de armazenamento local e clique no botão **Remover** na página de propriedades de **Armazenamento Local** e clique em **Sim** para confirmar a exclusão.

<a name="server_configuration_properties"></a>
### Propriedades de configuração do servidor ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Configuração de Servidor**. Nessa caixa de diálogo, você pode adicionar, remover e modificar o JDK e o servidor de aplicativos Java usados por sua implantação, bem como adicionar ou remover os aplicativos (como arquivos WAR, JAR ou EAR) usados por sua implantação.

### Configuração de JDK ###

Essa caixa de diálogo permite que você especifique o pacote JDK que será usado para a implantação. Se você estiver usando o Eclipse no Windows, poderá especificar o pacote JDK a ser usado localmente durante a execução no emulador do Azure, e você tem a opção de implantar essa instalação local no Azure. Em sistemas operacionais diferentes do Windows, a configuração de JDK do emulador não é aplicável, e não é possível implantar o JDK instalado localmente, pois ele não é compatível com o Windows. No entanto, independentemente do sistema operacional usando, você sempre pode escolher entre pacotes JDK de terceiros para implantação no Azure ou apontar para seu próprio pacote JDK compatível com Windows em um local de download alternativo.

Veja a seguir um exemplo de como você pode especificar um JDK no Windows.

![][ic780647]

Se você estiver usando o Eclipse no Windows, poderá especificar um JDK a ser usado com o emulador de computação. Para fazer isso, certifique-se de que a opção **Usar o JDK deste caminho de arquivo para testar localmente** esteja marcada na seção **Implantação no emulador**. Em seguida, especifique o caminho local até o JDK; você pode navegar até um JDK diferente se o que você deseja usar não estiver selecionado automaticamente. Você também tem a opção de implantar o JDK em seu serviço de nuvem do Azure. Para fazer isso, escolha a opção **Implantar meu JDK local (carregamento automático no armazenamento em nuvem)** na seção **Implantação de nuvem**.

Observação: em sistemas operacionais diferentes do Windows, as configurações da **Implantação no emulador** e a opção **Implantar meu JDK local** não estão disponíveis. O exemplo a seguir ilustra a especificação de um JDK em um Mac ou em outro sistema operacional diferente do Windows que tenha suporte:

![][ic789643]

Independentemente do sistema operacional usado, você tem as duas opções de **Implantação de nuvem** a seguir para a origem e o tipo do pacote JDK:

* **Implantar um pacote JDK de terceiros disponível no Azure** 
* **Implantar de um download personalizado** 

Se você estiver usando a opção **Implantar um pacote JDK de terceiros disponível no Azure**:

1. Marque a caixa de seleção **Implantar um pacote JDK de terceiros disponível no Azure**.
1. Na lista suspensa, selecione o pacote JDK de terceiros disponível no Azure.
1. A guia **JDK** será semelhante ao seguinte no Windows: ![][ic780648] E será semelhante ao seguinte no Mac OS ou em outros sistemas operacionais diferentes do Windows que tenham suporte: ![][ic789643]
1. Clique em **OK** para salvar as alterações.
1. Quando receber uma solicitação para aceitar o contrato de licença do provedor do pacote JDK de terceiros, revise os termos da licença. Se você aceitar os termos, clique em **Sim** para fechar a caixa de diálogo **Aceitar contrato de licença**. Observe que a lógica subjacente para exibição dos itens na lista suspensa da opção **Implantar um pacote JDK de terceiros disponível no Azure** pode ser personalizada. Para personalizar os itens, na caixa de diálogo **JDK**, clique no link **Personalizar**. Isso fechará a página de propriedades de **JDK** e abrirá o arquivo **componentsets.xml** no Eclipse, que pode ser modificado conforme o necessário. A documentação do **componentsets.xml** está incluída no próprio arquivo **componentsets.xml**.

Se você estiver usando a opção **Implantar um JDK de um download personalizado**:

1. Crie um ZIP de seu diretório de instalação do JDK, garantindo que o próprio nó do diretório seja o filho da estrutura ZIP, e não seu conteúdo. Anote o nome do diretório, pois precisará dele mais tarde, e lembre-se de que esta instalação do JDK será implantada em uma máquina virtual do Windows.
1. Carregue o ZIP em sua conta de armazenamento do Azure como um blob. Você pode fazer isso usando uma ferramenta disponível externamente para carregar blobs no armazenamento do Azure. Recomendamos o uso de um blob privado. Anote a URL do blob do conteúdo do ZIP.
1. Marque a caixa de seleção **Implantar um JDK de um download personalizado**. Se você quiser baixar de sua conta de armazenamento do Azure, selecione a conta de armazenamento na lista suspensa **Conta de armazenamento** (clique no link **Contas** para modificar o conteúdo da lista) e isso preencherá parcialmente o campo **URL**. Em seguida, preencha o restante da URL. Se você não quiser usar o armazenamento do Azure, selecione **(nenhum)** na lista suspensa **Conta de armazenamento** e digite a URL até o download de seu JDK no campo **URL**. Se você estiver usando o armazenamento do Azure, os nomes de blob na URL deverão estar em letra minúscula.
1. Verifique se a caixa de texto **JAVA\_HOME** faz referência ao nome do diretório correto. Por padrão, ela fará referência ao mesmo nome de diretório do JDK que o valor escolhido para uso local. Porém, se o diretório dentro do ZIP tiver um nome diferente (por exemplo, devido ao uso de uma versão diferente), atualize adequadamente o nome do diretório na caixa de texto **JAVA\_HOME**, já que essa configuração será usada na nuvem (não no emulador de computação).
1. Clique em **OK** para salvar as alterações.

É isso. Agora, quando você compilar para a nuvem, perceberá que o tamanho do pacote será muito menor, o processo de build normalmente deverá demorar menos tempo e a própria implantação, quando você publicá-la na nuvem, também deverá demorar menos tempo. Observe que as opções **Implantar meu JDK local (carregamento automático no armazenamento em nuvem)** ou **Implantar um JDK de um download personalizado** entram em vigor somente quando o aplicativo é implantado na nuvem. As opções não têm qualquer efeito em sua experiência no emulador de computação; a versão local dos componentes ainda será usada quando você implantar no emulador de computação.

### Configuração do servidor ###

Veja a seguir um exemplo de como você pode especificar um servidor de aplicativos.

![][ic796926]

Verifique se a caixa de seleção **Implantar um servidor desse tipo** está marcada e, em seguida, escolha o tipo de servidor de aplicativos que você deseja usar.

Para especificar um servidor a ser usado para implantação na nuvem, você pode aproveitar as seguintes opções:

1. **Implantar um servidor de terceiros disponível no Azure**: essa opção é aplicável principalmente em cenários de desenvolvimento/teste nos quais a simplicidade e a eficiência da implantação são prioridades, e o servidor não exige uma configuração personalizada. Ou quando você quiser usar um desses servidores como ponto de partida, mas quiser incluir etapas de personalização de servidor apropriadas na lógica de inicialização da implantação.
1. **Implantar de um download personalizado**: essa opção é aplicável principalmente em cenários de produção, quando você tem um servidor especialmente configurado e preparado para uso na nuvem.
1. **Implantar a instalação de meu servidor local**: essa opção é aplicável principalmente se a instalação do servidor local já estiver configurada de forma personalizada para uso. Se você escolher essa opção, deverá especificar também o caminho de seu servidor local na caixa de texto **Caminho do servidor local** abaixo.

Se você estiver usando a opção **Implantar um servidor de terceiros disponível no Azure**:

1. Marque a caixa de seleção **Implantar um servidor de terceiros disponível no Azure**.
1. No menu suspenso, selecione o software de servidor desejado a ser usado com sua implantação na nuvem. Observe que se você tiver especificado anteriormente um tipo de servidor a ser usado, você poderá escolher apenas um servidor de nuvem que esteja na mesma família do tipo de servidor. Porém, se você não escolheu um tipo de servidor, poderá escolher qualquer um dos servidores disponíveis atualmente no Azure, e o tipo de servidor será selecionado automaticamente para você.
1. Clique em **OK** para salvar as alterações.

Se usar a opção **Implantar de um download personalizado**:

1. Certifique-se de que você já selecionou um tipo de servidor, de acordo com as etapas anteriores. Isso informa ao plug-in como implantar o servidor a partir de seu download personalizado, pois ele deve ser da mesma família que o tipo de servidor selecionado.
1. Marque a caixa de seleção **Implantar de um download personalizado**. Se você quiser baixar de sua conta de armazenamento do Azure, escolha a conta de armazenamento na lista suspensa **Conta de armazenamento** (clique no link **Contas** para modificar o conteúdo da lista) e isso preencherá parcialmente o campo **URL**. Em seguida, preencha o restante da URL até o ZIP baixado de seu servidor (ao usar o armazenamento do Azure, os nomes de blob na URL deverão estar em letra minúscula). Se você não quiser usar o armazenamento do Azure, selecione **(nenhum)** na lista suspensa **Conta de armazenamento** e digite a URL até o ZIP baixado de seu servidor no campo **URL**. O ZIP contém uma pasta filho que representa o diretório de instalação do servidor de aplicativo. Por exemplo, se você estiver usando um zip para o Apache Tomcat 7.0.35, o conteúdo do zip seria a pasta filho que representa o diretório de instalação, por exemplo, **apache-tomcat-7.0.35**. 
1. Especifique o valor para a variável de ambiente do diretório base. Ele assumirá o padrão como o valor usado para o servidor de aplicativo local, se houver algum, mas você pode especificar um valor diferente se o servidor de aplicativos de nuvem for diferente do seu servidor de aplicativo local. No entanto, você precisa se certificar de que o servidor de aplicativos de nuvem seja da mesma família que o tipo de servidor selecionado anteriormente. Se você atualizar posteriormente o zip de seu servidor de aplicativos de nuvem, altere manualmente a configuração do diretório base ou, fazer com que ele corresponda novamente à configuração local (se você também alterou seu servidor de aplicativo local).
1. Clique em **OK** para salvar as alterações.

A lógica subjacente dos itens que aparecem na guia **Servidor** da página de propriedades **Configuração do Servidor** pode ser personalizada. Esse é um recurso avançado do qual você pode precisar se as suas necessidades ultrapassarem os valores padrão, ou se você quiser adicionar outros servidores. Para personalizar a lógica, na caixa de diálogo **Servidor**, clique no link **Personalizar**. Isso fechará a página de propriedades de **Configuração do Servidor** e abrirá o arquivo **componentsets.xml** no Eclipse para você modificar conforme o necessário a fim de estender o modelo de configuração do servidor. A documentação do **componentsets.xml** está incluída no próprio arquivo **componentsets.xml**.

Se você estiver usando a opção **Implantar meu servidor local (carregamento automático no armazenamento em nuvem)**:

1. Marque a caixa de seleção **Implantar meu servidor local (carregamento automático no armazenamento em nuvem)**.
1. Na lista suspensa **Conta de armazenamento**, selecione **(automática)**. Se você especificar **(automática)**, o kit de ferramentas do Eclipse usará a mesma conta de armazenamento para o servidor que aquela selecionada para a implantação na caixa de diálogo **Publicar no Azure**.
1. Clique em **OK** para salvar as alterações.

Selecione um caminho de instalação do servidor em seu computador na caixa de texto **Caminho do servidor local** se qualquer uma das condições a seguir for verdadeira:

* Você deseja testar a implantação no emulador (aplica-se apenas ao Windows).
* Você deseja implantar o servidor instalado localmente na nuvem.
* Você deseja usar um download de servidor personalizado próprio na nuvem. Nesse caso, marque também a opção **Implantar meu servidor local (carregamento automático no armazenamento em nuvem)**acima.

Se nenhuma das opções anteriores se aplicar à sua situação, a configuração do servidor local será opcional.

### Configuração de aplicativos ###

Veja a seguir um exemplo de como você pode especificar um aplicativo.

![][ic719512]

Clique em **Adicionar** para adicionar outro aplicativo, ou em **Remover** para remover um aplicativo. Para obter eficiência, se você quiser usar um download como a fonte de um aplicativo ao implantar na nuvem, use as [propriedades de componentes](#components_properties) para especificar uma URL, a conta de armazenamento etc.

A partir da versão de abril de 2014, os aplicativos são carregados automaticamente na mesma conta de armazenamento (no contêiner **eclipsedeploy**) selecionada para sua implantação. A lógica de inicialização de sua implantação contém uma etapa que baixa primeiro esses aplicativos dessa conta de armazenamento. Isso significa que você pode atualizar os aplicativos em sua implantação sem precisar recompilar e reimplantar todo o pacote, carregando manualmente as versões mais recentes do aplicativo diretamente na conta de armazenamento (usando o Portal do Azure, por exemplo), substituindo os arquivos WAR carregados originalmente pelo kit de ferramentas. Em seguida, basta iniciar a reciclagem de todas as instâncias de função usando novamente o Portal de Gerenciamento do Azure, ou por meio de utilitários de linha de comando. (No momento, não há suporte para a inicialização da reciclagem diretamente do kit de ferramentas do Eclipse.)

### Observações sobre a configuração do servidor ###

As alterações feitas na página de propriedades de **Configuração do servidor** são refletidas nos elementos `<component>` do arquivo package.xml.

Quando você usa as opções **Carregar automaticamente...** ou **Implantar do download...** para o JDK ou o servidor de aplicativos, e está compilando para a nuvem (não para o emulador de computação) e conectado à rede, talvez perceba mensagens de build como a seguinte na saída do Console, enquanto o compilador Ant verifica a disponibilidade do download:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...`

Se você tiver selecionado a opção**Implantar do download...**, o seguinte aviso poderá ser exibido, mas a compilação continuará:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).`

Esse aviso é a única indicação de que a disponibilidade do download ainda não foi verificada. Portanto, se uma implantação falhar na nuvem por algum motivo, verifique se você recebeu esse aviso.

Se você quiser desabilitar a verificação de download (por exemplo, se você se sentir que isso causa uma lentidão desnecessária no build), defina o atributo `verifydownloads` como `false` no elemento `<windowsazurepackage>` do arquivo package.xml:

`<windowsazurepackage verifydownloads="false" ...>`

Se você tiver selecionado a opção **Carregar automaticamente...**, você verá mensagens de build na janela do console informando sobre o progresso do carregamento a cada 5 segundos, sempre que um carregamento for necessário.

<a name="ssl_offloading_properties"></a>
### Propriedades de descarregamento de SSL ###

Abra o menu de contexto da função no painel Gerenciador de Projeto do Eclipse, clique em **Azure** e clique em **Descarregamento de SSL**.

![][ic719481]

Nessa caixa de diálogo, você pode habilitar o descarregamento de SSL e, assim, permitindo o suporte ao protocolo HTTPS (Hypertext Transfer Protocol Secure) na implantação Java no Azure, sem a necessidade de configurar SSL em seu servidor de aplicativos Java. Para saber mais, consulte [Descarregamento de SSL][] e [Como usar o descarregamento de SSL][].

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Instalação do Kit de Ferramentas do Azure para o Eclipse][]

[Criação de um aplicativo Hello World do Azure no Eclipse][]

[Propriedades do Projeto do Azure][]

[Lista de contas de armazenamento do Azure][]

Para saber mais sobre como usar o Azure com Java, confira a [Central de Desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de Desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propriedades do Projeto do Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Lista de contas de armazenamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[com.microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuração de uma instância de função específica em uma implantação com várias instâncias]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Depuração de aplicativos do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[Como usar caching colocalizado]: http://go.microsoft.com/fwlink/?LinkID=699542
[Como usar o descarregamento de SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Afinidade da sessão]: http://go.microsoft.com/fwlink/?LinkID=699548
[Descarregamento de SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!---HONumber=AcomDC_0114_2016-->
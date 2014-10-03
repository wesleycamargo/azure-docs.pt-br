<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# Configurando e implantando o aplicativo Azure Email Service - 2 de 5

Este é o segundo de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure. Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][].

Neste tutorial, você aprenderá:

-   Como configurar seu computador para o desenvolvimento Azure instalando o Azure SDK.
-   Como configurar e testar o aplicativo Azure Email Service em seu computador local.
-   Como publicar o aplicativo no Azure.
-   Como exibir e editar tabelas, filas e blobs do Azure usando o Gerenciador de Servidores do Visual Studio.
-   Como configurar o rastreamento e exibir dados de rastreamento.
-   Como dimensionar o aplicativo aumentando o número de instâncias de função de trabalho.

### Segmentos do tutorial

-   [Configurar o ambiente de desenvolvimento][]
-   [Baixar e executar a solução completa][]
-   [Visualizar armazenamento de desenvolvimento no Visual Studio][]
-   [Criar uma conta de Armazenamento do Azure][]
-   [Criar um Serviço de Nuvem][]
-   [Configurar o aplicativo para o Armazenamento do Azure][]
-   [Configurar o aplicativo para usar o SendGrid][]
-   [Implantar o aplicativo no Azure][]
-   [Promover o aplicativo do preparo para a produção][]
-   [Configurar e exibir dados de rastreamento][]
-   [Adicionar outra instância de função de trabalho para tratar o aumento da carga][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">Baixar e executar</span>Baixar e executar a solução completa

1.  Baixar e descompactar a [solução concluída][].

2.  Inicie o Visual Studio.

3.  No menu **Arquivo**, escolha **Abrir Projeto**, navegue até onde você baixou a solução e, em seguida, abra o arquivo de solução.

4.  Pressione CTRL+SHIFT+B para criar a solução.

    Por padrão, o Visual Studio restaura automaticamente o conteúdo do pacote NuGet, que não foi incluído no arquivo *.zip*. Se os pacotes não forem restaurados, instale-os manualmente acessando a caixa de diálogo **Gerenciar Pacotes NuGet para Solução** e clicando no botão **Restaurar** na parte superior direita.

5.  No **Gerenciador de Soluções**, verifique se **AzureEmailService** está selecionado como o projeto de inicialização.

6.  Pressione CTRL+F5 para executar o aplicativo.

    A home page do aplicativo é exibida em seu navegador.

7.  Clique em **Listas de correio** na barra de menus.

    (Capturas de tela mostram o estilo da página da Web a partir de modelos de projeto do Visual Studio 2012, mas o conteúdo é o mesmo para o Visual Studio 2013.)

    ![Execute o aplicativo.][]

8.  Clique em **Criar Novo**.

9.  Insira alguns dados de teste e, em seguida, clique em **Criar**.

    ![Execute o aplicativo.][1]

10. Crie mais duas entradas na lista de endereçamento.

    ![Página de índice da lista de endereçamento][]

11. Clique em **Subscribers** e adicione alguns assinantes. Defina **Verified** como `true`.

    ![Página do índice do assinante][]

12. Prepare-se para adicionar mensagens criando um arquivo *. txt* que contenha o corpo de um email que você deseja enviar. Em seguida, crie um arquivo *.htm* que contenha o mesmo texto, mas com algum HTML (por exemplo, tornar uma das palavras da mensagem em negrito ou itálico). Você usará esses arquivos na próxima etapa.

13. Clique em **Messages** e adicione algumas mensagens. Selecione os arquivos que você criou na etapa anterior. Não altere a data agendada cujo padrão é uma semana no futuro. O aplicativo não pode enviar mensagens até que você configure o SendGrid.

    ![Página de criação de mensagem][]

    ![Página de índice de mensagem][]

    Os dados que você inseriu e visualizou estão sendo gerenciados pelo emulador de armazenamento Azure. O emulador de armazenamento usa um banco de dados LocalDB SQL Server Express para emular a forma como o Azure Storage funciona na nuvem. O aplicativo está usando o emulador de armazenamento, porque é isso o que o projeto estava configurado para fazer quando você o transferiu por download. Essa configuração é armazenada em arquivos *.cscfg* no projeto **AzureEmailService**. O arquivo *ServiceConfiguration.Local.cscfg* determina o que é usado quando você executa o aplicativo localmente no Visual Studio, e o arquivo *ServiceConfiguration.Cloud.cscfg* determina o que é usado quando você implanta o aplicativo na nuvem. Posteriormente, você verá como configurar o aplicativo para usar uma conta do Armazenamento do Azure.

14. Feche o navegador.

## <a name="StorageExpVS"></a>Visualizar armazenamento de desenvolvimento no Visual Studio

O navegador **Armazenamento do Azure** no **Gerenciador de Servidores** fornece uma forma conveniente de trabalhar diretamente com os recursos do Armazenamento do Azure.

1.  No menu **Exibir** no Visual Studio, selecione **Gerenciador de Servidores**.

2.  Expanda o nó **Azure**, em seguida, expanda o nó **Storage** e expanda o nó **(Desenvolvimento)** abaixo do nó **Azure Storage**.

    Se você ainda não tiver feito logon no Azure no Visual Studio, serão solicitadas as suas credenciais Azure. Insira as credenciais para a conta que possui a assinatura em que deseja executar o serviço de nuvem.

3.  Expanda **Tabelas** para ver as tabelas que você criou nas etapas anteriores.

    ![Gerenciador de Servidores][]

4.  Clique duas vezes na tabela **MailingList**.

    ![Gerenciador de armazenamento do VS][]

    Observe como a janela mostra os diferentes esquemas na tabela. As entidades `MailingList` possuem as propriedades `Description` e `FromEmailAddress`, e as entidades `Subscriber` possuem a propriedade `Verified` (além de `SubscriberGUID`, que não é a mostrada porque a imagem não é ampla o suficiente). A tabela contém colunas para todas as propriedades e, se uma determinada linha da tabela for de uma entidade que não tenha um determinada propriedade, essa célula estará em branco.

Outra ferramenta que você pode usar para trabalhar com os recursos Azure Storage é a [Azure Storage Explorer][].

## <a name="createWASA"></a>Criar uma conta de Armazenamento do Azure

Quando você executa o aplicativo função Web no Visual Studio, você pode acessar tabelas, filas e blobs no emulador de armazenamento do Azure ou em uma conta do Armazenamento do Azure na nuvem. Nesta seção do tutorial, você cria a conta de Armazenamento do Azure onde você vai configurar o Visual Studio para usar posteriormente no tutorial.

1.  No seu navegador, abra o [Portal de Gerenciamento do Azure][].

2.  No [Portal de Gerenciamento do Azure][], clique em **Armazenamento**, em seguida, clique em **Novo**.

	![Novo Armazenamento][]

1.  Clique em **Criação Rápida**.

	![Criação Rápida][]

1.  Na caixa de entrada URL, digite um prefixo de URL.

    Esse prefixo, além do texto que você vê sob a caixa será a URL exclusiva de sua conta de armazenamento. Se o prefixo inserido já tiver sido usado por outra pessoa, você verá a mensagem "O nome do armazenamento já está em uso" acima da caixa de texto e será necessário escolher outro prefixo.

2.  Defina a região para a área onde você deseja implantar o aplicativo.

3.  Configure a caixa suspensa **Replicação** para **Localmente redundante**.

    Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um local secundário para habilitar o failover para esse local no caso de um desastre maior no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para obter mais informações, consulte [Como gerenciar contas de armazenamento][].

4.  Clique em **Criar Conta de Armazenamento**.

    Na imagem, uma conta de armazenamento é criada com a URL `aestest3.core.windows.net`.

    ![criar um armazenamento com o prefixo da URL][]

    Esta etapa pode levar vários minutos para ser concluída. Enquanto aguarda, você pode repetir essas etapas e criar uma conta de armazenamento de produção. Muitas vezes é conveniente ter uma conta de armazenamento de teste para desenvolvimento local, outra conta de armazenamento de teste para testes no Azure e uma conta de armazenamento de produção.

5.  Clique na conta de teste que você criou na etapa anterior, em seguida, clique no ícone **Gerenciar Chaves de Acesso**.

    ![Gerenciar Chaves][]

    ![GUID de chaves][]

    O Visual Studio configurará automaticamente as cadeias de conexão com uma dessas chaves ao selecionar a conta de armazenamento. Também é possível atualizar as cadeias de conexão manualmente.

    Existem duas chaves para que você possa alterar periodicamente a chave que você usa sem provocar uma interrupção no serviço em um aplicativo ativo. Você regenera a chave que não está usando e, em seguida, pode alterar a cadeia de conexão em seu aplicativo para usar a chave regenerada. Se houver apenas uma chave, o aplicativo poderá perder a conectividade com a conta de armazenamento quando você regenerar a chave. As chaves mostradas na imagem não são mais válidas porque foram regeneradas depois que a imagem foi capturada.

## <a name="createcloudsvc"></a><span class="short-header">Criar um Serviço de Nuvem</span>Criar um Serviço de Nuvem

1.  No [Portal de Gerenciamento do Azure][], clique em **Serviços de Nuvem** e, em seguida, clique no ícone **Novo**.

    ![Nuvem rápida][]

2.  Clique em **Criação Rápida**.

3.  Na caixa de entrada URL, digite um prefixo de URL.

    Como a URL de armazenamento, essa URL deve ser exclusiva e você receberá uma mensagem de erro se o prefixo escolhido já estiver em uso por outra pessoa.

4.  Defina a região para a área onde você deseja implantar o aplicativo.

    Você deve criar o serviço de nuvem na mesma região em que criou a conta de armazenamento. Quando a conta de armazenamento do serviço de nuvem estiver em outros datacenters (outras regiões), a latência será maior e você será cobrado pela largura de banda fora do data center. A largura de banda em um data center é gratuita.

    O grupos de afinidade do Azure fornecem um mecanismo para minimizar a distância entre os recursos em um data center, o que pode reduzir a latência. Este tutorial não usa grupos de afinidade. Para obter mais informações, consulte [Como criar um grupo de afinidade no Azure][].

5.  Clique em **Criar Serviço de Nuvem**.

    Na imagem a seguir, um serviço de nuvem é criado com a URL aescloud.cloudapp.net.

    ![criar um armazenamento com o prefixo da URL][2]

## <a name="conf4azureStorage"></a><span class="short-header">Usar sua conta de armazenamento</span>Configure o aplicativo para usar sua conta do Armazenamento do Azure

Em seguida, você verá como configurar o aplicativo para que ele use sua conta de Armazenamento do Azure, quando for executado no Visual Studio, em vez do armazenamento de desenvolvimento.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **MvcWebRole** sob **Funções** no projeto **AzureEmailService** e clique em **Propriedades**.

    ![Clicar com o botão direito do mouse em Propriedades][]

2.  Na janela **MvcWebRole [Função]**, clique na guia **Configurações**.

3.  Na caixa suspensa **Configuração do Serviço**, selecione **Local**.

4.  Selecione a entrada **StorageConnectionString** e você verá um botão de reticências (**...**) na extremidade direita da linha. Clique no botão de reticências para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

    ![Clicar com o botão direito do mouse em Propriedades][3]

5.  No diálogo **Criar Cadeia de Conexão de Armazenamento**, clique em **Sua assinatura** e escolha sua **Assinatura** e seu **Nome da conta** de armazenamento.

    ![Clicar com o botão direito do mouse em Propriedades][4]

6.  Siga o mesmo procedimento que usou para a cadeia de conexão de armazenamento `StorageConnectionString` para definir a cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

7.  Siga o mesmo procedimento usado para as duas cadeias de conexão para a função MvcWebRole para definir as cadeias de conexão para as funções WorkerRoleA e workerRoleB.

8.  Abra o arquivo **ServiceConfiguration.Local.cscfg** localizado no projeto **AzureEmailService**.

    No elemento `Role` para `MvcWebRole`, você verá um elemento `ConfigurationSettings` com as configurações que você atualizou usando a interface do usuário do Visual Studio.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    Nos elementos `Role` das duas funções de trabalho, você verá as mesmas duas cadeias de conexão.

    É possível editar esses arquivos diretamente em vez de usar a janela **[Função]** do Visual Studio. Para obter mais informações sobre os arquivos de configuração, consulte [Configurando um projeto do Azure][]

### Teste o aplicativo configurado para usar sua conta de armazenamento

1.  Pressione CTRL+F5 para executar o aplicativo. Insira alguns dados clicando nos links **Mailing Lists**, **Subscribers** e **Messages** como você fez anteriormente neste tutorial.

2.  No Visual Studio, abra **Gerenciador de Servidores**.

3.  Expanda o nó **Armazenamento** no nó **Azure** e expanda o nó para a conta de armazenamento que você configurou para que o aplicativo use.

4.  Expanda **Tables** e clique duas vezes na tabela `MailingList` para ver os dados inseridos nas páginas **Mailing List** e **Subscriber** do aplicativo.

### Etapas opcionais para desabilitar a inicialização automática do Emulador de Armazenamento do Azure

Se não estiver usando o emulador de armazenamento, você poderá reduzir o tempo de inicialização do projeto e usar menos recursos locais desabilitando a inicialização automática do emulador do Armazenamento do Azure.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto em nuvem **AzureEmailService** e selecione **Propriedades**.

    ![Selecionando as propriedades do projeto em nuvem][]

2.  Selecione a guia **Desenvolvimento**.

3.  Configure **Iniciar emulador de armazenamento do Azure** para **False**.

    ![Desabilitando a inicialização automática do emulador de armazenamento][]

    **Observação**: Você só deve configurar isto para false se não estiver usando o emulador de armazenamento.

    Essa janela também fornece uma maneira de alterar o arquivo de **Configuração do Serviço**, que é usado quando você executa o aplicativo localmente, de **Local** para **Nuvem** (de *ServiceConfiguration.Local.cscfg* para *ServiceConfiguration.Cloud.cscfg*.

4.  Na bandeja do sistema do Windows, clique com o botão direito do mouse no ícone do emulador de computação e clique em **Desligar o Emulador de Armazenamento**.

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>Configure o aplicativo para usar o SendGrid

O aplicativo de exemplo usa o SendGrid para enviar emails. Para enviar emails usando o SendGrid, você precisa configurar uma conta do SendGrid e, em seguida, atualizar um arquivo de configuração com as suas credenciais do SendGrid.

<div class="note"><p><strong>Observa&ccedil;&atilde;o:</strong> Se voc&ecirc; n&atilde;o quiser usar o SendGrid ou n&atilde;o puder usar o SendGrid, &eacute; poss&iacute;vel substituir facilmente seu pr&oacute;prio servi&ccedil;o de email. O c&oacute;digo que usa o SendGrid est&aacute; isolado em dois m&eacute;todos na fun&ccedil;&atilde;o de trabalho B. O [Tutorial 5][tut5] explica o que voc&ecirc; tem de alterar para implementar um m&eacute;todo diferente de enviar emails. Se voc&ecirc; quiser fazer isto, &eacute; poss&iacute;vel ignorar este procedimento e continuar com este tutorial; todo o restante no aplicativo funcionar&aacute; (p&aacute;ginas da web, planejamento de email, etc.), exceto o envio real de emails.</p></div>

### Criar uma conta do SendGrid

1.  Siga as instruções em [How to Send Email Using SendGrid with Azure][] para inscrever-se numa conta gratuita.

### Atualizar credenciais do SendGrid nas propriedades de função de trabalho

No início do tutorial, quando definiu as credenciais da conta de armazenamento para a função web e as duas funções de trabalho, você talvez tenha observado que a função de trabalho B tinha três configurações que não estavam na função web ou na função de trabalho A. Você pode usar essa mesma interface do usuário agora para configurar essas três configurações (selecione **Nuvem** na lista suspensa **Configuração do Serviço**).

As etapas a seguir mostram um método alternativo para definir as propriedades editando o arquivo de configuração.

1.  Edite o arquivo *ServiceConfiguration.Cloud.cscfg* no projeto `AzureEmailService` e insira os valores do nome e da senha do usuário do SendGrid que você obteve na etapa anterior para o elemento `WorkerRoleB` que possui essas configurações. O código a seguir mostra o elemento WorkerRoleB.

    ![SendGridSettings][]

2.  Também existe uma configuração de AzureMailServiceURL. Configure este valor para a URL que você selecionou quando criou seu Serviço de Nuvem do Azure, por exemplo: "<http://aescloud.cloudapp.net>".

Atualizando o arquivo de configuração de nuvem, você está definindo as configurações que serão usadas quando o aplicativo for executado na nuvem. Se você quiser que o aplicativo envie emails enquanto é executado localmente, você também precisará atualizar o arquivo *ServiceConfiguration.Local.cscfg*.

## <a name="deployAz"></a><span class="short-header">Implantar o aplicativo no Azure</span>Implantar o aplicativo no Azure

Para implantar o aplicativo, você pode criar um pacote no Visual Studio e carregá-lo usando o Portal de Gerenciamento do Azure ou publicá-lo diretamente no Visual Studio. Neste tutorial, você usará o método publish.

Você publicará o aplicativo primeiro no ambiente de preparo e mais tarde irá promover a implantação de preparo para a produção.

### Implantar restrições de IP

Quando você implanta na preparação, o aplicativo poderá ser publicamente acessível a qualquer pessoa que conheça a URL. Portanto, a primeira etapa é implementar as restrições de IP para garantir que nenhuma pessoa não autorizada possa usá-lo. Em um aplicativo de produção, você implantaria um mecanismo de autenticação e autorização, como ASP.NET Identity, mas essas funções foram omitidas do aplicativo função Web para simplificar a configuração, implantação e teste.

1.  Abra o arquivo *Web.Release.config* localizado na pasta raiz do projeto `MvcWebRole` e substitua o valor do atributo **ipAddress** 127.0.0.1 pelo seu endereço IP. Para ver o arquivo **Web.Release.config** no **Gerenciador de Soluções** é necessário expandir o arquivo *Web. config*.

    É possível localizar seu endereço IP procurando “Localizar meu IP" com [Bing][] ou outro mecanismo de pesquisa.

    Quando o aplicativo é publicado, as transformações especificadas no arquivo *Web.release.config* são aplicadas, e os elementos de restrição IP são atualizados no arquivo *web. config* implantado na nuvem. Você pode exibir o arquivo *web. config* transformado na pasta *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* após a criação do pacote.

### Configure o aplicativo para usar sua conta de armazenamento quando ele for executado na nuvem

No início do tutorial, quando você definiu as credenciais de conta de armazenamento para a função web e as duas funções de trabalho, você definiu as credenciais a serem usadas ao executar o aplicativo localmente. Agora você precisa definir as credenciais da conta de armazenamento a serem usadas ao executar o aplicativo na nuvem.

Para essa execução de teste, você usará as mesmas credenciais para a nuvem que usou para executar localmente. Se estiver implantando um aplicativo de produção, você normalmente usa uma conta de produção diferente da conta usada para teste. Uma prática recomendada para a produção também é usar uma conta para a connectionString de diagnóstico diferente da cadeia de conexão de armazenamento, mas para esta execução de teste, você usará a mesma conta.

Você pode usar a mesma interface de usuário para configurar as cadeias de conexão (apenas certifique-se de selecionar **Nuvem** na lista suspensa **Configuração do Serviço**. Como alternativa, você pode editar o arquivo de configuração, conforme explicado nas etapas a seguir.

1.  Abra o arquivo *ServiceConfiguration.Local.cscfg* no projeto **AzureEmailService** e copie os elementos `Setting` para `StorageConnectionString` e `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Abra o arquivo *ServiceConfiguration.Cloud.cscfg* no projeto **AzureEmailService** e cole os elementos copiados no elemento `Configuration Settings` para `MvcWebRole`, `WorkerRoleA` e `WorkerRoleB`, substituindo os elementos `Setting` já presentes.

3.  Verifique se todos os elementos da função web e das duas funções de trabalho definem as mesmas cadeias de conexão.

### Publicar o aplicativo

1.  Se ainda não estiver aberto, inicialize o Visual Studio e abra a solução **AzureEmailService**.

2.  Clique com o botão direito do mouse no projeto de nuvem **AzureEmailService** e selecione **Publicar**.

    ![Pacote][]

    A caixa de diálogo **Publicar aplicativo do Azure** é exibida.

    ![Pacote de nuvem][]

3.  Se você usou o método automático para importar credenciais de conta de armazenamento anteriormente, sua assinatura do Azure estará na lista suspensa e você poderá selecioná-la e clicar em **Avançar**. Caso contrário, clique em **Entrar para baixar credenciais** e siga as instruções em [Configurar o aplicativo para o Armazenamento do Azure][] para baixar e importar as configurações de publicação.

4.  Na guia **Configurações Comuns**, verifique se seu serviço de nuvem está selecionado na lista suspensa **Serviço de Nuvem**.

5.  Na lista suspensa **Ambiente**, altere **Produção** para **Preparo**.

    ![Painel][]

6.  Mantenha a configuração padrão **Versão** para **Configuração da Compilação** e **Nuvem** para **Configuração do serviço**.

    As configurações padrão na guia **Avançado** são adequadas para este tutorial. Na guia **Avançado** há algumas configurações que são úteis para desenvolvimento e teste. Para obter mais informações sobre a guia avançada, consulte, see [Assistente de publicação de aplicativo do Azure][].

7.  Clique em **Próximo**.

8.  Na etapa **Resumo** do assistente, clique no ícone **salvar** (o ícone de disquete mostrado à direita da lista suspensa do Perfil de destino) para salvar as configurações de publicação.

    Na próxima vez que você publicar o aplicativo, as configurações salvas serão usadas e você não precisará percorrer o assistente de publicação novamente.

9.  Examine as configurações e clique em **Publicar**.

    ![pub][]

	A janela **Log de atividades do Azure** é aberta no Visual Studio.

1.  Clique no ícone de seta para a direita para expandir os detalhes da implantação.

    ![pub][5]
    ![pub][6]

    A implantação pode levar cerca de cinco minutos ou mais para ser concluída.

2.  Quando o status da implantação for concluído, clique na **URL do Site** para iniciar o aplicativo.

    ![Painel][7]

3.  Insira alguns dados nas página da web **Mailing List**, **Subscriber** e **Message** para testar o aplicativo.

    **Observação**: Exclua o aplicativo após ter concluído o teste nele para evitar pagar por recursos que você não está usando. Se você estiver usando uma [conta de avaliação gratuita do Azure][], as três funções implantadas usarão o limite mensal em algumas semanas. Para excluir uma implantação usando o Portal de Gerenciamento do Azure, selecione o serviço de nuvem e clique **EXCLUIR** na parte inferior da página e, em seguida, selecione a implantação de produção ou de preparo.

    ![pub][8]

4.  No Log de Atividade do Azure no Visual studio, selecione **Abrir no Gerenciador de Servidores**.

    Em **Computação do Azure** no **Gerenciador de Servidores**, você pode monitorar a implantação. Se tiver selecionado **Habilitar Área de Trabalho para todas as funções** no assistente **Publicar Aplicativo do Azure**, você poderá clicar com o botão direito do mouse em uma instância da função e selecionar **Conectar Usando Área de Trabalho Remota**.

    ![pub][9]

## <a name="swap"></a>Promover o aplicativo do preparo para a produção

1.  No [Portal de Gerenciamento do Azure][], clique no ícone **Serviços de Nuvem** no painel esquerdo e selecione seu serviço de nuvem e clique na guia **Painel**.

2.  Clique em **Permutar**.

3.  Clique em **Sim** para concluir a permuta VIP (IP virtual). Esta etapa pode levar vários minutos para ser concluída.

    ![Painel][10]

4.  Quando o swap estiver concluído, role para baixo a guia **Painel** para a implantação de **Produção** para a seção **visão rápida** na parte inferior direito da página. Observe que a **URL do Site** foi alterada de um prefixo GUID para o nome do seu serviço de nuvem.

    ![Painel][11]

5.  Clique no llink em **URL do Site** ou copie e cole-o em um navegador para testar o aplicativo em produção.

    Se você não tiver alterado as configurações da conta de armazenamento, os dados inseridos durante o teste da versão de preparação do aplicativo serão mostrados quando você executar o aplicativo na nuvem.

## <a name="trace"></a>Configurar e Exibir Dados de Rastreamento

O rastreamento é uma ferramenta valiosa para a depuração de um aplicativo de nuvem. Nesta seção do tutorial, você verá como exibir dados de rastreamento.

1.  Verifique se a cadeia de conexão de diagnósticos está configurada para usar sua conta de Armazenamento do Azure e não o armazenamento de desenvolvimento.

    Se você seguiu as instruções anteriormente no tutorial, elas serão as mesmas. É possível verificar elas são as mesmas usando a UI do Visual Studio UI (a guia **Configurações** em **Propriedades** para as funções) ou examinando os arquivos *ServiceConfiguration.\*.cscfg*.

	**Observação:** Uma melhor prática é usar uma conta de armazenamento para dados de rastreamento diferente da conta de armazenamento usada para dados de produção, mas para simplicidade neste tutorial, você está configurando a mesma conta para rastreamento.

1.  No Visual Studio, abra *WorkerRoleA.cs* no projeto **WorkerRoleA**, pesquise por `ConfigureDiagnostics` e examine o método `ConfigureDiagnostics`.

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    Nesse código, o `DiagnosticMonitor` está configurado para armazenar até 500 MB de informações de rastreamento (depois de 500 MB, os dados mais antigos são substituídos) e para armazenar todas as mensagens de rastreamento (LogLevel.Verbose). O `ScheduledTransferPeriod` transfere os dados de rastreamento para o armazenamento a cada minuto. Você deve definir o `ScheduledTransferPeriod` para salvar os dados de rastreamento.

    O método `ConfigureDiagnostics` em cada uma das funções de trabalho e da web configura o ouvinte de rastreamento para registrar dados ao chamar a API de Rastreamento. Para obter mais informações, consulte [Usando rastreamento nos aplicativos da nuvem do Windows Azure][]

2.  No **Gerenciador de Servidores**, clique duas vezes em **WADLogsTable** (expanda **Azure** / **Armazenamento** / **yourstorageaccountname** / **Tabelas**) para a conta de armazenamento que você incluiu anteriormente. É possível inserir um [Filtro de serviços de dados WCF][] para limitar as entidades exibidas. Na imagem a seguir, somente mensagens de erro e de aviso são exibidas.

    ![Painel][12]

## <a name="addRole"></a>Adicionar outra instância de função de trabalho para tratar o aumento da carga

Há duas abordagens para dimensionar recursos de computação em funções do Azure, especificando os [tamanho de máquina virtual][] e/ou especificando a contagem de instância de máquinas virtuais em execução.

O tamanho da máquina virtual (VM) é especificado no atributo `vmsize` do elemento `WebRole` ou `WorkerRole` no arquivo *ServiceDefinition.csdef*. A configuração padrão é `Small`, que fornece um núcleo e 1,75 GB de RAM. Para aplicativos com multithread que usam muita memória, disco e largura de banda, você pode aumentar o tamanho da VM para melhorar o desempenho. Por exemplo, uma VM `ExtraLarge` tem oito núcleos de CPU e 14 GB de RAM. O aumento da memória, dos núcleos de cpu, do disco e da largura de banda em um único computador é conhecido como *escalar verticalmente*. Bons candidatos para a expansão incluem aplicativos da web ASP.NET que usam [métodos assíncronos][]. Consulte [Tamanhos de Máquinas Virtuais][tamanho de máquina virtual] para obter uma descrição dos recursos fornecidos por cada tamanho de VM.

A função de trabalho B neste aplicativo é o componente limitante sob carga alta porque faz o trabalho de envio de emails. (A função de trabalho A apenas cria mensagens da fila, que não é um uso intensivo de recursos.) Como a função de trabalho B não é multithread e não tem uma superfície de memória grande, ela não é uma boa candidata para escala vertical. A função de trabalho B pode ser dimensionada linearmente (isto é, quase o dobro do desempenho quando você dobra as instâncias) aumentando a contagem de instâncias. O aumento do número de instâncias de computação é conhecido como *escalar horizontalmente*. Há um custo para cada instância, você deve expandir apenas quando seu aplicativo exigir uma escala horizontal.

É possível escalar horizontalmente uma função de trabalho ou da web atualizando a configuração na UI do Visual Studio ou editando os arquivos *ServiceConfiguration.\*.cscfg* diretamente. A contagem de instâncias é especificada na guia **Configuração** da janela **Propriedades** da função e no elemento `Instances` nos arquivos *.cscfg*. Quando você atualiza a configuração, você precisa implantar o arquivo de configuração atualizado para que a alteração entre em vigor. Como alternativa, para aumentos de carga transitórios, é possível alterar o número de instâncias de função manualmente ou configurar o Azure para alterar automaticamente o número de instâncias baseadas nos critérios especificados. Para obter mais informações sobre autoescala automática, consulte [o último tutorial nesta série][].

Nesta seção do tutorial você irá escalar horizontalmente a função de trabalho B usando o portal de gerenciamento, mas primeiro você verá como isso é feito no Visual Studio.

Para fazer isso no Visual Studio, você clica com o botão direito do mouse em **Funções** no projeto de nuvem e seleciona **Propriedades**.

![Clicar com o botão direito do mouse em Propriedades][]

Em seguida, você selecione a guia **Configuração** à esquerda e seleciona **Nuvem** no menu suspenso **Configuração do Serviço**.

![Contagem de Instâncias][]

Observe que você também pode configurar o tamanho da VM nesta guia.

As etapas a seguir explicam como escalar horizontalmente usando o Portal de Gerenciamento do Azure.

1.  No Portal de Gerenciamento do Azure, selecione seu serviço de nuvem, em seguida, clique em **Dimensionar**.

2.  Aumente o número de instâncias da função de trabalho B e, em seguida, clique em **Salvar**.

    ![aumentar instâncias][]

    Pode levar alguns minutos para que as novas VMs sejam provisionadas.

3.  Selecione a guia **Instâncias** para ver cada instância de função em seu aplicativo.

    ![exibir instâncias][]

## <a name="nextsteps"></a>Próximas etapas

Agora você já viu como configurar, implantar e dimensionar o aplicativo concluído. Os tutoriais seguintes mostram como criar o aplicativo desde o início. No [próximo tutorial][] você criará a função web.

Para obter links para recursos adicionais a fim de trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte [o último tutorial nesta série][13].

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Tutorial 3</a></div>

  [o primeiro tutorial da série]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Configurar o ambiente de desenvolvimento]: #setupdevenv
  [Visualizar armazenamento de desenvolvimento no Visual Studio]: #StorageExpVS
  [Baixar e executar a solução completa]: #downloadcnfg
  [Criar uma conta de Armazenamento do Azure]: #createWASA
  [Criar um Serviço de Nuvem]: #createcloudsvc
  [Configurar o aplicativo para o Armazenamento do Azure]: #conf4azureStorage
  [Configurar o aplicativo para usar o SendGrid]: #sendGrid
  [Implantar o aplicativo no Azure]: #deployAz
  [Promover o aplicativo do preparo para a produção]: #swap
  [Configurar e exibir dados de rastreamento]: #trace
  [Adicionar outra instância de função de trabalho para tratar o aumento da carga]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [solução concluída]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Execute o aplicativo.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Página de índice da lista de endereçamento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Página do índice do assinante]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [Página de criação de mensagem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [Página de índice de mensagem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Gerenciador de Servidores]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [Gerenciador de armazenamento do VS]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Novo Armazenamento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Criação Rápida]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [Como gerenciar contas de armazenamento]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [criar um armazenamento com o prefixo da URL]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Gerenciar Chaves]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [GUID de chaves]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Nuvem rápida]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Como criar um grupo de afinidade no Azure]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Clicar com o botão direito do mouse em Propriedades]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Configurando um projeto do Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [Selecionando as propriedades do projeto em nuvem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Desabilitando a inicialização automática do emulador de armazenamento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [How to Send Email Using SendGrid with Azure]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "localizar meu IP"
  [Pacote]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Pacote de nuvem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Painel]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Assistente de publicação de aplicativo do Azure]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [conta de avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/ "conta de avaliação gratuita"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Usando rastreamento nos aplicativos da nuvem do Windows Azure]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Usando rastreamento no Windows Azure"
  [Filtro de serviços de dados WCF]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "Filtro WCF"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [tamanho de máquina virtual]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "tamanhos de VM"
  [métodos assíncronos]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "MVC assíncrono"
  [o último tutorial nesta série]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Contagem de Instâncias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [aumentar instâncias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [exibir instâncias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [próximo tutorial]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [13]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps

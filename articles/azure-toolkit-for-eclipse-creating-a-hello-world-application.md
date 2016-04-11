<properties
    pageTitle="Criar um Serviço de Nuvem Hello World para o Azure no Eclipse"
    description="Saiba como criar um aplicativo Hello World simples usando o Kit de Ferramentas do Azure para Eclipse."
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
    ms.date="03/28/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Criar um Serviço de Nuvem Hello World para o Azure no Eclipse #

As etapas a seguir mostram como criar e implantar um aplicativo JSP básico no Azure usando o Kit de Ferramentas do Azure para Eclipse. Um exemplo de JSP é exibido para manter a simplicidade, mas etapas muito semelhantes podem ser apropriadas para um servlet Java quando o assunto é a implantação do Azure.

O aplicativo será semelhante ao seguinte:

![][ic600360]

## Pré-requisitos ##

* Um JDK (Java Developer Kit) versão 1.7 ou posterior.
* Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor Web baseado em Java ou servidor de aplicativo, como o Apache Tomcat, o GlassFish, o JBoss Application Server, o Jetty ou o IBM® WebSphere® Application Server Liberty Core
* Uma assinatura de Azure, que pode ser adquirida em <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de Ferramentas do Azure para Eclipse. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para Eclipse][].

## Para criar um aplicativo Hello World ##

Primeiro, vamos começar com a criação de um projeto Java.

*  Inicie o Eclipse, no menu clique em **Arquivo**, clique em **Novo** e depois em **Projeto Web Dinâmico**. (Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível depois de clicar em **Arquivo**, **Novo**, faça o seguinte: clique em **Arquivo**, clique em **Novo**, clique em **Projeto...**, expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.)
*  Para o objetivo deste tutorial, nomeie o projeto **MyHelloWorld**. (Não deixe de usar esse nome, pois as etapas subsequentes deste tutorial esperam que seu arquivo WAR seja nomeado MyHelloWorld). Sua tela será semelhante à seguinte:
    ![][ic589576]
* Clique em **Concluir**.
* No modo de exibição do Gerenciador de Projeto do Eclipse, expanda **MyHelloWorld**. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
* Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo **index.jsp**. Mantenha a pasta pai como **MyHelloWorld/WebContent**, conforme mostrado a seguir:

    ![][ic659262]
* Para o objetivo deste tutorial, na caixa de diálogo **Selecionar Modelo JSP**, escolha **Novo Arquivo JSP (html)** e clique em **Concluir**.
* Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir dinamicamente **Hello World!** dentro do elemento `<body>` existente. Seu conteúdo `<body>` atualizado deve ser semelhante ao seguinte:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Salve o index.jsp.

## Para implantar seu aplicativo no Azure, a maneira rápida e simples ##

Assim que você tiver um aplicativo Web Java pronto para testar, poderá usar o atalho a seguir para experimentá-lo diretamente na nuvem do Azure.

1. No Gerenciador de Projeto do Eclipse, clique em **MyHelloWorld**.
1. Na barra de ferramentas do Eclipse, clique no botão **Publicar na Nuvem do Azure**
    ![][ic710882].
1. Se você estiver publicando esse aplicativo no Azure pela primeira vez e nunca tiver criado um projeto de implantação do Azure para este aplicativo, um projeto de implantação do Azure será criado automaticamente para você. Você deverá ver o seguinte prompt, que também lista o pacote JDK e o servidor de aplicativos que será automaticamente implantado para executar o aplicativo.

    ![][ic789598]
    Essa abordagem de atalho permite uma maneira rápida e fácil de testar seu aplicativo no Azure sem precisar configurar um servidor ou JDK específico diferente dos padrões. Se você estiver satisfeito com os padrões, clique em **OK** para continuar com as etapas a seguir.
    No entanto, se você quiser alterar o JDK ou o servidor de aplicativos a ser usado para seu aplicativo, faça isso mais tarde editando o projeto de implantação do Azure criado automaticamente para você, ou clique em **Cancelar** agora e leia a seção **Sobre projetos de implantação do Azure** deste tutorial.
1. Na caixa de diálogo **Publicar no Azure**:
    1. Se ainda não houver assinaturas para selecionar na lista **Assinatura**, execute estas etapas para importar as informações de sua assinatura:
        1. Clique em **Importar do arquivo PUBLISH-SETTINGS**.
        1. Na caixa de diálogo **Importar Informações de Assinatura**, clique em **Baixar arquivo PUBLISH-SETTINGS**. Se você ainda não estiver conectado à sua conta do Azure, receberá uma solicitação para fazer logon. Em seguida, você receberá uma solicitação para salvar um arquivo de configurações de publicação do Azure. Salve-o em seu computador local.
        1. Ainda na caixa de diálogo **Importar Informações de Assinatura**, clique no botão **Procurar**, selecione o arquivo de configurações de publicação que você salvou localmente na etapa anterior e, em seguida, clique em **Abrir**. Sua tela deve ser semelhante à seguinte:

            ![][ic644267]
        1. Clique em **OK**.
    1. Para **Assinatura**, selecione a assinatura que você deseja usar para sua implantação.
    1. Para **Conta de armazenamento**, selecione a conta de armazenamento que você deseja usar ou clique em **Novo** para criar uma nova conta de armazenamento.
    1. Para **Nome do serviço**, selecione o serviço de nuvem que você deseja usar ou clique em **Novo** para criar um novo serviço de nuvem.
    1. Para **SO de Destino**, selecione a versão do sistema operacional que você deseja usar para sua implantação.
    1. Em **Ambiente de destino**, para o objetivo deste tutorial, selecione **Preparo**. (Quando você estiver pronto para implantar em seu site de produção, convém alterar isso para **Produção**.)
    1. Opcional: verifique se **Substituir implantação anterior** está marcado se você quiser que a nova implantação substitua automaticamente a implantação anterior. Quando você habilitar essa opção, não enfrentará os problemas "409 - Conflito" ao publicar no mesmo local.
        Observe que a caixa de diálogo **Publicar no Azure** contém uma seção para **Acesso Remoto**. Por padrão, o Acesso Remoto não está habilitado, e não habilitaremos ele para este exemplo. Para habilitar o Acesso Remoto, seria necessário digitar um nome de usuário e senha para utilização ao fazer logon remotamente. Para saber mais sobre o Acesso Remoto, confira [Habilitação do Acesso Remoto para Implantações do Azure no Eclipse][].
        A caixa de diálogo **Publicar no Azure** será semelhante à seguinte:
        ![][ic719488]
1. Clique em **Publicar** para publicar no Ambiente de preparo. 
    Quando receber uma solicitação para executar uma compilação completa, clique em **Sim**.
    Isso pode levar alguns minutos na primeira compilação. Um **Log de Atividades do Azure** será exibido na seção de modos de exibição com guias do Eclipse.
    ![][ic719489]
    Você pode usar esse log, bem como o modo de exibição de **Console**, para ver o progresso da implantação. Uma alternativa é fazer logon no [Portal de Gerenciamento do Azure][] e usar a seção **Serviços de Nuvem** para monitorar o status.
1. Quando sua implantação tiver êxito, o **Log de Atividades do Azure** mostrará um status de **Publicada**. Clique em **Publicada**, conforme mostra a imagem a seguir, e o navegador abrirá uma instância de sua implantação.
    ![][ic719490]

Como essa era uma implantação em um ambiente de preparo, o nome DNS estará no formato http://&lt;*guid*&gt;.cloudapp.net, e a URL conterá o nome DNS mais um sufixo para seu aplicativo. Por exemplo: http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (A parte **MyHelloWorld** diferencia maiúsculas de minúsculas.) Você também pode ver o nome DNS se clicar no nome de implantação no Portal de Gerenciamento de Plataforma do Azure (dentro da parte Serviços de Nuvem do portal de gerenciamento).

Embora este passo a passo servisse para uma implantação no ambiente de preparo, a implantação em produção segue as mesmas etapas, exceto na caixa de diálogo **Publicar no Azure**, quando você deve escolher **Produção** em vez de **Preparo** para o **Ambiente de destino**. Uma implantação em produção resulta em uma URL baseada no nome DNS de sua escolha, em vez de um GUID como foi usado para o preparo.

>[AZURE.WARNING] Neste ponto, você já implantou o aplicativo do Azure na nuvem. No entanto, antes de prosseguir, perceba que um aplicativo implantado, mesmo que ele não esteja em execução, continuará acumulando horas faturáveis para sua assinatura. Portanto, é extremamente importante que você exclua as implantações indesejadas de sua assinatura do Azure.

## Sobre projetos de implantação do Azure ##

Para implantar um ou mais aplicativos Java no Azure, é necessário um Projeto de Implantação do Azure. Ele desempenha a função de "pacote" dentro do qual seus aplicativos precisam ser agrupados para serem publicados no Azure.

Além das informações sobre seus aplicativos, um projeto de implantação do Azure também contém informações sobre outros componentes importantes de sua implantação, acima de tudo: o contêiner de servidor de aplicativo para execução em seu aplicativo Web e o tempo de execução Java para executá-lo. O Azure suporta uma ampla seleção de tempos de execução Java e servidores de aplicativos Java para sua escolha.

Embora o exemplo usado aqui esteja bastante simplificado para fins educacionais, um projeto de implantação do Azure também pode conter outras informações importantes de configuração que permitem a criação quase aleatória de serviços de nuvem complexos, escalonáveis, altamente disponíveis e de várias camadas com seus aplicativos. Você pode habilitar a **afinidade de sessão ("sessões temporárias")**, **cache rápido**, **depuração remota**, **descarregamento de SSL**, **firewall/roteamento de porta**, **acesso remoto** e vários outros recursos avançados.

Se você tiver concluído a seção anterior deste tutorial ("Para implantar seu aplicativo no Azure, a maneira rápida e simples"), agora você verá um novo projeto de implantação do Azure no Gerenciador de Projeto gerado automaticamente para você e chamado "**MyHelloWorld\_onAzure**".

Você também pode iniciar este tutorial criando por conta própria um projeto de implantação do Azure em branco e adicionando seus aplicativos ao projeto. É um processo mais longo, mas oferece mais controle sobre a configuração inicial desde o princípio.

Para criar um novo projeto de implantação do Azure do zero, clique no botão **Novo Projeto de Implantação do Azure**![][ic710876].

Independentemente de você trabalhar com um projeto de implantação existente do Azure ou criar um do zero, é possível alterar facilmente suas configurações e componentes, como o JDK ou o servidor de aplicativos, a qualquer momento.

Para alterar o JDK, ou o servidor de aplicativos, ou a lista de aplicativos em um projeto de implantação do Azure existente:

1. Expanda o nó do projeto (por exemplo, **MyHelloWorld\_onAzure**) no Gerenciador de Projeto
2. Clique com o botão direito do mouse em **WorkerRole1**
3. Expanda o submenu **Azure** no menu de contexto
4. Clique em **Configuração do Servidor**

Independentemente de você ter iniciado essas etapas de configuração do servidor editando um projeto de implantação existente do Azure, conforme exibido acima, ou criando um novo a partir do zero, você verá o mesmo tipo de caixa de diálogo permitindo a configuração do JDK, dos componentes de servidor e do aplicativo. Para saber mais sobre como alterar as configurações nessas caixas de diálogo, por exemplo, alterar o JDK, o servidor de aplicativos e adicionar ou remover aplicativos em uma implantação, confira o artigo [Propriedades de configuração do servidor][].

## Somente Windows: para implantar seu aplicativo no emulador de computação ##

>[AZURE.NOTE] O emulador do Azure só está disponível no Windows. Ignore esta seção se você estiver usando um sistema operacional diferente do Windows.

Se você tiver criado um novo projeto de implantação do Azure executando as etapas descritas anteriormente, ou seja, implicitamente, publicando seu aplicativo no Azure, o JDK e os servidores de aplicativo terão sido configurados para a nuvem, mas não para emulação local. Para preparar seu projeto para teste no emulador local, execute estas etapas:

1. No Gerenciador de Projeto do Eclipse, clique em **MyHelloWorld\_onAzure**.
1. Clique com o botão direito do mouse em **WorkerRole1**.
1. Expanda o submenu **Azure** no menu de contexto.
1. Clique em **Configuração do Servidor**.
1. Na guia **JDK**, verifique se o Kit de ferramentas pré-configurou um JDK local padrão para você. Em caso negativo, ou se você quiser alterar os padrões assumidos, certifique-se de que a caixa de seleção **Usar o JDK deste caminho de arquivo para testar localmente** esteja marcada e o local de instalação do JDK que você deseja usar tenha sido especificado. Se você quiser alterá-lo, clique no botão **Procurar** e, usando o controle de navegação, selecione o local do diretório do JDK a ser usado.
1. Clique na guia **Servidor**.
1. Na caixa de texto **Caminho do servidor local** na parte inferior da caixa de diálogo, digite o caminho de um servidor instalado localmente que corresponda ao tipo e ao número de versão principal do servidor selecionado na parte superior da caixa de diálogo, na caixa de seleção **Implantar um servidor deste tipo**. Se você quiser usar um tipo ou versão principal diferente do servidor de aplicativos, primeiro altere a seleção nessa caixa de seleção.
1. Clique em **OK**.
1. Na barra de ferramentas do Eclipse, clique no botão **Executar no Emulador do Azure**, ![][ic710879]. Se o botão **Executar no Emulador do Azure** não estiver habilitado, verifique se **MyHelloWorld\_onAzure** está selecionada no Gerenciador de Projeto do Eclipse e certifique-se de que o foco esteja na janela do Gerenciador de Projeto do Eclipse. Isso iniciará primeiro uma compilação completa de seu projeto e, em seguida, iniciará seu aplicativo Web de Java no emulador de computação. (Observe que dependendo das características de desempenho do computador, a primeira compilação poderá demorar de alguns segundos a alguns minutos, mas as compilações subsequentes serão mais rápidas.) Após a conclusão da primeira etapa da compilação, você receberá uma solicitação do UAC (Controle de Conta de Usuário) do Windows para permitir que esse comando faça alterações em seu computador. Clique em **Sim**.

>[AZURE.IMPORTANT] Se você não vir o prompt do UAC, procure na barra de tarefas do Windows o ícone do UAC e clique nele primeiro. Às vezes, o prompt do UAC não aparece como uma janela de nível superior, mas fica visível somente como um ícone na barra de tarefas.

1. Examine a saída do emulador de computação da interface do usuário para determinar se há problemas com o seu projeto. Dependendo do conteúdo de sua implantação, pode demorar alguns minutos para seu aplicativo ser totalmente iniciado no emulador de computação.
1. Inicie o navegador e use a URL `http://localhost:8080/MyHelloWorld` como o endereço (a parte `MyHelloWorld` da URL diferencia maiúsculas de minúsculas). Você deve ver o aplicativo MyHelloWorld (a saída de index.jsp), semelhante à imagem a seguir:
    ![][ic589579]

Quando você estiver pronto para interromper a execução do aplicativo no emulador de computação, na barra de ferramentas do Eclipse, clique no botão **Redefinir Emulador do Azure**, ![][ic710880].

## Para excluir a implantação ##

Para excluir a implantação dentro do Kit de Ferramentas do Azure para Eclipse, selecione **MyHelloWorld\_onAzure** no Gerenciador de Projeto do Eclipse, certifique-se de que o foco esteja na janela do Gerenciador de Projeto do Eclipse e, em seguida, clique no botão **Cancelar publicação**, ![][ic710883], na barra de ferramentas do Eclipse. (Você pode executar a mesma operação clicando com o botão direito do mouse em **MyHelloWorld\_onAzure** no Gerenciador de Projeto do Eclipse, clicando em **Azure** e, em seguida, clicando em **Cancelar a Implantação da Nuvem do Azure**.) Isso exibirá a caixa de diálogo **Cancelar a Publicação do Projeto do Azure**.

![][ic719491]

Selecione a assinatura e o serviço de nuvem que contém sua implantação, escolha a implantação que você deseja excluir e, em seguida, clique em **Cancelar publicação**.

(Uma alternativa ao uso do kit de ferramentas para excluir a implantação é usar a seção **Serviços de Nuvem** do Portal de Gerenciamento do Azure: navegue até sua implantação, selecione-a e, em seguida, clique no botão **Excluir**. Isso interromperá e, em seguida, excluirá a implantação. Se você quiser apenas interromper a implantação e não excluí-la, clique no botão **Parar** em vez do botão **Excluir**, mas, como foi mencionado acima, se você não excluir a implantação, os encargos continuarão acumulando para sua implantação, mesmo que ela seja interrompida).

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Instalação do Kit de Ferramentas do Azure para Eclipse][]

[Novidades no Kit de Ferramentas do Azure para o Eclipse][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Habilitação do Acesso Remoto para Implantações do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Instalação do Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de configuração do servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!-----------HONumber=AcomDC_0330_2016-->
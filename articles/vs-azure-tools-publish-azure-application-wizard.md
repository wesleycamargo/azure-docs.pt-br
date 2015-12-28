<properties 
   pageTitle="Assistente de Aplicativo de Publicação do Azure | Microsoft Azure"
   description="Assistente Publicar aplicativo do Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="tarcher" />

# Assistente Publicar aplicativo do Azure

## Visão geral

Depois de desenvolver um aplicativo Web no Visual Studio, você pode publicá-lo mais facilmente em um serviço de nuvem do Azure usando o assistente **Publicar aplicativo do Azure**. A primeira seção explica as etapas que você deve concluir antes de usar o assistente, e as demais seções explicam os recursos do assistente.

>[AZURE.NOTE]Este tópico é sobre a implantação de serviços de nuvem, não em sites. Para obter informações sobre a implantação de sites, consulte [Como implantar um site da Web do Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## Pré-requisitos

Antes de publicar seu aplicativo Web no Azure, você precisa ter uma conta da Microsoft e uma assinatura do Azure, e precisa associar seu aplicativo Web a um serviço de nuvem do Azure. Se você já tiver concluído essas tarefas, pode ir para a próxima seção.

1. Obtenha uma conta da Microsoft e uma assinatura do Azure. Você pode tentar uma assinatura gratuita de um mês do Azure [aqui](https://azure.microsoft.com/pricing/free-trial/)

1. Crie um serviço de nuvem e uma conta de armazenamento no Azure. Você pode fazer isso no Gerenciador de Servidores no Visual Studio ou usando o [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para obter mais informações sobre como configurar seu ambiente do Azure, consulte [Configurando os serviços necessários para publicar um serviço de nuvem do Visual Studio](vs-azure-tools-publish-azure-application-wizard).

1. Habilite seu aplicativo Web para o Azure. Para habilitar seu aplicativo Web para ser publicado no Azure por meio do Visual Studio, você precisará associá-lo a um projeto de serviço de nuvem do Azure no Visual Studio. Para criar o projeto de serviço de nuvem associado, abra o menu de atalho para o projeto do aplicativo Web e, em seguida, escolha Converter, **Converter em Projeto de Serviço de Nuvem do Azure**.

1. Depois que o projeto de serviço de nuvem é adicionado à sua solução, abra o mesmo menu de atalho novamente e escolha **Publicar**. Para obter mais informações sobre como habilitar aplicativos para o Azure, consulte [Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE]Certifique-se de iniciar o Visual Studio com credenciais de administrador (Executar Como Administrador).

1. Quando estiver pronto para publicar seu aplicativo, abra o menu de atalho para o projeto de serviço de nuvem do Azure e escolha **Publicar**. As etapas a seguir mostram o assistente Publicar aplicativo do Azure.

## Escolhendo sua assinatura

### Para escolher uma assinatura

1. Antes de usar o assistente pela primeira vez, você deve entrar. Clique no link **Entrar**. Entre no portal do Azure quando solicitado e forneça seu nome e senha de usuário do Azure. 

    ![Essa é uma das telas do assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    A lista de assinaturas é preenchida com as assinaturas associados à sua conta. Você também pode ver as assinaturas de qualquer arquivo de assinatura importado anteriormente.

1. Na lista **Escolher sua assinatura** escolha a assinatura a ser usada para essa implantação.

   Se você escolher **<Gerenciar... >**, a caixa de diálogo **Gerenciar Assinaturas** será exibida e você poderá escolher a conta de usuário e a assinatura que quer usar. A guia **Contas** mostra todas as suas contas e a guia **Assinaturas** mostra todas as assinaturas associadas às contas. Também é possível escolher uma região por meio da qual usar os recursos do Azure, bem como criar ou importar certificados para sua assinatura no portal do Azure. Se você importou todas as assinaturas de um arquivo de assinatura, os certificados associados serão exibidos na guia **Certificados**. Quando tiver terminado, escolha o botão **Fechar**.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. Escolha o botão **Avançar** para continuar. 

    Se não houver serviços de nuvem em sua assinatura, você terá de criar um serviço de nuvem no Azure para hospedar seu projeto. A caixa de diálogo **Criar Serviço de Nuvem e Conta de Armazenamento** é exibida.

    Especifique um novo nome para o serviço de nuvem. O nome deve ser exclusivo no Azure. Especifique uma região ou grupo de afinidades para um data center que esteja perto de você ou da maioria de seus clientes. Esse nome também é usado para uma nova conta de armazenamento que o Azure cria para seu serviço de nuvem.

1. Modifique as configurações desejadas para essa implantação e, em seguida, publique-as escolhendo o botão **Publicar** (a próxima seção fornece mais detalhes sobre as várias configurações). Para rever as configurações antes de publicar, escolha o botão **Avançar**.

    >[AZURE.NOTE]Se você escolher Publicar nesta etapa, poderá monitorar o status dessa implantação no Visual Studio.

Você pode modificar as configurações avançadas e comuns para uma implantação usando o assistente **Publicar aplicativo do Azure**. Por exemplo, você pode escolher uma configuração para implantar seu aplicativo em um ambiente de teste antes de liberá-lo. A ilustração a seguir mostra a guia **Configurações Comuns** para uma implantação do Azure.

![Configurações comuns](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## Definindo suas configurações de publicação

### Para configurar as configurações de publicação

1. Na lista **Serviço de Nuvem**, execute um dos seguintes conjuntos de etapas:

   1. Na caixa de listagem suspensa, escolha um serviço de nuvem existente. O local do data center para o serviço é exibido. Você deve anotar esse local e garantir que o local da conta de armazenamento esteja no mesmo data center.

    1. Escolha **Criar Novo** para criar um serviço de nuvem hospedado pelo Azure. Na caixa de diálogo **Criar Serviço de Nuvem**, forneça um nome para o serviço e, em seguida, especifique uma região ou grupo de afinidades para especificar o local do data center em que você deseja hospedar esse serviço de nuvem. O nome deve ser exclusivo no Azure.

1. Na lista **Ambiente**, escolha **Produção** ou **Preparo**. Escolha o ambiente de preparo em que desejar implantar seu aplicativo em um ambiente de teste. Você pode mover seu aplicativo no ambiente de produção posteriormente.

1. Na lista **Configuração da Compilação** lista, escolha **Depurar** ou **Liberar**.

1. Na lista **Configuração de Serviço**, escolha **Nuvem** ou **Local**.

    Marque a caixa de seleção **Habilitar área de trabalho remota para todas as funções** se você quiser ser capaz de se conectar remotamente ao serviço. Essa opção é usada principalmente para solução de problemas. Ao marcar essa caixa de seleção, a caixa de diálogo **Configuração da Área de Trabalho Remota** é exibida. Escolha o link Configurações para alterar a configuração.

    Marque a caixa de seleção **Ativar Implantação da Web para todas as funções Web** para habilitar a implantação da Web para o serviço. Você deve habilitar a Área de Trabalho Remota para usar esse recurso. Para saber mais, consulte [[Publicando um serviço de nuvem usando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Para obter mais informações sobre essa opção, consulte [[Publicando um serviço de nuvem usando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Escolha a guia **Configurações Avançadas**. No campo **Rótulo de implantação**, aceite o nome padrão ou insira um nome de sua escolha. Para anexar a data ao rótulo da implantação, deixe a caixa de seleção marcada.

    ![Terceira tela do assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Na lista **Conta de armazenamento** escolha a conta de armazenamento a ser usada para essa implantação. Compare os locais dos data centers para seu serviço de nuvem e sua conta de armazenamento. Idealmente, esses locais devem ser o mesmo.

    >[AZURE.NOTE]A conta de armazenamento do Azure armazena o pacote para a implantação do aplicativo. Depois que o aplicativo é implantado, o pacote é removido da conta de armazenamento.

1. Marque a caixa de seleção **Atualização da implantação** se quiser implantar apenas os componentes atualizados. Esse tipo de implantação pode ser mais rápido do que uma implantação completa. Escolha o link **Configurações** para abrir a caixa de diálogo **Configurações de atualização da implantação** mostrada na ilustração a seguir.

    ![Configurações de implantação](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Você pode escolher uma das duas opções de implantação de atualização incremental ou simultânea. Uma implantação incremental atualiza uma instância implantada por vez, para que seu aplicativo permaneça online e disponível aos usuários. Uma implantação simultânea atualiza todas as instâncias implantadas ao mesmo tempo. Atualização simultânea é mais rápida do que a atualização incremental, mas se você escolher essa opção, seu aplicativo poderá não estar disponível durante o processo de atualização.

    Você deve marcar a caixa de seleção Se a implantação não puder ser atualizada, faça uma implantação completa se quiser que a implantação completa ocorra automaticamente caso uma implantação de atualização falhe. Uma implantação completa redefine o endereço VIP (IP Virtual) para o serviço de nuvem. Para obter mais informações, consulte [Como manter um endereço IP virtual constante para um serviço de nuvem](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Para depurar seu serviço, marque a caixa de seleção **Habilitar IntelliTrace** ou, se você estiver implantando uma configuração de **Depuração** e quer depurar seu serviço de nuvem no Azure, marque a caixa de seleção **Habilitar depurador remoto para todas as funções** para implantar os serviços de depuração remota.

2. Para criar um perfil de aplicativo, marque a caixa de seleção **Habilitar criação de perfil** e escolha o link **Configurações** para exibir as opções de criação de perfil.


    >[AZURE.NOTE]Você deve usar o Visual Studio Ultimate para habilitar o IntelliTrace ou o TIP (Criação de Perfil de Interação de Camada) e não pode habilitar ambos ao mesmo tempo.

    Para saber mais, consulte [Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) e [Testando o desempenho de um serviço de nuvem](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Escolha **Avançar** para exibir a página de resumo do aplicativo.

## Publicando o aplicativo

1. Você pode optar por criar um perfil de publicação das configurações que escolheu. Por exemplo, você pode criar um perfil para um ambiente de teste e outro para produção. Para salvar esse perfil, escolha o ícone **Salvar**. O assistente cria o perfil e o salva no projeto do Visual Studio. Para modificar o nome do perfil, abra a lista **Perfil de destino** e escolha **< Gerenciar... >**.

    ![Tela de resumo do assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE]O perfil de publicação aparece no Gerenciador de Soluções no Visual Studio, e as configurações do perfil são gravadas em um arquivo com a extensão .azurePubxml. As configurações são salvas como atributos de marcas XML.

1. Escolha **Publicar** para publicar seu aplicativo. Você pode monitorar o status do processo de **Saída** no Visual Studio.

## Consulte também

[Configurando os serviços necessários para publicar um serviço de nuvem no Visual Studio](https://msdn.microsoft.com/library/azure/ff683668.aspx)

[Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publicando um serviço de nuvem usando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Teste de desempenho de um serviço de nuvem](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=AcomDC_1217_2015-->
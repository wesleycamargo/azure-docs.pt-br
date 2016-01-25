<properties
    pageTitle="Lista de contas do Armazenamento do Azure"
    description="Gerenciar as configurações de conta de armazenamento usando o Kit de Ferramentas do Azure para o Eclipse"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# Lista de contas do Armazenamento do Azure #

As contas de armazenamento do Azure permitem que os locais de download sejam usados para o seu JDK, servidor de aplicativos e componentes arbitrários, bem como para armazenar o estado ao usar o caching. O Eclipse mantém uma lista de contas de armazenamento conhecidas que estão disponíveis para seus projetos no espaço de trabalho do Eclipse. Para abrir o diálogo **Contas de Armazenamento**, que é usado para gerenciar essa lista, no Eclipse, clique em **Janela**, clique em **Preferências**, expanda **Azure**, e clique em **Contas de Armazenamento**.

Veja a seguir o diálogo **Contas de Armazenamento**.

![][ic719496]

Essa caixa de diálogo também pode ser aberta a partir de um link **Contas** nas caixas de diálogo que usam contas de armazenamento, como as seguintes:

* A guia **JDK** do diálogo **Configuração do Servidor**.
* A guia **Servidor** do diálogo **Configuração do Servidor**.
* O diálogo **Adicionar Componente**.
* O diálogo de propriedades **Caching**.

## Para importar suas contas de armazenamento usando um arquivo de configurações de publicação ##

1. No diálogo **Contas de Armazenamento**, clique em **Importar do arquivo de CONFIGURAÇÕES DE PUBLICAÇÃO**.
2. (Ignore esta etapa se você já tiver salvo um arquivo de configurações de publicação em seu computador local.) No diálogo **Importar Informações de Assinatura**, clique em **Baixar Arquivo de CONFIGURAÇÕES DE PUBLICAÇÃO**. Se você ainda não estiver conectado à sua conta do Azure, receberá uma solicitação para fazer logon. Em seguida, você receberá uma solicitação para salvar um arquivo de configurações de publicação do Azure. (Você pode ignorar as instruções resultantes mostradas nas páginas de logon - elas são fornecidas pelo portal do Azure e são destinadas aos usuários do Visual Studio.) Salve-o em seu computador local.
3. Ainda no diálogo **Importar Informações de Assinatura**, clique no botão **Procurar**, selecione o arquivo de configurações de publicação que você salvou localmente na etapa anterior e clique em **Abrir**.
4. Clique em **OK** para fechar o diálogo **Importar Informações de Assinatura**.

## Para criar uma nova conta de armazenamento ##

1. No diálogo **Contas de Armazenamento**, clique em **Adicionar**.
2. No diálogo **Adicionar Conta de Armazenamento**, clique em **Novo**.
3. No diálogo **Nova Conta de Armazenamento**, especifique valores para os seguintes itens:
    * Nome da conta de armazenamento.
    * Local da conta de armazenamento.
    * Descrição da conta de armazenamento.
    * A assinatura à qual pertence a conta de armazenamento.
4. Clique em **OK** para fechar o diálogo **Nova Conta de Armazenamento**.

Pode levar vários minutos para que a sua conta de armazenamento seja criada. Depois de criá-la, clique em **OK** para fechar o diálogo **Adicionar Conta de Armazenamento** para que a sua nova conta de armazenamento seja adicionada à lista de contas de armazenamento disponíveis.

## Para adicionar uma conta de armazenamento existente à lista ##

1. Se você ainda não tem uma conta de armazenamento do Azure, crie uma seguindo as etapas listadas na seção **Para criar uma nova conta de armazenamento** acima. (Como alternativa, você pode criar uma nova conta de armazenamento no [Portal de Gerenciamento do Azure][].)
2. No diálogo **Contas de Armazenamento**, clique em **Adicionar**.
3. No diálogo **Adicionar Conta de Armazenamento**, insira valores para **Nome** e **Chave de Acesso**. O nome da conta e a chave de acesso devem ser de uma conta existente do armazenamento do Azure. Use a seção **Armazenamento** do [Portal de Gerenciamento do Azure][] para exibir seus nomes e chaves de conta de armazenamento. O diálogo **Adicionar Conta de Armazenamento** será semelhante ao seguinte:

    ![][ic719497]

4. Clique em **OK** para fechar o diálogo **Adicionar Conta de Armazenamento**.

## Para modificar uma conta de armazenamento para usar uma nova chave de acesso ##

1. No diálogo **Contas de Armazenamento**, clique na conta de armazenamento que você deseja editar e clique em **Editar**.
2. No diálogo **Editar Chave de Acesso da Conta de Armazenamento**, modifique o valor de **Chave de Acesso**.
3. Clique em **OK** para fechar o diálogo **Chave de Acesso da Conta de Armazenamento**.

## Para remover uma conta de armazenamento da lista mantida no Eclipse ##

1. No diálogo **Contas de Armazenamento**, clique na conta de armazenamento que você deseja editar e clique em **Remover**.
2. Clique em **OK** quando for solicitado a remover a conta de armazenamento.

>[AZURE.NOTE]A remoção da conta de armazenamento por meio do diálogo **Contas de Armazenamento** apenas a remove da lista de contas de armazenamento que podem ser exibidas no Eclipse. Ela não remove a conta de armazenamento de sua assinatura do Azure. Além disso, a conta de armazenamento pode reaparecer em sua lista, depois que o Eclipse carregar novamente os detalhes de sua assinatura.

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Instalação do Kit de Ferramentas do Azure para o Eclipse][]

[Criação de um aplicativo Hello World do Azure no Eclipse][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!---HONumber=AcomDC_0114_2016-->
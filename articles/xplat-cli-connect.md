<properties
	pageTitle="Faça logon na interface de linha de comando do Azure (CLI do Azure) | Microsoft Azure"
	description="Conectar-se à assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)

A CLI do Azure é um conjunto de comandos entre plataformas, de software livre, para trabalhar com a plataforma Azure. Este documento descreve como conectar-se à sua assinatura do Azure na CLI do Azure. Para obter instruções de instalação, confira [Instalar a CLI do Azure](xplat-cli-install.md).

<a id="configure"></a>
## Como se conectar à assinatura do Azure

A maioria dos comandos fornecidos pela CLI do Azure exige uma conexão com uma conta do Azure. Há duas maneiras de configurar a CLI do Azure para trabalhar com a sua assinatura:

* Fazer logon no Azure usando uma conta corporativa ou de estudante (também chamada de uma conta organizacional). Nesse caso, o Active Directory do Azure é usado para autenticar as credenciais.

ou

* Baixar e usar um arquivo de configurações para publicação. Esse método instala um certificado que permite realizar tarefas de gerenciamento enquanto a assinatura e o certificado forem válidos.

Para obter mais informações sobre a autenticação e o gerenciamento da assinatura, consulte ["Qual é a diferença entre autenticação com base na conta e autenticação com base no certificado"][authandsub]

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][free-trial].

> [AZURE.NOTE]Os serviços mais flexíveis e avançados do Azure usam o Gerenciador de Recursos do Azure ou o [modo ARM](xplat-cli-azure-resource-manager.md). O modo ARM requer conexão com o Azure usando uma conta corporativa ou de estudante, com o método de logon descrito a seguir.

### Usar o método de logon

Esse método de logon funciona somente com uma conta corporativa ou de estudante. Essa conta é gerenciada pela sua organização e definida no Active Directory do Azure da organização.

> [AZURE.NOTE]Se você não tiver uma conta corporativa ou de estudante no momento e estiver usando uma conta pessoal para fazer logon na assinatura do Azure, poderá criar uma facilmente usando as etapas a seguir.
>
> 1. Faça logon no [Portal do Azure][portal] e selecione **Active Directory**.
>
> 2. Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.
>
> 3. Selecione o diretório e adicione um novo usuário. Esse novo usuário é uma conta corporativa ou de estudante.
>
>     Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações, pois elas serão necessárias posteriormente.
>
> 4. No Portal do Azure, selecione **Configurações** e, em seguida, **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta corporativa ou de estudante gerencie sua assinatura do Azure.
>
> 5. Por fim, faça logoff do portal do Azure e faça logon outra vez usando a nova conta corporativa ou de estudante. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.
>
> 6. Verifique se as suas assinaturas são exibidas ao fazer logon com a conta corporativa ou de estudante.
>
>Para saber mais sobre contas corporativas ou de estudante, confira [Inscrever-se no Microsoft Azure como uma organização][signuporg].

Para fazer logon na CLI do Azure usando uma conta corporativa ou de estudante, use o seguinte comando:

	azure login -u <username>

e digite a senha quando for solicitado.

> [AZURE.NOTE]Se este for o primeiro logon usando essas credenciais, você deverá verificar se deseja armazenar em cache um token de autenticação. Esse aviso também ocorrerá se você já tiver usado o comando `azure logout` descrito a seguir. Para ignorar esse aviso em cenários de automação, use o parâmetro `-q` com o comando `azure login`.

Para fazer logoff, use o seguinte comando:

	azure logout -u <username>

> [AZURE.NOTE]Se as assinaturas associadas à conta forem autenticadas apenas com o Active Directory, o logout excluirá as informações de assinatura do perfil local. Porém, se um arquivo de configurações de publicação também tiver sido importado para as assinaturas, o logoff só excluirá as informações relacionadas ao Active Directory do perfil local.

### Usar o método do arquivo de configurações de publicação

> [AZURE.NOTE]Se você se conectar usando este método, só poderá usar os comandos do Gerenciamento de Serviços do Azure (ou o modo ASM).

Para baixar as configurações de publicação de sua conta, use o seguinte comando:

	azure account download

Esse comando abrirá o navegador padrão e solicitará que você entre no [Portal do Azure][portal]. Depois que você entrar, o arquivo `.publishsettings` será baixado. Anote onde esse arquivo está salvo.

> [AZURE.NOTE]Se a conta estiver associada a vários locatários do Active Directory do Azure, você deverá selecionar para qual Active Directory você deseja baixar um arquivo de configurações de publicação.
>
> Depois de selecionado por meio da página de download ou acessando o Portal do Azure, o Active Directory escolhido se torna o padrão usado pelo portal e pela página de download. Depois que um padrão tiver sido estabelecido, você verá o texto '__clique aqui para retornar à página de seleção__' na parte superior da página de download. Use o link fornecido para retornar à página de seleção.

Em seguida, importe o arquivo `.publishsettings` executando o seguinte comando:

	azure account import <path to your .publishsettings file>

Depois de importar as configurações de publicação, você deverá excluir o arquivo `.publishsettings`, uma vez que ele não é mais exigido pelas CLI do Azure e impõe um risco à segurança, pois pode ser usado para obter acesso à sua assinatura.

> [AZURE.NOTE]Quer você faça logon com uma conta corporativa ou de estudante ou importe as configurações de publicação, as informações para acessar sua assinatura do Azure são armazenadas em um diretório `.azure` localizado em seu diretório `user`. O diretório `user` é protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user`. Você pode fazer isso das seguintes maneiras:
>
> * No Windows, modifique as propriedades do diretório ou use o BitLocker.
> * No Mac, ative o FileVault para o diretório.
> * No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições Linux oferecem recursos equivalentes.

### Várias assinaturas

Se você tiver várias assinaturas do Azure, a conexão ao Azure dará acesso a todas as assinaturas associadas às suas credenciais. Uma assinatura será selecionada como padrão e será usada pela CLI do Azure durante a realização das operações. Você pode visualizar as assinaturas, bem como qual delas é a padrão, usando o comando `azure account list`. Esse comando retornará informações semelhantes às seguintes:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Na lista acima, a coluna **Atual** indica a assinatura padrão atual como Azure-sub-1. Para alterar a assinatura padrão, use o comando `azure account set` e especifique a assinatura que você deseja que seja a padrão. Por exemplo:

	azure account set Azure-sub-2

Isso irá alterar a assinatura padrão para Azure-sub-2.

> [AZURE.NOTE]A alteração da assinatura padrão entra em vigor imediatamente, e é uma alteração global. Novos comandos da CLI do Azure, quer sejam executados na mesma instância de linha de comando ou em outra instância, usarão a nova assinatura padrão.

Se quiser usar uma assinatura não padrão com a CLI do Azure, mas não quiser alterar o padrão atual, você poderá usar a opção `--subscription` do comando e fornecer o nome da assinatura que deseja usar na operação.

Uma vez conectado à sua assinatura do Azure, você pode começar a usar os comandos da CLI do Azure. Para saber mais, confira [Como usar a CLI do Azure](xplat-cli.md).

<a id="additional-resources"></a>
## Recursos adicionais

* [Usando a CLI do Azure com os comandos de Gerenciamento de Serviços (ou modo ASM)][cliasm]

* [Usando a CLI do Azure com os comandos de Gerenciamento de Recursos (ou modo ARM)][cliarm]

* Para saber mais sobre a CLI do Azure, baixar o código fonte, relatar problemas ou contribuir com o projeto, visite o [Repositório GitHub da CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Para saber mais sobre o Azure, confira [http://azure.microsoft.com/](http://azure.microsoft.com).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=July15_HO1-->
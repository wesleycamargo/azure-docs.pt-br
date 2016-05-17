<properties
	pageTitle="Fazer logon no Azure da CLI | Microsoft Azure"
	description="Conectar-se à assinatura do Azure a partir da CLI (Interface de Linha de Comando) do Azure para Mac, Linux e Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="danlep"/>

# Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)

A CLI do Azure é um conjunto de comandos entre plataformas, de software livre, para trabalhar com a plataforma Azure. Este artigo descreve maneiras de fornecer as credenciais de conta do Azure para conectar a CLI do Azure à sua assinatura do Azure. Se você ainda não tiver instalado a CLI, consulte [Instalar a CLI do Azure](xplat-cli-install.md). Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](http://azure.microsoft.com/free/) em apenas alguns minutos.

Há duas formas de se conectar à sua assinatura da CLI do Azure:

* **Entre no Azure usando uma conta corporativa ou de estudante ou uma identidade de conta da Microsoft** - use o comando `azure login` no CLI versão 0.9.10 e posteriores com qualquer tipo de identidade de conta para autenticar através do Azure Active Directory. A CLI (versão 0.9.9 e superior) também dá suporte a autenticação interativa através de um portal da Web para contas que tenham a autenticação multifator habilitada. Use também o comando `azure login` para autenticar uma entidade de serviço para um aplicativo do Azure Active Directory, que é útil para executar serviços automatizados. Depois de fazer logon com uma identidade de conta com suporte, você pode usar o modo Azure Resource Manager ou comandos do modo Gerenciamento de Serviços do Azure.

* **Baixar e usar um arquivo de configurações de publicação** - Instala um certificado no computador local que permite executar tarefas de gerenciamento durante o tempo em que a assinatura e o certificado forem válidos. Esse método só permite que você use comandos do modo Gerenciamento de Serviços do Azure.

>[AZURE.NOTE] Se estiver usando uma versão da CLI do Azure anterior à versão 0.9.10, é possível usar o comando `azure login` apenas com uma conta corporativa ou de estudante; as identidades de conta da Microsoft não funcionam. No entanto, se desejar, você pode [criar uma ID corporativa ou de estudante a partir da sua ID de conta da Microsoft](virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Para obter informações sobre identidades de outra conta e sobre as assinaturas do Azure, confira [Como as assinaturas do Azure como são associadas ao Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

## Usar o logon do Azure para autenticar interativamente

Use o comando `azure login` – sem nenhum argumento – para autenticar de modo interativo com:

- uma identidade corporativa ou de estudante (também chamada de uma *conta organizacional*) que requer autenticação multifator, ou
- uma identidade de conta da Microsoft para acessar comandos do modo Resource Manager

> [AZURE.NOTE]  Em ambos os casos, a autenticação e a autorização são executadas com o Active Directory do Azure. Se você usar uma identidade de conta da Microsoft, o processo de logon acessará seu domínio padrão do Active Directory do Azure. (Se você se inscreveu para obter uma conta gratuita do Azure, você pode não estar ciente de que o Azure Active Directory criou um domínio padrão para a sua conta).

Fazer logon no modo interativo é fácil: digite `azure login` e siga os prompts, como mostrado abaixo:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Copie o código oferecido, acima, e abra um navegador em http://aka.ms/devicelogin. Digite o código; em seguida, você será solicitado a inserir o nome de usuário e a senha para a identidade que deseja usar. Quando esse processo for concluído, o shell de comando concluirá o processo de logon. Ele poderia ser semelhante ao seguinte:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Usar o logon do Azure com um nome de usuário e senha


Use o comando `azure login` com um parâmetro de nome de usuário ou com um nome de usuário e uma senha para autenticação quando você deseja usar uma conta corporativa ou de estudante que não exija autenticação multifator. O exemplo a seguir passa o nome de usuário de uma conta organizacional:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Insira a senha quando solicitado.

Se essa for a primeira vez fazendo logon com essas credenciais, você será solicitado a verificar se deseja armazenar um token de autenticação no cache. Esse aviso também ocorrerá se você tiver usado anteriormente o comando `azure logout` (descrito mais abaixo no artigo). Para ignorar esse aviso em cenários de automação, execute `azure login` com o parâmetro `-q`.

   

## Usar o logon do Azure com uma entidade de serviço

Se você criou uma entidade de serviço para um aplicativo do Active Directory e a entidade de serviço tem permissões em sua assinatura, você pode usar o comando `azure login` para autenticar a entidade de serviço. Dependendo do cenário, você pode fornecer as credenciais da entidade de serviço como parâmetros explícitos do comando `azure login` ou por meio de um script CLI ou código de aplicativo. Você também pode usar um certificado para autenticar a entidade de serviço não interativa para cenários de automação. Para obter detalhes e exemplos, confira [Autenticar uma entidade de serviço com o Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Usar um arquivo de configurações de publicação

Se você precisa apenas usar os comandos CLI do modo Gerenciamento de Serviços do Azure, é possível conectar usando um arquivo de configurações de publicação.

* **Para baixar o arquivo de configurações de publicação** para sua conta, use o seguinte comando (disponível apenas no modo Gerenciamento de Serviço):

		azure account download

    Isso abre o navegador padrão e solicita que você entre no [portal clássico do Azure](https://manage.windowsazure.com). Depois de entrar, um arquivo `.publishsettings` é baixado. Anote onde esse arquivo está salvo.

    > [AZURE.NOTE] Se a conta estiver associada a vários locatários do Active Directory do Azure, você deverá selecionar para qual Active Directory você deseja baixar um arquivo de configurações de publicação.

    Depois de selecionado por meio da página de download ou acessando o Portal Clássico do Azure, o Active Directory escolhido se torna o padrão usado pelo portal clássico e pela página de download. Depois que um padrão tiver sido estabelecido, você verá o texto `__clique aqui para retornar à página de seleção__` na parte superior da página de download. Use o link fornecido para retornar à página de seleção.

* **Para importar o arquivo de configurações de publicação**, execute o seguinte comando:

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]Depois de importar as suas configurações de publicação, exclua o arquivo `.publishsettings`. Ele não é mais exigido pelas CLI do Azure e apresenta um risco de segurança já que pode ser usado para obter acesso à sua assinatura.

## Várias assinaturas

Se você tiver várias assinaturas do Azure, a conexão ao Azure dará acesso a todas as assinaturas associadas às suas credenciais. Uma assinatura é selecionada como padrão e usada pela CLI do Azure durante a realização das operações. Você pode visualizar as assinaturas, bem como qual delas é a padrão, usando o comando `azure account list`. Esse comando retorna informações semelhantes às seguintes:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Na lista acima, a coluna **Atual** indica a assinatura padrão atual como Azure-sub-1. Para alterar a assinatura padrão, use o comando `azure account set` e especifique a assinatura que você deseja que seja a padrão. Por exemplo:

	azure account set Azure-sub-2

Isso muda a assinatura padrão para o Azure sub-2.

> [AZURE.NOTE] A alteração da assinatura padrão entra em vigor imediatamente, e é uma alteração global. Novos comandos da CLI do Azure, quer sejam executados na mesma instância de linha de comando ou em outra instância, usam a nova assinatura padrão.

Se quiser usar uma assinatura não padrão com a CLI do Azure, mas não quiser alterar o padrão atual, você poderá usar a opção `--subscription` do comando e fornecer o nome da assinatura que deseja usar na operação.

Uma vez conectado à sua assinatura do Azure, você pode começar a usar os comandos da CLI do Azure para trabalhar com recursos do Azure.

## Modos de comando da CLI

A CLI do Azure fornece dois modos de comando para trabalhar com recursos do Azure, com vários conjuntos de comandos:

* **Modo do Gerenciador de Recursos** - para trabalhar com recursos do Azure no modelo de implantação do Gerenciador de Recursos. Para definir esse modo, execute `azure config mode arm`.

* **Modo Gerenciamento de Serviços** - para trabalhar com recursos do Azure no modelo de implantação clássico. Para definir esse modo, execute `azure config mode asm`.

Quando instalada inicialmente, a CLI estará no modo Gerenciamento de Serviços.

>[AZURE.NOTE]O modo do Gerenciador de Recursos e o modo do Gerenciamento de Serviços são mutuamente excludentes. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

## Armazenamento de configurações da CLI

Quer você faça logon com o comando `azure login` ou importe as configurações de publicação, os seus logs e perfil CLI serão armazenados em um diretório `.azure` localizado no seu diretório `user`. O diretório `user` é protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user`. Você pode fazer isso das seguintes maneiras:

* No Windows, modifique as propriedades do diretório ou use o BitLocker.
* No Mac, ative o FileVault para o diretório.
* No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições do Linux oferecem recursos semelhantes.

## Fazendo logout

Para fazer logoff, use o seguinte comando:

	azure logout -u <username>

Se as assinaturas associadas à conta forem autenticadas apenas com o Active Directory, o logoff excluirá as informações da assinatura do perfil local. No entanto, se um arquivo de configurações de publicação também tiver sido importado para as assinaturas, o logoff excluirá apenas as informações relacionadas ao Active Directory do perfil local.
## Próximas etapas

* Para usar os comandos da CLI do Azure, confira [Comandos da CLI do Azure no modo do Gerenciador de Recursos](./virtual-machines/azure-cli-arm-commands.md) e [comandos da CLI do Azure no modo Gerenciamento de Serviços do Azure](virtual-machines-command-line-tools.md).

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

<!---HONumber=AcomDC_0504_2016-->
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
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/27/2015"
	ms.author="danlep"/>

# Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)

A CLI do Azure é um conjunto de comandos entre plataformas, de software livre, para trabalhar com a plataforma Azure. Este artigo descreve como conectar-se à sua assinatura do Azure na CLI do Azure para usar todos os comandos da CLI. Se você ainda não tiver instalado a CLI, consulte [Instalar a CLI do Azure](xplat-cli-install.md).


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


Há duas formas de se conectar à sua assinatura da CLI do Azure:

* **Fazer logon no Azure usando uma conta corporativa ou de estudante ou uma identidade de conta da Microsoft** - Essa opção usa um desses tipos de identidade de conta para fazer a autenticação. A partir da versão 0.9.9 da CLI, a CLI dá suporte à autenticação interativa para contas que têm a autenticação multifator habilitada. Após o logon no modo interativo, você pode usar o Gerenciador de Recursos ou os comandos clássicos (Gerenciamento de Serviços).

* **Baixe e use um arquivo de configurações de publicação**: instala um certificado que permite executar tarefas de gerenciamento pelo tempo que a assinatura e o certificado forem válidos. Esse método permite que você use apenas comandos clássicos (Gerenciamento de Serviços).

Para obter mais informações sobre a autenticação e o gerenciamento da assinatura, consulte [Qual é a diferença entre a autenticação baseada em conta e a autenticação baseada em certificado][authandsub].

Se não tiver uma conta do Azure, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][free-trial].

> [AZURE.NOTE]Se estiver usando uma versão da CLI do Azure anterior à versão 0.9.9, é possível usar o comando `azure login` apenas com identidades de conta corporativa ou de estudante; as identidades de conta da Microsoft não funcionam. Entretanto, você pode usar qualquer identidade para fazer logon em sua conta com o comando `azure login` interativo com as versões 0.9.9 e superior da CLI do Azure.



## Usar o método de logon interativo

Use o comando `azure login` – sem nenhum argumento – para fazer a autenticação de modo interativo com:

- uma identidade de conta corporativa ou de estudante que exija a autenticação multifator, ou
- uma identidade de conta da Microsoft quando desejar acessar a funcionalidade de modo de implantação do Gerenciador de Recursos

> [AZURE.NOTE]Em ambos os casos, a autenticação e a autorização são realizadas com o Active Directory do Azure; no caso de contas da Microsoft, pelo acesso ao seu domínio padrão do Active Directory do Azure. (Se você se inscreveu para obter uma avaliação gratuita, você pode não estar ciente de que o Active Directory do Azure criou um domínio padrão para a sua conta.)

Fazer logon no modo interativo é fácil: digite `azure login` e siga os prompts, como mostrado abaixo:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code B4MGHQS7K to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.
	
Copie o código oferecido, acima, e abra um navegador em http://aka.ms/devicelogin. Digite o código; em seguida, você será solicitado a inserir o nome de usuário e a senha para a identidade que deseja usar. Quando esse processo for concluído, o shell de comando concluirá o processo de logon. Ele poderia ser semelhante ao seguinte:
	
	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Usando o logon não interativo com uma conta corporativa ou de estudante


O método de logon não interativo funciona apenas com um conta corporativa ou de estudante, também chamada de *conta organizacional*. Essa conta é gerenciada pela sua organização e definida no Active Directory do Azure da organização. Você pode [criar uma conta organizacional](#create-an-organizational-account) se não tiver uma ou [criar uma ID corporativa ou de estudante por meio de sua ID de conta da Microsoft](./virtual-machines/resource-group-create-work-id-from-personal.md). Isso exige que você especifique um nome de usuário ou um nome de usuário e uma senha para o comando `azure login`, desta forma:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK
	
Insira a senha quando solicitado.

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **Para fazer logoff**, use o seguinte comando:

		azure logout -u <username>

	Se as assinaturas associadas à conta forem autenticadas apenas com o Active Directory, o logoff excluirá as informações da assinatura do perfil local. No entanto, se um arquivo de configurações de publicação também tiver sido importado para as assinaturas, o logoff excluirá apenas as informações relacionadas ao Active Directory do perfil local.

## Usar o método do arquivo de configurações de publicação

Se você precisa apenas usar os comandos clássicos (Gerenciamento de Serviços) da CLI, é possível conectar usando um arquivo de configurações de publicação.

* **Para baixar o arquivo de configurações de publicação** de sua conta, use o seguinte comando:

		azure account download

Isso abre o navegador padrão e solicita que você entre no [Portal do Azure][portal]. Depois de entrar, um arquivo `.publishsettings` é baixado. Anote onde esse arquivo está salvo.

	> [AZURE.NOTE] If your account is associated with multiple Azure Active Directory tenants, you may be prompted to select which Active Directory you wish to download a publish settings file for.
	>
	> Once selected using the download page, or by visiting the Azure Portal, the selected Active Directory becomes the default used by the portal and download page. Once a default has been established, you will see the text '__click here to return to the selection page__' at the top of the download page. Use the provided link to return to the selection page.

* **Para importar o arquivo de configurações de publicação**, execute o seguinte comando:

		azure account import <path to your .publishsettings file>
	
	Depois de importar as configurações de publicação, você deverá excluir o arquivo `.publishsettings`, uma vez que ele não é mais exigido pelas CLI do Azure e impõe um risco à segurança, pois pode ser usado para obter acesso à sua assinatura.


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

> [AZURE.NOTE]A alteração da assinatura padrão entra em vigor imediatamente, e é uma alteração global. Novos comandos da CLI do Azure, quer sejam executados na mesma instância de linha de comando ou em outra instância, usam a nova assinatura padrão.

Se quiser usar uma assinatura não padrão com a CLI do Azure, mas não quiser alterar o padrão atual, você poderá usar a opção `--subscription` do comando e fornecer o nome da assinatura que deseja usar na operação.

Uma vez conectado à sua assinatura do Azure, você pode começar a usar os comandos da CLI do Azure. Para saber mais, confira [Como usar a CLI do Azure](xplat-cli-install.md).

## Armazenamento de configurações da CLI

Se você fizer logon com uma conta corporativa ou de estudante ou importar as configurações de publicação, seus logs e o perfil da CLI serão armazenados em um diretório `.azure` localizado no seu diretório `user`. O diretório `user` é protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user`. Você pode fazer isso das seguintes maneiras:

* No Windows, modifique as propriedades do diretório ou use o BitLocker.
* No Mac, ative o FileVault para o diretório.
* No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições do Linux oferecem recursos semelhantes.

## Recursos adicionais

* [Usando a CLI do Azure com os comandos do Gerenciamento de Serviços (clássico)][cliasm]

* [Usando a CLI do Azure com os comandos do Gerenciador de Recursos][cliarm]

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Nov15_HO1-->
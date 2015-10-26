<properties
	pageTitle="Baixar o SDK do Azure para PHP"
	description="Saiba como baixar e instalar o SDK do Azure para PHP."
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="tomfitz"/>

#Baixar o SDK do Azure para PHP

## Visão geral

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do PHP para o Azure. O SDK do Azure para o PHP inclui especificamente o seguinte:

* **As bibliotecas de cliente PHP para Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem.  
* **A Interface de Linha de Comando do Azure para Mac, Linux e Windows (Azure CLI)**. Esse é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. O CLI do Azure em qualquer plataforma, incluindo Mac, Linux e Windows.
* **PowerShell para Azure (somente Windows)**. Esse é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Windows Azure, como Serviços de Nuvem e Máquinas Virtuais.
* **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções deste tópico pressupõem que você tenha o [PHP][install-php] instalado.

> [AZURE.NOTE]Você deve ter o PHP 5.3 ou superior para usar as bibliotecas de cliente PHP para Azure.

##As bibliotecas de cliente PHP para Azure.

As Bibliotecas de Cliente PHP para Azure fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem, de qualquer sistema operacional. Essas bibliotecas podem ser instaladas através do Composer ou dos gerenciadores de pacotes PEAR ou manualmente.

Para obter informações sobre como usar as Bibliotecas de Cliente do PHP para Azure, consulte [Como usar o serviço Blob][blob-service], [Como usar o serviço Tabela][table-service] e [Como usar o serviço Fila][queue-service].

###Instalar por meio do Composer

1. [instalar o Git][install-git].


	> [AZURE.NOTE]No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Baixe o **[composer.phar][composer-phar]** na raiz do seu projeto.

4. Abra um prompt de comando e execute esta função na raiz do projeto

		php composer.phar install

###Instalar como um pacote PEAR

Para instalar as Bibliotecas de Clientes PHP para Azure como um pacote PEAR, siga estas etapas:

1. [Instale o PEAR][install-pear].
2. Configure o canal PEAR do Azure:

		pear channel-discover pear.windowsazure.com
3. Instale o pacote PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

Depois que a instalação for concluída, você poderá fazer referência às bibliotecas de classes do seu aplicativo.

###Instalar manualmente

Para baixar e instalar as Bibliotecas de Cliente PHP para o Azure manualmente, siga estas etapas:

1. Fazer o download de um arquivo. zip que contém as bibliotecas de [GitHub][php-sdk-github]. Como alternativa, divida o repositório e clone-o para sua máquina local. (A última opção requer uma conta do GitHub e ter o Git instalado localmente).

	> [AZURE.NOTE]As Bibliotecas de Cliente PHP para o Azure têm uma dependência de pacotes PEAR [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) e [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). A maneira recomendada para resolver essas dependências é instalando esses pacotes com o [gerenciador de pacotes PEAR](http://pear.php.net/manual/en/installation.php).

2. Copie o diretório `WindowsAzure` do arquivo baixado para a estrutura de diretórios do seu aplicativo e faça referência às classes do seu aplicativo.

##O PowerShell do Azure e o Emuladores do Azure.

O PowerShell do Azure é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como Serviços de Nuvem e Máquinas Virtuais). Os Emuladores do Azure são emuladores de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Esses componentes recebem suporte apenas no Windows.

A maneira recomendada de instalar os emuladores do PowerShell do Azure e do Azure é usando o [Microsoft Web Platform Installer][download-wpi]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers da Microsoft do SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar o PowerShell do Azure, consulte [Como usar o PowerShell do Azure][powershell-tools].

##CLI do Azure

O CLI do Azure é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. Para obter informações sobre como instalar a CLI do Azure, consulte [Instalar a CLI do Azure](xplat-cli-install.md).

## Próximas etapas

Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=Oct15_HO3-->
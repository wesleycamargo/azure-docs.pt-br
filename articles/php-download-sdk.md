<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Baixar o SDK do Azure para PHP

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do PHP para o Azure. O SDK do Azure para o PHP inclui especificamente o seguinte:

-   **As bibliotecas de cliente PHP para Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem.
-   **As Ferramentas de Linha de Comando do Azure para Mac e Linux**. Esse é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como Sites do Azure e Máquinas Virtuais do Azure. Essas ferramentas funcionam em qualquer plataforma, incluindo Mac, Linux e Windows.
-   **PowerShell para Azure (somente Windows)**. Esse é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Windows Azure, como Serviços de Nuvem e Máquinas Virtuais.
-   **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções deste tópico pressupõem que você tenha o [PHP][PHP] instalado.

> [WACOM.NOTE]
> Você deve ter o PHP 5.3 ou superior para usar as bibliotecas de cliente PHP para Azure.

## As bibliotecas de cliente PHP para Azure.

As Bibliotecas de Cliente PHP para Azure fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem, de qualquer sistema operacional. Essas bibliotecas podem ser instaladas através do Composer ou dos gerenciadores de pacotes PEAR ou manualmente.

Para obter informações sobre como usar as Bibliotecas de Cliente do PHP para Azure, consulte [Como usar o serviço Blob][Como usar o serviço Blob], [Como usar o serviço Tabela][Como usar o serviço Tabela] e [Como usar o serviço Fila][Como usar o serviço Fila].

### Instalar por meio do Composer

1.  [instalar o Git][instalar o Git].

    > [WACOM.NOTE]
    > No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2.  Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  Baixe o **[composer.phar][composer.phar]** na raiz do seu projeto.

4.  Abra um prompt de comando e execute esta função na raiz do projeto

        php composer.phar install

### Instalar como um pacote PEAR

Para instalar as Bibliotecas de Clientes PHP para Azure como um pacote PEAR, siga estas etapas:

1.  [Instale o PEAR][Instale o PEAR].
2.  Configure o canal PEAR do Azure:

        pear channel-discover pear.windowsazure.com

3.  Instale o pacote PEAR:

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

Depois que a instalação for concluída, você poderá fazer referência às bibliotecas de classes do seu aplicativo.

### Instalar manualmente

Para baixar e instalar as Bibliotecas de Cliente PHP para o Azure manualmente, siga estas etapas:

1.  Fazer o download de um arquivo. zip que contém as bibliotecas de [GitHub][GitHub]. Como alternativa, divida o repositório e clone-o para sua máquina local. (A última opção requer uma conta do GitHub e ter o Git instalado localmente).

    > [WACOM.NOTE]
    > As Bibliotecas de Clientes do PHP para Azure têm uma dependência dos pacotes PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] e [Mail\_mimeDecode][Mail\_mimeDecode]. A maneira recomendada para resolver essas dependências é instalando esses pacotes com o [gerenciador de pacotes PEAR][gerenciador de pacotes PEAR].

2.  Copie o diretório do `WindowsAzure` do arquivo morto baixado para a estrutura de diretórios do seu aplicativo e faça referência às classes do seu aplicativo.

## O PowerShell do Azure e o Emuladores do Azure.

O PowerShell do Azure é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como Serviços de Nuvem e Máquinas Virtuais). Os Emuladores do Azure são emuladores de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Esses componentes recebem suporte apenas no Windows.

A maneira recomendada de instalar os emuladores do PowerShell do Azure e do Azure é usando o [Microsoft Web Platform Installer][Microsoft Web Platform Installer]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers da Microsoft do SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar o PowerShell do Azure, consulte [Como usar o PowerShell do Azure][Como usar o PowerShell do Azure].

## Ferramenta de linha de comando do Azure para Mac e Linux

As Ferramentas de Linha de Comando do Azure para Mac e Linux são um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como Sites do Azure e Máquinas Virtuais do Azure. A lista a seguir descreve como instalar as ferramentas, dependendo do sistema operacional:

-   **Mac**: Baixe o Instalador do SDK do Azure: [][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>. Abra o arquivo .pkg baixado e conclua as etapas de instalação quando for solicitado.

-   **Linux**: instale a versão mais recente do [Node.js][Node.js] (consulte [Instalar o Node.js por meio do Gerenciador de Pacotes][Instalar o Node.js por meio do Gerenciador de Pacotes]), em seguida, execute o seguinte comando:

        npm install azure-cli -g

    > [WACOM.NOTE]
    > talvez seja necessário executar esse comando com privilégios elevados: `sudo npm install azure-cli -g`

Para obter informações sobre como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux, consulte [Como usar as Ferramentas de Linha de Comando para Mac e Linux][Como usar as Ferramentas de Linha de Comando para Mac e Linux].

  [PHP]: http://www.php.net/manual/en/install.php
  [Como usar o serviço Blob]: http://go.microsoft.com/fwlink/?LinkId=252714
  [Como usar o serviço Tabela]: http://go.microsoft.com/fwlink/?LinkId=252715
  [Como usar o serviço Fila]: http://go.microsoft.com/fwlink/?LinkId=252716
  [instalar o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [Instale o PEAR]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [gerenciador de pacotes PEAR]: http://pear.php.net/manual/en/installation.php
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Como usar o PowerShell do Azure]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Instalar o Node.js por meio do Gerenciador de Pacotes]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Como usar as Ferramentas de Linha de Comando para Mac e Linux]: http://go.microsoft.com/fwlink/?LinkId=252717

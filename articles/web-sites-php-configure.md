<properties
	pageTitle="Configure o PHP em aplicativos Web do Serviço de Aplicativo do Azure"
	description="Saiba como configurar a instalação padrão do PHP ou adicione uma instalação de PHP personalizada para aplicativos Web no Serviço de Aplicativo do Azure".
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Configure o PHP em aplicativos Web do Serviço de Aplicativo do Azure 

## Introdução

Este guia mostrará como configurar o tempo de execução do PHP interno em aplicativos Web para o [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), fornecer um tempo de execução personalizado do PHP e habilitar extensões. Para usar o Serviço de Aplicativo, inscreva-se para a [avaliação gratuita]. Para aproveitar ao máximo este guia, você deve primeiro criar um aplicativo Web do PHP no Serviço de Aplicativo.

## Como: Alterar a configuração do PHP interno
Por padrão, quando você cria um aplicativo Web do Serviço de Aplicativo, o PHP 5.4 é instalado e fica imediatamente disponível para uso. O melhor modo de visualizar a revisão da versão, sua configuração padrão e as extensões habilitadas é implantar um script que chame a função [phpinfo()].

Versões 5.5 e 5.6 do PHP também estão disponíveis, mas não são habilitadas por padrão. Para atualizar a versão do PHP, siga essas etapas:

1. Navegue até o seu aplicativo Web no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e clique no botão **Configurações**.

![Web App Settings][settings-button]

2. Na lâmina **Configurações**, selecione **Configurações do aplicativo** e escolha a nova versão do PHP.

![Application Settings][application-settings]

3. Clique no botão **Salvar** na parte superior da lâmina **Configurações do aplicativo Web**.

![Save configuration settings][save-button]

Para qualquer tempo de execução interno do PHP, é possível alterar as opções de configuração que não sejam diretrizes de nível de sistema apenas seguindo as etapas abaixo. (Para informações sobre diretrizes exclusivas de nível de sistema, consulte [Lista de diretrizes php.ini].)

1. Adicione um arquivo [.user.ini] no seu diretório raiz.
2. Adicione as definições de configuração ao arquivo `.user.ini`, usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse ativar a configuração `display_errors` e definir a configuração `upload_max_filesize` como 10 M, o seu arquivo `.user.ini` conteria esse texto:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Implante seu aplicativo Web.
4. Reinicie o aplicativo Web. (É necessário reiniciar, pois a frequência com a qual o PHP lê arquivos `.user.ini` é regida pela configuração `user_ini.cache_ttl`, que é uma configuração de nível de sistema e é igual, por padrão, a 300 segundos (5 minutos). Reiniciar o aplicativo Web força o PHP a ler as novas configurações no arquivo `.user.ini`).

Como uma alternativa para o uso de um arquivo `.user.ini`, é possível usar a função [ini_set()] em scripts para definir opções de configuração que não sejam diretrizes de nível de sistema.

## Como: habilitar extensões no tempo de execução padrão do PHP
Conforme indicado na seção anterior, a melhor forma de visualizar a versão padrão do PHP, sua configuração padrão e as extensões habilitadas é implantar um script que chame [phpinfo()]. Para habilitar extensões adicionais, siga as etapas abaixo.

1. Adicionar um diretório `bin` ao diretório raiz.
2. Coloque a extensão `.dll` no diretório `bin` (por exemplo, `php_mongo.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP (que, no momento da produção deste texto, é PHP 5.4), que sejam compatíveis com VC9 e nts (não thread-safe).
3. Implante seu aplicativo Web.
4. Navegue até o seu aplicativo Web no Portal do Azure e clique no botão **Configurações**.

	![Web App Settings][settings-button]

5. Na lâmina **Configurações**, selecione **Configurações do aplicativo** e role até a seção **Configurações do aplicativo**.
6. Na seção **Configurações do aplicativo**, crie uma chave **PHP_EXTENSIONS**. O valor para essa chave seria um caminho relativo à raiz do site: **bin\seu-arquivo-ext**.

	![Enable extension in app settings][php-extensions]

7. Clique no botão **Salvar** na parte superior da lâmina **Configurações do aplicativo Web**.

	![Save configuration settings][save-button]

Também há suporte para extensões Zend usando uma chave **PHP_ZENDEXTENSIONS**. Para habilitar múltiplas extensões, inclua uma lista de arquivos `.dll` separados por vírgulas para o valor de configuração do aplicativo.

## Como: usar um tempo de execução PHP personalizado
Em vez do tempo de execução padrão do PHP, os aplicativos Web do Serviço de Aplicativo podem usar um tempo de execução de PHP fornecido por você para executar scripts PHP. O tempo de execução que você fornece pode ser configurado por um arquivo `php.ini`, também fornecido por você. Para usar um tempo de execução personalizado do PHP com aplicativos Web, siga as etapas abaixo.

1. Obtenha uma versão do PHP para Windows que seja compatível com VC9 e que não seja thread-safe. Versões recentes do PHP para Windows podem ser encontradas aqui: [http://windows.php.net/download/]. Versões mais antigas podem ser encontradas neste arquivo: [http://windows.php.net/downloads/releases/archives/].
2. Modifique o arquivo `php.ini` para o seu tempo de execução. Observe que quaisquer definições de configuração que forem diretrizes exclusivamente de nível de sistema serão ignoradas por Aplicativos Web. (Para informações sobre diretrizes exclusivas de nível de sistema, consulte [Lista de diretrizes php.ini]).
3. Opcionalmente, adicione extensões para o seu tempo de execução do PHP e habilite-as no arquivo `php.ini`.
4. Adicione o diretório `bin` ao seu diretório raiz e coloque lá o diretório que contém o seu tempo de execução do PHP (por exemplo, `bin\php`).
5. Implante seu aplicativo Web.
4. Navegue até o seu aplicativo Web no Portal do Azure e clique no botão **Configurações**.

	![Web App Settings][settings-button]

7. Na lâmina **Configurações**, selecione **Configurações do aplicativo** e role até a seção **Mapeamentos de manipulador**. Adicione '*.php' para o campo Extensão e adicionar o caminho para o executável `php-cgi.exe`. Se você colocar seu tempo de execução do PHP no diretório `bin` na raiz do seu aplicativo, o caminho será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

8. Clique no botão **Salvar** na parte superior da lâmina **Configurações do aplicativo Web**.

	![Save configuration settings][save-button]

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretivas php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->
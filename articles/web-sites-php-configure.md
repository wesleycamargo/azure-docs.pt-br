<properties title="How to Configure PHP in Azure Websites" pageTitle="How to Configure PHP in Azure Websites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Como configurar o PHP nos Sites do Azure

Este guia mostrará a você como configurar o tempo de execução interno do PHP nos Sites do Azure, como fornecer um tempo de execução personalizado do PHP e habilitar extensões nos Sites do Azure. Para usar os Sites do Azure, inscreva-se para uma [avaliação gratuita][avaliação gratuita]. Para obter o máximo deste guia, você deverá, primeiramente, criar um site PHP nos Sites do Azure (consulte os [Tutoriais do centro de desenvolvedores de PHP][Tutoriais do centro de desenvolvedores de PHP]). Para informações gerais sobre como configurar os Sites do Azure, consulte [Como configurar sites][Como configurar sites].

## Sumário

-   [O que é são os sites do Azure?][O que é são os sites do Azure?]
-   [Como: Alterar a configuração PHP padrão][Como: Alterar a configuração PHP padrão]
-   [Como: habilitar extensões no tempo de execução interno do PHP][Como: habilitar extensões no tempo de execução interno do PHP]
-   [Como: Usar um tempo de execução PHP personalizado][Como: Usar um tempo de execução PHP personalizado]
-   [Próximas etapas][Próximas etapas]

## <a name="WhatIs"></a>O que são os sites do Azure?

Os sites do Azure permitem que você crie sites altamente dimensionáveis no Azure. É possível implantar sites de maneira fácil e rápida em um ambiente de nuvem altamente escalonável que permite que você comece pequeno e expanda com o crescimento do tráfego. Os Sites do Azure usam os idiomas e aplicativos de software livre de sua escolha e dão suporte à implantação com Git, FTP e TFS. É possível integrar com facilidade outros serviços, como MySQL, Banco de Dados SQL, Caching, CDN e Armazenamento.

## <a name="ChangeBuiltInPHP"></a>Como: Alterar a configuração do PHP interno

Por padrão, quando você cria um Site do Azure, o PHP 5.4 é instalado e torna-se imediatamente disponível para uso. A melhor forma de visualizar a revisão da versão, sua configuração padrão e as extensões habilitadas é implantar um script que chame a função [phpinfo()][phpinfo()].

O PHP 5.5 também está disponível, mas não está habilitado por padrão. Para habilitá-lo, siga estas etapas:

1.  No Portal do Windows Azure, navegue para o painel do seu site e clique em **Configurar**.

    ![Configurar a guia no painel dos sites][Configurar a guia no painel dos sites]

2.  Clique em PHP 5.5.

    ![Selecione a versão do PHP][Selecione a versão do PHP]

3.  Na parte inferior da página, clique em **Salvar**.

    ![Salvar definições de configuração][Salvar definições de configuração]

Para qualquer tempo de execução interno do PHP, é possível alterar as opções de configuração que não sejam diretrizes de nível de sistema apenas seguindo as etapas abaixo. (Para informações sobre diretrizes de nível de sistema apenas, consulte [Lista de diretrizes php.ini][Lista de diretrizes php.ini].)

1.  Adicione um arquivo [.user.ini][.user.ini] no seu diretório raiz.
2.  Adicione as definições de configuração ao arquivo `.user.ini`, usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse ativar a configuração \``display_errors` e definir a configuração \``upload_max_filesize` para 10M, o seu arquivo `.user.ini` conteria este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

3.  Implante seu aplicativo.
4.  Reinicie seu site. (É necessário reiniciar, pois a frequência com a qual o PHP lê arquivos `.user.ini` é regida pela configuração `user_ini.cache_ttl`, que é uma configuração de nível de sistema e é, por padrão, 300 segundos (5 minutos). Reiniciar o site força o PHP a ler as novas configurações no arquivo `.user.ini`).

Uma alternativa para o uso de um arquivo `.user.ini` é usar a função [ini\_set()][ini\_set()] em scripts para definir opções de configuração que não sejam diretrizes de nível de sistema apenas.

## <a name="EnableExtDefaultPHP"></a>Como: habilitar extensões no tempo de execução padrão do PHP

Conforme indicado na seção anterior, a melhor forma de visualizar a versão padrão do PHP, sua configuração padrão e as extensões habilitadas é implantar um script que chame [phpinfo()][phpinfo()]. Para habilitar extensões adicionais, siga as etapas abaixo.

1.  Adicionar um diretório `bin` ao seu diretório raiz.
2.  Colocar arquivos de extensão `.dll` no diretório `bin` (por exemplo, `php_mongo.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP (que, no momento deste texto, é PHP 5.4), que sejam não thread safe e compatíveis com a versão VC9.
3.  Implante seu aplicativo.
4.  No Portal do Windows Azure, navegue para o painel do seu site e clique em **Configurar**.

    ![Configurar a guia no painel dos sites][Configurar a guia no painel dos sites]

5.  Na seção **configurações do aplicativo**, crie uma chave **PHP\_EXTENSIONS** e um valor **bin\\your-ext-file**. Para habilitar várias extensões, inclua uma lista de arquivos `.dll` separada por vírgulas.

    ![Habilitar extensões em configurações do aplicativo][Habilitar extensões em configurações do aplicativo]

6.  Na parte inferior da página, clique em **Salvar**.

    ![Salvar definições de configuração][Salvar definições de configuração]

## <a name="UseCustomPHP"></a>Como: Usar um tempo de execução PHP personalizado

Em vez do tempo de execução padrão do PHP, os Sites do Azure podem usar um tempo de execução fornecido por você para executar scripts PHP. O tempo de execução que você fornece pode ser configurado por um arquivo `php.ini` também fornecido por você. Para usar um tempo de execução personalizado para o PHP nos Sites do Azure, siga as etapas abaixo.

1.  Obtenha uma versão do PHP para Windows que seja não thread safe e compatível com a versão VC9. Versões recentes do PHP para Windows podem ser encontradas aqui: [][]<http://windows.php.net/download/></a>. Versões mais antigas podem ser encontradas neste arquivo: [][1]<http://windows.php.net/downloads/releases/archives/></a>.
2.  Modifique o arquivo `php.ini` para o seu tempo de execução. Observe que quaisquer definições de configuração, que forem diretrizes de nível de sistema apenas, serão ignoradas pelos Sites do Azure. (Para informações sobre diretrizes de nível de sistema apenas, consulte [Lista de diretrizes php.ini][Lista de diretrizes php.ini].)
3.  Opcionalmente, adicione extensões para o seu tempo de execução do PHP e habilite-as no arquivo `php.ini`.
4.  Adicione o diretório `bin` ao seu diretório raiz e coloque lá o diretório que contém o seu tempo de execução do PHP (por exemplo, `bin\php`).
5.  Implante seu aplicativo.
6.  No Portal do Windows Azure, navegue para o painel do seu site e clique em **Configurar**.

    ![Configurar a guia no painel dos sites][Configurar a guia no painel dos sites]

7.  Na seção **mapeamentos de manipulador**, adicione `*.php` à EXTENSÃO e adicione o caminho ao `php-cgi.exe` executável. Se você colocar seu tempo de execução do PHP no diretório `bin` na raiz do seu aplicativo, o caminho será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Especificar o manipulador em mapeamentos de manipulador][Especificar o manipulador em mapeamentos de manipulador]

8.  Na parte inferior da página, clique em **Salvar**.

    ![Salvar definições de configuração][Salvar definições de configuração]

## <a name="NextSteps"></a>Próximas etapas

Agora que você aprendeu como configurar o PHP nos Sites do Azure, siga os links abaixo para saber como realizar mais.

-   [Configurar, monitorar e dimensionar seus sites no Azure][Configurar, monitorar e dimensionar seus sites no Azure]
-   [Baixar o SDK do Azure para PHP][Baixar o SDK do Azure para PHP]

  [avaliação gratuita]: https://www.windowsazure.com/pt-br/pricing/free-trial/
  [Tutoriais do centro de desenvolvedores de PHP]: https://www.windowsazure.com/pt-br/develop/php/tutorials/
  [Como configurar sites]: https://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-configure-websites/
  [Como: Alterar a configuração PHP padrão]: #ChangeBuiltInPHP
  [Como: habilitar extensões no tempo de execução interno do PHP]: #EnableExtDefaultPHP
  [Como: Usar um tempo de execução PHP personalizado]: #UseCustomPHP
  [Próximas etapas]: #NextSteps
  [Configurar a guia no painel dos sites]: ./media/web-sites-php-configure/configure.png
  [Selecione a versão do PHP]: ./media/web-sites-php-configure/select-php-version.png
  [Salvar definições de configuração]: ./media/web-sites-php-configure/save-button.png
  [Lista de diretrizes php.ini]: http://www.php.net/manual/en/ini.list.php
  [.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
  [Habilitar extensões em configurações do aplicativo]: ./media/web-sites-php-configure/app-settings.png
  []: http://windows.php.net/download/
  [1]: http://windows.php.net/downloads/releases/archives/
  [Especificar o manipulador em mapeamentos de manipulador]: ./media/web-sites-php-configure/handler-mappings.png
  [Configurar, monitorar e dimensionar seus sites no Azure]: http://www.windowsazure.com/pt-br/manage/services/web-sites/
  [Baixar o SDK do Azure para PHP]: http://www.windowsazure.com/pt-br/develop/php/common-tasks/download-php-sdk/

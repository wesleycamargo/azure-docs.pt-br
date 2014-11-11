<properties linkid="develop-python-web-site-with-django" urlDisplayName="Websites with Django" pageTitle="Python Websites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python website on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Criando Sites com o Django

Este tutorial descreve como começar a execução do Python em Sites do Azure. Os Sites do Azure fornecem hospedagem gratuita limitada e rápida implantação, além de poder usar o Python! Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Este tutorial mostra como implantar um aplicativo criado usando o framework Django. O tutorial descreve as etapas de implantação de seu aplicativo e todas as bibliotecas necessárias, incluindo Django. Você colocará tudo isso em um repositório Git que torna rápido e simples o envio de atualizações para o seu site. E, finalmente, você configurará o site recém-criado através de do Azure para que ele execute seu aplicativo Python.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Este tutorial usa Python 2.7 e Django 1.4. Você pode obtê-los usando uma maneira própria ou fazendo a instalação de maneira rápida e fácil usando o link do Windows Installer no [][]<http://www.windowsazure.com/pt-br/develop/python/></a>

**Observação**: Os Sites do Azure agora vêm com o Python (2.7.3 ou 3.4.0, a escolha é sua) e o manipulador wfastcgi pré-instalados. No entanto, as estruturas da web, como Django, não estão incluídas. Se preferir, você ainda pode usar um intérprete de Python diferente. Basta incluí-lo no repositório Git e configurar o site para usar esse intérprete em vez do intérprete Python 2.7 já instalado.

> [WACOM.NOTE] Agora você pode escolher a versão do Python que deseja usar no portal dos Sites do Azure. Para isso, basta abrir a guia Configurar de seu site e alterar a configuração **Versão do Python**.

Você também precisará instalar uma opção de implantação para enviar o site para o Azure. Há várias ferramentas de implantação disponíveis, mas este tutorial usa Git. É recomendável [msysgit][msysgit]

**Observação**: A publicação do TFS atualmente não é suportada em projetos do Python.

Depois que você tiver instalado o Python, Django e Git, você terá tudo o que precisa para começar.

## Criação de sites no Portal

A primeira etapa na criação de seu aplicativo é criar o site através do Portal de Gerenciamento do Azure. Para fazer isso, você precisará fazer logon no portal e clicar no botão NOVO no canto inferior esquerdo. Uma janela será exibida. Clique em **Criação Rápida**, digite uma URL e selecione **Criar Site**.

![][0]

O site será rapidamente configurado. Em seguida, você adicionará suporte para publicação via Git. Isso pode ser feito escolhendo **Configurar a implantação do controle do código-fonte**.

![][1]

Na caixa de diálogo **Configurar implantação**, role para baixo e selecione a opção **Git Local**. Clique na seta direita para continuar.

![][2]

Depois de configurar a publicação do Git, você verá momentaneamente uma página informando que o repo está sendo criado. Quando o repo estiver pronto, você será levado para a guia de implantações. A guia implantações inclui instruções sobre como conectar-se.

![][3]

## Desenvolvimento de site

Agora que seu repositório Git no Azure está criado, você começará a preenchê-lo com o site a partir de seu computador local. A primeira etapa é clonar o site vazio existente utilizando o url fornecido:

![][4]

A partir daqui, você estará pronto para configurar a inscrição com o site. Você precisará fazer algumas coisas:

1.  Incluir a biblioteca do Django e outras bibliotecas que você usará para executar o site.
2.  Incluir o código do aplicativo Django.

Primeiro, inclua a biblioteca Django. Para fazer isso, crie um novo diretório chamado site-packages e copie sua versão instalada do Django usando esses comandos:

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

Esse comando copia todas as bibliotecas localizadas nos pacotes de site, incluindo Django. Se há bibliotecas não usadas pelo site, fique à vontade para removê-las.

![][5]

Em seguida, crie seu aplicativo Django inicial. Você pode fazer isso exatamente como você criaria qualquer outro aplicativo Django a partir da linha de comando, ou você pode usar [Ferramentas Python para Visual Studio][Ferramentas Python para Visual Studio] para criar o projeto. As duas opções são mostradas aqui.

**Opção 1:**
 Para criar o novo projeto a partir de uma linha de comando, execute o seguinte comando. O comando cria o aplicativo Django na pasta DjangoApplication:

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![][6]

**Opção 2:**
 Também é possível criar um novo site usando as Ferramentas Python para Visual Studio. Inicie o Visual Studio com ferramentas do Python para o Visual Studio instalado e selecione **Arquivo**-\>**Novo projeto**. Em **Outros idiomas**, vá até Projetos de Python e selecione **Aplicativo Django** Digite **DjangoApplication** como nome do projeto e deixe a opção **Criar diretório para solução** desmarcada para obter a mesma estrutura de diretório de quando você cria um aplicativo Django a partir da linha de comando. Esta opção configurará um arquivo de projeto e solução do Visual Studio que dão a você uma experiência de desenvolvimento local excelente, que inclui depuração de modelo e intellisense.

![][7]

Agora, basta adicionar todos os arquivos que você acabou de adicionar e enviar o site para o Git. Para fazer isso, execute estes comandos:

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

O primeiro comando irá adicionar seus arquivos não rastreados para que sejam rastreados. O segundo comando confirmará os arquivos que você acabou de adicionar ao repositório. O terceiro comando adiciona um remoto com o nome *azure* em seu repositório. O comando final leva as alterações e as envia para o repositório remoto. Este último comando também iniciará a implantação. Depois de fazer isso, você verá um resultado semelhante a este:

![][8]

Depois de fazer o push, você verá a atualização do portal do Azure e a implantação ativa será exibida:

![][9]

## Configuração do Site

Agora você precisa configurar o site para que ele saiba sobre seu projeto Django e use o manipulador wfastcgi. Para fazer isso, você pode clicar na guia Configurar na parte superior da tela e rolar até a parte inferior da página que contém as seções **configurações do aplicativo** e **mapeamentos de manipulador**.

Todas as configurações definidas aqui serão convertidas em variáveis de ambiente durante a solicitação atual. Isso significa que você pode usar isso para configurar a variável de ambiente DJANGO\\\_SETTINGS\\\_MODULE, bem como PYTHONPATH e WSGI\\\_HANDLER. Se seu aplicativo tiver outros valores de configuração, você pode atribuí-los aqui e coletá-los do ambiente. Às vezes, você desejará definir algo que é um caminho para um arquivo no seu site. Por exemplo, você desejará fazer isso para PYTHONPATH. Se estiver em execução como um Site do Azure, seu site ficará em \*\*D:\\home\\site\\wwwroot\*\*, de maneira que você pode usá-lo em qualquer lugar onde seja necessário usar um caminho inteiro para um arquivo em disco.

Para configurar um aplicativo Django, você precisa criar três variáveis de ambiente. A primeira é DJANGO\\\_SETTINGS\\\_MODULE, que fornece o nome do módulo do aplicativo Django que será usado para a configuração. A segunda é a variável de ambiente PYTHONPATH, que especifica o pacote onde reside o módulo de configurações. A terceira é WSGI\\\_HANDLER. Essa variável é um nome de módulo/pacote, seguido pelo atributo do módulo a ser usado (por exemplo, mypackage.mymodule.handler). Adicione parênteses para indicar que o atributo deve ser chamado. Para essas variáveis, faça a seguinte configuração:

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![][10]

Agora você precisa configurar o mapeamento de manipulador. Para isso, registre o manipulador para todas as extensões, usando o caminho para o intérprete de Python e o caminho do script wfastcgi.py:

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![][11]

Neste ponto, você pode clicar no botão **salvar** na parte inferior.

Por fim, volte para o Painel. Vá até o **URL do SITE** no lado esquerdo. Clique no link e abra o novo site Django, que terá a seguinte aparência:

![][12]

## Próximas etapas

A partir daqui, você pode continuar o desenvolvimento do seu aplicativo Django com as ferramentas que você já está usando. Se você estiver usando [Ferramentas Python para Visual Studio][Ferramentas Python para Visual Studio] para o desenvolvimento, você provavelmente desejará instalar [VisualGit][VisualGit] para obter a integração do controle do código-fonte de dentro do Visual Studio.

Seu aplicativo pode ter dependências além de Python e Django. Se você instalou o Python usando o instalador em [][]<http://www.windowsazure.com/pt-br/develop/python/></a>, o PIP já estará instalado e poderá ser usado para adicionar novas dependências rapidamente. Por exemplo, para instalar o Kit de ferramentas de linguagem Natural e todas as suas dependências, digite:

    pip install nltk

Você precisa atualizar o diretório de pacotes de site copiando os arquivos do C:\\Python27\\Lib\\site-packages em seu diretório de pacotes de site local.

Depois de copiar os arquivos, execute o comando **status git** para ver os arquivos recentemente adicionados, e **adicionar git** seguido de **confirmar git** para confirmar as alterações no repo. Por fim, você pode fazer um **push git**, que implementa o site atualizado no Azure.

Agora você pode ir para o diretório DjangoApplication e usar manage.py como faria normalmente para começar a adicionar novos aplicativos ao seu projeto Django.

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [0]: http://www.windowsazure.com/pt-br/develop/python/
  [msysgit]: http://code.google.com/p/msysgit/
  [0]: ./media/web-sites-python-create-deploy-django-app/django-ws-003.png
  [1]: ./media/web-sites-python-create-deploy-django-app/django-ws-004.png
  [2]: ./media/web-sites-python-create-deploy-django-app/django-ws-005.png
  [3]: ./media/web-sites-python-create-deploy-django-app/django-ws-006.png
  [4]: ./media/web-sites-python-create-deploy-django-app/django-ws-007.png
  [5]: ./media/web-sites-python-create-deploy-django-app/django-ws-008.png
  [Ferramentas Python para Visual Studio]: http://pytools.codeplex.com/
  [6]: ./media/web-sites-python-create-deploy-django-app/django-ws-010.png
  [7]: ./media/web-sites-python-create-deploy-django-app/django-ws-011.png
  [8]: ./media/web-sites-python-create-deploy-django-app/django-ws-013.png
  [9]: ./media/web-sites-python-create-deploy-django-app/django-ws-014.png
  [10]: ./media/web-sites-python-create-deploy-django-app/django-ws-015.png
  [11]: ./media/web-sites-python-create-deploy-django-app/django-ws-016.png
  [12]: ./media/web-sites-python-create-deploy-django-app/django-ws-017.png
  [VisualGit]: http://code.google.com/p/visualgit/

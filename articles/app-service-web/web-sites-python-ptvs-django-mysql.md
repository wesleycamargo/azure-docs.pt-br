---
title: Django e MySQL no Azure com Ferramentas Python 2.2 para Visual Studio
description: Aprenda a usar o Python Tools para Visual Studio para criar um aplicativo Django que armazena dados em uma instância do banco de dados MySQL e o implanta em Aplicativos Web do Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo

---
# Django e MySQL no Azure com Ferramentas Python 2.2 para Visual Studio
[!INCLUDE [guias](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste tutorial, usaremos o [Python Tools para Visual Studio](PTVS.md) para criar um aplicativo Web de votação simples, usando um dos modelos de exemplo de PTVS. Você aprenderá a usar um serviço MySQL hospedado no Azure, configurar o aplicativo Web para usar o MySQL e publicar o aplicativo Web para os [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [!NOTE]
> As informações contidas neste tutorial também estão disponíveis no vídeo a seguir:
> 
> [PTVS 2.1: aplicativo do Django com o MySQL][video]
> 
> 

Confira o [Python Developer Center] para obter mais artigos que abrangem o desenvolvimento de Aplicativos Web do Serviço de Aplicativo do Azure com PTVS usando estruturas da Web Bottle, Flask e Django, com serviços Armazenamento de Tabelas do Azure, MySQL e Banco de dados SQL. Embora este artigo se concentre no Serviço de Aplicativo, as etapas são semelhantes ao desenvolvimento de [Serviços de Nuvem do Azure].

## Pré-requisitos
* Visual Studio 2015
* [Python 2.7 de 32 bits] ou [Python 3.4 de 32 bits]
* [Ferramentas Python 2.2 para Visual Studio]
* [VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]
* [Ferramentas do SDK do Azure para VS 2015]
* Django 1.9 ou posterior

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido e não há compromissos.
> 
> 

## Criar o projeto
Nesta seção, criaremos um projeto do Visual Studio usando um modelo de amostra. Você irá criar um ambiente virtual e instalará os pacotes necessários. Você irá criar um banco de dados local usando sqlite. Em seguida, irá executar o aplicativo localmente.

1. No Visual Studio, selecione **Arquivo**, **Novo Projeto**.
2. Os modelos de projeto das [Ferramentas do Python 2.2 para Amostras VSIX do Visual Studio] estão disponíveis em **Python**, **Amostras**. Selecione **Projeto Web Django de Votações** e clique em OK para criar o projeto.
   
    ![Caixa de diálogo Novo Projeto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. Você será solicitado a instalar pacotes externos. Selecione **Instalar em um ambiente virtual**.
   
    ![Caixa de diálogo Pacotes Externos](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. Selecione **Python 2.7** ou **Python 3.4** como o interpretador base.
   
    ![Caixa de diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Python** e, em seguida, selecione **Django Migrate**. Em seguida, selecione **Criar Superusuário do Django**.
6. Isso abrirá um Console de Gerenciamento do Django e criará um banco de dados sqlite na pasta do projeto. Siga os prompts para criar um usuário.
7. Confirme se o aplicativo funciona pressionando `F5`.
8. Clique em **Fazer logon** na barra de navegação na parte superior.
   
    ![Barra de navegação do Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. Insira as credenciais para o usuário que você criou quando sincronizou o banco de dados.
   
    ![Formulário de logon](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. Clique em **Criar Votações de Exemplo**.
    
     ![Criar Votações de Exemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. Clique em uma pesquisa e vote.
    
     ![Votação em votações de exemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Criar um banco de dados MySQL
Para banco de dados, você irá criar um banco de dados ClearDB MySQL hospedado no Azure.

Como alternativa, você pode criar sua própria máquina virtual em execução no Azure, em seguida, instalar e administrar o MySQL por conta própria.

Você pode criar um banco de dados com um plano grátis seguindo estas etapas.

1. Faça logon no [Portal do Azure].
2. Na parte superior do painel de navegação, clique em **NOVO** e, em seguida, clique em **Dados + Armazenamento**, e depois clique em **Banco de dados MySQL**.
3. Configure o novo banco de dados MySQL, criando um novo grupo de recursos e selecione o local apropriado para o mesmo.
4. Depois de criar o banco de dados MySQL, clique em **Propriedades** na folha do banco de dados.
5. É possível usar o botão de cópia para colocar o valor de **CADEIA DE CONEXÃO** na área de transferência.

## Configurar o projeto
Nesta seção, você irá configurar nosso aplicativo Web para usar o banco de dados MySQL que acabou de criar. Também irá instalar pacotes adicionais Python necessários para usar bancos de dados MySQL com Django. Em seguida, você irá executar o aplicativo Web localmente.

1. No Visual Studio, abra **settings.py**, na pasta *ProjectName*. Cole temporariamente a cadeia de conexão obtida no editor. A cadeia de conexão está neste formato:
   
        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>
   
    Altere o **MECANISMO** do banco de dados padrão para usar MySQL e defina os valores para **NOME**, **USUÁRIO**, **SENHA** e **HOST** de **CADEIA DE CONEXÃO**.
   
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }
2. No Gerenciador de Soluções, em **Ambientes Python**, clique com o botão direito do mouse no ambiente virtual e selecione **Instalar Pacote Python**.
3. Instale o pacote `mysqlclient` usando **pip**.
   
    ![Caixa de diálogo Instalar Pacote](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Python** e, em seguida, selecione **Django Migrate**. Em seguida, selecione **Criar Superusuário do Django**.
   
    Isso criará as tabelas de banco de dados MySQL que você criou na seção anterior. Siga os prompts para criar um usuário, que não tem de corresponder ao usuário no banco de dados sqlite criado na primeira seção deste artigo.
5. Execute o aplicativo com `F5`. As votações criadas com **Criar Votações de Exemplo** e os dados enviados por voto serão serializados no banco de dados MySQL.

## Publicar aplicativo Web para Serviço de Aplicativo do Azure
O SDK .NET do Azure fornece uma forma fácil de implantar seu aplicativo Web no Serviço de Aplicativo do Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.
   
    ![Caixa de diálogo Web Publicar](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. Clique em **Serviço de Aplicativo do Microsoft Azure**.
3. Clique em **Novo** para criar um novo aplicativo Web.
4. Preencha os campos a seguir e clique em **Criar**:
   
   * **Nome do aplicativo Web**
   * **Plano do Serviço de Aplicativo**
   * **Grupo de recursos**
   * **Região**
   * Deixe **Servidor de banco de dados** definido como **Nenhum banco de dados**
5. Aceite todos os outros padrões e clique em **Publicar**.
6. Seu navegador da Web será aberto automaticamente para o aplicativo Web publicado. Você deve ver o aplicativo Web funcionando conforme o esperado, usando o banco de dados **MySQL** hospedado no Azure.
   
    ![Navegador da Web](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    Parabéns! Você publicou com êxito seu aplicativo Web com base em MySQL no Azure.

## Próximas etapas
Siga estas etapas para aprender mais sobre o Python Tools para Visual Studio, Django e MySQL.

* [Ferramentas Python para documentação do Visual Studio]
  * [Projetos da Web]
  * [Projetos do serviço de nuvem]
  * [Depuração remota no Microsoft Azure]
* [Documentação do Django]
* [MySQL]

Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

<!--Link references-->

[Python Developer Center]: /develop/python/
[Serviços de Nuvem do Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->

[Portal do Azure]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do Python 2.2 para Amostras VSIX do Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX de amostra de Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do SDK do Azure para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Ferramentas Python para documentação do Visual Studio]: http://aka.ms/ptvsdocs
[Depuração remota no Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projetos da Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projetos do serviço de nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentação do Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo

<!---HONumber=AcomDC_0713_2016-->
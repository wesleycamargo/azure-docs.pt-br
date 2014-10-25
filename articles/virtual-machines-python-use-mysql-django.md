<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Django Hello World - MySQL Windows Edition

Este tutorial descreve como usar o MySQL em conjunto com o Django em uma única máquina virtual do Azure. Este guia pressupõe que você tem alguma experiência anterior com o Azure e o Django. Para obter uma introdução do Azure e do Django, consulte [Django Hello World][Django Hello World]. Este guia também pressupõe que você tem algum conhecimento do MySQL. Para obter uma visão geral do MySQL, consulte o [site do MySQL][site do MySQL].

Neste tutorial, você aprenderá a:

-   Configure uma máquina virtual do Azure para hospedar o MySQL e o Django. Este tutorial explica o procedimento no Windows Server 2008 R2, mas o mesmo pode ser feito em uma VM do Linux hospedada no Azure.
-   Instalar um [driver do MySQL][driver do MySQL] para o Python.
-   Configure um aplicativo existente do Django para usar um banco de dados do MySQL.
-   Use o MySQL diretamente do Python.
-   Hospede e execute o seu aplicativo do MySQL Django.

Você expandirá o exemplo Olá, [Mundo do Django][Django Hello World] utilizando um banco de dados do MySQL, hospedado em uma VM do Azure, para encontrar um substituto melhor para *Mundo*. O substituto, por sua vez, será determinado através de um aplicativo *contador* do Django garantido pelo MySQL. Como foi o caso do exemplo Olá Mundo, esse aplicativo do Django será novamente hospedado em uma máquina virtual do Azure.

Os arquivos de projeto deste tutorial serão armazenados em **C:\\django\\helloworld** e o aplicativo completo será semelhante a:

![][]

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## Configurando uma máquina virtual para hospedar o MySQL e o Django

1.  Siga as instruções fornecidas [aqui][aqui]para criar uma máquina virtual do Azure da distribuição do *Windows Server 2008 R2*.

2.  Abra uma porta do TCP para transações do MySQL na máquina virtual:

 -   Navegue até a sua máquina virtual recém-criada no Portal do Azure e clique na guia *PONTOS DE EXTREMIDADE*.
 -   Clique em *ADICIONAR PONTO DE EXTREMIDADE* na parte inferior da tela.
 -   Adicione um ponto de extremidade com NOME = **mysql**, PROTOCOLO = **TCP**, PORTA PÚBLICA = **3306**, PORTA PRIVADA = **3306**.

1.  Adicione um outro ponto de extremidade com NOME = **web**, PROTOCOLO = **TCP**, PORTA PÚBLICA = **80**, PORTA PRIVADA = **80**. Isso redirecionará as solicitações externas da Internet para a porta na qual o Django é executado, isto é, *80*.

2.  Use a *Área de Trabalho Remota* do Windows para fazer logon remotamente na máquina virtual recém-criada do Azure.

3.  Abra a porta TCP **80** na máquina virtual:

 -   No menu **Iniciar**, selecione **Ferramentas Administrativas** e depois **Firewall do Windows com segurança avançada**.
 -   No painel esquerdo, selecione **Regras de Entrada**. À direita, no painel **Ações**, selecione **Nova Regra...**.
 -   No **Assistente para Nova Regra de Entrada**, selecione **Porta** e clique em **Avançar**.
 -   Selecione **TCP** e **Portas locais específicas**. Especifique uma porta de "80” (a porta que o Django escuta) e clique em **Avançar**.
 -   Selecione **Permitir a conexão** e clique em **Avançar**.
 -   Clique em **Avançar** novamente.
 -   Especifique um nome para a regra, como "DjangoPort", e clique em Concluir.

1.  Instale a versão mais recente do [MySQL Community Server][MySQL Community Server] para Windows na máquina virtual:

    **Observação**: é recomendável instalar o banco de dados em uma partição de dados diferente do SO.

 -   Execute o *Windows (x86, 64 bits), o link do MSI Installer* [aqui][MySQL Community Server] e baixe o instalador adequado do MSI do espelhamento de download mais próximo de você. Algumas dicas úteis:
    -   Selecione um Tipo de Instalação *Completo*.
    -   Selecione uma *Configuração Detalhada* no Assistente de Configuração.
    -   **Verifique se você habilitou a Rede TCP/IP na Porta Número 3306 e adicionou uma exceção de firewall para a porta.**
    -   Defina uma senha raiz e habilite o acesso à raiz a partir de máquinas remotas.
 -   Instale um [banco de dados "mundo"][banco de dados "mundo"] exemplo (versão do MyISAM):
    -   Baixe [este][este] arquivo zip na Máquina Virtual do Azure.
    -   **Descompacte-o em *C:\\Users\\Administrator\\Desktop\\world.sql*.**

1.  Depois de instalar o MySQL, clique no menu *Iniciar* do Windows e execute o recém-instalado *Cliente de Linha de Comando MySQL 5.5*. Dê os seguintes comandos:

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

	Você deverá ver uma resposta semelhante à seguinte:

	![][1]

1.  Antes que você possa começar a desenvolver o aplicativo do Django, será necessário instalar o Python+Django na máquina virtual. Isso pode ser feito através do [Web Platform Installer][Web Platform Installer]. Depois de instalar o PI da Web, use-o para pesquisar "Django" e instale o produto Django (Python).

	**Observação:** basta apenas instalar o produto *Django* do WebPI para ativar este tutorial. **Não** é necessário instalar o *Python Tools for Visual Studio* ou o Azure Python SDK para a nossa finalidade.

1.  Instale o pacote de cliente do MySQL Python. Você pode instalá-lo diretamente [deste link][deste link]. Depois de concluído, execute o comando a seguir para verificar a sua instalação:

	![][2]

## Expanda o aplicativo Django Hello World

1.  Siga as instruções fornecidas no tutorial do [Django Hello World][Django Hello World] para criar um aplicativo da Web "Olá Mundo" no Django.

2.  Abra **C:\\django\\helloworld\\helloworld\\settings.py** no editor de texto de sua preferência. Modifique o dicionário global **DATABASES** para ler:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'djangoazure',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                },
            'world': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'world',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                }
            }

	Como você pode ver, acabamos de dar instruções ao Django sobre onde encontrar nosso banco de dados MySQL.

	**Observação:** você **deve** alterar a chave *HOST* para corresponder ao endereço IP **permanente**da VM do seu Azure (MySQL). A esta altura, o *HOST* deve ser definido para o que quer que os *ipconfig* relatórios de comando do Windows estejam indicando.

	Depois que você modificar o *HOST* para corresponder ao endereço IP da VM do MySQL, salve e feche este arquivo.

1.  Agora que já referenciamos o nosso banco de dados *djangoazure*, vamos fazer algo útil com ele. Para isso, criaremos um modelo para um app *contador* trivial. Para instruir o Django a criá-lo, execute os comandos a seguir:

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

	Se o Django não relatar nenhuma saída do comando final acima, significa que ele foi bem-sucedido.

1.  Acrescente o texto a seguir em **C:\\django\\helloworld\\counter\\models.py**:

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

	Tudo o que fizemos aqui foi definir uma subclasse do *Django* do de nome de classe *Model* com um único campo inteiro, *count*. Esse modelo de contador trivial gravará o número de acessos ao nosso aplicativo Django.

1.  Em seguida, vamos tornar o Django ciente da existência do *Contador*:

 -   Edite o **C:\\django\\helloworld\\helloworld\\settings.py** novamente. Adicione *'contador'* à tupla *INSTALLED\_APPS*.
 -   A partir de um prompt de comando, execute:

            cd C:\django\helloworld
            C:\Python27\python manage.py sql counter
            C:\Python27\python manage.py syncdb

    Esses comandos armazenam o modelo do *Contador* no banco de dados dinâmico do Django e resultam em uma saída semelhante à seguinte:

        C:\django\helloworld> C:\Python27\python manage.py sql counter
        BEGIN;
        CREATE TABLE `counter_counter` (
            `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
            `count` integer NOT NULL
        )
        ;
        COMMIT;

        C:\django\helloworld> C:\Python27\python manage.py syncdb
        Creating tables ...
        Creating table auth_permission
        Creating table auth_group_permissions
        Creating table auth_group
        Creating table auth_user_user_permissions
        Creating table auth_user_groups
        Creating table auth_user
        Creating table django_content_type
        Creating table django_session
        Creating table django_site
        Creating table counter_counter

        You just installed Django's auth system, which means you don't have any superusers defined.
        Would you like to create one now? (yes/no): no
        Installing custom SQL ...
        Installing indexes ...
        Installed 0 object(s) from 0 fixture(s)

1.  Substitua o conteúdo de **C:\\django\\helloworld\\helloworld\\views.py**. A nova implementação da função *hello* abaixo usa o nosso modelo *Contador* em conjunto com uma amostra de banco de dados separada que instalamos anteriormente, *world*, para gerar um substituto adequando para a cadeia "*Mundo*":

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter

        def getCountry(intId):
            #Connect to the MySQL sample database 'world'
            cur = django.db.connections['world'].cursor()
            #Execute a trivial SQL query which returns the name of 
            #all countries contained in 'world'
            cur.execute("SELECT name from country")
            tmp = cur.fetchall()
            #Clean-up after ourselves
            cur.close()
            if intId >= len(tmp):
                return "countries exhausted"
            return tmp[intId][0]

        def hello(request):
            if len(Counter.objects.all())==0:
                #when the database corresponding to 'helloworld.counter' is 
                #initially empty...
                c = Counter(count=0)
            else:
                c = Counter.objects.all()[0]
                c.count += 1
            c.save()       
            world = getCountry(int(c.count))
            return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

## Implantando e executando seu site do Django

Observação: Veja a seguir como executar o Django em um ambiente de teste. Para executá-lo em produção, siga a seção "Configurando o IIS com FastCGI" no "Tutorial Django Hello World". Usar o Cliente de Firewall do Windows para abrir a porta 80 para o tráfego da Internet na máquina virtual do Windows Server 2K8 R2 não é necessário com o FastCGI.

1.  Volte para a janela do PowerShell do Windows e digite os comandos a seguir para implantar seu site do Django publicamente:

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    O parâmetro **runserver** instrui o para executar nosso site *helloworld* na porta TCP *80*. Os resultados desse comando devem ser semelhantes a:

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...

        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  No seu navegador da Web local, abra \*\*<http://*yourVmName>\*.cloudapp.net\*\* (onde *yourVmName* é qualquer nome que você usou na etapa de criação da máquina virtual). Você deve ver o "Hello ... !" conforme mostrado na captura de tela abaixo. Isso indica que o Django está em execução na máquina virtual e funcionando corretamente.

    ![][]

	Atualize o navegador da Web algumas vezes para ver a alteração da mensagem de *"Hello **\<country abc\>**"* para *"Hello **\<some other country\>**"*.

1.  Para interromper o Django de hospedar o site, basta alternar para a janela do PowerShell e pressionar **CTRL-C**.

## Desligando a máquina virtual do Azure

Depois que você concluir este tutorial, desligue e/ou remova sua máquina virtual recém-criada do Azure para liberar recursos para outros tutoriais e evitar a incidência de encargos de uso do Azure.

  [Django Hello World]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [site do MySQL]: http://dev.mysql.com/doc/
  [driver do MySQL]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  []: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [aqui]: /pt-br/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  [banco de dados "mundo"]: http://dev.mysql.com/doc/index-other.html
  [este]: http://downloads.mysql.com/docs/world.sql.zip
  [1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
  [Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [deste link]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
  [2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png

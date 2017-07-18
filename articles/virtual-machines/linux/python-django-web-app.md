---
title: Aplicativo Web Python com Django em uma VM Linux do Azure | Microsoft Docs
description: Saiba como hospedar um aplicativo Web baseado em Django no Azure usando uma VM Linux.
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: pt-br
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Aplicativo Web Django Olá, Mundo em uma VM do Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

Este tutorial mostra como hospedar um site baseado em Django no Linux em máquinas virtuais do Azure. No tutorial, supomos que não há nenhuma experiência anterior com o Azure. Quando concluir o tutorial, você pode ter um aplicativo baseado em Django funcionando na nuvem.

Saiba como:

* Configure uma máquina virtual Azure para hospedar o Django. Embora este tutorial explique como fazer isso para **Linux**, você pode fazer o mesmo para uma VM do Windows Server hospedada no Azure. 
* Crie um novo aplicativo Django do Linux.

O tutorial mostra como criar um aplicativo Web Olá, Mundo básico. O aplicativo é hospedado em uma máquina virtual do Azure.

A captura de tela a seguir mostra o aplicativo concluído:

![Uma janela do navegador exibe a página Olá, Mundo no Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual do Azure para hospedar o Django

1. Para criar uma máquina virtual do Azure com a distribuição Ubuntu Server 14.04 LTS, consulte [Criar uma máquina virtual Linux no Portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também pode escolher a autenticação de senha em vez de usar uma chave pública SSH.
2. Para editar o grupo de segurança de rede para permitir o tráfego HTTP de entrada para a porta 80, consulte [Criar grupos de segurança de rede no Portal do Azure](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. (Opcional) Por padrão, a nova máquina virtual não tem um FQDN (nome de domínio totalmente qualificado).  Para criar uma VM com um FQDN, consulte [Criar um FQDN no Portal do Azure para uma VM Windows](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esta etapa não é necessária para concluir este tutorial.

## <a id="setup"> </a>Configurar o ambiente de desenvolvimento
> [!NOTE]
> Se precisar instalar o Python ou se quiser usar as bibliotecas do cliente, confira o [Guia de instalação do Python](../../python-how-to-install.md).

A VM Ubuntu Linux tem o Python 2.7 pré-instalado, mas ele não vem com o Apache ou o Django. Conclua as etapas a seguir para se conectar à sua VM e instalar o Apache e o Django:

1. Abra uma nova janela de terminal.
2. Para se conectar à VM do Azure, insira o seguinte comando. Se não tiver criado um FQDN, poderá se conectar usando o endereço IP público exibido no resumo da máquina virtual no Portal do Azure.
   
       $ ssh yourusername@yourVmUrl
3. Para instalar o Django, digite os seguintes comandos:
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Para instalar o Apache com mod-wsgi, insira o seguinte comando:
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>Criar um novo aplicativo Django
1. Para usar o SSH para acessar sua VM, abra a janela do terminal usada na seção anterior.
2. Para criar um novo projeto de Django, insira os seguintes comandos:
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   O script `django-admin.py` gera uma estrutura básica para sites da web baseados em Django:
   
   * `helloworld/manage.py` ajuda a iniciar a hospedagem e parar a hospedagem do seu site da web baseado em Django.
   * `helloworld/helloworld/settings.py` tem configurações do Django para seu aplicativo.
   * `helloworld/helloworld/urls.py` tem o código de mapeamento entre cada URL e sua exibição.
3. No diretório /var/www/helloworld/helloworld, crie um novo arquivo chamado views.py. Esse arquivo contém a exibição que renderiza a página “Olá, Mundo”. Em seu editor de código, digite os seguintes comandos:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Substitua o conteúdo do arquivo urls.py com os seguintes comandos:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Configurar o Apache
1. Na pasta /etc/apache2/sites-available/helloworld.conf, crie um arquivo de configuração do host virtual Apache. Defina o conteúdo para os seguintes valores. Substitua *yourVmName* pelo nome real do computador que você está usando (por exemplo *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Para ativar o site, use o seguinte comando:
   
       $ sudo a2ensite helloworld
3. Para reiniciar o Apache, use o seguinte comando:
   
       $ sudo service apache2 reload
4. Carregue a página da Web no seu navegador:
   
   ![Uma janela do navegador exibe a página Olá, Mundo no Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Desligar a máquina virtual do Azure
Quando você concluir este tutorial, é recomendável desligar ou remover a VM do Azure que você criou para o tutorial. Isso libera os recursos para outros tutoriais e você pode evitar incorrer em encargos de uso do Azure.



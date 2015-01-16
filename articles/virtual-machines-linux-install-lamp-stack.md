<properties urlDisplayName="Install LAMP stack" pageTitle="Instalar a pilha LAMP em uma máquina virtual Linux" metaKeywords="" description="Saiba como instalar a pilha LAMP em uma VM (máquina virtual) Linux no Azure. Você pode instalar no Ubuntu ou no CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />



#Instalar a pilha LAMP em uma máquina virtual Linux no Azure

Uma pilha de LÂMPADA consiste nos seguintes elementos diferentes:

- **L**inux - Sistema operacional
- **A**pache Servidor Web
- **M**ySQL - Servidor de banco de dados
- **P**HP - Linguagem de programação


##Instalando no Ubuntu

Será necessário ter os seguintes pacotes instalados:

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

Após executar `apt-get update` para atualizar os pacotes de lista local, você pode instalar esses pacotes com um simples comando `apt-get install`:

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências.  Pressione 'y' e depois 'ENTER' para continuar e siga todas as solicitações para definir uma senha administrativa para MySQL.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# apt-cache search php5


##Instalando o CentOS & Oracle Linux

Será necessário ter os seguintes pacotes instalados:

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

Você pode instalar esses pacotes com um simples comando `yum install`:

	# sudo yum install httpd mysql mysql-server php php-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências.  Pressione 'y' e depois 'Enter' para continuar.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# yum search php


## Instalando o SUSE Linux Enterprise Server

Será necessário ter os seguintes pacotes instalados:

- apache2
- MySQL
- apache2-mod_php53
- php53-mysql

Você pode instalar esses pacotes com um simples comando `zypper install`:

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências.  Pressione 'y' e depois 'Enter' para continuar.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# zypper search php


Configurando
----------

1. Configurar **Apache**

	- Execute o comando a seguir para assegurar que o servidor Web Apache foi iniciado:

		- Ubuntu e SLES: `sudo service apache2 restart`

		- CentOS e Oracle: `sudo service httpd restart`

	- Apache ouve na porta 80 por padrão. Você precisa abrir o ponto de extremidade para acessar o servidor Apache remotamente.  Consulte a documentação sobre [configuração de pontos de extremidade](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-set-up-endpoints/) para instruções mais detalhadas.

	- Você pode verificar agora se o Apache está executado e fazendo a manutenção do conteúdo.  Aponte o servidor para `http://[MYSERVICE].cloudapp.net`, where **[MYSERVICE]** é o nome do serviço de nuvem onde sua máquina virtual está.Em algumas distribuições você pode simplesmente encontrar uma página da Web padrão dizendo apenas "Está funcionando".Em outras, você pode ver uma página da Web completa com links para documentação e conteúdo adicional para a configuração do servidor Apache.

2. Configurar **MySQL**

	- Observe que esse passo não é necessário no Ubuntu, o qual o solicita uma senha `root` MySQL quando o pacote do servidor MySQL for instalado.

	- Em outras distribuições, defina a senha raiz para MySQL, executando o seguinte comando:

			# mysqladmin -u root -p password yourpassword

	- Então é possível gerenciar MySQL usando os utilitários `mysql` ou `mysqladmin`.


##Leitura adicional

Há muitos recursos para configurar uma pilha de LÂMPADA no Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

<!--HONumber=35.1-->

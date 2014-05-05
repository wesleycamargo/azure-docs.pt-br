<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Instalar a pilha de LÂMPADA" pageTitle="instalar a pilha de LÂMPADA em uma máquina virtual Linux" metaKeywords="" description="aprender a instalar a pilha de LÂMPADA em uma máquina virtual do Linux (VM) no Azure. Você pode instalar o Ubuntu ou CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalar a pilha de LÂMPADA no computador virtual Linux no Azure" authors="" solutions="" manager="" editor="" />




#Instalar a pilha LAMP em uma máquina virtual Linux no Azure

Uma pilha de LÂMPADA consiste nos seguintes elementos diferentes:

- **L**inux - sistema operacional
- **A**pache - Servidor da Web
- **M**ySQL - o servidor de banco de dados
- **P**HP - linguagem de programação


##Instalando no Ubuntu

Você terá os seguintes pacotes instalados:

- `apache2`
- `servidor MySQL`
- `php5`
- `php5 mysql`
- `libapache2-mod-auth-mysql`
- `libapache2-mod-php5`
- `php5 xsl`
- `gd php5`
- `PHP pear`

Você pode executar isso como um único `apt-get install` comando:

	apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear


##Instalando o CentOS

Você terá os seguintes pacotes instalados:

- `httpd`
- `MySQL`
- `servidor MySQL`
- `php`
- `php5-mysql`

Você pode executar isso como um único comando `yum install`:

	yum install httpd mysql mysql-server php-php-mysql


Configurando
----------

1. Configurar **Apache**

	1. Você precisará reiniciar o servidor Web Apache. Execute o comando a seguir:

			sudo /etc/init.d/apache2 restart

	2. Verificar se a instalação está sendo executado. Aponte seu navegador para: [http://localhost](http://localhost) Deve ler "Ele funciona!".

2. Configurar **MySQL**
	1. Definir a senha raiz para mysql, executando o seguinte comando
	
			mysqladmin -u root -p password yourpassword

	2. Faça login no console usando o `mysql` ou uma variedade de clientes de MySQL.

3. Configurar **PHP**

	1. Habilite o módulo Apache PHPexec utando o seguinte comando:

			sudo a2enmod php5

	2. Reinicie o Apache executando o seguinte comando:

			sudo service apache2 restart


##Leitura adicional

Há muitos recursos para configurar uma pilha de LÂMPADA no Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
- [http://fedorasolved.org/server-solutions/lamp-stack](http://fedorasolved.org/server-solutions/lamp-stack)


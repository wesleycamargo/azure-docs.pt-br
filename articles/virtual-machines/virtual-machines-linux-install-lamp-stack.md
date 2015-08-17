<properties
	pageTitle="Instalar a pilha LAMP em uma máquina virtual Linux"
	description="Saiba como instalar a pilha LAMP em uma VM (máquina virtual) Linux no Azure. Você pode instalar no Ubuntu ou no CentOS."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/29/2015"
	ms.author="szark"/>



#Instalar a pilha LAMP em uma máquina virtual Linux no Azure

Uma pilha de LÂMPADA consiste nos seguintes elementos diferentes:

- **L**inux - Sistema operacional
- **A**pache Servidor da Web
- **M**ySQL - Servidor do bancos de dados
- **P**HP - Linguagem de programação


##Instalando no Ubuntu

Será necessário ter os seguintes pacotes instalados:

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

Após executar `apt-get update` para atualizar a lista local de pacotes, você pode instalar esses pacotes com um simples comando `apt-get install`:

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências. Pressione 'y' e depois 'ENTER' para continuar e siga todas as solicitações para definir uma senha administrativa para MySQL.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# apt-cache search php5


##Instalando no CentOS e no Oracle Linux

Será necessário ter os seguintes pacotes instalados:

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

Você pode instalar esses pacotes com um simples comando `yum install`:

	# sudo yum install httpd mysql mysql-server php php-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências. Pressione 'y' e depois 'Enter' para continuar.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# yum search php


## Instalando o servidor do SUSE Linux Enterprise

Você terá os seguintes pacotes instalados:

- apache2
- MySQL
- apache2-mod\_php53
- php53-mysql

Você pode instalar esses pacotes com um simples comando `zypper install`:

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Após executar o comando acima, você será solicitado a instalar esses pacotes e algumas outras dependências. Pressione 'y' e depois 'Enter' para continuar.

Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

	# zypper search php


Configurando
----------

1. Configurar **Apache**

	- Execute o comando a seguir para assegurar que o servidor Web Apache foi iniciado:

		- Ubuntu e SLES: `sudo service apache2 restart`

		- CentOS e Oracle: `sudo service httpd restart`

	- Apache ouve na porta 80 por padrão. Você precisa abrir o ponto de extremidade para acessar o servidor Apache remotamente. Consulte a documentação em [configuração de pontos de extremidade](virtual-machines-set-up-endpoints.md) para obter instruções mais detalhadas.

	- Você pode verificar agora se o Apache está executado e fazendo a manutenção do conteúdo. Aponte o navegador para `http://[MYSERVICE].cloudapp.net`, onde **[MYSERVICE]** é o nome do serviço de nuvem onde reside sua máquina virtual. Em algumas distribuições você pode simplesmente encontrar uma página da Web padrão dizendo apenas “Está funcionando". Em outras, você pode ver uma página da web completa com links para documentação e conteúdo adicional para a configuração do servidor Apache.

2. Configurar **MySQL**

	- Observe que esta etapa não é necessária no Ubuntu, que solicita a senha de `root` do MySQL de quando o pacote do servidor MySQL for instalado.

	- Em outras distribuições, defina a senha raiz para MySQL, executando o seguinte comando:

			# mysqladmin -u root -p password yourpassword

	- Você pode gerenciar o MySQL usando os utilitários `mysql` ou `mysqladmin`.


##Leitura adicional

Vamos supor que você deseja automatizar estas etapas para implantar aplicativos nas máquinas virtuais Linux remotas? Você pode fazer isso usando a extensão Linux CustomScript. Consulte [Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux](virtual-machines-linux-script-lamp.md).

Há muitos outros recursos para configurar uma pilha LAMP no Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
 

<!---HONumber=August15_HO6-->
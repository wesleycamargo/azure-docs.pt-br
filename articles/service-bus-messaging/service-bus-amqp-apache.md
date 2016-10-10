<properties 
    pageTitle="Como instalar o Proton-C Qpid do Apache em uma VM do Linux | Microsoft Azure"
    description="Como criar uma VM CentOS Linux usando as Máquinas Virtuais do Azure e como compilar e instalar a biblioteca Proton-C Qpid do Apache."
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Instalar o Apache Qpid Proton-C em uma VM Linux do Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Esta seção mostra como criar uma VM CentOS Linux usando Máquinas Virtuais do Azure e como baixar, criar e instalar a biblioteca Proton-C Qpid do Apache junto com as associações das linguagens Python e PHP. Depois de concluir estas etapas, você poderá executar os exemplos de Python e PHP incluídos neste guia.

A primeira etapa é realizada usando o [portal clássico do Azure][]. A captura de tela a seguir mostra como uma VM CentOS chamada "scott-centos" é criada:

![Proton em uma VM Linux do Azure][0]

Após o provisionamento, o portal exibirá o seguinte:

![Proton em uma VM Linux do Azure][1]

Para fazer logon no computador, você deverá saber qual é a porta do ponto de extremidade para o SSH. É possível obter esse valor do [portal clássico do Azure][] selecionando a VM recém-criada e clicando na guia **Pontos de extremidade**. A captura de tela a seguir mostra que a porta SSH pública para este computador é a 57146.

![Proton em uma VM Linux do Azure][2]

Agora você pode se conectar ao computador usando o SSH. Este exemplo usa a ferramenta PuTTY, como na captura de tela a seguir:

![Proton em uma VM Linux do Azure][3]

Para os aplicativos Python e PHP, este exemplo usa as bibliotecas cliente Proton do Apache. Essas bibliotecas estão disponíveis para download em [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O arquivo Leiame no pacote de distribuição explica as etapas necessárias para instalar as dependências e compilar o Proton. Veja um resumo das etapas:

1.  Edite o arquivo de configuração yum (/etc/yum.conf) e comente a exclusão de atualizações para cabeçalhos de kernel (# exclude=kernel*). Isso é necessário para instalar o compilador gcc.

2.  Instale os pacotes de pré-requisito:

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Baixe a biblioteca Proton:

	```
	[azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	--2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
	Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 868226 (848K) [application/x-gzip]
	Saving to: ‘qpid-proton-0.9.tar.gz’
	
	qpid-proton-0.9.tar.gz                               
	
	100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
	
	2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
	```

1.  Extraia o código Proton do arquivo de distribuição:

	```
	tar xvfz qpid-proton-0.9.tar.gz
	```

1.  Compile e instale o código usando as etapas a seguir, retiradas do arquivo Leiame:

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

Após a realização destas etapas, o Proton estará instalado no computador e pronto para uso.

## Próximas etapas

Está pronto(a) para saber mais? Visite o link a seguir:

- [Visão geral do AMQP do Barramento de Serviço][]

[Visão geral do AMQP do Barramento de Serviço]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[portal clássico do Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0928_2016-->
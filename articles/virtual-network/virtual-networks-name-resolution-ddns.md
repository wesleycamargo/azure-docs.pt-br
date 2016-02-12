<properties 
   pageTitle="Usando o DNS Dinâmico para registrar nomes de host"
   description="Esta página fornece alguns detalhes sobre como configurar o DNS dinâmico para registrar os nomes de host em seus próprios servidores DNS."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Usando o DNS dinâmico para registrar nomes de host
O [Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para VMs e instâncias de função. Quando a resolução de nome precisa ir além do que é fornecido pelo Azure, você pode fornecer seus próprios servidores DNS. Isso permite que você personalize sua solução DNS para ajustá-la às suas próprias necessidades específicas. Por exemplo, acessar seu Controlador de Domínio do AD local.

O Azure não tem a capacidade (por exemplo, credenciais) para registrar diretamente registros nos servidores DNS, de modo que organizações alternativas são muitas vezes necessárias. Veja a seguir alguns detalhes de alto nível de alguns dos cenários mais comuns.

## Clientes Windows ##
Clientes Windows fora do domínio tentam atualizações DDNS não seguras quando inicializam ou o respectivo endereço IP muda. O nome DNS é o nome do host mais o sufixo DNS primário. O Azure deixa o sufixo DNS primário em branco, mas ele pode ser substituído na VM, por meio da [interface de usuário](https://technet.microsoft.com/library/cc794784.aspx) ou [com a automação](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Clientes Windows ingressados no domínio registram seus IPs com o Controlador de Domínio usando DDNS Seguro. O processo de ingresso no domínio define o sufixo DNS primário no cliente e gerencia a relação de confiança.

## Clientes Linux ##
Clientes Linux geralmente não se registram no servidor DNS na inicialização, eles supõem que o servidor DHCP faça isso. O pacote Bind vem com uma ferramenta chamada *nsupdate* que pode ser usada para enviar atualizações de DDNS. Como o protocolo DDNS é padronizado, *nsupdate* pode ser usado mesmo quando não estiver usando Bind no servidor DNS.

O cliente DHCP Linux fornece ganchos que permitem executar scripts quando um endereço IP é atribuído ou alterado. Por exemplo, em */etc/dhcp/dhclient-exit-hooks.d/*. Isso pode ser usado para registrar o nome de host no DNS. Por exemplo:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

O comando *nsupdate* também pode executar atualizações de DDNS seguro. Por exemplo, ao usar um servidor DNS Bind, um par de chaves pública/privada é [gerado](http://linux.yyz.us/nsupdate/) e o servidor DNS é [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte pública da chave para que ele possa verificar a assinatura da solicitação. Para assinar a solicitação de atualização de DDNS, o par de chaves deve ser fornecido a *nsupdate* usando sua opção *-k*.

Ao usar um servidor DNS do Windows, a autenticação Kerberos pode ser usada com a opção *-g* de nsupdate (não disponível na versão Windows de *nsupdate* ). Para usá-la, use *kinit* para carregar as credenciais (por exemplo, de um [arquivo keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)) e, em seguida, *nsupdate -g* selecionará as credenciais no cache.

Se necessário, um sufixo de pesquisa DNS pode ser adicionado às VMs. O sufixo DNS é especificado no arquivo */etc/resolv.conf*. A maioria das distribuições Linux gerencia automaticamente o conteúdo desse arquivo, de modo que, normalmente, ele não pode ser editado. No entanto, o sufixo pode ser substituído usando o comando *supersede* do cliente DHCP. Em */etc/dhcp/dhclient.conf*, adicione:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->
<properties 
   pageTitle="Configurar o MPIO em um host Linux do StorSimple | Microsoft Azure"
   description="Configurar o MPIO no StorSimple conectado a um host Linux que esteja executando o CentOS 6.6"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/28/2015"
   ms.author="alkohli" />

# Configurar o MPIO em um host do StorSimple executando o CentOS

Este artigo explica as etapas necessárias para a configuração do Multipathing IO (MPIO) em seu servidor host do Centos 6.6. O servidor host está conectado ao dispositivo Microsoft Azure StorSimple para alta disponibilidade por meio de iniciadores iSCSI. Ele descreve detalhadamente a descoberta automática de dispositivos de vários caminhos e a configuração específica somente para volumes do StorSimple.

Este procedimento é aplicável a todos os modelos de dispositivos da série StorSimple 8000.

>[AZURE.NOTE]Este procedimento não pode ser usado para um dispositivo virtual StorSimple. Para saber mais, veja como configurar servidores host para seu dispositivo virtual.

## Sobre vários caminhos 

O recurso de vários caminhos permite configurar vários caminhos de E/S entre um servidor host e um dispositivo de armazenamento. Esses caminhos de E/S são conexões físicas da SAN que podem incluir cabos, switches, interfaces de rede e controladores separados. Vários caminhos agregam os caminhos de E/S para a configuração de um novo dispositivo associado a todos os caminhos agregados.

A finalidade de vários caminhos é dupla:

- **Alta disponibilidade**: oferece um caminho alternativo caso qualquer elemento do caminho de E/S (como um cabo, um switch, uma interface de rede ou um controlador) falhe.

- **Balanceamento de carga**: dependendo da configuração do seu dispositivo de armazenamento, ela poderá melhorar o desempenho detectando cargas nos caminhos de E/S e rebalanceando dinamicamente essas cargas.


### Sobre componentes de vários caminhos 

Vários caminhos em Linux consiste em componentes de kernel e em componentes de espaço do usuário como mostrados na tabela abaixo.

- **Kernel**: o componente principal é o *device-mapper*, que redireciona a E/S e oferece suporte a failover de caminhos e de grupos de caminho.

1. **User-space**: são *multipath-tools* que gerenciam dispositivos de vários caminhos instruindo o módulo device-mapper de vários caminhos o que fazer. As ferramentas consistem em:

	- **Multipath**: lista e configura os dispositivos de vários caminhos.
		
	- **Vários caminhos**: daemon que executa vários caminhos e monitora os caminhos.
	
	- **Devmap-name**: fornece um device-name significativo para udev para os devmaps.
 
	- **Kpartx**: mapeia devmaps lineares para partições de dispositivo para tornar os mapas de vários caminhos particionáveis.
	
	- **Multipath.conf**: arquivo de configuração do daemon de vários caminhos, usado para substituir a tabela de configuração interna.

### Sobre o arquivo de configuração multipath.conf

O arquivo de configuração `/etc/multipath.conf` faz com que muitos dos recursos de vários caminhos possam ser configurados pelo usuário. O comando `multipath` e o daemon de kernel `multipathd` usam informações encontradas neste arquivo. O arquivo só é consultado durante a configuração dos dispositivos de vários caminhos. Certifique-se de que todas as alterações sejam feitas antes de executar o comando `multipath`. Se você modificar o arquivo posteriormente, precisará parar e iniciar vários caminhos novamente para que as alterações entrem em vigor.

O arquivo multipath.conf tem cinco seções:

- **System level defaults** *(defaults)*: você pode substituir os padrões no nível do sistema.

1. **Blacklisted devices** *(blacklist)*: você pode especificar a lista de dispositivos que não devem ser controlados pelo device-mapper.

1. **Blacklist exceptions** *(blacklist\_exceptions)*: você pode identificar dispositivos específicos a serem tratados como dispositivos de vários caminhos, mesmo se relacionados na lista de bloqueios.

1. **Storage controller specific settings** *(devices)*: você pode especificar as definições de configuração que serão aplicadas a dispositivos com informações sobre o fornecedor e o produto.

1. **Device specific settings** *(multipaths)*: você pode usar esta seção para ajustar as definições de configuração para LUNs individuais.

## Configurar vários caminhos no StorSimple conectado ao host Linux

Um dispositivo StorSimple conectado a um host Linux pode ser configurado para alta disponibilidade e balanceamento de carga. Por exemplo, se o host Linux tiver duas interfaces conectadas à SAN e o dispositivo tem duas interfaces conectadas à SAN, de modo que essas interfaces estejam na mesma sub-rede, então haverá 4 caminhos disponíveis. No entanto, se cada interface DATA na interface do dispositivo e do host estiver em uma sub-rede IP diferente (e não roteável), então somente dois caminhos estarão disponíveis. Você pode configurar vários caminhos para descobrir automaticamente todos os caminhos disponíveis, escolher um algoritmo de balanceamento de carga para esses caminhos, aplicar as configurações específicas a volumes só do StorSimple e então habilitar e verificar vários caminhos.

O procedimento a seguir descreve como configurar vários caminhos quando um dispositivo StorSimple com duas interfaces de rede está conectado a um host com duas interfaces de rede.

## Pré-requisitos

Esta seção detalha os pré-requisitos de configuração para o servidor CentOS e seu dispositivo StorSimple.

### No host CentOS



1. Certifique-se de que seu host CentOS tenha duas interfaces de rede habilitadas. Digite:

	`ifconfig`

	O exemplo a seguir mostra a saída quando duas interfaces de rede (`eth0` e `eth1`) estiverem presentes no host.

    	[root@centosSS ~]# ifconfig
    	eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
      	inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     	RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
    	eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
      	inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      	RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
    	loLink encap:Local Loopback  
      	inet addr:127.0.0.1  Mask:255.0.0.0
      	inet6 addr: ::1/128 Scope:Host
      	UP LOOPBACK RUNNING  MTU:65536  Metric:1
      	RX packets:12 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:0 
      	RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Instale *iSCSI-initiator-utils* em seu servidor CentOS. Execute as etapas a seguir para instalar o *iSCSI-initiator-utils*.

	1. Faça logon como `root` em seu host CentOS.

	1. Instale o *iSCSI-initiator-utils*. Digite:
		
		`yum install iscsi-initiator-utils`


	1. Após a instalação bem-sucedida do *iSCSI-Initiator-utils*, inicie o serviço iSCSI. Digite:

		`service iscsid start`

		Em algumas ocasiões, talvez o `iscsid` não seja realmente iniciado e a opção `--force` poderá ser necessária

	1. Para garantir que o iniciador iSCSI esteja habilitado no momento da inicialização, use o comando `chkconfig` para habilitar o serviço.

		`chkconfig iscsi on`


	1. Para verificar se ele foi configurado corretamente, execute o comando:
	
		`chkconfig --list | grep iscsi`
	
		Um exemplo de saída é mostrado abaixo.

			iscsi   0:off   1:off   2:on3:on4:on5:on6:off
			iscsid  0:off   1:off   2:on3:on4:on5:on6:off

		No exemplo acima, você pode ver que o seu ambiente iSCSI será executado no momento da inicialização em níveis de execução 2, 3, 4 e 5.


1. Instale o *device-mapper-multipath*. Digite:

	`yum install device-mapper-multipath`

	A instalação será iniciada. Digite **S** para continuar quando a confirmação for solicitada.



### No dispositivo StorSimple

O dispositivo StorSimple deve ter:

- No mínimo duas interfaces habilitadas para iSCSI. Para verificar se duas interfaces são habilitadas para iSCSI em seu dispositivo StorSimple, execute as seguintes etapas no Portal de Gerenciamento do seu dispositivo StorSimple:

	1. Faça logon no Portal de Gerenciamento do seu dispositivo StorSimple.

	1. Selecione o serviço StorSimple Manager, clique em **Dispositivos** e escolha o dispositivo StorSimple específico. Clique em **Configurar** e verifique as configurações da interface de rede. Uma captura de tela com duas interfaces de rede habilitadas para iSCSI é mostrada abaixo. Aqui, DATA 2 e DATA 3, ambas as interfaces 10 GbE estão habilitadas para iSCSI.
	
		![Configuração de DATA 2 do StorsSimple MPIO](./media/storsimple-configure-mpio-on-linux/IC761347.png)
	
		![Configuração de DATA 3 do StorsSimple MPIO](./media/storsimple-configure-mpio-on-linux/IC761348.png)

		Na página **Configurar**

		1. Verifique se ambas as interfaces de rede estão habilitadas para iSCSI. O campo **Habilitada para iSCSI** deve ser definido como **Sim**.
		2. Verifique se as interfaces de rede têm a mesma velocidade, ambas devem ser de 1 GbE ou de 10 GbE.
		3. Anote os endereços IPv4 das interfaces habilitadas para iSCSI e salve-os para uso posterior no host.


- As interfaces iSCSI em seu dispositivo StorSimple devem poder ser acessadas do servidor CentOS.

	Para verificar isso, você precisa fornecer os endereços IP das interfaces da rede habilitadas para iSCSI do StorSimple em seu servidor host. Os comandos usados e a saída correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) são mostrados abaixo:

    	[root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    	10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    	10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### Configuração de hardware

É recomendável que você conecte as duas interfaces de rede iSCSI em caminhos separados para redundância. A figura a seguir mostra a configuração de hardware recomendada para vários caminhos de alta disponibilidade e de balanceamento de carga para seu servidor CentOS e o dispositivo StorSimple.

![Configuração de hardware do MPIO para StorSimple para o host Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Como mostrado na figura anterior:

- O dispositivo StorSimple está em uma configuração ativo-passivo com dois controladores.

- Dois switches de SAN estão conectados aos seus controladores de dispositivo.
 
- Dois iniciadores iSCSI estão habilitados no seu dispositivo StorSimple.
 
- Duas interfaces de rede estão habilitadas em seu host CentOS.

A configuração acima gerará quatro caminhos separados entre o dispositivo e o host se o host e as interfaces de dados forem roteáveis.

>[AZURE.IMPORTANT]
>
>- É recomendável que você não misture as interfaces de rede de 1 GbE e de 10 GbE para vários caminhos. Quando você estiver usando duas interfaces de rede, ambas as interfaces deverão ser do tipo idêntico.
>- Em seu dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 são interfaces de 1 GbE, enquanto DATA2 e DATA3 são interfaces de rede de 10 GbE.|

## Etapas da configuração

As etapas de configuração para vários caminhos envolvem a configuração dos caminhos disponíveis para a descoberta automática, especificando o algoritmo de balanceamento de carga a ser usado, habilitando vários caminhos e, por fim, verificando a configuração. Cada uma das etapas acima será discutida nas seções a seguir.

### Etapa 1: Configurar vários caminhos para a descoberta automática

Os dispositivos multipath-supported podem ser automaticamente descobertos e configurados.

1. Inicialize o arquivo `/etc/multipath.conf`. Digite:

	 `Copy mpathconf --enable`
	
	O comando acima criará um arquivo `sample/etc/multipath.conf`.


1. Inicie o serviço de vários caminhos. Digite:

    ``Copy service multipathd start``
	
	Você verá esta saída:

	`Starting multipathd daemon:`

1. Habilite a descoberta automática dos vários caminhos. Digite:

	`mpathconf --find_multipaths y`

	Isso modificará a seção de padrões de seu `multipath.conf` como mostrado abaixo:

		defaults {
		find_multipaths yes
		user_friendly_names yes
		path_grouping_policy multibus
		}

### Etapa 2: Configurar vários caminhos para volumes StorSimple

Por padrão, todos os dispositivos estão na lista de bloqueios no arquivo multipath.conf e serão ignorados. Será necessário criar exceções de lista de bloqueios para permitir vários caminhos para volumes desde dispositivos StorSimple.

1. Edite o arquivo `/etc/mulitpath.conf`. Digite:

	`vi /etc/multipath.conf`

1. Localize a seção blacklist\_exceptions no arquivo multipath.conf. Seu dispositivo StorSimple precisa estar relacionado como uma exceção de lista de bloqueios nesta seção. Você pode retirar o comentário de linhas relevantes neste arquivo para modificá-lo como mostrado abaixo (use somente o modelo específico do dispositivo que você estiver usando):

    	blacklist_exceptions {
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8100*"
    	    }
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8600*"
    	    }
    	   }

### Etapa 3: Configurar vários caminhos de round robin

Esse algoritmo de balanceamento de carga usa todos os vários caminhos disponíveis para o controlador ativo em um round robin balanceado.

1. Edite o arquivo `/etc/multipath.conf`. Digite:

	`vi /etc/multipath.conf`

1. Na seção `defaults`, defina a `path_grouping_policy` como `multibus`. A `path_grouping_policy` especifica a política de agrupamento de caminho padrão a ser aplicada a vários caminhos não especificados. A seção de padrões terá a aparência mostrada abaixo.

	    defaults {
	            user_friendly_names yes
	            path_grouping_policy multibus
	    }



> [AZURE.NOTE]Os valores mais comuns de `path_grouping_policy` incluem:
	
> - failover = um caminho por grupo de prioridade
> - multibus = todos os caminhos válidos em um grupo de prioridade

### Etapa 4: Habilitar vários caminhos

1. Reinicie o daemon `multipathd`. Digite:

    `service multipathd restart`

1. A saída será como mostrado abaixo:

    	[root@centosSS ~]# service multipathd start
    	Starting multipathd daemon:  [OK]




### Etapa 5: Verificar vários caminhos

1. Primeiro, verifique se a conexão iSCSI foi estabelecida como dispositivo StorSimple da seguinte maneira:


	1. Descubra seu dispositivo StorSimple. Digite:
		
		`iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

		A saída quando o endereço IP de DATA0 é 10.126.162.25 e a porta 3260 está aberta no dispositivo StorSimple para o tráfego iSCSI de saída como mostrado abaixo:

		    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
		    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


		Copie o IQN do seu dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, desde a saída anterior.



	1. Conecte-se ao dispositivo usando o IQN de destino. O dispositivo StorSimple é o destino iSCSI aqui. Digite:

		`iscsiadm -m node --login -T <IQN of iSCSI target>`

		O exemplo a seguir mostra a saída com um destino IQN do `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. A saída indica que você se conectou com êxito às duas interfaces de rede habilitadas para iSCSI em seu dispositivo.

		    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
	    		Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


		Se você vir somente uma interface de host e dois caminhos aqui, precisará habilitar ambas as interfaces no host para iSCSI. Você pode seguir as [instruções detalhadas na documentação do Linux](https://access.redhat.com/documentation/pt-BR/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

	
	1. Um volume é exposto ao servidor CentOS do dispositivo StorSimple. Para saber mais, veja como [Etapa 6: Criar um volume](https://sandboxmsdnstage.redmond.corp.microsoft.com/pt-BR/library/azure/dn772357.aspx) por meio do Portal de Gerenciamento em seu dispositivo StorSimple.

	1. Verifique os caminhos disponíveis. Digite:

		`multipath –l`

		O exemplo a seguir mostra a saída de duas interfaces de rede em um dispositivo StorSimple conectado a uma interface de rede de host com dois caminhos disponíveis.

		    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 7:0:0:1 sdc 8:32 active undef running
    		  `- 6:0:0:1 sdd 8:48 active undef running

		O exemplo a seguir mostra a saída de duas interfaces de rede em um dispositivo StorSimple conectado a duas interfaces de rede de host com quatro caminhos disponíveis.
		
		    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 17:0:0:0 sdb 8:16 active undef running
    		  |- 15:0:0:0 sdd 8:48 active undef running
    		  |- 14:0:0:0 sdc 8:32 active undef running
    		  `- 16:0:0:0 sde 8:64 active undef running

		Após a configuração dos caminhos, consulte as instruções específicas no sistema operacional do seu host (Centos 6.6) para montar e formatar este volume.


## Solucionar problemas de vários caminhos

Esta seção fornece algumas dicas úteis se você tiver algum problema durante a configuração de vários caminhos.

P. Não vejo as alterações no arquivo `multipath.conf` entrarem em vigor.

R. Se você tiver alguma alteração no arquivo `multipath.conf`, precisará reiniciar o serviço de vários caminhos. Digite o seguinte comando:
    
    service multipathd restart

P. Habilitei duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no host. Quando eu listo os caminhos disponíveis, vejo apenas dois caminhos. Eu esperava ver quatro caminhos disponíveis.

R. Verifique se os dois caminhos estão na mesma sub-rede e se são roteáveis. Se as interfaces de rede estiverem em vLANs diferentes e se não forem roteáveis, você verá somente dois caminhos. Uma maneira de verificar isso é garantir que você possa acessar as interfaces de host de uma interface de rede no dispositivo StorSimple. Você precisará [contatar o Suporte da Microsoft](storsimple-contact-microsoft-support.md), já que essa verificação só poderá ser feita por meio de uma sessão de suporte.

P. Quando eu listo os caminhos disponíveis, não vejo nenhuma saída.

R. Normalmente, não ver todos os vários caminhos sugere um problema com o daemon de vários caminhos e é mais provável que qualquer problema aqui esteja no arquivo `multipath.conf`.

Também vale a pena verificar se você realmente pode ver alguns discos depois de se conectar ao destino, já que nenhuma resposta das listagens de vários caminhos também poderia significar que você não tem discos.

- Use o comando a seguir para examinar novamente o barramento SCSI: 
 
	`$ rescan-scsi-bus.sh `(parte do pacote sg3\_utils)
 
- Digite os seguintes comandos:

	`$ dmesg | grep sd*`
 
- Ou

	`$ fdisk –l`
 
	Eles retornarão detalhes de discos adicionados recentemente.
  
- Para determinar se ele é um disco StorSimple, use os seguintes comandos:
 
	`cat /sys/block/<DISK>/device/model`
 
	Isso retornará uma cadeia de caracteres, o que determinará se é um disco StorSimple.

Uma causa menos provável, mas possível, também poderia ser um pid iscsid obsoleto. Use o comando a seguir para fazer logoff das sessões iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita esse comando para todas as interfaces de rede conectadas no destino iSCSI, que é o seu dispositivo StorSimple. Depois de fazer logoff de todas as sessões iSCSI, use o IQN de destino iSCSI para restabelecer a sessão iSCSI. Digite o seguinte comando:

    iscsiadm -m node --login -T <TARGET_IQN>


P. Não sei se meu dispositivo está na lista de permissões.

R. Para verificar se seu dispositivo está na lista de permissões, use o seguinte comando interativo de solução de problemas:

	multipathd –k
	multipathd> show devices
	available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Para saber mais, veja como [usar o comando interativo de solução de problemas para vários caminhos](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## Lista de comandos úteis

|Tipo|Command|Descrição|
|---|---|---|
|**iSCSI**|`service iscsid start`|Iniciar o serviço iSCSI|
||`service iscsid stop`|Parar o serviço iSCSI| ||`service iscsid restart`|Reiniciar o serviço iSCSI| ||`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Descobrir os destinos disponíveis no endereço especificado| ||`iscsiadm -m node --login -T <TARGET_IQN>`|Fazer logon no destino iSCSI| ||`iscsiadm -m node --logout -p <Target_IP>`|Fazer logoff no destino iSCSI| ||`cat /etc/iscsi/initiatorname.iscsi`|Imprimir o nome do iniciador iSCSI| ||`iscsiadm –m session –s <sessionid> -P 3`|Verificar o estado da sessão iSCSI e o volume descoberto no host| ||`iscsi –m session`|Mostra todas as sessões iSCSI estabelecidas entre o host e o dispositivo StorSimple| | | | | |**Vários caminhos**|`service multipathd start`|Iniciar o daemon de vários caminhos| ||`service multipathd stop`|Parar o daemon de vários caminhos| ||`service multipathd restart`|Reiniciar o daemon de vários caminhos| ||`chkconfig multipathd on` </br> OU </br> `mpathconf –with_chkconfig y`|Habilitar o daemon de vários caminhos a ser iniciado no momento da inicialização| ||`multipathd –k`|Iniciar o console interativo para a solução de problemas| ||`multipath –l`|Listar conexões e dispositivos de vários caminhos| ||`mpathconf --enable`|Criar um arquivo mulitpath.conf de exemplo no `/etc/mulitpath.conf`| ||||

## Próximas etapas

Já que você está configurando o MPIO no host Linux, talvez também seja necessário consultar os seguintes documentos do CentoS 6.6:

- [Configurando o MPIO no CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Guia de treinamento do Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

<!---HONumber=Sept15_HO2-->
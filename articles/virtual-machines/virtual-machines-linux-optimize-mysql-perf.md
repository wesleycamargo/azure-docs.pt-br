<properties
	pageTitle="Otimizar o desempenho do MySQL em máquinas virtuais do Linux | Microsoft Azure"
	description="Aprenda a otimizar o MySQL em execução em uma máquina virtual do Azure (VM) executando o Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Otimizando o desempenho do MySQL nas VMs Linux do Azure

Há muitos fatores que afetam o desempenho do MySQL no Azure, tanto na configuração de software como na seleção de hardware virtual. Este artigo se concentra na otimização de desempenho por meio de armazenamento, sistema e configurações de banco de dados.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


##Usando o RAID em uma máquina virtual do Azure
O armazenamento é o principal fator que afeta o desempenho do banco de dados em ambientes de nuvem. Em comparação com um único disco, o RAID pode fornecer acesso mais rápido por meio de simultaneidade. Confira [Níveis do RAID padrão](http://en.wikipedia.org/wiki/Standard_RAID_levels) para obter mais detalhes.

Taxa de transferência de e/s de disco e tempo de resposta de e/s no Azure podem ser melhorados significativamente através do RAID. Nossos testes de laboratório mostram e/s de disco, a taxa de transferência pode ser duplicada e o tempo de resposta de e/s pode ser reduzido pela metade em média quando o número de discos RAID é duplicado (de 2 a 4, 4 a 8, etc.). Confira o [Apêndice A](#AppendixA) para obter detalhes.

Além de e/s de disco, o desempenho do MySQL melhora quando você aumenta o nível do RAID. Confira o [Apêndice B](#AppendixB) para obter detalhes.

Você talvez queira considerar o tamanho da parte. Em geral quando você tem um grande parte, você obterá uma sobrecarga mais baixa, especialmente para grandes gravações. No entanto, quando o tamanho da parte for muito grande, ela pode adicionar uma sobrecarga adicional e você não pode tirar proveito do RAID. O tamanho atual do padrão é 512KB, que provou ser ideal para ambientes de produção mais gerais. Confira o [Apêndice C](#AppendixC) para obter detalhes.

Observe que há limites em quantos discos que você pode adicionar para diferentes tipos de máquinas virtuais. Esses limites são detalhados em [Tamanhos de máquinas virtuais e serviço de nuvem para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Você precisará de 4 discos de dados anexados para seguir o exemplo do RAID neste artigo, embora você possa optar por configurar o RAID com menos discos.

Este artigo pressupõe que você já criou uma máquina virtual Linux e já tenha o MYSQL instalado e configurado. Para obter mais informações sobre como começar consulte Como instalar o MySQL no Azure.

###Configurando o RAID no Azure
As etapas a seguir mostram como criar o RAID no Azure usando o portal clássico do Azure. Você também pode definir o RAID usando scripts do Windows PowerShell. Neste exemplo, configuraremos o RAID 0 com 4 discos.

####Etapa 1: adicionar um disco de dados à sua máquina virtual  

Na página de máquinas virtuais do portal clássico do Azure, clique na máquina virtual a qual você deseja adicionar um disco de dados. Neste exemplo, a máquina virtual é mysqlnode1.

![][1]

Na página para a máquina virtual, clique em **Painel**.

![][2]


Na barra de tarefas, clique em **Anexar**.

![][3]

Em seguida, clique em **Anexar disco vazio**.

![][4]

Para discos de dados, a **Preferência de Cache do Host** deve ser definida como **Nenhum**.

Isso adicionará um disco vazio à sua máquina virtual. Repita essa etapa mais três vezes para que você tenha 4 discos de dados para o RAID.

Você pode ver as unidades adicionadas na máquina virtual, observando o log de mensagens do kernel. Por exemplo, para ver isso no Ubuntu, use o seguinte comando:

	sudo grep SCSI /var/log/dmesg

####Etapa 2: criar o RAID com os discos adicionais
Siga este artigo para obter as etapas detalhadas de configuração do RAID:

[Configurar RAID de software no Linux](virtual-machines-linux-configure-RAID.md)

>[AZURE.NOTE]Se você estiver usando o sistema de arquivos XFS, siga as etapas abaixo após ter criado o RAID.

Para instalar o XFS no Debian, Ubuntu ou Linux Mint, use o seguinte comando:

	apt-get -y install xfsprogs  

Para instalar o XFS no Fedora, CentOS ou RHEL, use o seguinte comando:

	yum -y install xfsprogs  xfsdump


####Etapa 3: configurar um novo caminho de armazenamento
Use o seguinte comando:

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####Etapa 4: copiar os dados originais para o novo caminho de armazenamento
Use o seguinte comando:

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####Etapa 5: modificar permissões para que o MySQL possa acessar (leitura e gravação) o disco de dados
Use o seguinte comando:

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##Ajustar o algoritmo de agendamento de e/s de disco
O Linux implementa quatro tipos de algoritmos de agendamento de e/s:

-	Algoritmo NOOP (nenhuma operação)
-	Algoritmo de prazo (prazo)
-	Algoritmo de fila completamente justa (CFQ)
-	Algoritmo de período de orçamento (antecipado)  

Você pode selecionar diferentes agendadores de e/s em cenários diferentes para otimizar o desempenho. Em um ambiente de acesso completamente aleatório, não há uma grande diferença entre os algoritmos CFQ e de prazo para o desempenho. Geralmente, é recomendável definir o ambiente de banco de dados do MySQL como prazo para estabilidade. Se houver muita e/s sequencial, o CFQ pode reduzir o desempenho de e/s de disco.

Para o SSD e outros equipamentos, usando o NOOP ou o prazo é possível obter um desempenho melhor que o agendador padrão.

Do kernel 2.5, o algoritmo de agendamento de e/s padrão é o prazo final. Começando no kernel 2.6.18, o CFQ tornou-se o algoritmo de agendamento de e/s padrão. Você pode especificar essa configuração no momento da inicialização do Kernel ou modificar essa configuração dinamicamente quando o sistema está em execução.

O exemplo a seguir demonstra como verificar e definir o agendador padrão para o algoritmo NOOP.

Para a família de distribuição do Debian:

###Etapa 1.Exibir o agendador de e/s atual
Use o seguinte comando:

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Você verá o seguinte resultado, que indica o agendador atual.

	noop [deadline] cfq


###Etapa 2. Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de e/s
Use os seguintes comandos:

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE]Configurar este somente para /dev/sda não é útil. Ele precisa ser definido em todos os discos de dados onde o banco de dados reside.

Você verá o resultado, indicando que o grub.cfg foi recriado com êxito e que o agendador padrão foi atualizado para o NOOP.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Para a família de distribuição Redhat, só é necessário o seguinte comando:

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##Definir as configurações de operações de arquivos do sistema
Uma prática recomendada é desabilitar o recurso de log no sistema de arquivos. Atime é a hora do último acesso ao arquivo. Sempre que um arquivo é acessado, o sistema de arquivos registra o carimbo de data/hora no log. No entanto, essas informações raramente são usadas. Você pode desabilitá-lo se não precisar dele, o que reduzirá o tempo de acesso total do disco.

Para desabilitar o log atime, você precisa modificar o arquivo de configuração do sistema de arquivos /etc/ fstab e adicionar a opção **noatime**.

Por exemplo, edite o arquivo vim /etc/fstab, adicionando o noatime conforme mostrado abaixo.

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the “noatime” option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Em seguida, monte novamente o sistema de arquivos com o seguinte comando:

	mount -o remount /RAID0

Teste o resultado modificado. Observe que, quando você modifica o arquivo de teste, o tempo de acesso não é atualizado.

Antes do exemplo:

![][5]

Depois do exemplo:

![][6]

##Aumentar o número máximo de manipuladores de sistema para alta simultaneidade
O MySQL é o banco de dados de alta simultaneidade. O número de manipuladores simultâneos é 1024 para Linux, que nem sempre é suficiente. **Use as etapas a seguir para aumentar os manipuladores simultâneos máximos do sistema para dar suporte à alta simultaneidade do MySQL**.

###Etapa 1: modificar o arquivo limits.conf
Adicione as seguintes quatro linhas no arquivo /etc/security/limits.conf para aumentar o máximo de manipuladores simultâneos permitido. Observe que 65536 é o número máximo ao qual o sistema pode dar suporte.

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###Etapa 2: atualizar o sistema para os novos limites
Execute os seguintes comandos:

	ulimit -SHn 65536
	ulimit -SHu 65536

###Etapa 3: assegurar se os limites são atualizados no momento da inicialização
Colocar os seguintes comandos de inicialização no arquivo /etc/rc.local para que ele entre em vigor durante cada tempo de inicialização.

	echo “ulimit -SHn 65536” >>/etc/rc.local
	echo “ulimit -SHu 65536” >>/etc/rc.local

##Otimização de banco de dados do MySQL
Você pode usar a mesma estratégia de ajuste de desempenho para configurar o MySQL no Azure como em um computador local.

As principais regras de otimização de e/s são:

-	Aumente o tamanho do cache.
-	Reduza o tempo de resposta de E/S.  

Para otimizar as configurações do servidor MySQL, você pode atualizar o arquivo my.cnf, que é o arquivo de configuração padrão para computadores cliente e servidor.

Os seguintes itens de configuração são os principais fatores que afetam o desempenho do MySQL:

-	**innodb\_buffer\_pool\_size**: o pool de buffers contém dados armazenados em buffer e o índice. Isto é geralmente definido como 70% da memória física.
-	**innodb\_log\_file\_size**: esse é o tamanho do redo log. Você pode usar os logs de refazer para garantir que as operações de gravação sejam rápidas, confiáveis e recuperáveis após uma falha. Isto é definido para 512MB, o que lhe dará bastante espaço para operações de gravação em log.
-	**max\_connections**: às vezes, os aplicativos não fecham as conexões corretamente. Um valor maior oferece ao servidor mais tempo para reciclar conexões ociosas. O número máximo de conexões é 10000, mas o máximo recomendado é 5000.
-	**Innodb\_file\_per\_table**: esta configuração habilita ou desabilita a capacidade de InnoDB para armazenar as tabelas em arquivos separados. Ativar a opção garantirá que várias operações de administração avançada possam ser aplicadas com eficiência. Do ponto de vista de desempenho, ela pode acelerar a transmissão de espaço de tabela e otimizar o desempenho do gerenciamento de resíduos. Portanto, a configuração recomendada para esta opção é Ativada.</br> No MySQL 5.6, a configuração padrão é Ativada. Portanto, nenhuma ação é necessária. Para outras versões, que sejam anteriores a 5.6, as configurações padrão estão Desativadas. É necessário ativá-las. E deve aplicá-la antes do carregamento de dados, porque apenas as tabelas criadas recentemente são afetadas.
-	**innodb\_flush\_log\_at\_trx\_commit**: o valor padrão é 1, com o escopo definido como 0~2. O valor padrão é a opção mais adequada para o banco de dados do MySQL autônomo. A configuração de 2 habilita mais integridade de dados e é adequada para mestre no cluster do MySQL. A configuração de 0 permite a perda de dados, que pode afetar a confiabilidade, em alguns casos com melhor desempenho e é adequada para o subordinado no cluster do MySQL.
-	**Innodb\_log\_buffer\_size**: o buffer de log permite que as transações sejam executados sem ter que liberar o log no disco antes que as transações sejam confirmadas. No entanto, se houver objetos binários grandes ou campo de texto, o cache será consumido rapidamente e a e/s de disco frequente será disparada. É melhor aumentar o tamanho do buffer se a variável de estado Innodb\_log\_waits não é 0.
-	**query\_cache\_size**: a melhor opção é desabilitá-lo desde o início. Defina query\_cache\_size como 0 (este agora é a configuração padrão no MySQL 5.6) e use outros métodos para agilizar as consultas.  

Confira o [Apêndice D](#AppendixD) para comparar o desempenho após a otimização.


##Ativar o log de consultas lentas do MySQL para analisar o estrangulamento de desempenho
O log de consultas lentas do MySQL pode ajudá-lo a identificar as consultas lentas para o MySQL. Depois de habilitar o log de consultas lentas do MySQL, você pode usar as ferramentas do MySQL como **mysqldumpslow** para identificar o estrangulamento de desempenho.

Observe que por padrão isso não está habilitado. Ativar o log de consultas lentas, pode consumir alguns recursos da CPU. Portanto, é recomendável que você habilite isso temporariamente para solucionar problemas de estrangulamentos de desempenho.

###Etapa 1: modificar o arquivo my.cnf ao adicionar as seguintes linhas ao final   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###Etapa 2: reiniciar o servidor mysql
	service  mysql  restart

###Etapa 3: verificar se a configuração está produzindo efeito usando o comando “show”

![][7]

![][8]

Neste exemplo, você pode ver que o recurso de consulta lenta foi ativado. Você pode usar a ferramenta **mysqldumpslow** para determinar os estrangulamentos de desempenho e otimizar o desempenho, como a adição de índices.





##Apêndice

Os seguintes são dados de teste de desempenho de amostra produzidos no ambiente de laboratório de destino, eles oferecem informações gerais sobre a tendência de dados de desempenho com abordagens de ajuste diferentes, no entanto, os resultados podem variar em versões de produto ou ambiente diferentes.

<a name="AppendixA"></a>Apêndice A: **desempenho de disco (IOPS) com níveis de RAID diferentes**


![][9]

**Comandos de teste:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: a carga de trabalho deste teste usa 64 threads, tentando alcançar o limite superior do RAID.

<a name="AppendixB"></a>Apêndice B: **comparação de desempenho do MySQL (taxa de transferência) com níveis de RAID diferentes** (sistema de arquivos XFS)


![][10] ![][11]

**Comandos de teste:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparação de desempenho (OLTP) do MySQL com níveis de RAID diferentes** ![][12]

**Comandos de teste:**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Apêndice C: **comparação de desempenho de disco (IOPS) para tamanhos de partes diferentes** (sistema de arquivos XFS)


![][13]

**Comandos de teste:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Observe que o tamanho do arquivo usado para esse teste é 30GB e 1GB respectivamente, com o RAID 0(4 discos) sistema de arquivos XFS.


<a name="AppendixD"></a>Apêndice D: **comparação de desempenho do MySQL (taxa de transferência) antes e depois da otimização** (sistema de arquivos XFS)


![][14]

**Comandos de teste:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A definição de configuração para padrão e otimização é a seguinte:**

|Parâmetros |Padrão |otimização
|-----------|-----------|-----------
|**innodb\_buffer\_pool\_size** |Nenhum |7G
|**innodb\_log\_file\_size** |5 M |512 M
|**max\_connections** |100 |5\.000
|**innodb\_file\_per\_table** |0 |1
|**innodb\_flush\_log\_at\_trx\_commit** |1 |2
|**innodb\_log\_buffer\_size** |8 M |128 M
|**query\_cache\_size** |16 M |0


Parâmetros de configuração de otimização mais detalhados, consulte as instruções oficiais do mysql.

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar\_innodb\_flush\_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Ambiente de teste**

|Hardware |Detalhes
|-----------|-------
|Cpu |Processador AMD Opteron(tm) 4171 HE/4 cores
|Memória |14G
|disk |10G/disco
|os |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png

<!---HONumber=AcomDC_1223_2015-->
<properties
   pageTitle="Guia de início rápido para a instalação manual do SAP HANA em VMs do Azure | Microsoft Azure"
   description="Guia de início rápido para a instalação manual do SAP HANA em VMs do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Guia de início rápido para a instalação manual do SAP HANA de instância única em VMs do Azure

## Introdução

Este guia de início rápido ajudará você a configurar uma instância única do sistema protótipo/demonstração SAP HANA em VMs do Azure por meio de uma instalação manual do SAP NetWeaver 7.5 e do SAP HANA SP12. O guia presume que o leitor já esteja familiarizado com conceitos básicos do Azure IaaS, por exemplo, implantar máquinas virtuais ou redes virtuais por meio do Portal do Azure ou do Powershell/CLI, incluindo a opção de usar modelos json. Além disso, espera-se que o leitor esteja familiarizado com o SAP HANA, com o SAP NetWeaver e como instalá-los localmente.

Espera-se que o leitor esteja ciente da documentação geral de SAP-Azure mencionada na seção de informações gerais ao final do artigo.

Devido à restrição para sistemas de não produção, este guia não abrange tópicos como alta disponibilidade, backup, recuperação de desastres, alto desempenho ou considerações especiais de segurança.

O exemplo de configuração foi feito usando duas máquinas virtuais para realizar uma instalação distribuída do SAP NetWeaver por meio do modelo do Azure Resource Manager, pois SAP-Linux-Azure só recebe suporte no Azure Resource Manager e não no modelo clássico. Encontre links para mais informações sobre o Azure Resource Manager na seção informações gerais ao final deste artigo.

Estas foram as duas VMs de teste usadas para o exemplo de instalação:

* hana-appsrv (tipo DS3) para hospedar a instância NW 7.5 ASCS + PAS
* hana-dbsrv (tipo GS4) para hospedar HANA SP12
* ambas as VMs pertenciam a uma rede virtual do Azure (azure-hana-test-vnet )
* O sistema operacional nos dois casos foi o SLES 12 SP1


Lembre-se do fato de que a partir de julho de 2016 o SAP HANA só recebe suporte total para sistemas de produção de OLAP (BW) no tipo GS5 de VM do Azure. Para os testes nos quais não há a expectativa de suporte oficial da SAP, é possível usar algo inferior, por exemplo, o GS4. O que sempre deve ser usado para SAP HANA no Azure é o Armazenamento Premium do Azure para arquivos de dados e de log do HANA - consulte a seção "Configuração de disco" mais adiante. Para saber mais detalhes sobre os produtos SAP com suporte no Azure, confira a seção de informações gerais ao final deste artigo.


O guia descreve duas maneiras diferentes de instalar manualmente o SAP HANA em VMs do Azure:

* instalar o SAP HANA por meio do SWPM (SAP Software Provisioning Manager) como parte de uma instalação distribuída de NetWeaver na etapa "instância de banco de dados"
* instalar o SAP HANA usando a ferramenta hdblcm (HANA Life Cycle Manager) e instalando o NetWeaver logo depois

Também é possível usar o SWPM e instalar todos os componentes (SAP HANA, servidor de aplicativos SAP, instância do ASCS, SAP GUI) em uma única VM. Essa opção não está descrita neste artigo, mas os itens que precisam ser considerados são os mesmos.

Antes de iniciar a instalação, leia a seção após as listas de verificação abaixo para saber como configurar as VMs de teste do Azure a fim de evitar vários erros básicos que acontecerão ao usar apenas uma configuração de VM do Azure padrão.


## Lista de verificação de instalação do SAP HANA por meio do SAP SWPM

Esta é uma lista de verificação simples dos principais itens relacionados a uma instalação manual do SAP HANA, para fins de demonstração ou de protótipos via SAP SWPM, realizando uma instalação distribuída do SAP NW 7.5. Os itens individuais são explicados e exibidos na forma de capturas de tela detalhadas em todo o artigo:

* criar uma rede virtual do Azure que incluirá as duas VMs de teste
* implantar duas VMs do Azure com o sistema operacional SLES 12 SP1 por meio do modelo do Azure Resource Manager
* anexar dois discos de armazenamento padrão na VM do servidor de aplicativo (por exemplo, 75 GB e 500 GB)
* anexar quatro discos à VM do servidor de banco de dados do HANA - dois discos de armazenamento padrão para a VM do servidor de aplicativo + dois discos de armazenamento premium (por exemplo, 2 x 512 GB)
* Dependendo dos requisitos de tamanho e/ou de taxa de transferência, anexe vários discos e crie volumes distribuídos usando lvm ou mdadm no nível do sistema operacional dentro da VM
* criar sistemas de arquivos XFS os discos anexados/volumes lógicos
* montar os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para armazenar todo o software SAP e o outro, por exemplo, para o diretório sapmnt e talvez backups. No servidor do banco de dados SAP HANA monte os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Isso é tudo o que você precisa para evitar que o sistema de arquivos raiz, que não é muito grande em VMs Linux do Azure, fique cheio
* insira os endereços IP locais das VMs de teste em/etc/hosts
* insira o parâmetro nofail em /etc/fstab
* defina os parâmetros de kernel de acordo com a observação sobre HANA-SLES-12 SAP (veja mais detalhes abaixo na seção de parâmetros de kernel)
* adicione espaço de permuta
* se quiser, instale uma área de trabalho gráfica nas VMs de teste. Caso contrário, use uma instalação remota sapinst
* baixe o software SAP no serviço marketplace da SAP
* instale a instância SAP ASCS na VM do servidor de aplicativos
* compartilhe o diretório sapmnt via NFS entre as VMs de teste (a VM do servidor de aplicativos é o servidor NFS)
* instale a instância de banco de dados, incluindo HANA via SWPM na VM do servidor de banco de dados
* instale o PAS na VM do servidor de aplicativos
* inicie o SAP MC e conecte-se, por exemplo, por meio do SAP GUI/HANA Studio



## Lista de verificação de instalação do SAP HANA por meio do hdblcm

Esta é uma lista de verificação simples dos principais itens relacionados a uma instalação manual do SAP HANA, para fins de demonstração ou de protótipos via SAP SWPM, realizando uma instalação distribuída do SAP NW 7.5. Os itens individuais são explicados e exibidos na forma de capturas de tela detalhadas em todo o artigo:

* criar uma rede virtual do Azure que incluirá as duas VMs de teste
* implantar duas VMs do Azure com o sistema operacional SLES 12 SP1 por meio do modelo do Azure Resource Manager
* anexar dois discos de armazenamento padrão na VM do servidor de aplicativo (por exemplo, 75 GB e 500 GB)
* anexar quatro discos à VM do servidor de banco de dados do HANA - dois armazenamentos padrão para a VM do servidor de aplicativo + 2 discos de armazenamento premium (por exemplo, 2 x 512 GB)
* Dependendo dos requisitos de tamanho e/ou de taxa de transferência, anexe vários discos e crie volumes distribuídos usando lvm ou mdadm no nível do sistema operacional dentro da VM
* criar sistemas de arquivos XFS os discos anexados/volumes lógicos
* montar os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para armazenar todo o software SAP e o outro, por exemplo, para o diretório sapmnt e talvez backups. No servidor do banco de dados SAP HANA monte os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Isso é tudo o que você precisa para evitar que o sistema de arquivos raiz, que não é muito grande em VMs Linux do Azure, fique cheio
* insira os endereços IP locais das VMs de teste em/etc/hosts
* insira o parâmetro nofail em /etc/fstab
* defina os parâmetros de kernel de acordo com a observação sobre HANA-SLES-12 SAP (veja mais detalhes abaixo na seção de parâmetros de kernel)
* adicione espaço de permuta
* se quiser, instale uma área de trabalho gráfica nas VMs de teste. Caso contrário, use uma instalação remota sapinst
* baixe o software SAP no serviço marketplace da SAP
* criar o grupo "sapsys" com a ID de grupo 1001 na VM do HANA DB Server
* instalar o SAP HANA na VM do servidor de banco de dados usando hdblcm
* instale a instância SAP ASCS na VM do servidor de aplicativos
* compartilhe o diretório sapmnt via NFS entre as VMs de teste (a VM do servidor de aplicativos é o servidor NFS)
* instale a instância de banco de dados, incluindo HANA via SWPM na VM do servidor de banco de dados
* instale o PAS na VM do servidor de aplicativos
* inicie o SAP MC e conecte-se, por exemplo, por meio do SAP GUI/HANA Studio




## Preparar VMs do Azure para instalação manual do SAP HANA

Este capítulo sobre a preparação das VMs do Azure para a instalação manual do SAP HANA é formado por cinco seções que abordam os tópicos a seguir:

* Configuração de disco
* Parâmetros de kernel
* Sistemas de arquivos
* /etc/hosts
* /etc/fstab


### Configuração de disco

O sistema de arquivos raiz em uma VM Linux no Azure tem tamanho limitado. Portanto, é necessário anexar o espaço em disco adicional a uma VM para execução do SAP. No caso de uma VM de servidor de aplicativos SAP usada em um ambiente puro de protótipo/demonstração, é possível usar discos de armazenamento padrão do Azure. Enquanto para os arquivos de dados e de log de Banco de dados do SAP HANA, os discos de Armazenamento Premium do Azure devem ser usados mesmo em um cenário de não produção.

Vejam alguns detalhes sobre como anexar discos a uma VM Linux [aqui](virtual-machines-linux-add-disk.md)

Quando o assunto é cache de disco do Azure, é necessário usar "Nenhum" para discos que serão usados para armazenar os logs de transação do HANA. Para arquivos de dados do HANA, é possível usar o cache de leitura. Como HANA é um banco de dados na memória, ele depende do padrão de uso geral para saber a quantidade de cache de leitura no nível do disco do Azure melhorará o desempenho (por exemplo, iniciando HANA e lendo dados do disco na memória).

Veja detalhes sobre o Armazenamento Premium do Azure [aqui](../storage/storage-premium-storage.md)

[Aqui](https://github.com/Azure/azure-quickstart-templates) você encontrará exemplos de modelos de json para criar VMs. O "101-vm-simple-linux" mostra a aparência de um modelo básico, incluindo a seção de armazenamento, que adiciona um disco de dados de 100 GB.

[Este artigo](virtual-machines-linux-sap-on-suse-quickstart.md) inclui algumas informações sobre como localizar uma imagem SUSE por meio do Powershell ou da CLI e sobre a importância de anexar um disco via UUID.


Dependendo do tamanho dos requisitos de sistema e de taxa de transferência, talvez seja necessário anexar vários discos em vez de um e, posteriormente, criar um conjunto de distribuição entre os discos no nível do sistema operacional. Estes são os dois aspectos com base nos quais uma pessoa criaria um conjunto de distribuição em vários discos do Azure:

* aumentar a taxa de transferência
* necessidade de um único sistema de arquivos > 1TB, pois o limite de tamanho de disco atual do Azure é de 1TB (desde julho de 2016)


Saiba mais sobre as duas ferramentas principais para configurar a distribuição aqui:

[Artigo sobre como usar mdadm para configurar o software raid do Linux em uma VM do Azure](virtual-machines-linux-configure-raid.md)

[Artigo sobre como configurar o Gerenciador de Volume Lógico em uma VM Linux do Azure](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

No ambiente de teste, dois discos de armazenamento padrão do Azure foram anexados à VM do servidor de aplicativos SAP. Um foi usado para armazenar todo o software SAP para instalação (por exemplo, NetWeaver 7.5, SAP GUI, SAP HANA...) e o outro para ter espaço suficiente para o que for necessário (por exemplo, backup, dados de teste), bem como o diretório sapmnt (por exemplo, os perfis SAP) a ser compartilhado entre todas as VMs que pertencem ao mesmo cenário SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Ao contrário da VM do servidor de aplicativo, quatro discos foram anexados à VM do servidor SAP HANA. Assim como antes, dois discos foram usados para manter o software SAP (um também podia compartilhar o disco de software SAP por meio de NFS) e ter espaço suficiente, por exemplo, para backup. Os dois discos adicionais eram discos de Armazenamento Premium do Azure para manter os arquivos de dados e de log do SAP HANA, bem como o diretório /usr/sap.


### Parâmetros de kernel


O SAP HANA exige configurações específicas de kernel do Linux que não fazem parte das imagens padrão da Galeria do Azure e precisam ser configuradas manualmente. Há uma observação SAP específica que descreve as configurações.


SAP Note SAP HANA DB: configurações recomendadas de sistema operacional para SLES 12/SLES para Aplicativos SAP 12: [ SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Confira [aqui](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache), no capítulo 6.1 Kernel: limite de cache de página, um tópico adicional sobre cache de página relacionado à execução do SAP HANA em SLES

Há também um SAP Note sobre o limite de cache de página [SAP Note 1557506](https://service.sap.com/sap/support/notes/1557506)

O SLES 12 tem uma nova ferramenta que substitui o utilitário sapconf antigo. É o "tuned-adm", e há um perfil especial do SAP HANA disponível. Confira mais detalhes sobre essa ferramenta nos dois links a seguir.

A documentação do SLES sobre tuned-adm profile sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

A documentação do SLES sobre o perfil sap-hana com tuned-adm - capítulo 6.2 Sistemas de ajuste para Cargas de Trabalho SAP com tuned-adm - pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Aqui é possível ver como "tuned-adm" alterou os valores de transparent\_hugepage e de numa\_balancing de acordo com as configurações necessárias do SAP HANA.


Para tornar as configurações de kernel SAP HANA permanentes, é preciso usar grub2 no SLES 12. Encontre mais informações sobre a grub2 [aqui](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Esta captura de tela mostra como as configurações de kernel foram alteradas no arquivo de configuração e, em seguida, "compiladas" por meio de grub2-mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Outra opção seria alterar as configurações usando Yast e as configurações de parâmetro de kernel do Carregador de Inicialização.


### Sistemas de arquivos 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Aqui é possível ver os dois sistemas de arquivos que foram criados na VM do servidor de aplicativos SAP sobre os dois discos de armazenamento padrão do Azure conectados. Ambos os sistemas de arquivos são do tipo XFS e montados em /sapdata e /sapsoftware.

Não é obrigatório para fazer dessa maneira. Há opções diferentes para estruturar o espaço em disco. O aspecto mais importante é evitar que o sistema de arquivos raiz fique sem espaço.


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Sobre a VM de BD do SAP HANA é importante saber que durante uma instalação de banco de dados por meio de sapinst (swpm) e usando apenas a opção de instalação simples "típica", ela instalará por padrão coisas em /hana e /usr/sap. A configuração padrão para o backup de log do SAP HANA, por exemplo, é em /usr/sap. Assim como antes, é fundamental evitar que o sistema de arquivos raiz fique sem espaço. Portanto, verifique se há espaço livre suficiente em /hana e /usr/sap antes da instalação do SAP HANA por meio de swpm.

[Este artigo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) da SAP descreve o layout do sistema de arquivos padrão do SAP HANA


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Ao instalar o SAP NetWeaver em uma imagem padrão de SLES 12 da Galeria do Azure, uma mensagem avisando que não há nenhum espaço de permuta será exibida. Para eliminar essa mensagem é possível, por exemplo, adicionar manualmente um arquivo de permuta, conforme descrito neste documento, usando dd, mkswap e swapon. Basta procurar por "Adicionar um arquivo de permuta manualmente" [neste artigo](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Outra opção é configurar o espaço de permuta por meio do agente de VM do Linux. Encontre mais informações [aqui](virtual-machines-linux-agent-user-guide.md)


### /etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Outro aspecto importante antes de começar a instalar o SAP é incluir nomes de host e endereços IP das VMs SAP no arquivo /etc/hosts. Implante todas as VMs SAP em uma rede virtual do Azure e, em seguida, use os endereços IP internos.

### /etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante a fase de teste, percebemos ser uma boa ideia adicionar o parâmetro nofail a fstab. Se algo der errado com os discos, a VM ainda funcionará e não travará durante o processo de inicialização. Mas, é preciso ficar atento, pois nesse caso talvez não haja espaço adicional em disco, e os processos podem preencher o sistema de arquivos raiz. No caso de /hana não estar presente, o SAP HANA não seria iniciado.


## Instalar o desktop Gnome gráfico no SLES 12

Este capítulo é formado por duas seções que abrangem os tópicos a seguir:

* Instalação do Gnome desktop e do xrdp no SLES 12
* Execução do SAP MC baseado em Java usando o Firefox no SLES 12

Também é possível usar alternativas como Xterminal, VNC, mas a partir de setembro de 2016 este artigo descreve apenas xrdp.

### Instalação do Gnome desktop e do xrdp no SLES 12

Especialmente para aqueles que têm experiência com o Microsoft Windows e gostariam de usar uma área de trabalho gráfica diretamente nas VMs SAP Linux para executar o Firefox, Sapinst, SAP GUI, SAP MC ou HANA Studio, e talvez se conectar à VM via RDP em um computador com Microsoft Windows, há uma maneira simples de fazer isso. Embora não seja apropriado, por exemplo, para um servidor de banco de dados de produção, isso serve para um ambiente puro de protótipo/demonstração. Estas são as etapas para instalar o desktop Gnome em uma VM SLES 12 do Azure:

instale o desktop gnome com o seguinte comando (por exemplo, em uma janela putty):

   zypper in -t pattern gnome-basic

em seguida, instale o xrdp para permitir a conexão com a VM via RDP:

   zypper in xrdp

edite /etc/sysconfig/windowmanager e defina o gerenciador do windows padrão como Gnome:

   DEFAULT\_WM="gnome"

execute o comando chkconfig para ter certeza de que o xrdp será iniciado automaticamente após a reinicialização:

  chkconfig -level 3 xrdp on

caso ocorra um problema com a conexão de RDP, tente reiniciar (talvez de uma janela putty):

  /etc/xrdp/xrdp.sh restart

caso a reinicialização xrdp mencionada acima não funcione, verifique se há um arquivo .pid e remova-o:

  verifique /var/run e procure xrdp.pid,   
  remova-o e, em seguida, tente novamente a reinicialização



### SAP MC


Para iniciar SAP MC gráfico baseado em Java no Firefox em execução em uma VM SLES 12 do Azure após a instalação do desktop Gnome, conforme descrito na seção anterior, você obterá um erro devido à ausência do plug-in de navegador Java.

A URL para iniciar o SAP MC é <servidor>: <número\_instância> 13 5

Encontre mais detalhes [aqui](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Na captura de tela acima, é possível ver a aparência da mensagem de erro quando o plug-in de navegador Java estiver ausente.

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Uma opção para resolver o problema é simplesmente instalar o plug-in ausente via Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

A repetição da URL do SAP MC mostrará uma pequena caixa de diálogo na primeira vez solicitando a ativação do plug-in.


Um problema adicional que talvez surja é uma mensagem de erro sobre um arquivo ausente: javafx.properties Provavelmente, isso está relacionado à instalação do Java 1.8, que é necessário para o SAP GUI 7.4

A versão do IBM Java por meio do Yast não inclui esse arquivo. A solução é um download de Java no site da Oracle. Confira [aqui](https://scn.sap.com/thread/3908306) um artigo que fala sobre esse problema específico



## Instalação manual do SAP HANA por meio de SWPM como parte de uma instalação do NetWeaver 7.5


A lista de capturas de tela a seguir mostra as principais etapas da instalação do SAP NetWeaver 7.5 e do SAP HANA SP12 via SWPM (sapinst). Como parte de uma instalação do NW 7.5, o SWPM também tem a capacidade de instalar o banco de dados HANA como uma única instância.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Para o ambiente de teste de exemplo, apenas um servidor de aplicativo ABAP foi instalado. A opção "Distributed System" foi usada para instalar a instância do ASCS e a instância do servidor de Aplicativo principal em uma VM do Azure, e o SAP HANA como o sistema de banco de dados em outra VM do Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Após a instalação da instância do ASCS na VM do servidor de aplicativo e sua definição como "verde" no SAP MC, o diretório sapmnt que inclui, por exemplo, o diretório de perfil do SAP, precisa ser compartilhado com a VM do servidor de banco de dados do SAP HANA. A etapa de instalação do banco de dados precisa acessar essas informações. A melhor maneira é usar NFS, que pode ser configurado usando Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Na VM do servidor de aplicativo, o diretório sapmnt deve ser compartilhado por meio de NFS usando as opções "rw", bem como "no\_root\_squash". O padrão é "ro" e "root\_squash", o que pode resultar em problemas ao instalar a instância de banco de dados.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Na VM do servidor de banco de dados do SAP HANA, o compartilhamento de sapmnt da VM do servidor de aplicativo deve ser configurado por meio do "Cliente NFS" (por exemplo, com a ajuda do Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Em seguida, é necessário fazer logon na VM do servidor de banco de dados do SAP HANA e iniciar o SWPM para realizar a próxima etapa de uma instalação distribuída do NW 7.5 - "Instância de banco de dados".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Com relação à instalação do SAP HANA, não há muita coisa a fazer após a seleção de uma instalação "típica". Além do caminho até a mídia de instalação, é necessário inserir um SID de banco de dados, o nome do host, o número da instância e a senha de Administrador de sistema do banco de dados.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

A próxima etapa é inserir a senha para o esquema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Logo vem a pergunta sobre a senha do esquema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

No final, deve haver apenas as marcas de seleção verde na frente de cada fase do processo de instalação do banco de dados, e a pequena caixa de mensagem que surge mostrando "A execução da... Instância de banco de dados foi concluída".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Após a instalação bem-sucedida, o SAP MC também deve mostrar a instância do banco de dados como "verde", e a lista completa de processos do SAP HANA (por exemplo, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de tela mostra as partes da estrutura de arquivos em /hana/shared criada pelo SWPM durante a instalação do HANA. Não havia opção para especificar um caminho diferente. Por isso é tão importante montar o espaço em disco adicional em /hana antes da instalação do SAP HANA via SWPM, a fim de evitar que o sistema de arquivos raiz fique sem espaço livre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Aqui é possível ver a mesma coisa, conforme descrito antes para o diretório /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

A última etapa da instalação ABAP distribuída é a "Instância primária do servidor de aplicativo"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Após a instalação do PAS, bem como do SAP GUI, é possível verificar por meio da transação "dbacockpit" que tudo parece certo com a instalação do SAP HANA.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Como uma último etapa, é possível instalar o SAP HANA Studio na VM do servidor de aplicativo SAP e conectar-se à instância do SAP HANA em execução na VM do servidor de banco de dados.




## Instalação manual do SAP HANA por meio da ferramenta hdblcm do Gerenciador de ciclo de vida HANA


Além de instalar o SAP HANA como parte de uma instalação distribuída por meio de SWPM, também é possível instalar primeiro o HANA autônomo usando hdblcm e depois instalar, por exemplo, o SAP NetWeaver 7.5. A lista de capturas de tela abaixo mostra como isso funciona.

Confira três fontes de informações sobre a ferramenta hdblcm de HANA:

[Choosing the Correct SAP HANA HDBLCM for Your Task (Escolhendo o HDBLCM do SAP HANA correto para sua tarefa)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA Lifecycle Management Tools (Ferramentas de gerenciamento de ciclo de vida do SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Installation and Update Guide (Guia de instalação e atualização do servidor SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Para evitar problemas posteriores com uma configuração de ID de grupo padrão para o usuário adm <HANA SID> (criado pela ferramenta hdblcm), é necessário definir um novo grupo chamado "sapsys" com ID de grupo 1001 antes da instalação do SAP HANA usando hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Ao iniciar o hdblcm pela primeira vez, será exibido um menu Iniciar simples no qual será necessário selecionar o item 1 "Instalar Novo Sistema"



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Nessa captura de tela, é possível ver todas as principais opções inseridas antes. Importante - os diretórios que foram nomeados para volumes de dados e de log do HANA, bem como o caminho de instalação (hana/shared neste exemplo) e usr/sap NÃO devem fazer parte do sistema de arquivos raiz, mas devem pertencer aos discos de dados do Azure que foram anexados à VM, conforme descrito na seção de configuração da VM do Azure. Isso evitará o risco de o sistema de arquivos raiz ficar sem espaço. Também é possível ver que o usuário admin do HANA tem a ID de usuário 1005 e faz parte do grupo sapsys (ID 1001) que foi definido antes da instalação.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

É possível verificar os detalhes do usuário HANA <HANA SID>adm (azdadm neste exemplo) em /etc/passwd



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Após a instalação do SAP HANA usando hdblcm, ele poderá ser visto no SAP HANA Studio. O esquema SAPABAP1 que inclui, por exemplo, todas as tabelas do SAP NetWeaver ainda não está disponível.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Após a instalação do SAP HANA, é possível instalar o SAP NetWeaver nele. Neste exemplo, isso foi feito usando uma "instalação distribuída" por SWPM, conforme descrito na seção correspondente acima. Ao instalar a instância de banco de dados via SWPM, é necessário apenas precisa inserir os mesmos dados de antes com hdblcm (por exemplo, nome de host, HANA SID, número de instância). Em seguida, o SWPM usará a instalação existente do HANA e adicionará outros esquemas.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Esta é a imagem da etapa de instalação SWPM, na qual é necessário inserir dados sobre o esquema DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Em seguida, o SWPM espera inserir dados sobre o esquema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Após a conclusão da instalação da instância do banco de dados SWPM, é possível ver o esquema SAPABAP1 no HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

E, finalmente, após a instalação do servidor de aplicativo SAP e do SAP GUI, deve ser possível verificar a instância de banco de dados do HANA com transação "dbacockpit".




## Informações gerais relacionadas às certificações SAP do Azure, execução do SAP HANA no Azure e o download de software SAP

* documentação geral do SAP Azure sobre como executar o SAP no Azure com o sistema operacional Windows no modo clássico: [Como usar o SAP em máquinas virtuais do Windows no Azure](virtual-machines-windows-classic-sap-get-started.md)

* informações sobre modelos SAP existentes para uso dos clientes: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) (Modelos de início rápido do Azure para SAP)

* documentação geral do SAP Azure sobre como executar o SAP no Azure com o sistema operacional Linux no modo do Azure Resource Manager: [Como usar o SAP em VMs (máquinas virtuais) do Linux](virtual-machines-linux-sap-get-started.md)

* diretório de hardware SAP HANA certificado que lista os tipos de VM do Azure com suporte para a produção: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Diretório de hardware HANA SAP® certificado)

* informações sobre tamanhos de máquina virtual, especialmente para cargas de trabalho Linux: [Tamanhos de máquinas virtuais no Azure](virtual-machines-linux-sizes.md)

* SAP Note que lista todos os produtos SAP com suporte no Azure e tipos de VM do Azure com suporte para SAP: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* SAP Note sobre o SAP "monitoramento avançado" com VMs Linux no Azure: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* Oferta do SAP HANA em "instâncias grandes"do Azure. É importante entender que isso não se trata da execução do SAP HANA em VMs do Azure, mas em um ambiente híbrido no qual os servidores de aplicativo SAP são executados em VMs do Azure, porém o SAP HANA é executado em servidores bare-metal: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* SAP Note com informações sobre SAPOSCOL no Linux: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Principais métricas de monitoramento para SAP no Microsoft Azure: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](../resource-group-overview.md)

* Informações sobre implantação de VMs Linux usando modelos: [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure](virtual-machines-linux-cli-deploy-templates.md)

* Comparação de modelos de implantação entre o Azure Resource Manager e o clássico: [Implantação do Azure Resource Manager versus clássica: compreenda os modelos de implantação e o estado de seus recursos](../resource-manager-deployment-model.md)

* Baixe o NetWeaver 7.5 para Linux/HANA no SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Baixe o HANA SP12 Platform Edition no SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!----HONumber=AcomDC_0928_2016-->
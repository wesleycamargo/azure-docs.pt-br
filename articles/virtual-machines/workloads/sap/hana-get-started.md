---
title: "Guia de início rápido: instalação manual do SAP HANA de instância única em VMs do Azure | Microsoft Docs"
description: "Guia de início rápido para a instalação manual do SAP HANA de instância única em VMs do Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6521e1e71b18a352404c1c63c5d745d637951ea9
ms.lasthandoff: 04/03/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guia de início rápido: instalação manual do SAP HANA de instância única em VMs do Azure
## <a name="introduction"></a>Introdução
Este guia de início rápido o ajuda a configurar um sistema de protótipo ou demonstração do SAP HANA de instância única em VMs (máquinas virtuais) do Azure durante a instalação manual do SAP NetWeaver 7.5 e do SAP HANA SP12.

O guia pressupõe que você conhece as noções básicas do Azure IaaS, por exemplo:
 * Como implantar máquinas ou redes virtuais usando o portal do Azure ou o PowerShell.
 * A CLI (linha de comando) entre plataformas do Azure, incluindo a opção de usar modelos JSON (JavaScript Object Notification).

Este guia também pressupõe que você conhece o SAP HANA e o SAP NetWeaver e sabe como instalá-los no local.

Além disso, você deve conhecer a documentação do SAP Azure mencionada na seção "Informações gerais" no final do guia.

Como o conteúdo do guia é restrito aos sistemas de não produção, ele não abrange tópicos como HA (alta disponibilidade), backup, DR (recuperação de desastres), alto desempenho ou aspectos especiais de segurança.

SAP-Linux-Azure tem suporte apenas no Azure Resource Manager e não no modelo de implantação clássico. Por isso, executamos uma configuração de exemplo usando duas máquinas virtuais para realizar uma instalação do SAP NetWeaver distribuída usando o modelo do Azure Resource Manager. Para saber mais sobre o Gerenciador de Recursos, confira a seção "Informações gerais" no final do guia.

Para a instalação de exemplo, usamos as duas VMs de teste abaixo:

* hana-appsrv (tipo DS3) para hospedar a instância ASCS (ABAP SAP Central Services) + PAS do NetWeaver 7.5
* hana-dbsrv (tipo GS4) para hospedar o HANA SP12

As VMs pertenciam a uma rede virtual do Azure (azure hana-test-vnet) e o sistema operacional em ambos os casos era SLES 12 SP1.

Desde julho de 2016, o SAP HANA só tem suporte integral aos sistemas de produção OLAP (BW) no tipo de VM do Azure GS5. Para fins de teste, se você achar que não precisará de suporte oficial da SAP, poderá usar algo menor, como a GS4. No caso de SAP HANA no Azure, use sempre armazenamento premium do Azure para arquivos de dados e log do HANA (confira a seção "Configuração do disco" no final do guia). Para saber mais sobre quais produtos da SAP têm suporte no Azure, confira a seção "Informações gerais" no final do guia.

O guia descreve duas maneiras diferentes de instalar o SAP HANA manualmente em VMs do Azure:

* Usando o SWPM (Software Provisioning Manager) da SAP como parte de uma instalação distribuída do NetWeaver na etapa "instância de banco de dados".
* Usando a ferramenta de gerenciamento de ciclo de vida HANA hdblcm e instalando o NetWeaver posteriormente.

Também é possível usar o SWPM e instalar todos os componentes (SAP HANA, servidor de aplicativos SAP, instância do ASCS, SAP GUI) em uma única VM. Essa opção não está descrita no guia, mas os itens a ser considerados são os mesmos.

Antes de iniciar uma instalação, leia o documento "Preparar VMs do Azure para instalação manual do SAP HANA" que vem imediatamente após as duas listas de verificação da instalação do SAP HANA. Isso pode ajudar a evitar vários erros básicos que podem ocorrer quando você usa apenas uma configuração de VM do Azure padrão.

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>Lista de verificação da instalação do SAP HANA usando o SWPM da SAP
Esta seção lista as etapas principais para uma instalação manual do SAP de instância única para fins de protótipo ou demonstração com o uso do SWPM da SAP para executar uma instalação distribuída do SAP NetWeaver 7.5. Os itens individuais são explicados em mais detalhes nas capturas de tela que aparecem neste guia.

* Criar uma rede virtual do Azure que inclui as duas VMs de teste.
* Implantar duas VMs do Azure com o sistema operacional SLES/SLES Linux Enterprise Server para Aplicativos SAP 12 SP1 usando o modelo do Azure Resource Manager.
* Anexar dois discos de armazenamento padrão à VM do servidor de aplicativo (por exemplo, discos de 75 GB ou 500 GB).
* Anexar quatro discos à VM do servidor de banco de dados do HANA: dois discos de armazenamento padrão (como aqueles para a VM do servidor de aplicativo) e dois discos de armazenamento premium (por exemplo, dois discos de 512 GB).
* Dependendo dos requisitos de tamanho ou produtividade, anexar vários discos e criar volumes distribuídos por meio de LVM (gerenciamento de volumes lógicos) ou de mdadm (utilitário de administração de vários dispositivos) no nível do sistema operacional dentro da VM.
* Criar sistemas de arquivos XFS nos discos anexados/volumes lógicos.
* Montar os novos sistemas de arquivos XFS no nível do sistema operacional. Usar um sistema de arquivos para armazenar todo o software SAP e o outro, por exemplo, para o diretório sapmnt e talvez backups. No servidor de banco de dados do SAP HANA, montar os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Esse processo é necessário para impedir o sistema de arquivos raiz fique cheio, já que ele não é grande em VMs Linux do Azure.
* Inserir os endereços IP locais das VMs de teste em /etc/hosts.
* Inserir o parâmetro nofail em /etc/fstab.
* Definir parâmetros de kernel de acordo com a observação SAP HANA-SLES-12. Para saber mais, confira a seção "Parâmetros de Kernel".
* Adicionar espaço de troca.
* Como opção, instalar uma área de trabalho gráfica nas VMs de teste. Caso contrário, usar uma instalação remota SAPinst.
* Baixar o software SAP no marketplace do serviço SAP.
* Instalar a instância ASCS do SAP na VM do servidor de aplicativo.
* Compartilhar o diretório /sapmnt entre as VMs de teste usando NFS. A VM do servidor de aplicativo é o servidor NFS.
* Instalar a instância de banco de dados, incluindo o HANA, usando o SWPM na VM do servidor de banco de dados.
* Instalar o PAS (servidor de aplicativo principal) na VM do servidor de aplicativo.
* Iniciar o SAP MC (console de gerenciamento) e conectar-se com o SAP GUI/HANA Studio, por exemplo.

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>Lista de verificação da instalação do SAP HANA usando hdblcm
Esta seção lista as etapas principais para uma instalação manual do SAP de instância única para fins de protótipo ou demonstração com o uso do hdblcm da SAP para executar uma instalação distribuída do SAP NetWeaver 7.5. Os itens individuais são explicados em mais detalhes nas capturas de tela que aparecem neste guia.

* Criar uma rede virtual do Azure que inclui as duas VMs de teste.
* Implantar duas VMs do Azure com o sistema operacional SLES/SLES Linux Enterprise Server para Aplicativos SAP 12 SP1 usando o modelo do Azure Resource Manager.
* Anexar dois discos de armazenamento padrão à VM do servidor de aplicativo (por exemplo, discos de 75 GB ou 500 GB).
* Anexar quatro discos à VM do servidor de banco de dados do HANA: dois discos de armazenamento padrão (como aqueles para a VM do servidor de aplicativo) e dois discos de armazenamento premium (por exemplo, dois discos de 512 GB).
* Dependendo dos requisitos de tamanho ou produtividade, anexar vários discos e criar volumes distribuídos por meio de LVM (gerenciamento de volumes lógicos) ou de mdadm (utilitário de administração de vários dispositivos) no nível do sistema operacional dentro da VM.
* Criar sistemas de arquivos XFS nos discos anexados/volumes lógicos.
* Montar os novos sistemas de arquivos XFS no nível do sistema operacional. Usar um sistema de arquivos para armazenar todo o software SAP e o outro, por exemplo, para o diretório sapmnt e talvez backups. No servidor de banco de dados do SAP HANA, montar os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Esse processo é necessário para ajudar a impedir que o sistema de arquivos raiz fique cheio, já que ele não é grande em VMs Linux do Azure.
* Inserir os endereços IP locais das VMs de teste em /etc/hosts.
* Inserir o parâmetro nofail em /etc/fstab.
* Definir parâmetros de kernel de acordo com a observação SAP HANA-SLES-12. Para saber mais, confira a seção "Parâmetros de Kernel".
* Adicionar espaço de troca.
* Como opção, instalar uma área de trabalho gráfica nas VMs de teste. Caso contrário, usar uma instalação remota SAPinst.
* Baixar o software SAP no marketplace do serviço SAP.
* Criar o grupo "sapsys" com a ID de grupo 1001 na VM do servidor de banco de dados do HANA.
* Instalar o SAP HANA na VM do servidor de banco de dados usando o HDBLCM (gerenciador de ciclo de vida do banco de dados do HANA).
* Instalar a instância ASCS do SAP na VM do servidor de aplicativo.
* Compartilhar o diretório /sapmnt entre as VMs de teste usando NFS. A VM do servidor de aplicativo é o servidor NFS.
* Instalar a instância de banco de dados, incluindo o HANA, usando o SWPM na VM do servidor de banco de dados do HANA.
* Instalar o PAS (servidor de aplicativo principal) na VM do servidor de aplicativo.
* Iniciar o SAP MC e conectar-se usando o SAP GUI/HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparar VMs do Azure para instalação manual do SAP HANA
Esta seção contém os seguintes tópicos:

* Atualizações de SO
* Configuração de disco
* Parâmetros de kernel
* Sistemas de arquivos
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>Atualizações de SO
Já que o SUSE Linux Enterprise Server fornece atualizações e correções para o sistema operacional que ajudam a manter a segurança e a operação contínua, é aconselhável verificar se há atualizações disponíveis antes de instalar software adicional.
Uma chamada de suporte muitas vezes poderia ser evitada se o sistema está no nível de patch real.

As imagens do Azure sob demanda serão automaticamente conectadas à infraestrutura de atualização do SUSE em que software adicional e atualizações estão disponíveis.
As imagens BYOS precisam ser registrados com a Central do Cliente SUSE (https://scc.suse.com)

Basta marcar os patches disponíveis com:

 `sudo zypper list-patches`

Dependendo do tipo de defeito, os patches são classificados por categoria e gravidade.

Os valores usados para categoria são: segurança, recomendado, opcional, recurso, documento ou yast.

Os valores usados para gravidade são: crítica, importante, moderada, baixa ou não especificada.

O zypper só procurará as atualizações necessárias para os pacotes instalados.

Um exemplo de comando pode ser:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Se você adicionar o parâmetro *--dry-run*, você poderá testar a atualização, mas ela não atualiza realmente o sistema.


### <a name="disk-setup"></a>Configuração de disco
O sistema de arquivos raiz em uma VM Linux no Azure tem tamanho limitado. Portanto, é necessário anexar espaço em disco adicional a uma VM para executar o SAP. Se a VM de servidor do aplicativos SAP é usada em um ambiente puro de protótipo ou demonstração, é possível usar discos de armazenamento padrão do Azure. Para arquivos de log e dados do banco de dados do SAP HANA, use discos de armazenamento premium do Azure até mesmo em um cenário de não produção.

Para saber mais, confira [como anexar discos a uma VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No caso do cache de disco do Azure, insira **Nenhum** para discos que devem ser usados no armazenamento dos logs de transação do HANA. No caso dos arquivos de dados do HANA, é possível usar o cache de leitura. Como HANA é um banco de dados na memória, a quantidade de cache de leitura no nível do disco do Azure para melhorar o desempenho (por exemplo, iniciando o HANA e lendo dados do disco na memória) depende do padrão de uso geral.

Para saber mais, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../../storage/storage-premium-storage.md).

Para localizar modelos JSON de exemplo para criar VMs, vá para [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
O modelo "vm-simple-sles" mostra um modelo básico que inclui uma seção de armazenamento com um mais um disco de dados de 100 GB.

Para saber mais sobre como localizar uma imagem do SUSE usando o PowerShell ou a CLI e entender a importância de anexar um disco usando o UUID, confira [Executando o SAP NetWeaver em VMs Linux do Microsoft Azure SUSE](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dependendo dos requisitos de tamanho ou de produtividade do sistema, talvez seja necessário anexar vários discos e, depois, criar um conjunto de distribuição no nível do sistema operacional. Você criará um conjunto de distribuição em vários discos do Azure por dois motivos:

* Para aumentar a produtividade.
* Para ter um sistema de arquivo único que seja maior que 1 TB, já que o limite de tamanho do disco do Azure atualmente é de 1 TB (a partir de julho de 2016).

Para saber mais sobre as duas principais ferramentas para configurar distribuição, confira os seguintes artigos:

* [Configurar RAID de software no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar o LVM em uma VM Linux no Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

No ambiente de teste, dois discos de armazenamento padrão do Azure foram anexados à VM do servidor de aplicativo SAP, como mostra a captura de tela a seguir. Um disco foi usado para armazenar todo o software SAP para instalação (ou seja, NetWeaver 7.5, SAP GUI, SAP HANA e outros). O segundo disco garantiu espaço livre suficiente para os requisitos adicionais (por exemplo, dados de backup e teste) e fez com que o diretório /sapmnt (ou seja, perfis SAP) fosse compartilhado entre todas as VMs que pertencem ao mesmo cenário SAP.

![Janela Discos do servidor de aplicativo SAP HANA exibindo dois discos de dados e seus tamanhos](./media/hana-get-started/image003.jpg)

Diferentemente do cenário de VM do servidor de aplicativo, quatro discos foram anexados à VM do servidor SAP HANA, como mostra a captura de tela a seguir. Dois discos foram usados para armazenar o software SAP (você também pode compartilhar o disco do software SAP usando NFS) e liberar espaço livre suficiente (para backup, por exemplo). Os dois discos adicionais eram discos de armazenamento premium do Azure para manter os arquivos de dados e de log do SAP HANA, bem como o diretório /usr/sap.

![Janela Discos do servidor de aplicativo SAP HANA exibindo quatro discos de dados e seus tamanhos](./media/hana-get-started/image004.jpg)

### <a name="kernel-parameters"></a>Parâmetros de kernel
O SAP HANA exige configurações específicas de kernel do Linux que não fazem parte das imagens padrão da galeria do Azure e precisam ser configuradas manualmente. Uma nota do SAP HANA descreve as configurações recomendadas de sistema operacional para SLES 12/SLES para Aplicativos SAP 12: [ nota SAP 2205917](https://launchpad.support.sap.com/#/notes/2205917).


Aplicativos SLES para SAP 12 GA e SP1 têm uma nova ferramenta que substitui o antigo utilitário sapconf. Ele se chama "tuned-adm" e há um perfil especial do SAP HANA disponível para ele. Para ajustar o sistema para SAP HANA, simplesmente digite como usuário raiz: 'tuned-adm profile sap-hana'

Para saber mais sobre o tuned-adm, confira a documentação do SUSE em:

* [Documentação de SLES para Aplicativos SAP 12 SP1 sobre tuned-adm profile sap-hana.](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

Na captura de tela a seguir, você pode ver como tuned-adm alterou os valores de transparent_hugepage e de numa_balancing de acordo com as configurações obrigatórias do SAP HANA.

![A ferramenta tuned-adm altera valores de acordo com as configurações obrigatórias do SAP HANA](./media/hana-get-started/image005.jpg)

Para tornar as configurações de kernel SAP HANA permanentes, use grub2 no SLES 12. Para saber mais sobre grub2, vá para a seção ["Estrutura de arquivo de configuração" da documentação do SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

A captura de tela a seguir mostra como as configurações de kernel foram alteradas no arquivo de configuração e compiladas com o uso de grub2-mkconfig.

![Configurações de kernel alteradas no arquivo de configuração e compiladas usando grub2 mkconfig](./media/hana-get-started/image006.jpg)

Outra opção seria alterar as configurações usando o YaST e as configurações **Carregador de Inicialização** > **Parâmetros de Kernel**.

![A guia de configurações Parâmetros de Kernel no carregador de inicialização do YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de arquivos
A captura de tela a seguir mostra dois sistemas de arquivos que foram criados na VM do servidor de aplicativo SAP usando os dois discos de armazenamento padrão do Azure anexados. Os dois sistemas de arquivos são do tipo XFS e estão montados em /sapdata e /sapsoftware.

Não é obrigatório estruturar os sistemas de arquivos dessa maneira. Você tem outras opções para estruturar o espaço em disco. O mais importante é evitar que o sistema de arquivo raiz fique sem espaço livre.

![Dois sistemas de arquivos criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image008.jpg)

Em relação à VM do banco de dados do SAP HANA, é importante saber que durante a instalação de um banco de dados usando SAPinst (swpm) e a opção de instalação simples "típica", ela instala tudo em /hana e /usr/sap por padrão. A configuração padrão para o backup de log do SAP HANA é em /usr/sap. Novamente, como é importante evitar que o sistema de arquivos raiz fique sem espaço de armazenamento, verifique se há espaço livre suficiente em /hana e /usr/sap antes de instalar o SAP HANA usando SWPM.

Para obter uma descrição do layout do sistema de arquivos padrão do SAP HANA, confira o [Guia de atualização e instalação do servidor SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).
![Outros sistemas de arquivos criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image009.jpg)

Quando você instala o SAP NetWeaver em uma imagem da galeria do Azure SLES/SLES para Aplicativos SAP 12 padrão, uma mensagem é exibida dizendo que não há nenhum espaço de troca. Para ignorar essa mensagem, você pode adicionar um arquivo de permuta manualmente usando dd, mkswap e swapon. Para saber como fazer isso, pesquise "Adicionando um arquivo de permuta manualmente" na seção ["Usando o particionador YaST" da documentação do SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Outra opção é configurar o espaço de troca por meio do agente de VM do Linux. Para saber mais, confira o [Guia de usuário do agente Linux para o Azure](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![Mensagem pop-up informando que não há espaço de troca suficiente](./media/hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
Outra etapa importante antes de começar a instalar o SAP é incluir nomes de host e endereços IP das VMs SAP no arquivo /etc/hosts. Implante todas as VMs SAP em uma rede virtual do Azure e use os endereços IP internos.

![Nomes de host e endereços IP das VMs SAP listados no arquivo /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

Durante a fase de teste, percebemos ser uma boa ideia adicionar o parâmetro nofail a fstab. Se algo der errado com os discos, a VM ainda funcionará e não travará durante o processo de inicialização. Mas fique atento, já que, como neste cenário, o espaço em disco adicional pode não estar disponível e os processos podem preencher o sistema de arquivos raiz. Se /hana estiver ausente, o SAP HANA não será iniciado.

![Adicionar parâmetro nofail a fstab](./media/hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Instalar a área de trabalho Gnome gráfica no SLES 12/SLES para Aplicativos SAP 12
Esta seção contém os seguintes tópicos:

* Instalando a área de trabalho Gnome gráfica e o xrdp no SLES 12/SLES para Aplicativos SAP 12
* Executando o SAP MC baseado em Java usando o Firefox no SLES 12/SLES para Aplicativos SAP 12

Você também pode usar alternativas como Xterminal ou VNC, mas, a partir de setembro de 2016, o guia descreverá apenas xrdp.

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalando a área de trabalho Gnome gráfica e o xrdp no SLES 12/SLES para Aplicativos SAP 12
Se você souber usar o Microsoft Windows, poderá facilmente usar uma área de trabalho gráfica diretamente nas VMs SAP Linux para executar o Firefox, o Sapinst, a SAP GUI, o MC SAP ou o HANA Studio e conectar-se à VM via RDP em um computador Windows. Embora o procedimento possa não ser apropriado para um servidor de banco de dados de produção, ele serve para um ambiente puro de protótipo/demonstração. Veja como instalar a área de trabalho Gnome em uma VM SLES 12/SLES para Aplicativos SAP 12 do Azure:

Instale a área de trabalho Gnome digitando o seguinte comando (por exemplo, em uma janela putty):

`zypper in -t pattern gnome-basic`

Instale o xrdp para permitir uma conexão com a VM por meio de RDP:

`zypper in xrdp`

Edite /etc/sysconfig/windowmanager e defina o gerenciador de janelas padrão como Gnome:

`DEFAULT_WM="gnome"`

Execute o comando chkconfig para ter certeza de que o xrdp será iniciado automaticamente após a reinicialização:

`chkconfig -level 3 xrdp on`

Se você tiver um problema com a conexão do RDP, tente reiniciar (talvez de uma janela putty):

`/etc/xrdp/xrdp.sh restart`

Caso a reinicialização do xrdp mencionada acima não funcione, verifique se há um arquivo .pid e remova-o:

`check /var/run`e procure `xrdp.pid`

Remova-o e tente reiniciá-lo novamente.

### <a name="sap-mc"></a>SAP MC
Depois de instalar a área de trabalho Gnome, a inicialização do MC SAP gráfico baseado em Java no Firefox que está sendo executado em uma VM SLES 12/SLES para Aplicativos SAP 12 do Azure poderá exibir um erro devido à falta do plug-in de navegador Java.

A URL para iniciar o SAP MC é <server>:5<número_instância>13.

Para saber mais, confira [Iniciando o console de gerenciamento SAP baseado na Web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

A captura de tela a seguir mostra a mensagem de erro que é exibida quando o plug-in de navegador Java está ausente.

![Mensagem de erro indicando a falta do plug-in de navegador Java](./media/hana-get-started/image013.jpg)

Uma maneira de resolver o problema é simplesmente instalar o plug-in ausente usando o YaST, como mostra a captura de tela a seguir.

![Usando o YaST para instalar o plug-in ausente](./media/hana-get-started/image014.jpg)

A repetição da URL do Console de Gerenciamento SAP exibe uma caixa de diálogo que solicita a ativação do plug-in.

![Caixa de diálogo solicitando a ativação do plug-in](./media/hana-get-started/image015.jpg)

Outro problema que pode ocorrer é uma mensagem de erro sobre um arquivo ausente, javafx.properties. Isso está relacionado ao requisito de ter o Oracle Java 1.8 para SAP GUI 7.4. [consulte a Observação SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424) Nem a versão IBM Java nem o pacote openjdk fornecido SLES/SLES para Aplicativos SAP 12 incluem o javafx necessário. A solução é baixar e instalar o Java SE8 da Oracle.

Um artigo que fala sobre um problema semelhante no openSUSE com openjdk pode ser encontrado em [SAPGui 7.4 Java para openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>Instalar o SAP HANA manualmente usando SWPM como parte de uma instalação do NetWeaver 7.5
A série de capturas de tela desta seção mostra as principais etapas para instalar o SAP NetWeaver 7.5 e o SAP HANA SP12 com o uso do SWPM (SAPinst). Como parte de uma instalação NetWeaver 7.5, o SWPM também pode instalar o banco de dados do HANA como uma única instância.

No ambiente de teste de exemplo, instalamos apenas um servidor de aplicativos ABAP (Advanced Business Application Programming). Como mostra a captura de tela a seguir, usamos a opção "Sistema Distribuído" para instalar as instâncias ASCS e de servidor de aplicativos principal em uma VM do Azure e o SAP HANA como o sistema de banco de dados em outra VM do Azure.

![Instâncias ASCS e de servidor de aplicativos principal instaladas usando a opção "Sistema Distribuído"](./media/hana-get-started/image012.jpg)

Após a instalação da instância do ASCS na VM do servidor de aplicativo e sua definição como "verde" no SAP Management Console, o diretório /sapmnt (incluindo o diretório do perfil SAP) precisa ser compartilhado com a VM do servidor de banco de dados do SAP HANA. A etapa de instalação do banco de dados precisa acessar essas informações. A melhor maneira de fornecer acesso é usando NFS, que pode ser configurado com YaST.

![SAP Management Console mostrando a instância do ASCS instalada na VM do servidor de aplicativos e definida como "verde"](./media/hana-get-started/image016.jpg)

Na VM do servidor de aplicativo, o diretório sapmnt deve ser compartilhado por meio de NFS usando as opções **rw** e **no_root_squash**. Os padrões são "ro" e "root_squash", o que pode levar a problemas durante a instalação da instância de banco de dados.

![Compartilhamento do diretório /sapmnt por meio de NFS usando as opções "rw" e "no_root_squash"](./media/hana-get-started/image017b.jpg)

Como mostra a próxima captura de tela, o compartilhamento de /sapmnt da VM do servidor de aplicativos deve ser configurado na VM do servidor de banco de dados do SAP HANA usando o **Cliente NFS** (com a ajuda do YaST).

![O compartilhamento de /sapmnt configurado usando o "Cliente NFS"](./media/hana-get-started/image018b.jpg)

Para executar uma instalação distribuída do NetWeaver 7.5, **Instância de Banco de Dados**, como mostra a captura de tela a seguir, entre na VM do servidor de banco de dados do SAP HANA e inicie o SWPM.

![Instalação de uma instância de banco de dados entrando na VM do servidor de banco de dados do SAP HANA e iniciando o SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar uma instalação "típica" e o caminho para a mídia de instalação, insira um SID de banco de dados, o nome do host, o número de instância e a senha do Administrador do Sistema de Banco de Dados.

![A página de entrada do Administrador de Sistema de Banco de Dados do SAP HANA](./media/hana-get-started/image035b.jpg)

Insira a senha para o esquema DBACOCKPIT.

![A caixa de entrada de senha para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Insira uma pergunta para a senha do esquema SAPABAP1.

![Insira uma pergunta para a senha do esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Depois que a tarefa é concluída, uma marca de seleção verde é exibida ao lado de cada fase do processo de instalação do banco de dados. Uma janela de Caixa de Mensagem deve exibir uma mensagem que diz: "a execução da... instância de banco de dados foi concluída".

![Janela de tarefa concluída com mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após a instalação bem-sucedida, o SAP Management Console também deve mostrar a instância de banco de dados como "verde" e exibir a lista completa de processos do SAP HANA (hdbindexserver, hdbcompileserver e assim por diante).

![Janela do SAP Management Console com lista de processos do SAP HANA](./media/hana-get-started/image024.jpg)

A captura de tela a seguir mostra as partes da estrutura de arquivos em /hana/shared criada pelo SWPM durante a instalação do HANA. Como não havia nenhuma opção para especificar um caminho diferente, é importante montar o espaço em disco adicional em /hana antes da instalação do SAP HANA usando o SWPM para impedir que o sistema de arquivos raiz fique sem espaço livre.

![A estrutura de arquivo /hana/shared criada durante a instalação do HANA](./media/hana-get-started/image025.jpg)

Esta captura de tela mostra a estrutura de arquivos do diretório /usr/sap.

![A estrutura de arquivo de diretório /usr/sap](./media/hana-get-started/image026.jpg)

A última etapa da instalação ABAP distribuída é a "Instância Primária do Servidor de Aplicativos".

![Instalação ABAP mostrando a Instância Primária do Servidor de Aplicativos como a etapa final](./media/hana-get-started/image027b.jpg)

Depois que a Instância Primária do Servidor de Aplicativos e a SAP GUI são instaladas, use a transação "dbacockpit" para confirmar que a instalação do SAP HANA foi concluída corretamente.

![Janela DBA Cockpit confirmando a instalação bem-sucedida](./media/hana-get-started/image028b.jpg)

Como etapa final, primeiro instale o SAP HANA Studio na VM do servidor de aplicativos SAP e conecte-se à instância do SAP HANA que está em execução na VM do servidor de banco de dados.

![Instalando o SAP HANA Studio na VM do servidor de aplicativos SAP](./media/hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>Instalar o SAP HANA manualmente usando hdblcm (ferramenta de gerenciamento de ciclo de vida do HANA)
Além de instalar o SAP HANA como parte de uma instalação distribuída usando SWPM, você pode instalar primeiro o HANA autonomamente usando hdblcm. Depois disso, você pode instalar, por exemplo, o SAP NetWeaver 7.5. As capturas de tela a seguir mostram como esse processo funciona.

Confira três fontes de informações sobre a ferramenta hdblcm do HANA:

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Escolhendo o HDBLCM do SAP HANA correto para sua tarefa)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Ferramentas de gerenciamento de ciclo de vida do SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guia de instalação e atualização do servidor SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Para evitar problemas com uma configuração de ID de grupo padrão para o usuário administrador \<SID HANA SID\> (criado pela ferramenta hdblcm), defina um novo grupo chamado "sapsys" usando a ID de grupo 1001 antes de instalar o SAP HANA com hdblcm.

![Grupo novo "sapsys" definido usando a ID de grupo 1001](./media/hana-get-started/image030.jpg)

Quando você inicializa o hdblcm pela primeira vez, um menu de início simples é exibido. Selecione item 1, **Instalar novo sistema**, como mostra a captura de tela a seguir.

![Opção "Instalar o novo sistema" na janela de início do hdblcm](./media/hana-get-started/image031.jpg)

A captura de tela a seguir exibe todas as opções essenciais que você selecionou anteriormente.

> [!IMPORTANT]
> Os diretórios nomeados para log e volumes de dados do HANA, o caminho de instalação (hana/shared neste exemplo) e /usr/sap não devem ser parte do sistema de arquivos raiz. Os diretórios pertencem aos discos de dados do Azure que foram anexados à VM, conforme descrito na seção Configuração da VM do Azure. Essa abordagem ajudará a evitar o risco de ver o sistema de arquivos raiz ficar sem espaço. Você também pode ver que o usuário administrador do HANA tem a ID de usuário 1005 e faz parte do grupo sapsys (ID 1001) que foi definido antes da instalação.

![Lista de todos os principais componentes do SAP HANA selecionados anteriormente](./media/hana-get-started/image032.jpg)

Você pode verificar os detalhes do usuário administrador do HANA \<SID HANA\> (azdadm na seguinte captura de tela) em /etc/passwd.

![Detalhes do usuário administrador do HANA \<HANA SID\> listados em /etc/passwd](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA usando hdblcm, você poderá ver a estrutura de arquivo no SAP HANA Studio. O esquema SAPABAP1, que inclui, por exemplo, todas as tabelas do SAP NetWeaver, ainda não está disponível.

![Estrutura de arquivos do SAP HANA no SAP HANA Studio](./media/hana-get-started/image034.jpg)

Após a instalação do SAP HANA, você poderá instalar o SAP NetWeaver nele. Conforme mostrado nesta captura de tela, a instalação foi executada como uma "instalação distribuída" usando SWPM (descrito na seção anterior). Quando você instala a instância de banco de dados usando SWPM, insere os mesmos dados usando hdblcm (por exemplo, nome do host, SID HANA e número de instância). O SWPM usa a instalação do HANA existente e adiciona mais esquemas.

![Uma instalação distribuída realizada pelo SWPM](./media/hana-get-started/image035b.jpg)

A captura de tela a seguir mostra a etapa de instalação do SWPM onde você insere dados sobre o esquema DBACOCKPIT.

![A etapa de instalação do SWPM onde são inseridos os dados do esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Insira dados sobre o esquema SAPABAP1.

![Inserir dados sobre o esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Após a conclusão da instalação da instância do banco de dados SWPM, você poderá ver o esquema SAPABAP1 no HANA Studio.

![O esquema SAPABAP1 no SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Por fim, depois que as instalações do servidor de aplicativos SAP e da SAP GUI forem concluídas, você poderá verificar a instância de banco de dados do HANA usando a transação "dbacockpit".

![A instância de banco de dados do HANA verificada com transação "dbacockpit"](./media/hana-get-started/image039b.jpg)

## <a name="about-sap-azure-certifications-and-running-sap-hana-on-azure"></a>Sobre as certificações do SAP Azure e a execução do SAP HANA no Azure
Para saber mais, confira a seguinte documentação:
* Informações gerais do SAP Azure sobre como executar o SAP no Azure com o sistema operacional Windows no modo clássico: [usando o SAP em máquinas virtuais Windows no Azure](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Informações sobre modelos SAP existentes para uso pelos clientes: [modelos de início rápido do Azure para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).
* Informações gerais do SAP Azure sobre como executar o SAP no Azure com o sistema operacional Linux no modelo do Azure Resource Manager: [usando SAP em VMs (máquinas virtuais) Linux](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Diretório de hardware SAP HANA certificado que lista os tipos de VM do Azure com suporte para a produção: [diretório de hardware SAP HANA® certificado](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
* Informações sobre tamanhos de máquina virtual especialmente para cargas de trabalho Linux: [tamanhos de máquinas virtuais no Azure](../../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Nota SAP que lista todos os produtos SAP com suporte no Azure e tipos de VM do Azure com suporte para SAP: [nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533/E).
* Nota SAP sobre "monitoramento avançado" do SAP com VMs Linux no Azure: [nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E).
* Oferta do SAP HANA em "instâncias grandes"do Azure. É importante entender que essa informação não é sobre a execução do SAP HANA em VMs do Azure. Trata-se de um ambiente híbrido em que os servidores de aplicativos SAP são executados em VMs do Azure, mas o SAP HANA é executado em servidores bare-metal: [nota SAP 2316233](https://launchpad.support.sap.com/#/notes/2316233/E).
* Nota SAP com informações sobre SAPOSCOL no Linux: [nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E).
* Principais métricas de monitoramento para SAP no Microsoft Azure: [nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E).
* Para saber mais sobre o Azure Resource Manager: [visão geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).
* Informações sobre implantação de VMs Linux usando modelos: [implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure](../../linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Comparação dos modelos de implantação do Azure Resource Manager e clássica: [Implantação do Azure Resource Manager versus clássica: compreenda os modelos de implantação e o estado de seus recursos](../../../resource-manager-deployment-model.md).

## <a name="sap-software-downloads"></a>Downloads de software SAP
Você pode baixar o software do Marketplace do serviço SAP como mostram as capturas de tela a seguir.

* Baixar o NetWeaver 7.5 para Linux/HANA:

 ![Janela Instalação e Atualização do Serviço SAP para baixar o NetWeaver 7.5](./media/hana-get-started/image001.jpg)
* Baixar o HANA SP12 Platform Edition:

 ![Janela Instalação e Atualização do Serviço SAP para baixar o HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)



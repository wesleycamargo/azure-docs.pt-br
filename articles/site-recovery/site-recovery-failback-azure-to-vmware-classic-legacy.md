<properties 
   pageTitle="Execute o failback de máquinas virtuais VMware e servidores físicos do Azure para o VMware (herdado) | Microsoft Azure" 
   description="Este artigo descreve como executar o failback de uma máquina virtual VMware que foi replicada no Azure com o Azure Site Recovery." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="03/06/2016"
   ms.author="ruturajd@microsoft.com"/>

# Executar o failback de máquinas virtuais VMware e servidores físicos do Azure para o VMware com o Azure Site Recovery (herdado)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal Clássico do Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal clássico do Azure (Herdado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para ter uma breve visão geral, leia [O que é Azure Site Recovery?](site-recovery-overview.md)

## Visão geral

Este artigo descreve como fazer failback de máquinas virtuais VMware e servidores físicos Windows/Linux do Azure para seu site local depois de você replicar do site local no Azure.

>[AZURE.NOTE] Este artigo descreve um cenário herdado. Você só deve usar as instruções neste artigo se replicou no Azure usando [estas instruções herdadas](site-recovery-vmware-to-azure-classic-legacy.md). Se você configurar a replicação usando a [implantação aprimorada](site-recovery-vmware-to-azure-classic-legacy.md), siga as instruções [neste artigo](site-recovery-failback-azure-to-vmware-classic.md) para fazer o failback.


## Arquitetura

Este diagrama representa o cenário de failover e failback. As linhas azuis são as conexões usadas durante o failover. As linhas vermelhas são as conexões usadas durante o failback. As linhas com setas acessam a internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Antes de começar 

- Você deve ter feito failover em suas máquinas virtuais VMware ou servidores físicos e eles devem estar em execução no Azure.
- Observe que você pode executar o failback apenas de máquinas virtuais VMware e de servidores físicos Windows/Linux do Azure para máquinas virtuais VMware no site primário local. Se você estiver executando o failback de uma máquina física, o failover para o Azure a converterá em uma VM do Azure, e o failback para o VMware a converterá em uma VM do VMware.

Veja como configurar o failback:

1. **Configurar componentes de failback**: você precisará configurar um servidor vContinuum localmente e apontar para a VM do servidor de configuração no Azure. Você também configurará um servidor de processo como uma VM do Azure para enviar dados de volta ao servidor de destino mestre local. Registre o servidor de processo com o servidor de configuração que tratou do failover. Instale um servidor de destino mestre local. Se você precisar de um servidor de destino mestre Windows, ele será configurado automaticamente quando o vContinuum for instalado. Se você precisar de Linux, deverá defini-lo manualmente em um servidor separado.
2. **Habilitar proteção e failback**: depois de configurar os componentes, no vContinuum, você precisará habilitar a proteção de failover de máquinas virtuais do Azure. Você executará uma verificação de preparação nas máquinas virtuais e executará um failover do Azure para o site local. Após a conclusão do failback, proteja as máquinas locais novamente para que iniciem replicando no Azure.



## Etapa 1: Instalar o vContinuum no local

Será necessário instalar um servidor vContinuum no local e apontá-lo para o servidor de configuração.

1.  [Baixar o vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Baixe a versão de [atualização do vContinuum atualização](http://go.microsoft.com/fwlink/?LinkID=533813).
3. Instale a versão mais recente do vContinuum. Na página **Boas-vindas**, clique em **Avançar**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Na primeira página do assistente, especifique o endereço IP do servidor CX e a porta do servidor CX. Escolha **Usar HTTPS**.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Localize o endereço IP do servidor de configuração na guia **Painel** da VM do servidor de configuração no Azure. ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Localize a porta pública HTTPS do servidor de configuração na guia **Pontos de Extremidade** da VM do servidor de configuração no Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Na página **Detalhes da Senha do CS** especifique a senha que você anotou ao registrar o servidor de configuração. Se você não lembrar da senha, verifique em **C:\\Arquivos de Programa (x86)\\InMage Systems\\private\\connection.passphrase** na VM do servidor de configuração.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Na página **Selecionar Local de Destino** especifique onde você deseja instalar o servidor vContinuum e clique em **Instalar**.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Após a conclusão da instalação, inicie o vContinuum. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Etapa 2: Instalar um servidor de processo no Azure 

É necessário instalar um servidor de processo no Azure para que as VMs no Azure possam enviar os dados de volta para um servidor de destino mestre local.

1.  Na página **Servidores de Configuração** no Azure, selecione para adicionar um novo servidor de processo.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especifique um nome de servidor de processo, um nome e senha para se conectar à máquina virtual como administrador. Selecione o servidor de configuração no qual você deseja registrar o servidor de processo. Ele deve ser o mesmo servidor que você está usando para proteger e fazer fail over em suas máquinas virtuais.
3.  Especifique a rede do Azure na qual o servidor de processo deve ser implantado. Deve ser a mesma rede que o servidor de configuração. Especifique um endereço IP exclusivo na sub-rede selecionada e inicie a implantação.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Um trabalho será disparado para a implantação do servidor de processo.

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Após a implantação do servidor de processo no Azure, você poderá fazer logon no servidor usando as credenciais especificadas. Registre o servidor de processo da mesma maneira que você registrou o servidor de processo local.

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Os servidores registrados durante o failback não ficarão visíveis nas propriedades da VM na Recuperação de Site. Eles poderão ser vistos apenas na guia **Servidores** do servidor de configuração no qual foram registrados. Pode demorar cerca de 10 a 15 minutos até que o servidor de processo apareça na guia.


## Etapa 3: Instalar um servidor de destino mestre no local

Dependendo do sistema operacional das máquinas virtuais de origem, talvez seja necessário instalar um servidor de destino mestre com Windows ou Linux no local.

### Implantar um servidor de destino mestre com Windows

Um destino mestre com Windows já faz parte da instalação do vContinuum. Quando você instala o vContinuum, um servidor mestre também é implantado na mesma máquina e registrado no servidor de configuração.

1.  Para iniciar a implantação, crie uma máquina local vazia no host ESX no qual você deseja recuperar as VMs do Azure.

2.  Verifique se há pelo menos dois discos anexados à VM, um para o sistema operacional e o outro para a unidade de retenção.

3.  Instale o sistema operacional.

4.  Instale o vContinuum no servidor. Isso também concluirá a instalação do servidor de destino mestre.

### Implantar um servidor de destino mestre com Linux

1.  Para iniciar a implantação, crie uma máquina local vazia no host ESX no qual você deseja recuperar as VMs do Azure.

2.  Verifique se há pelo menos dois discos anexados à VM, um para o sistema operacional e o outro para a unidade de retenção.

3.  Instale o sistema operacional Linux. O sistema de destino mestre com Linux não deve usar LVM para raiz ou espaços de armazenamento de retenção. Um servidor de destino mestre com Linux é configurado por padrão para evitar a detecção de partições/discos LVM.
4.  As partições que você pode criar são:

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Execute as etapas posteriores à instalação, exibidas abaixo, antes de iniciar a instalação do destino mestre.


#### Etapas posteriores à instalação do SO:

Para obter as IDs de SCSI de cada disco SCSI em uma máquina virtual Linux, habilite o parâmetro "disk.EnableUUID = TRUE" da seguinte maneira:

1. Desligue sua máquina virtual.
2. Clique com o botão direito na entrada da VM no painel esquerdo > **Editar Configurações**.
3. Clique na guia **Opções**. Escolha **Avançado > Item geral** > **Parâmetros de Configuração**. A opção **Parâmetros de Configuração** só fica disponível quando a máquina é desligada.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Verifica se há uma linha com **disk.EnableUUID**. Se houver e estiver definida como **False**, defina-a como **True** (não diferencia maiúsculas de minúsculas). Se houver e estiver definida como true, clique em **Cancelar** e teste o comando SCSI dentro do sistema operacional convidado após a inicialização. Se não houver uma, clique em **Adicionar Linha**.
5. Adicione disk.EnableUUID na coluna **Nome**. Defina seu valor como TRUE. Não adicione os valores acima com aspas duplas.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Baixar e instalar os pacotes adicionais

OBSERVAÇÃO: verifique se o sistema tem conectividade com a internet antes de baixar e instalar os pacotes adicionais.

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Esse comando baixa estes 15 pacotes do repositório CentOS 6.6 e os instala:

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm
	
perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

OBSERVAÇÃO: se a máquina de origem usar o sistema de arquivos Reiser ou XFS para o dispositivo raiz ou de inicialização, os pacotes a seguir deverão ser baixados e instalados no destino mestre com Linux antes da proteção.

\# cd /usr/local

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicar alterações de configuração personalizadas

Antes de aplicar essas alterações, conclua a seção anterior e execute estas etapas:


1. Copie o binário Agente Unificado RHEL 6-64 para o SO recém-criado.

2. Execute este comando para descompactar o binário: **tar -zxvf <Nome do arquivo>**

3. Execute este comando para dar permissões: # **chmod 755 ./ApplyCustomChanges.sh**

4. Execute o script: **# ./ApplyCustomChanges.sh**. Execute o script apenas uma vez no servidor. Reinicie o servidor após a execução do script.



### Instalar o servidor Linux


1. [Baixe](http://go.microsoft.com/fwlink/?LinkID=529757) o arquivo de instalação.
2. Copie o arquivo na máquina virtual de destino mestre com Linux usando um utilitário de cliente sftp de sua escolha. Como alternativa, você pode fazer logon na máquina virtual de destino mestre com Linux e usar wget para baixar o pacote de instalação do link fornecido.
3. Faça logon na máquina virtual do servidor de destino mestre com Linux usando um cliente ssh de sua escolha.
4. Se você estiver conectado à rede do Azure no qual implantou o servidor de destino mestre com Linux por meio de uma conexão VPN, use o endereço IP interno do servidor, obtido na guia **Painel** da máquina virtual, e a porta 22 para se conectar ao servidor de destino mestre com Linux usando Secure Shell.
5. Se você estiver se conectando ao servidor de destino mestre com Linux em uma conexão de internet pública, use o endereço IP virtual público do servidor de destino mestre com Linux (na guia **Painel** das máquinas virtuais) e o ponto de extremidade público criado para ssh a fim de fazer logon no servidor com Linux.
6. Extraia os arquivos tar compactado com gzipped do instalador do servidor de destino com Linux executando: *“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64\\”* do diretório que contém o arquivo do instalador.

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Se você extraiu os arquivos do instalador para um diretório diferente, altere para o diretório no qual o conteúdo do arquivo tar foi extraído. Desse caminho de diretório, execute “sudo ./install.sh”.

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Quando receber a solicitação para escolher uma função primária, escolha **2 (Destino Mestre)**. Deixe as outras opções de instalação interativas com seus valores padrão.
9. Aguarde a continuação da instalação e a exibição da Interface de Configuração do Host. O utilitário de Configuração do Host para o servidor de destino mestre com Linux é um utilitário de linha de comando. Não redimensione a janela do utilitário cliente ssh. Use as teclas de direção para selecionar a opção **Global** e pressione ENTER no teclado.

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. No campo **IP**, digite o endereço IP Interno do servidor de configuração (encontre-o na guia **Painel** da VM do servidor de configuração) e pressione ENTER. Em **Porta** insira **22** e pressione ENTER.
11.  Deixe **Usar HTTPS** como **Sim** e pressione ENTER.
12.  Insira a Senha gerada no servidor de configuração. Se você estiver usando um cliente PUTTY de uma máquina com Windows para aplicar ssh à máquina virtual com Linux, use Shift+Insert para colar o conteúdo da área de transferência. Copie a senha na área de transferência local usando Ctrl-C e use Shift+Insert para colá-la. Pressione ENTER.
13.  Use a tecla de direção para a direita para sair e pressione ENTER. Aguarde a conclusão da instalação.

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se por algum motivo você não registrar o servidor de destino mestre com Linux no servidor de configuração, faça isso novamente executando o utilitário de configuração do host de /usr/local/ASR/Vx/bin/hostconfigcli (primeiro será necessário definir as permissões de acesso para esse diretório executando chmod como um superusuário)


#### Validar o registro de destino mestre

Você pode validar se o servidor de destino mestre foi registrado com êxito no servidor de configuração no cofre do Azure Site Recovery > **Servidor de Configuração** > **Detalhes do Servidor**.

>[AZURE.NOTE] Se, após o registro do servidor de destino mestre, você receber erros de configuração indicando que a máquina virtual pode ter sido excluída do Azure ou que os pontos de extremidade não estão configurados corretamente, isso será uma indicação de que, embora a configuração de destino mestre seja detectada pelos dndpoints do Azure na implantação do destino mestre no Azure, isso não vale para um servidor de destino mestre local. Isso não afetará o failback e você pode ignorar esses erros.



## Etapa 4: Proteger as máquinas virtuais no site local

Você precisa proteger as VMs no site local antes de realizar o failback.

### Antes de começar

Quando uma VM está em estado de failover para o Azure, ocorre a adição de uma unidade temp para o arquivo de paginação. Essa unidade extra normalmente não é exigida por sua VM em estado de failover, pois ele já pode ter uma unidade dedicada ao arquivo de paginação. Antes de iniciar a proteção inversa das máquinas virtuais, você precisa garantir que essa unidade seja colocada offline para que não seja protegida. Faça isso da seguinte forma:

1.  Abra o Gerenciamento do Computador e selecione Gerenciamento de Armazenamento para listar os discos online e conectados à máquina.
2.  Selecione o disco temporário conectado à máquina e escolha colocá-lo offline. 

### Proteger as VMs

1. No Portal do Azure, examine os estados da máquina virtual e certifique-se de que passaram por failover.

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Inicie o vContinuum em sua máquina.

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Clique em **Nova Proteção** e selecione o tipo de sistema operacional, o

4.  Na nova janela aberta, selecione o **Tipo de SO** > **Obter Detalhes** para as VMs das quais você deseja realizar o failback. Em **Detalhes do servidor primário**, identifique e selecione as máquinas virtuais que você deseja proteger. As VMs estão listadas sob o nome de host vCenter que tinham antes do failover.
5.  Quando você seleciona uma máquina virtual para proteção (e ela já está em estado de failover no Azure) uma janela pop-up fornece duas entradas para a máquina virtual. Isso ocorre porque o servidor de configuração detectou duas instâncias de máquinas virtuais registradas nele. É necessário remover a entrada para a VM local para que você possa proteger a VM correta. Para identificar a entrada da VM do Azure correta, faça logon na VM do Azure e acesse C:\\Program Files (x86) \\Microsoft Azure Site Recovery\\Application Data\\etc. No arquivo drscout.conf , identifique a ID do Host. Na caixa de diálogo vContinuum, mantenha a entrada para a qual você encontrou a ID do host na VM. Exclua todas as outras entradas. Para selecionar a VM correta, você pode fazer referência ao endereço IP. O intervalo de endereço IP local será a VM local.

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. No servidor do vCenter, interrompa a máquina virtual. Você também pode excluir as VMs locais.
7. Em seguida, especifique o servidor de DM local no qual você deseja proteger as VMs. Para fazer isso, conecte-se ao servidor do vCenter ao qual você deseja realizar o failback

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Selecione o servidor de destino mestre com base no host no qual você deseja recuperar a VM.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Forneça a opção de replicação para cada uma das máquinas virtuais Para fazer isso, você precisa selecionar o armazenamento de dados do lado de recuperação no qual as VMs serão recuperadas. A tabela resume as diferentes opções que você precisa fornecer para cada VM.

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**Opção** | **Valor recomendado para a opção**
	---|---
	Processar o endereço IP do servidor | Selecionar o servidor de processo implantado no Azure
	Tamanho de retenção em MB| 
	Valor de retenção | 1
	Dias/Horas | Dias
	Intervalo de consistência | 1
	Selecione o Armazenamento de Dados de Destino | O armazenamento de dados disponível no local da recuperação. O armazenamento de dados deve ter espaço suficiente e também estar disponível para o host ESX no qual você deseja restaurar a máquina virtual.


10. Configure as propriedades que serão adquiridas pela máquina virtual após o failover para o site local. As propriedades estão resumidas na tabela a seguir.

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**Propriedade** | **Detalhes**
	---|---
	Configuração de rede| Selecione cada adaptador de rede detectado e clique em **Alterar** para configurar o endereço IP de failback da máquina virtual. 
	Configuração de hardware| Especifique os valores de CPU e memória da VM. As configurações podem ser aplicadas a todas as VMs que você está tentando proteger. Para identificar os valores corretos para CPU e memória, consulte o tamanho da função das VMs no IAAS e veja o número de núcleos e a memória atribuída.
	Nome de exibição | Após o failback para o local, você poderá renomear as máquinas virtuais da forma como elas aparecerão no inventário do vCenter. O nome padrão é o nome de host do computador da máquina virtual. Para identificar o nome da VM, consulte a lista de VMs no grupo Proteção.
	Configuração de NAT | Discutido com mais detalhes abaixo

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### Definir as configurações de NAT

1. Para habilitar a proteção das máquinas virtuais, é necessário estabelecer dois canais de comunicação. O primeiro canal está entre a máquina virtual e o Servidor de Processo. Esse canal coleta os dados da VM e os envia ao servidor de processo que envia os dados ao servidor de destino mestre. Se o servidor de processo e a máquina virtual que serão protegidos estiverem na mesma rede virtual do Azure, não será necessário usar as configurações de NAT. Caso contrário, especifique as configurações de NAT. Exiba o endereço IP público do servidor de processo no Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. O segundo canal está entre o servidor de processo e o servidor de destino mestre. A opção de usar ou não NAT dependerá da utilização de uma conexão com base em VPN ou de uma comunicação pela Internet. Não selecione NAT se você estiver usando VPN, mas apenas se você estiver usando uma conexão com a internet.

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Se você não tiver excluído as máquinas virtuais locais, conforme especificado, e se o armazenamento de dados para o qual você estiver realizando o failback ainda contiver o VMDK antigo, será necessário garantir que a VM do failback seja criada em um novo local. Para isso, selecione as configurações **Avançadas** e especifique uma pasta alternativa para restauração em **Configurações de Nome de Pasta**. Deixe as outras opções com as configurações padrão. Aplique as configurações de nome de pasta a todos os servidores.

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Execute a Verificação de Preparação para garantir que as máquinas virtuais estejam prontas para serem protegidas no local.

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Aguarde a conclusão. Se ela for bem-sucedida em todas as VMs, você poderá especificar um nome para o plano de proteção. Em seguida, clique em **Proteger** para começar.

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Você pode monitorar o progresso no vContinuum.

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Após a conclusão da etapa **Ativação do Plano de Proteção**, você pode monitorar a proteção da VM no portal de Recuperação de Site.

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Você pode ver o status exato clicando na VM e monitorando seu progresso.

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparar o plano de failback

Você pode preparar um plano de failback usando o vContinuum para que o aplicativo possa passar por failback para o site local a qualquer momento. Esses planos de recuperação são muito parecidos com os planos de recuperação na Recuperação de Site.

1.  Inicie o vContinuum e selecione **Gerenciar planos** > **Recuperar.** Você pode ver a lista de todos os planos que foram usados para execução de failover de VMs. Você pode usar os mesmos planos para a recuperação.

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Selecione o plano de proteção e todas as VMs que você deseja recuperar nele. Ao selecionar cada VM, você pode ver mais detalhes, incluindo o servidor ESX de destino e o disco da VM de origem. Clique em **Avançar** para começar o Assistente de Recuperação e selecione as VMs que você deseja recuperar.

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Você pode recuperar com base em várias opções, mas recomendamos o uso de **Marca mais Recente** e a seleção de **Aplicar a Todas as VMs** para garantir que os dados mais recentes da máquina virtual sejam usados.
4. Execute a **Verificação de Preparação.** Isso verificará se os parâmetros certos foram configurados para habilitar a recuperação da VM. Clique em **Avançar** se todas as verificações forem bem-sucedidas. Caso contrário, verifique o log e resolva os erros.

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Em **Configuração da VM**, certifique-se de que as configurações de recuperação estejam definidas corretamente. Se for necessário, altere as configurações da VM.

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Examine a lista de máquinas virtuais que serão recuperadas e especifique uma ordem de recuperação. Observe que as VMs estão listadas com o nome de host do computador. Talvez seja difícil mapear o nome de host do computador para a máquina virtual. Para mapear os nomes, acesse o **Painel** das máquinas virtuais no Azure e verifique o nome de host da VM.

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Especifique um nome de plano e selecione **Recuperar posteriormente**. Recomendamos a recuperação posterior, pois talvez a proteção inicial não esteja completa.
11. Clique em **Recuperar** para salvar o plano ou disparar a recuperação se você tiver selecionado a recuperação imediatada e não posterior. Você pode verificar o status da recuperação para ver se o plano foi salvo.

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Recuperar as máquinas virtuais

Após a criação do plano, você poderá recuperar as máquinas virtuais. Antes de fazer isso, verifique se as máquinas virtuais concluíram a sincronização. Se o status de replicação mostrar OK, a proteção terá sido concluída e o limite de RPO terá sido atingido. Você pode verificar a integridade nas propriedades da VM.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Desative as máquinas virtuais do Azure antes de iniciar a recuperação. Isso garante que não ocorre nenhuma partição e que os usuários acessem somente uma cópia do aplicativo.


1.  Inicie o plano salvo. No vContinuum, escolha **Monitorar** os planos. Isso lista todos os planos que foram executados.

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Selecione o plano em **Recuperação** e clique em **Iniciar**. Você pode monitorar a recuperação. Após a ativação das VMs, você pode conectar-se a elas no vCenter.

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## Proteger novamente no Azure após o failback

Após a conclusão do failback, convém provavelmente proteger mais uma vez as máquinas virtuais. Faça isso da seguinte forma:

1.  Verifique se as máquinas virtuais locais estão funcionando e se os aplicativos podem ser acessados.
2.  No portal do Azure Site Recovery, selecione as máquinas virtuais e as exclua. Selecione para desativar a proteção das máquinas virtuais. Isso garante que as VMs não estejam mais protegidas.
3.  No Azure, exclua as máquinas virtuais do Azure em estado de failover
4.  Exclua a máquina virtual antiga no vSpehere. Essas são as VMs das quais você realizou o failover anteriormente no Azure.
5.  No portal de Recuperação de Site, proteja as VMs que sofreram failover recentemente. Após a proteção, você poderá adicioná-las a um plano de recuperação.
 
## Próximas etapas



- [Leia sobre](site-recovery-vmware-to-azure-classic.md) como replicar máquinas virtuais VMware e servidores físicos no Azure usando a implantação avançada.

 

<!---HONumber=AcomDC_0629_2016-->
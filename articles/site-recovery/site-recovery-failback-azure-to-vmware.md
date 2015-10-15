<properties 
   pageTitle="Etapas para realização do failback do Azure para o VMware" 
   description="Este artigo descreve como o Azure Site Recovery e a ferramenta vContinuum podem ser usados para realizar o failback de uma máquina virtual para o VMware." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/05/2015"
   ms.author="ruturajd@microsoft.com"/>

# Etapas para realização do failback do Azure para o VMware

Este documento orientará você pelas etapas necessárias para realização do failback do Azure de volta para o site do VMware. Você já deve ter executado as etapas fornecidas no tutorial para [Proteção e recuperação do VMware para o Azure](site-recovery-vmware-to-azure.md).

Após um failover bem-sucedido para o Azure, as máquinas virtuais estarão disponíveis na guia de máquinas virtuais. Execute as etapas abaixo quando decidir realizar o failback.

Observe que quando você realiza o failback do Azure de volta para o site do VMware, a recuperação pode ocorrer apenas para uma máquina virtual. Mesmo se a fonte inicial no VMware fosse uma máquina física, o failover para o Azure seguido por um failback para o VMware a converterá em uma máquina virtual.

## Visão geral

1.  Instalar o servidor vContinuum no local

    a. Configure-o para apontar para o CS

2.  Implantar um SP no Azure

3.  Instalar um DM no local

4.  Etapas para proteger as VMs com failover de volta para o local

    a. Considerações sobre a configuração

5.  Monitoramento da proteção de VMs de volta para o local

6.  Realizar failover das VMs de volta para o local

Veja abaixo uma visão geral da configuração que executaremos com as etapas a seguir. Parte da configuração já foi concluída durante o failover.

-   As linhas azuis são as conexões usadas durante o failover.

-   As linhas vermelhas são as conexões usadas durante o failback.

-   As linhas com setas acessam a internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Instalar o vContinuum no local

A configuração do vContinuum estará disponível no [local de download](http://go.microsoft.com/fwlink/?linkid=526305). Além disso, instale o patch fornecido aqui em vContinuum - disponível em [local de download](http://go.microsoft.com/fwlink/?LinkID=533813).

1.  Inicie a configuração para começar a instalação do vContinuum. Clique em **Próximo**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  Especifique o endereço IP do servidor CX e a porta do servidor CX. Selecione HTTPS.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  Para descobrir o endereço IP CX vá para a implantação do CS no Azure e exiba o seu painel.

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  Para descobrir a porta pública de CX, acesse a guia pontos de extremidade na página da VM e identifique a porta pública de pontos de extremidade HTTPs.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  Especifique a senha do CS. Você deve ter anotado a senha durante o registro do CS. Também usou a senha durante as implantações do DM e do SP. Caso você não se lembre da senha, entre no servidor CS no Azure e localize a senha armazenada em C:\\Arquivos de Programas (x86) \\InMage Systems\\private\\connection.passphrase

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  Especifique o local de instalação do servidor vContinuum e comece a instalação.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  Após a conclusão da instalação, você pode iniciar o vContinuum e verificar o funcionamento. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Instalar o servidor SP no Azure

É necessário instalar um Servidor de Processo no Azure para que as VMs no Azure possam enviar os dados de volta para o DM local. Você precisa implantar o SP no Azure na mesma rede que o Servidor de Configuração.

1.  Na página **Servidores de configuração** no Azure, selecione para adicionar um novo servidor de processo.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especifique um nome de servidor de processo e digite um nome e uma senha para se conectar à máquina virtual como administrador. Selecione o servidor de configuração para o qual você está registrando o servidor de processo. Ele deve ser o mesmo servidor que você está usando para proteger e fazer fail over em suas máquinas virtuais. Especifique a rede do Azure na qual o servidor de processo deve ser implementado. Deve ser a mesma rede que o servidor de configuração. Especifique um endereço IP exclusivo nas opções de sub-rede e inicie a implantação.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


Ocorrerá o disparo de um trabalho de implantação do servidor de processo.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

Após a implantação do Servidor do processo no Azure, você poderá fazer logon no servidor usando as credenciais especificadas. Use as mesmas etapas usadas durante a direção de encaminhamento de proteção para registrar o SP.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

Os servidores registrados durante o failback não ficarão visíveis nas Propriedades da VM. Eles poderão ser vistos apenas na guia Servidores, no Servidor de configuração no qual foram registrados. Talvez demore cerca de 10 a 15 minutos para que o SP apareça listado em CS.

## Instalar um servidor de DM no local

Dependendo das máquinas virtuais no lado de origem, talvez seja necessário instalar um Linux ou um Servidor de destino mestre do Windows no local.

### Implantar um DM do Windows

Um DM do Windows já faz parte da instalação do vContinuum. Quando você instala o vContinuum, um DM também é implantado no mesmo computador e registrado no Servidor de configuração.

1.  Para iniciar a implantação, crie uma máquina vazia local no host ESX no qual você deseja recuperar as VMs do Auzre.

2.  Verifique se há pelo menos dois discos anexados à VM, um para o sistema operacional e o outro para a Unidade de Retenção.

3.  Instale o sistema operacional.

4.  Instale o vContinuum no servidor. Isso também concluirá a instalação do DM.

### Implantar um DM do Linux

1.  Para iniciar a implantação, crie uma máquina vazia local no host ESX no qual você deseja recuperar as VMs do Auzre.

2.  Verifique se há pelo menos dois discos anexados à VM, um para o sistema operacional e o outro para a Unidade de Retenção.

3.  Instale o sistema operacional Linux. O sistema Master Target (MT) da Linux não deve usar LVM para a raiz ou espaços de armazenamento de retenção. O DM do Linux é configurado por padrão para evitar a detecção de partições/discos LVM.
4.  As partições que você pode criar são

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Execute as etapas posteriores à instalação, exibidas abaixo, antes de iniciar a instalação do DM.


#### Etapas posteriores à instalação do SO:

Para obter a ID de SCSI de cada disco SCSI em uma máquina virtual Linux, você deve habilitar o parâmetro "disk.EnableUUID = TRUE". Para habilitar esse parâmetro, execute as etapas conforme indicado abaixo:

1. Desligue sua máquina virtual.
2. Clique com o botão direito do mouse na entrada da VM no painel esquerdo > **Editar Configurações.**
3. Clique na guia **Opções**. Selecione **Avançado > Item geral** à esquerda e clique em **Parâmetros de Configuração** à direita. A opção "Parâmetros de Configuração" estará em um estado desativado quando a máquina estiver em execução. Para ativar essa guia, desligue a máquina.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Verifique se já existe uma linha com **disk.EnableUUID**. Se já existir e se o valor estiver definido como Falso, substitua o valor por Verdadeiro (os valores Verdadeiro e Falso diferenciam letras maiúsculas de minúsculas). Se já existir e estiver definida como Verdadeiro, clique em Cancelar e teste o comando SCSI dentro do sistema operacional convidado após a inicialização. Se não existir, clique em **Adicionar Linha.**
5. Adicione disk.EnableUUID à coluna Nome. Defina seu valor como TRUE. Não adicione os valores acima com aspas duplas.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Baixar e instalar os pacotes adicionais

OBSERVAÇÃO: verifique se o sistema tem conexão com a Internet antes de baixar e instalar os pacotes adicionais.

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

O comando acima baixará os 15 pacotes mencionados abaixo do repositório CentOS 6.6 e os instalará.

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

OBSERVAÇÃO: se a máquina de origem usar o sistema de arquivos Reiser ou XFS para o dispositivo raiz ou de inicialização, será necessário baixar e instalar os pacotes a seguir no Destino mestre do Linux antes da proteção.

\# cd /usr/local

\# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget
<http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicar alterações de configuração personalizadas

Antes de aplicar alterações de configuração personalizadas, execute as Etapas Pós-instalação

Para aplicar alterações de configuração personalizadas, execute as etapas mencionadas abaixo:

1. Copie o binário Agente Unificado RHEL 6-64 para o SO recém-criado.

2. Execute o comando abaixo para descompactar o binário.

**tar -zxvf <Nome do arquivo>**

3. Execute o comando abaixo para conceder a permissão.

\# **chmod 755 ./ApplyCustomChanges.sh**

4. Execute o comando abaixo para executar o script.

**# ./ApplyCustomChanges.sh**

OBSERVAÇÃO: execute o script apenas uma vez no servidor. **REINICIALIZE** o servidor após a execução bem-sucedida do script acima.

### Iniciar a instalação do DM

[Baixe](http://go.microsoft.com/fwlink/?LinkID=529757) o arquivo de instalação do Servidor de destino mestre do Linux.

Copie o instalador baixado do Servidor de destino mestre do Linux para a máquina virtual de Destino mestre do Linux usando um utilitário de cliente sftp de sua escolha. Como alternativa, você pode fazer logon na máquina virtual de Destino mestre do Linux e use wget para baixar o pacote de instalação do link fornecido.

Faça logon na máquina virtual do Servidor de destino mestre do Linux usando um cliente ssh de sua escolha.

Se você estiver conectado à rede do Azure no qual implantou o Servidor de destino mestre do Linux por meio de uma conexão VPN, use o endereço IP interno do Servidor de destino mestre do Linux obtido no painel da máquina virtual e na porta 22 para se conectar ao Servidor de destino mestre do Linux usando Secure Shell.

Se você estiver se conectando ao Servidor de destino mestre do Linux em uma conexão de internet pública, use o endereço IP virtual público do Servidor de destino mestre do Linux (da página de painéis de máquinas virtuais) e o ponto de extremidade público criado para ssh a fim de fazer logon no Servidor de destino mestre do Linux.

Extraia os arquivos do arquivo tar do instalador do Servidor de destino mestre do Linux compactado via gzip executando

*“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”* do diretório no qual o instalador do Servidor de destino mestre do Linux foi copiado.

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

Se você extraiu os arquivos do instalador para um diretório diferente, altere o diretório para o diretório no qual o conteúdo do arquivo tar foi extraído. Desse caminho de diretório execute "sudo ./install.sh"

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

Quando receber uma solicitação para escolher a Função principal desse Agente selecione 2 (Destino Mestre)

Deixe as outras opções de instalação interativas com seus valores padrão.

Aguarde o restante da instalação e a exibição da Interface de Configuração do Host. O utilitário de Configuração do Host para o Servidor de destino mestre do Linux é um utilitário de linha de comando. Não redimensione a janela do utilitário cliente ssh. Use as teclas de direção para selecionar a opção Global e pressione Enter no teclado.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  No campo IP digite o endereço IP Interno do Servidor de configuração obtido na página de painéis de máquinas virtuais e pressione Enter.

2.  No campo Porta insira a 22 e pressione Enter.

3.  Deixe Usar https: como Sim. Pressione Enter mais uma vez.

4.  Insira a senha gerada no Servidor de Configuração. Se você estiver usando o cliente PuTTY de uma máquina com Windows para aplicar ssh na máquina virtual do Servidor de destino mestre do Linux, use Shift+Insert para colar o conteúdo da área de transferência. Copie a senha na área de transferência local usando Ctrl-C e use Shift+Insert para colá-la. Pressione Enter

5.  Use a tecla de direção para a direita para sair e pressione Enter.

Aguarde a conclusão da instalação

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se por algum motivo você não registrar o Servidor de destino mestre do Linux no Servidor de configuração, faça isso novamente executando o utilitário de configuração do host de /usr/local/ASR/Vx/bin/hostconfigcli (primeiro será necessário definir as permissões de acesso para esse diretório executando chmod como um superusuário)


#### Validar o registro do Servidor de destino mestre para o Servidor de Configuração.

Você pode validar se o Servidor de destino mestre foi registrado com êxito com o Servidor de Configuração visitando a página Detalhes do Servidor na página de Configuração do Servidor no cofre do Azure Site Recovery

Observação: depois de registrar o MT, você pode achar que ele apresenta um erro de configuração com as possíveis causas - A Máquina Virtual pode ter sido excluída do Azure ou os pontos de extremidade não estão configurados corretamente. Isso ocorre porque a configuração do MT é detectada pelos Pontos de Extremidade do Azure quando o MT é implantado no Azure. No entanto, isso não é verdadeiro para o MT local e o erro poderá ser ignorado. O failback não terá problemas devido a isso.


## Começar protegendo as máquinas virtuais de volta ao local

Antes de realizar o failback das VMs de volta ao local, primeiro você precisa proteger as máquinas virtuais que retornarão ao local. Execute as etapas a seguir para proteger as VMs de um aplicativo.

### Observação sobre a unidade temp

Quando uma VM está em estado de failover para o Azure, ocorre a adição de uma unidade temp para o arquivo de paginação. Essa unidade extra normalmente não é exigida por sua VM em estado de failover, pois ele já pode ter uma unidade dedicada ao arquivo de paginação.

Antes de começar a proteção inversa das máquinas virtuais, você precisa colocar a unidade em um estado offline para que não seja protegida.

Para fazer isto,

1.  Abra Gerenciamento do Computador (por meio da ferramenta administrativa do painel de controle ou clicando com o botão direito do mouse neste PC na janela do explorer e selecionando Gerenciar).

2.  Selecione Gerenciamento de Armazenamento para listar os discos online e conectados à máquina.

3.  Selecione o disco temporário conectado à máquina e escolha colocá-lo offline.

4.  Depois colocá-lo offline, você poderá continuar com a proteção da máquina virtual na direção inversa.

### Plano de proteção para VMs

No Portal do Azure, examine os estados da máquina virtual e certifique-se de que estejam em estado de failover.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

Observação: durante o failover do Azure de volta para o local, a VM do Azure é considerada semelhante a uma VM física. O failover para o local ocorre para uma máquina virtual.

1.  Iniciar o vContinuum em seu computador

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  Na configuração **Escolher Aplicativo**, selecione **P2V**

3.  Clique na opção **Nova Proteção** para começar

4.  Na nova janela exibida, você começará a proteger as máquinas virtuais de volta ao local.

    a. Selecione o **tipo de SO** de acordo com as VMs das quais deseja realizar o failback e selecione **Obter Detalhes**

    b. Nos **Detalhes do servidor primário**, você precisa identificar as máquinas virtuais que deseja proteger.

    c. As máquinas virtuais estão relacionadas de acordo com os Nomes de Host de seus Computadores e não como são visíveis no vCenter ou no Azure

    d. As máquinas virtuais são listadas sob o Nome de Host vCenter nos quais estavam, antes do failover.

    e. Após identificar as VMs que você deseja proteger, selecione-as individualmente.

5.  Quando você seleciona uma máquina virtual para proteção (e ela já está em estado de failover no Azure) uma janela pop-up é exibida com duas entradas para a máquina virtual. Isso ocorre porque o CS detectou duas instâncias das máquinas virtuais registradas. É necessário remover a entrada para a VM local para que você possa proteger a VM correta. Observe que você verá as entradas de acordo com o nome de host do computador. Para identificar a entrada da VM do Azure correta, faça logon na VM do Azure e acesse C:\\Arquivos de Programa (x86) \\Microsoft Azure Site Recovery\\Application Data\\etc. No arquivo drscout.conf , identifique a ID do Host. Na caixa de diálogo vContinuum, mantenha a entrada para a qual a hostID foi encontrada na VM. Exclua todas as outras entradas.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  Para selecionar a VM correta, você pode fazer referência ao endereço IP. O intervalo de endereço IP local será a VM local.
7.  Clique em **Remover** para excluir a entrada.

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  Vá para o vCenter e pare a máquina virtual no vCenter
9.  Em seguida você também pode excluir as máquinas virtuais locais
10.  Em seguida, você precisa especificar o servidor de DM local no qual você deseja proteger as VMs.
11.  Para fazer isso, conecte-se ao vCenter ao qual você deseja realizar o failback

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  Selecione o servidor de DM com base no host no qual você deseja recuperar as máquinas virtuais

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  Em seguida, forneça a opção de replicação para cada uma das máquinas virtuais

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  Para fazer isso, você precisa selecionar o lado de recuperação **Datastore** – este é o repositório de dados no qual as VMs serão recuperadas

Estas são as várias opções que você precisa fornecer de acordo com a VM

**Opção** | **Valor recomendado para a opção**
---|---
IP do Servidor de Processo | Selecione o SP implantado no Azure
Tamanho de retenção em MB| 
Valor de retenção | 1
Dias/Horas | Dias
Intervalo de Consistência | 1
Selecione o Armazenamento de Dados de Destino | O armazenamento de dados disponível no lado da recuperação. Esse armazenamento de dados deve ter espaço suficiente e também estar disponível para o host ESX no qual você deseja realizar a máquina virtual.
15.  Em seguida, você pode configurar as propriedades que serão adquiridas pela máquina virtual após o failover para o site local. Estas são as diferentes propriedades que você pode configurar

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**Propriedade** | **Como configurar**
---|---
Configuração de rede|Para cada NIC detectado, configure o endereço IP de failback da máquina virtual. Selecione a NIC e clique em **Alterar** para especificar os detalhes do endereço IP.
Configuração de hardware|Você pode especificar os valores CPU e Memória da VM. Essa configuração pode ser aplicada a todas as VMs que você está tentando proteger.
Nome de exibição|Para identificar os valores corretos para CPU e Memória, consulte o tamanho da função das VMs no IAAS e veja o número de núcleos e a Memória atribuída.
Nome de exibição|Após o failover de volta para o local, você pode optar por renomear as máquinas virtuais exibidas no inventário do vCenter. Observe que o valor padrão visto aqui é o nome de host do computador da máquina virtual. Para identificar o nome da VM, consulte a lista de VMs no grupo Proteção.
Configuração de NAT|Discutido com mais detalhes abaixo

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **Opções de configuração de NAT do SP**
>
> Para habilitar a proteção das máquinas virtuais, é necessário estabelecer dois canais de comunicação.
>
> O primeiro canal está entre a máquina virtual e o Servidor de Processo. Esse canal coleta os dados da VM e os envia para o SP Em seguida, o SP envia esses dados ao Destino mestre. Se o servidor de processo e a máquina virtual que serão protegidos estiverem no mesmo vNet do Azure, não será necessário usar as configurações de NAT. Se o SP e a máquina virtual que serão protegidos estiverem em dois vNet diferentes, será necessário especificar as configurações de NAT para o SP e marcar a primeira opção.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> Para identificar o IP Público do Servidor de Processo, acesse a implantação do SP no Azure e verifique seu endereço IP Público.
>
> O segundo canal está entre o Servidor de Processo e o Destino mestre. A opção de usar ou não o NAT dependerá da utilização de uma conexão com base em VPN entre o DM e o SP, ou de uma proteção pela Internet. Se o SP se comunica com o DM por meio de uma VPN, não selecione essa opção. Se o Destino mestre precisa se comunicar com o Servidor de Processo pela internet, especifique as configurações de NAT para o SP.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> Para identificar o IP Público do Servidor de Processo, acesse a implantação do SP no Azure e verifique seu endereço IP Público.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  Se você não tiver excluído as máquinas virtuais locais, conforme especificado na etapa 5.d, e se o armazenamento de dados para o qual você está realizando o failback, conforme selecionado na etapa 7.a, ainda contiver o VMDK antigo, também será necessário garantir que a VM do failback seja criada em um novo local. Para isso, você pode selecionar as Configurações Avançadas e especificar uma pasta alternativa para restauração na seção **Configurações de Nome de Pasta** das Configurações Avançadas.

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

As outras opções nas Configurações Avançadas podem ser deixadas com os valores padrão. Aplique as configurações de nome de pasta a todos os servidores.

2.  Em seguida, vá para o estágio final da Proteção. Aqui, será necessário executar uma Verificação de Preparação para garantir que as máquinas virtuais estejam prontas para serem protegidas no local.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  Agora a Proteção vai começar.

    a. Você pode ver o progresso da proteção no vContinuum

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

Também é possível ver o status exato dos pares clicando na máquina virtual e monitorando seu progresso na seção de replicação de volume.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparar o plano de Failback

Você pode preparar um plano de failback usando o vContinuum para que o aplicativo possa passar por failback para o local a qualquer momento. Esses planos de recuperação são muito semelhantes aos Planos de Recuperação de ASR.

1.  Inicie o vContinuum e selecione a opção para **Gerenciar planos**.

2.  Use as sub-opções e selecione **Recuperar.**

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  Você pode ver a lista de todos os Planos usados para proteger as máquinas virtuais. Esses são os mesmos planos que você pode usar para a recuperação.

4.  Selecione o Plano de Proteção e todas as VMs que você deseja recuperar dentro dele.

    a. Na seleção de cada VM, você pode ver mais detalhes sobre a VM de origem, sobre o servidor ESX de destino no qual a VM será recuperada e sobre o disco VM de origem

5.  Clique em Avançar para iniciar o Assistente de **Recuperação**

6.  Selecione as máquinas virtuais que você deseja recuperar

    a. Consulte a lista de todas as máquinas virtuais que podem ser recuperadas

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  Execute a **Verificação de Preparação.** Isso informará se os parâmetros certos foram configurados a fim de permitir a recuperação de marca mais recente da máquina virtual. Se todas as verificações forem bem-sucedidas cliquem em Avançar, caso contrário examine o log e resolva os erros.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Na etapa de Configuração da VM do assistente, certifique-se de que as configurações de recuperação estejam definidas corretamente. Caso as configurações de VM sejam diferentes das necessárias, altere-as. Como já concluímos essa ação durante a proteção, ignore isso.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  Finalmente, examine a lista de máquinas virtuais que serão recuperadas.

    a. Especifica uma ordem de recuperação das máquinas virtuais.

Observação: as máquinas virtuais estão listadas com o nome de host do computador. Talvez seja difícil mapear o nome de host do computador da máquina virtual. Para mapear os nomes, acesse o painel de máquinas virtuais no Azure IAAS e examine o nome de host da máquina virtual.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  Forneça o **nome do plano de recuperação** e selecione **Recuperar posteriormente** nas **Opções de recuperação.**

    a. Caso queira recuperar imediatamente, você poderá optar por **Recuperar agora** nas **Opções de recuperação**.

    b. Recomendamos Recuperar posteriormente, pois talvez a replicação inicial da proteção não tenha sido concluída

    c. Finalmente, clique no botão **Recuperar** para salvar o plano ou para disparar a recuperação com base em suas **Opções de recuperação**.

11.  Você pode ver o Status da recuperação e verificar se o plano foi salvo com êxito.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  Se você optou por recuperar posteriormente, receberá a confirmação de que o plano foi criado e que você poderá recuperar mais tarde.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Clique em Máquinas Virtuais

Após a criação do plano, você poderá optar por recuperar as máquinas virtuais. Como pré-requisito, é necessário ter certeza sobre a conclusão da sincronização das máquinas virtuais.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Se o Status de Replicação for OK, a proteção terá sido concluída e o limite de RPO terá sido atingido. Para confirmar a integridade do par de replicação, acesse as propriedades da máquina virtual e verifique a integridade da replicação.

**Desative as máquinas virtuais do Azure antes de iniciar a recuperação. Isso garantirá a não existência de partições e o atendimento de seus clientes finais por uma cópia do aplicativo. Continue com as etapas abaixo somente após desativar as VMs do Azure.**

Para começar a recuperação das máquinas virtuais de volta para o local, é necessário iniciar o plano salvo.

1.  No vContinuum selecione **Monitorar** os planos.

2.  O assistente listará os planos que foram executados até o momento.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  Selecione o nó **Recuperação** e selecione o plano que você deseja recuperar.

    a. Ele informará que o plano ainda não foi iniciado.

4.  Clique em **Iniciar** para iniciar a recuperação.

5.  Você pode monitorar a recuperação das máquinas virtuais


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. Após a ativação das VMs, conecte-se às máquinas virtuais em seu vCenter.

## Proteger novamente para o Azure após o failback

Após a conclusão do failback, convém proteger mais uma vez as máquinas virtuais. As etapas abaixo ajudarão você a configurar novamente a proteção

1.  Verifique se as máquinas virtuais locais estão funcionando e se o aplicativo é capaz de alcançar seus clientes finais.

2.  No portal do Azure Site Recovery, selecione as máquinas virtuais e as exclua.

    a. Selecione para desativar a proteção das máquinas virtuais. Isso garantirá que as VMs não estão mais protegidas.

3.  Acesse as máquinas virtuais do Azure IAAS e exclua as VMs em estado de failover.

4.  Exclua as VMs antigas no vSpehere – são as VMs das quais você realizou o failover anteriormente no Azure.

5.  No portal ASR, selecione para proteger as máquinas virtuais que sofreram failover recentemente.

6.  Assim que as VMs estiverem protegidas, adicione-as a um plano de recuperação e receba uma proteção contínua.


 

<!----HONumber=Oct15_HO1-->
<properties
   pageTitle="Implantar a StorSimple Virtual Array - Provisionar no VMware"
   description="Esse segundo tutorial sobre a série de implantação da StorSimple Virtual Array envolve o provisionamento de um dispositivo virtual no VMware."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="alkohli"/>


# Implantar a StorSimple Virtual Array – Provisionar uma Matriz Virtual no VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## Visão geral 
Este tutorial de provisionamento se aplica aos StorSimple Virtual Arrays (também conhecidos como dispositivos virtuais locais StorSimple ou dispositivos virtuais StorSimple) que executam a versão GA (disponibilidade geral) de março de 2016. Este tutorial descreve como provisionar e conectar-se a uma StorSimple Virtual Array em um sistema de host que executa VMware ESXi 5.5 e superior. Este artigo se aplica à implantação de Matrizes virtuais do StorSimple no portal clássico do Azure, bem como na Nuvem de governo do Microsoft Azure.

Você precisará de privilégios de administrador para provisionar e conectar-se a um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos.


## Pré-requisitos de provisionamento

Aqui você encontrará os pré-requisitos para provisionar um dispositivo virtual em um sistema de host que executa o VMware ESXi 5.5 e posterior.

### Para o serviço StorSimple Manager

Antes de começar, verifique se:

-   Você concluiu todas as etapas em [Preparar o portal para o StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md).

-   Você baixou a imagem do dispositivo virtual para o VMware no portal do Azure. Para obter mais informações, veja [Etapa 3: Baixar a imagem do dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Para o dispositivo virtual StorSimple 

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host que executa o Hyper-V (2008 R2 ou superior) que pode ser usado para provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

	-   Um mínimo de quatro núcleos.

	-   Pelo menos 8 GB de RAM.

	-   Uma interface de rede.

	-   Um disco virtual de 500 GB para dados do sistema.

### Para a rede no datacenter 

Antes de começar, verifique se:

-   Você revisou os requisitos de rede para implantar um dispositivo virtual StorSimple e configurou a rede de datacenter de acordo com os requisitos. Para obter mais informações, consulte o Guia de Requisitos do Sistema da Matriz Virtual Microsoft Azure StorSimple.

## Provisionamento passo a passo 

Para provisionar e se conectar a um dispositivo virtual, você precisará executar as seguintes etapas:

1.  Verificar se o sistema de host tem recursos suficientes para cumprir os requisitos mínimos de dispositivos virtuais.

2.  Provisionar um dispositivo virtual no seu hipervisor.

3.  Iniciar o dispositivo virtual e obter o endereço IP.

## Etapa 1: verificar se o sistema de host cumpre os requisitos mínimos de dispositivo virtual

Para criar um dispositivo virtual, você precisará de:

-   Acesso a um sistema host executando o VMware ESXi Server 5.5 e superior.

-   Cliente VMware vSphere em seu sistema para gerenciar o host ESXi.

	-   Um mínimo de quatro núcleos.

	-   Pelo menos 8 GB de RAM.

	-   Uma interface de rede conectada à rede capaz de rotear o tráfego para a Internet. A largura de banda de Internet mínima deve ser de 5 Mbps para permitir o funcionamento ideal do dispositivo.

	-   Um disco virtual de 500 GB para dados.

## Etapa 2: provisionar um dispositivo virtual no hipervisor

Execute as etapas a seguir para provisionar um dispositivo virtual no seu hipervisor.

1.  Copie a imagem do dispositivo virtual no seu sistema. Esta é a imagem que você baixou por meio do portal clássico do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.

2.  Faça logon no servidor ESXi usando o cliente vSphere. Você precisará ter privilégios de administrador para criar uma máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  No cliente vSphere, na seção de estoque no painel esquerdo, selecione o servidor ESXi.

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Primeiro, você carregará o VMDK para o servidor ESXi. Navegue até a guia **Configuração** no painel direito. Em **Hardware**, selecione **Armazenamento**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  No painel direito, em **Armazenamentos de Dados**, selecione o armazenamento de dados no qual deseja carregar o VMDK. O repositório de dados deve ter espaço livre suficiente para os discos de sistema operacional e de dados.

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Clique com o botão direito do mouse e selecione **Procurar no Armazenamento de Dados**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Uma janela **Navegador de Armazenamento de Dados** será exibida.

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Na barra de ferramentas, clique no ícone ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) para criar uma nova pasta. Especifique o nome da pasta e anote-o. Você usará posteriormente este nome de pasta ao criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  A nova pasta aparecerá no painel à esquerda do **Navegador do Armazenamento de Dados**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Clique no ícone Carregar ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) e selecione **Carregar Arquivo**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Agora você deve procurar e apontar o VMDK que você baixou.

	![](./media/storsimple-ova-deploy2-provision-vmware/image12.png)

1.  Clique em **Abrir**. Agora, isso iniciará o upload do arquivo VMDK para o repositório de dados especificado.

	![](./media/storsimple-ova-deploy2-provision-vmware/image13.png)

1.  Pode levar vários minutos para que o arquivo seja carregado. Quando o upload for concluído, você verá o arquivo no repositório de dados, na pasta que você criou.

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

1.  Retorne à janela do cliente vSphere. Com o servidor ESXi selecionado, clique com o botão direito do mouse e selecione **Nova Máquina Virtual**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Uma janela **Criar Nova Máquina Virtual** será exibida. Na página **Configuração**, selecione a opção **Personalizada**. Clique em **Próximo**.![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Na página **Nome e Local**, especifique o nome da máquina virtual. Esse nome deve corresponder ao nome da pasta (melhor prática recomendada) que você especificou anteriormente na Etapa 8.

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Na página **Armazenamento** selecione um armazenamento de dados que deseja usar para provisionar sua VM.

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Na página **Versão da Máquina Virtual**, selecione **Versão da Máquina Virtual: 8**. Observe que essa é a única opção com suporte para esta versão.

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Na página **Sistema Operacional Convidado**, selecione o **Sistema Operacional Convidado** como **Windows**. Para **Versão**, na lista suspensa, selecione **Microsoft Windows Server 2012 (64 bits)**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Na página **CPUs**, ajuste o **Número de soquetes virtuais** e o **Número de núcleos por soquete virtual** para que o **Número total de núcleos** seja 4 (ou mais). Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Na página **Memória**, especifique 8 GB (ou mais) de RAM. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Na página **Rede** especifique o número das interfaces de rede. O requisito mínimo é uma interface de rede.

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Na página **Controlador SCSI**, aceite o padrão **Controlador SAS de Lógica LSI**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Na página **Selecionar um Disco**, escolha **Usar um disco virtual existente**. Clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Na página **Selecionar Disco Existente**, em **Caminho de Arquivo de Disco**, clique em **Procurar**. Isso abrirá um diálogo **Procurar nos Armazenamentos de Dados**. Navegue até o local em que você carregou o VMDK. Selecione o arquivo e clique em **OK**. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Na página **Opções Avançadas**, aceite o padrão e clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Na página **Pronto para Concluir**, examine todas as configurações associadas à nova máquina virtual. Verifique **Editar as configurações de máquina virtual antes da conclusão**. Clique em **Continuar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Na página **Propriedades de Máquinas Virtuais**, na guia **Hardware**, localize o hardware de dispositivo. Selecione **Novo Disco Rígido**. Clique em **Adicionar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Isso abrirá a janela **Adicionar Hardware**. Na página **Tipo de Dispositivo**, em **Escolha o tipo de dispositivo que deseja adicionar**, selecione **Disco Rígido** e clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Na página **Selecionar um Disco**, escolha **Criar um novo disco virtual**. Clique em **Avançar**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Na página **Criar um Disco**, altere o **Tamanho do Disco** para 500 GB (ou mais). Em **Provisionamento de Disco**, selecione **Provisionamento Dinâmico**. Clique em **Próximo**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Na página **Opções Avançadas**, aceite o padrão.

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Na página **Pronto para Concluir**, examine as opções de disco. Clique em **Concluir**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Agora, você retornará à página Propriedades de Máquina Virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Com a máquina virtual selecionada no painel direito, navegue até a guia **Resumo**. Examine as configurações para sua máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Sua máquina virtual está agora provisionada. A próxima etapa é ligar esse computador e obter o endereço IP.

## Etapa 3: iniciar o dispositivo virtual e obter o IP

Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual. No vSphere Configuration Manager, no painel esquerdo, selecione seu dispositivo e clique com o botão direito do mouse para abrir o menu de contexto. Selecione **Ligar/Desligar** e, em seguida, selecione **Ligar**. Isso deve ligar sua máquina virtual. É possível exibir o status no painel inferior **Tarefas Recentes** do cliente vSphere.

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  As tarefas de configuração levarão alguns minutos para serem concluídas. Quando o dispositivo estiver em execução, navegue até a guia **Console**. Envie Ctrl+Alt+Delete para fazer logon no dispositivo. Como alternativa, você pode apontar o cursor na janela do console e pressionar Ctrl+Alt+Insert. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Password1*.

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Por motivos de segurança, a senha do administrador do dispositivo expira no primeiro logon. Será solicitado que você altere a senha.

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Insira uma senha que contenha pelo menos oito caracteres. A senha deve conter três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais. Insira a senha novamente para confirmá-la. Você será notificado de que a senha foi alterada.

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Depois que a senha for alterada com êxito, o dispositivo virtual poderá ser reiniciado. Aguarde a conclusão da reinicialização. O console do Windows PowerShell do dispositivo poderá ser exibido junto com uma barra de progresso.

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  As etapas 6 a 8 se aplicam somente ao iniciar em um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você verá a tela a seguir.

	![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

	Agora, você precisará configurar a rede.

1.  Use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela será `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Um exemplo é mostrado abaixo:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e o registro.

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Opcional) Execute esta etapa somente se você estiver implantando seu dispositivo na Nuvem do governo. Agora, você habilitará o modo FIPS (Federal Information Processing Standard) dos Estados Unidos em seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais.
	1. Para habilitar o modo FIPS, execute o seguinte cmdlet:
		
		`Enter-HcsFIPSMode`

	2. Reinicialize o dispositivo após ter habilitado o modo FIPS para que as validações criptográficas tenham efeito.

		> [AZURE.NOTE] Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não tem suporte.


Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa (mostrado abaixo). Será necessário modificar a configuração do dispositivo para que ele tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração na [Etapa 1: Verificar se o sistema de host atende aos requisitos mínimos do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Caso observe algum outro erro durante a configuração inicial usando a interface do usuário da Web local, veja os seguintes fluxos de trabalho em [Gerenciar o StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).

-   Execute testes de diagnóstico para [solucionar problemas na configuração da interface do usuário da Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Gere um pacote de log e exiba os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## Próximas etapas

-   [Configurar sua StorSimple Virtual Array como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar sua StorSimple Virtual Array como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0316_2016-->
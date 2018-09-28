---
title: Tutorial sobre como provisionar o Gateway do Azure Data Box no Hyper-V | Microsoft Docs
description: O segundo tutorial de implantação do Gateway do Azure Data Box envolve o provisionamento de um dispositivo virtual no Hyper-V.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: bf744d2aaab168b8ce918f7b776d8855cdc5ad16
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975234"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v-preview"></a>Tutorial: Provisionar o Gateway do Azure Data Box no Hyper-V (Versão prévia)

## <a name="overview"></a>Visão geral

Este tutorial descreve como provisionar um Gateway do Data Box em um sistema host que executa o Hyper-V no Windows Server 2016, Windows Server 2012 R2 ou Windows Server 2012. 

Você precisa de privilégios de administrador para provisionar e configurar um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> - O Gateway do Data Box está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução.

## <a name="prerequisites"></a>Pré-requisitos

Veja a seguir os pré-requisitos para provisionar um dispositivo virtual em um sistema host que executa o Hyper-V no Windows Server 2016 ou no Windows Server 2012 R2.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso de Gateway do Data Box

Antes de começar, verifique se:

* Você concluiu todas as etapas em [Preparar o portal para o Gateway do Data Box](data-box-gateway-deploy-prep.md).
* Você fez o download da imagem do dispositivo virtual para Hyper-V do portal do Azure, conforme descrito em [Preparar o portal para o Gateway do Data Box](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.
 
### <a name="for-the-data-box-gateway-virtual-device"></a>Para o dispositivo virtual do Gateway do Data Box

Antes de implantar um dispositivo, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V no Windows Server 2012 R2 ou superior que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. 
  * Uma interface de rede.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Revise os requisitos de rede para implantar um Gateway do Data Box e configurar a rede de datacenter de acordo com os requisitos. Saiba mais em [Requisitos de rede do Gateway do Data Box](data-box-gateway-system-requirements.md#networking-requirements).
- Verifique se a largura de banda de Internet mínima é de 20 Mbps, para permitir o funcionamento ideal do dispositivo.


## <a name="check-the-host-system"></a>Verificar o sistema host

Para criar um dispositivo virtual, você precisa de:

* A função do Hyper-V instalada no Windows Server 2016, Windows Server 2012 R2 ou Windows Server 2012.
* Microsoft Hyper-V Manager em um cliente Microsoft Windows conectado ao host.
* Verifique se o hardware subjacente (sistema host) em que está criando o dispositivo virtual possa dedicar os recursos a seguir ao seu dispositivo virtual:

    * Um mínimo de quatro núcleos.
    * Pelo menos 8 GB de RAM.
    * Uma interface de rede conectada à rede capaz de rotear o tráfego para a Internet. .
    * Um disco de SO de 250 GB.
    * Um disco virtual de 2 TB de dados do sistema.

## <a name="provision-a-virtual-device-in-hypervisor"></a>Provisionar um dispositivo virtual no hipervisor

Execute as etapas a seguir para provisionar um dispositivo no seu hipervisor.

1. No seu host do Windows Server, copie a imagem do dispositivo virtual para uma unidade local. Você baixou essa imagem VHDX por meio do portal do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.
2. Abra o **Gerenciador do Servidor**. No canto superior direito, clique em **Ferramentas** e selecione **Gerenciador do Hyper-V**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image1.png)  
  
3. No **Gerenciador do Hyper-V**, no painel de escopo, clique com o botão direito do mouse no nó do sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Na página **Antes de começar** do Assistente de Nova Máquina Virtual, clique em **Avançar**.
5. Na página **Especificar nome e localização**, forneça um **Nome** para seu dispositivo virtual. Clique em **Próximo**.
   
   > [!IMPORTANT]
   > Nesta versão, você só pode usar letras maiúsculas para o nome do seu dispositivo virtual.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Na página **Especificar geração**, escolha **Geração 2** para o tipo de imagem do dispositivo vhdx e clique em **Avançar**.    

   ![](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Na página **Atribuir memória**, especifique uma **Memória de inicialização** de pelo menos **8192 MB**, não habilite a memória dinâmica e clique em **Avançar**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Na página **Configurar rede**, especifique o comutador virtual que está conectado à Internet e clique em **Avançar**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Na página **Conectar disco rígido virtual**, escolha **Usar um disco rígido virtual existente**, especifique a localização da imagem do dispositivo virtual e clique em **Avançar**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Examine o **Resumo** e clique em **Concluir** para criar a máquina virtual.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Para cumprir os requisitos mínimos, você precisará de quatro núcleos. Para adicionar quatro processadores virtuais, selecione o sistema de host na janela **Gerenciador Hyper-V**. No painel à direita da lista de **máquinas virtuais**, localize a máquina virtual que você acabou de criar. Selecione e clique com o botão direito do mouse no nome do computador e selecione **Configurações**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Na página **Configurações**, no painel à esquerda, clique em **Processador**. No painel à direita, defina o **número de processadores virtuais** como quatro (ou mais). Clique em **Aplicar**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Para cumprir os requisitos mínimos, você também precisa adicionar um disco de dados virtual de 2 TB. Na página de **Configurações** :

    1. No painel à esquerda, selecione **Controlador SCSI**.
    2. No painel à direita, selecione **Disco Rígido** e clique em **Adicionar**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Na página **Disco rígido**, selecione a opção **Disco rígido virtual** e clique em **Novo**. O **Assistente novo disco rígido Virtual** é iniciado.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
1. Na página **Antes de começar** do Assistente de Novo Disco Rígido Virtual, clique em **Avançar**.
2. Na página **Escolher Formato de Disco**, aceite a opção padrão de formato **VHDX**. Clique em **Próximo**.
   
17. Na página **Escolher Tipo de Disco**, defina o tipo de disco rígido virtual como **Expansão dinâmica** (recomendado). Um disco de **Tamanho fixo** funcionaria, mas talvez você precise aguardar bastante. É recomendável que você não use a opção **Diferenciar** . Clique em **Próximo**. 

    ![](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Na página **Especificar Nome e Localização**, forneça um **nome** e também uma **localização** (é possível navegar até um) para o disco de dados. Clique em **Próximo**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Na página **Configurar Disco**, selecione a opção **Criar um novo disco de rígido virtual em branco** e especifique o tamanho como **2 TB** (ou mais). Embora 2 TB seja o requisito mínimo, você sempre poderá provisionar um disco maior. Observe que não é possível reduzir o disco após o provisionamento.  No entanto, você pode expandir o disco adicionando um disco de dados. Clique em **Próximo**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Na página **Resumo**, examine os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Concluir** para criar o disco. O assistente é fechado e um disco rígido virtual é adicionado à sua máquina.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Retorne para a página **Configurações** . Clique em **OK** para fechar a página **Configurações** e retornar à janela do Gerenciador do Hyper-V.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

## <a name="start-the-virtual-device-and-get-the-ip"></a>Iniciar o dispositivo virtual e obter o IP
Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Depois que o dispositivo estiver em execução, selecione-o, clique com o botão direito do mouse e selecione **Conectar**.

3. Você terá que esperar de 10 a 15 minutos para que o dispositivo esteja pronto. Será exibida uma mensagem de status no console para indicar o andamento. Depois que o dispositivo estiver pronto, vá para **Ação**. Pressione `Ctrl + Alt + Delete` para entrar no dispositivo virtual. O usuário padrão é *EdgeUser* e a senha padrão é *Password1*.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image21.png)
4. Por motivos de segurança, a senha do administrador do dispositivo expira no primeiro logon. Você será solicitado a alterar a senha.

   Insira uma senha que contenha pelo menos oito caracteres. A senha deve cumprir pelo menos três dos quatro requisitos a seguir: caracteres maiúsculos, minúsculos, numéricos e especiais. Insira a senha novamente para confirmá-la. Você é notificado de que a senha foi alterada.
   
5. Depois que a senha for alterada com êxito, o dispositivo virtual poderá ser reiniciado. Aguarde até o dispositivo iniciar.  O console do Windows PowerShell do dispositivo é exibido junto com uma barra de progresso.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image22.png)

6. As etapas 6 a 8 se aplicam somente na inicialização de um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você uma mensagem sobre isso.
    
7. Para configurar a rede, use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `DATA1`.

8. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Veja os exemplos a seguir:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`
    
9. Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Use esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e ativação.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image23.png)
      

Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa. Modifique a configuração do dispositivo para que o computador tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração na [Etapa 1: Verificar se o sistema de host atende aos requisitos mínimos do dispositivo virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

<!--If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Vá para o próximo tutorial para aprender a conectar, configurar e ativar suas redes virtuais.

> [!div class="nextstepaction"]
> [Conectar-se e configurar seu Gateway do Data Box](./data-box-gateway-deploy-connect-setup-activate.md)



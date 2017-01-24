---
title: "Configuração de servidor iSCSI de StorSimple Virtual Array | Microsoft Docs"
description: "Descreve como realizar a configuração inicial, registrar seu servidor iSCSI do StorSimple e concluir a configuração do dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 1a854bb3-3068-4db9-87b7-9f3692ab64e4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 715720d22b58ddd3d0e5042de151219e49549c5e


---
# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Implantar o StorSimple Virtual Array – Configurar seu dispositivo virtual como um servidor iSCSI
![fluxo do processo de instalação iscsi](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Visão geral
Este tutorial de implantação se aplica ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual local StorSimple ou dispositivo virtual StorSimple) que executa a versão GA (disponibilidade geral) de março de 2016. Este tutorial descreve como executar a instalação inicial, registrar o servidor iSCSI do StorSimple, concluir a configuração do dispositivo e, em seguida, criar, montar, inicializar e formatar volumes em seu servidor iSCSI do dispositivo virtual StorSimple. As informações de implantação do StorSimple publicadas neste artigo se aplicam somente à StorSimple Virtual Array. 

Os procedimentos descritos aqui levam um intervalo de aproximadamente 30 minutos a 1 hora para concluir. As informações publicadas nesse artigo aplicam-se somente a Matrizes Virtuais StorSimple.

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração
Antes de configurar e configurar o dispositivo virtual StorSimple, certifique-se de que:

* Você provisionou um dispositivo virtual e se conectou a ele, conforme descrito em [Implantar o StorSimple Virtual Array – Provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar o StorSimple Virtual Array – Provisionar uma matriz virtual no VMware](storsimple-ova-deploy2-provision-vmware.md).
* Você tem a chave de registro do serviço StorSimple Manager que você criou para gerenciar dispositivos virtuais StorSimple. Para obter mais informações, veja **Etapa 2: Obter a chave de registro do serviço** em [Implantar o StorSimple Virtual Array – Preparar o portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se esse for o segundo dispositivo virtual ou dispositivo virtual subsequente que você está registrando com um serviço StorSimple Manager existente, você deve ter a chave de criptografia de dados do serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Caso tenha perdido essa chave, veja **Obter a chave de criptografia de dados de serviço** em [Usar a interface do usuário da Web para administrar o StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuração passo a passo
Use as instruções passo a passo a seguir para preparar e configurar seu dispositivo virtual StorSimple:

* [Etapa 1: concluir a configuração de interface do usuário da Web local e registrar seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Etapa 2: concluir a configuração obrigatória do dispositivo](#step-2-complete-the-required-device-setup)
* [Etapa 3: adicionar um volume](#step-3-add-a-volume)
* [Etapa 4: montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Etapa 1: concluir a configuração de interface do usuário da Web local e registrar seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registrar o dispositivo
1. Abra uma janela do navegador e conecte-se à interface do usuário da Web, digitando:
   
    `https://<ip-address of network interface>`
   
    Use a URL de conexão observada na etapa anterior. Você verá um erro informando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**.
   
    ![erro de certificado de segurança](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)
2. Entre na interface do usuário da Web de seu dispositivo virtual como **StorSimpleAdmin**. Insira a senha do administrador do dispositivo alterado na Etapa 3: iniciar o dispositivo virtual em [Implantar o StorSimple Virtual Array – Provisionar um dispositivo virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar o StorSimple Virtual Array – Provisionar um dispositivo virtual no VMware](storsimple-ova-deploy2-provision-vmware.md).
   
    ![Página de entrada](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)
3. Você será levado à página **Inicial** . Esta página descreve as várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço StorSimple Manager. Observe que **Configurações de rede**, **Configurações de proxy Web** e **Configurações de hora** são opcionais. As únicas configurações obrigatórias são as **Configurações do dispositivo** e **Configurações de nuvem**.
   
    ![Página inicial](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)
4. Na página **Configurações de rede**, em **Interfaces de rede**, DATA 0 será configurado automaticamente para você. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Assim, um endereço IP, a sub-rede e gateway serão atribuídos automaticamente (tanto para IPv4 quanto para IPv6).
   
    Já que planeja implantar o dispositivo como um servidor iSCSI (para provisionar o armazenamento em bloco), recomendamos desabilitar a opção **Obter endereço IP automaticamente** e configurar endereços IP estáticos.
   
    ![Página de configurações de rede](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)
   
    Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui. Observe que você pode configurar a interface de rede apenas como IPv4 ou como IPv4 e IPv6. Não há suporte para configurações somente IPv6.
5. Os servidores DNS são necessários porque eles são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento de nuvem, ou então para resolver seu dispositivo por nome, se ele estiver configurado como um servidor de arquivos. Na página **Configurações de rede**, em **Servidores DNS**:
   
   1. Um servidor DNS primário e um secundário serão configurados automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.
   2. Clique em **Aplicar**. Isso aplicará e validará as configurações de rede.
6. Na página **Configurações do dispositivo** :
   
   1. Atribua um **Nome** exclusivo ao seu dispositivo. Esse nome pode ter de 1 a 15 caracteres e pode conter letras, números e hifens.
   2. Clique no ícone do **Servidor iSCSI** ![ícone do Servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para o **Tipo** de dispositivo que você está criando. Um servidor iSCSI permitirá a você provisionar armazenamento em bloco.
   3. Especifique se deseja que este dispositivo seja ingressado no domínio. Se o dispositivo é um servidor iSCSI, ingressar no domínio é opcional. Se optar por não ingressar o servidor iSCSI em um domínio, clique em **Aplicar**, aguarde até que as configurações sejam aplicadas e vá para a próxima etapa.
      
       Se você quiser adicionar o dispositivo a um domínio. Insira um **Nome de domínio** e clique em **Aplicar**.
      
      > [!NOTE]
      > Se estiver ingressando em seu servidor iSCSI em um domínio, certifique-se de que sua matriz virtual esteja em sua própria unidade organizacional (UO) do Microsoft Azure Active Directory e que não haja objetos de política de grupo (GPO) aplicados a ele.
      > 
      > 
   4. Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.
      
       ![credenciais](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Isso aplicará e validará as configurações do dispositivo.
7. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui.
   
    ![configurar o proxy Web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)
   
    Na página **Proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http://endereço IP do host * or *FDQN: Número de porta*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **Autenticação** como **Básica** ou **Nenhuma**.
   3. Se estiver usando a autenticação, também será necessário fornecer um **Nome de Usuário** e uma **Senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy Web definidas.
8. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
   
    ![Configurações de hora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)
   
    Na página **Configurações de hora** :
   
   1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica em que o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.
   2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.
   3. Opcionalmente, especifique um **Servidor NTP secundário** para o dispositivo.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de hora definidas.
9. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração de dispositivo local e, em seguida, registrará o dispositivo com o serviço StorSimple Manager.
   
   1. Insira a **Chave de registro do serviço** obtida na **Etapa 2: Obter a chave de registro do serviço** em [Implantar o StorSimple Virtual Array – Preparar o portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se este não for o primeiro dispositivo que você está registrando nesse serviço, será necessário fornecer a **Chave de criptografia de dados do serviço**. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço StorSimple Manager. Para obter mais informações, veja [Obter a chave de criptografia de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) na interface do usuário da Web local.
   3. Clique em **Registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar de 2 a 3 minutos até que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado à página de entrada.
      
      ![Registrar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)
10. Retorne ao portal clássico do Azure. Na página **Dispositivos** , verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve ser **Ativo**.
    
    ![Página Dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Etapa 2: concluir a configuração obrigatória do dispositivo
Para concluir a configuração de dispositivo do seu dispositivo StorSimple, é necessário:

* Selecionar uma conta de armazenamento para associar ao seu dispositivo.
* Escolha as configurações de criptografia para os dados que são enviados para a nuvem.

Execute as etapas a seguir no portal clássico do Azure para concluir a configuração obrigatória do dispositivo.

#### <a name="to-complete-the-minimum-device-setup"></a>Para concluir a configuração mínima do dispositivo
1. Na página **Dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo aparecerá como **Ativo**. Clique na seta ao lado do nome do dispositivo e clique em **Início Rápido**.
   
    ![Página Dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)
2. Clique em **Concluir a instalação do dispositivo** para iniciar o assistente Configurar dispositivo.
   
    ![Assistente Configurar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)
3. No assistente Configurar dispositivo, na página **Configurações Básicas**, faça o seguinte:
   
   1. Especifique uma conta de armazenamento para ser usada com seu dispositivo. Nesta assinatura, é possível selecionar uma conta de armazenamento existente na lista suspensa ou especificar **Adicionar mais** para escolher uma conta de uma assinatura diferente.
   2. Defina as configurações de criptografia para todos os dados em repouso que serão enviados para a nuvem. (o StorSimple usa a criptografia AES-256). Para criptografar seus dados, marque a caixa de seleção **Habilitar criptografia de armazenamento em nuvem**. Insira uma chave de criptografia de armazenamento em nuvem que contenha 32 caracteres. Redigite a chave para confirmá-la.
   3. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).
      
      ![Configurações Básicas](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)
      
      As configurações agora serão atualizadas. Depois que as configurações forem atualizadas com êxito, o botão concluir configuração de dispositivo estará indisponível. Você será retornado para a página **Início Rápido** do dispositivo.                                                        

> [!NOTE]
> Você pode modificar todas as outras configurações do dispositivo a qualquer momento acessando a página **Configurar** .
> 
> 

## <a name="step-3-add-a-volume"></a>Etapa 3: adicionar um volume
Execute as etapas a seguir no portal clássico do Azure para criar um volume.

#### <a name="to-create-a-volume"></a>Para criar um volume
1. No página **Início Rápido** do dispositivo, clique em **Adicionar um volume**. Isso inicia o assistente Adicionar um volume.
2. No assistente Adicionar um volume, em **Configurações Básicas**, faça o seguinte:
   
   1. Fornecer um nome exclusivo para o volume. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.
   2. Forneça uma descrição para o volume. A descrição ajudará a identificar os proprietários de volume.
   3. Selecione um tipo de uso para o volume. O tipo de uso pode ser **Volume em camadas** ou **Volume localmente afixado.** (**Volume em camadas** é o padrão.) Para as cargas de trabalho que exigem garantias locais, latências baixas e um melhor desempenho, selecione **volume** **Localmente afixado**. Para todos os outros dados, selecione **Volume** em **camadas**.
      
       Um volume fixado localmente é provisionado estaticamente e garante que os dados primários no volume permaneçam como locais para o dispositivo e não sejam divulgados na nuvem. Se você criar um volume fixado localmente, o dispositivo verificará o espaço disponível nas camadas locais para provisionar um volume do tamanho solicitado. Criar um volume fixado localmente pode exigir a perda de dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume pode ser longo. O tempo total depende do tamanho do volume provisionado, da largura de banda disponível e dos dados no dispositivo.
      
       Um volume em camadas, por outro lado, é provisionado dinamicamente e pode ser criado bem rapidamente. Quando você cria um volume em camadas, aproximadamente 10% do espaço é provisionado na camada de local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionar um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que, se você ficar sem todo o espaço local no dispositivo, você não poderá provisionar um compartilhamento em camadas (porque 10% não estarão disponíveis).
   4. Especifique a capacidade provisionada para o seu volume. Observe que a capacidade especificada deve ser menor do que a capacidade disponível. Se você estiver criando um volume em camadas, o tamanho deverá estar entre 500 GB e 5 TB. Para um volume fixo localmente, especifique um tamanho de volume entre 50 e 500 GB. Use a capacidade disponível como um guia para o provisionamento de um volume. Se a capacidade local disponível é 0 GB, você não terá permissão para provisionar um volume fixado localmente ou em camadas.
      
       ![Configurações Básicas](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)
   5. Clique no ícone de seta  ![ícone de seta](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir para a próxima página.
3. Na página **Configurações adicionais** , adicione um novo registro de controle de acesso (ACR):
   
   1. Dê um **Nome** para o seu ACR.
   2. Em **Nome do Iniciador iSCSI**, forneça o iSCSI IQN (nome qualificado) do host Windows. Se você não tiver o IQN, vá para o [Apêndice A: Obter o IQN de um host do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   3. É recomendável que você habilite um backup padrão ao marcar a caixa de seleção **Habilitar um backup padrão para este volume** . O backup padrão criará uma política que é executa às 22:30 todos os dias (hora do dispositivo) e cria um instantâneo de nuvem desse volume.
      
       ![Configurações adicionais](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)
   4. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Isso inicia o trabalho de criação do volume. Você receberá uma mensagem de andamento semelhante à mostrada a seguir.
      
       ![mensagem sobre o andamento](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)
      
       Será criado um volume com as configurações especificadas. Por padrão, monitoramento e backup estarão habilitados para o volume.
   5. Para confirmar se o volume foi criado com êxito, vá para a página **Volumes** . Você deve ver o volume listado.
      
       ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Etapa 4: montar, inicializar e formatar um volume
Execute as etapas a seguir para montar, inicializar e formatar os volumes StorSimple em um host do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume
1. Inicie o iniciador iSCSI da Microsoft.
2. Na janela **Propriedades do Iniciador iSCSI** na guia **Descoberta**, clique em **Descobrir Portal**.
   
    ![Descobrir Portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Descobrir Portal de Destino**, forneça o endereço IP de seu adaptador de rede habilitado para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI** na guia **Destinos**, localize os **Destinos descobertos**. (Cada volume será um destino descoberto.) O status do dispositivo deve aparecer como **Inativo**.
   
    ![destinos descobertos](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo de destino e clique em **Conectar**. Após o dispositivo ter sido conectado, o status deverá mudar para **Conectado**. (Para obter mais informações sobre como usar o iniciador iSCSI da Microsoft, veja [Instalando e configurando o iniciador iSCSI da Microsoft][1]).
   
    ![selecionar dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)
6. No host do Windows, aperte a tecla do logotipo do Windows + X e depois clique em **Executar**.
7. Na caixa de diálogo **Executar**, digite **Diskmgmt.msc**. Clique em **OK** e a caixa de diálogo **Gerenciamento de Disco** será exibida. O painel do lado direito exibirá os volumes do seu host.
8. Na janela **Gerenciamento de Disco** , os volumes montados serão exibidos conforme exibido na ilustração a seguir. Clique com o botão direito no volume descoberto (clique no nome do disco) e depois clique em **Online**.
   
    ![Gerenciamento de Disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)
9. Clique com o botão direito do mouse e selecione **Inicializar Disco**.
   
    ![inicializar disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione os discos a serem inicializados e clique em **OK**.
    
    ![inicializar disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)
11. O assistente de Novo Volume Simples é iniciado. Selecione um tamanho de disco e clique em **Avançar**.
    
    ![assistente de novo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)
12. Atribua uma letra da unidade ao volume e clique em **Avançar**.
    
    ![assistente de novo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)
13. Insira os parâmetros para formatar o volume. **No Windows Server, há suporte somente para NTFS.** Defina AUS como 64K. Forneça um rótulo para o volume. É uma melhor prática recomendada que esse nome seja idêntico ao nome do volume fornecido em seu dispositivo virtual StorSimple. Clique em **Próximo**.
    
    ![assistente de novo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)
14. Verifique os valores do volume e clique em **Concluir**.
    
    ![assistente de novo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)
    
    Os volumes serão exibidos como **Online** on the **Gerenciamento de Disco** .
    
    ![volumes online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Próximas etapas
Saiba como usar a interface do usuário da Web local para [administrar o StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice A: Obter o IQN de um host do Windows Server
Execute as etapas a seguir para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN do host do Windows
1. Inicie o iniciador Microsoft iSCSI no host do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, na guia **Configuração** selecione e copie a cadeia de caracteres do campo **Nome do Iniciador**.
   
    ![Propriedades do iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)
3. Salve esta cadeia de caracteres.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx






<!--HONumber=Dec16_HO2-->



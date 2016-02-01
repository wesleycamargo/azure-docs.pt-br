<properties 
   pageTitle="Configuração de servidor iSCSI de StorSimple Virtual Array | Microsoft Azure"
   description="Descreve como realizar a configuração inicial, registrar seu servidor iSCSI do StorSimple e concluir a configuração do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/20/2016"
   ms.author="alkohli" />


# Implantar StorSimple Virtual Array - configurar seu dispositivo virtual como um servidor iSCSI (visualização)

![fluxo do processo de instalação iscsi](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## Visão geral

Este tutorial de implantação se aplica à Matriz Virtual Microsoft Azure StorSimple (também conhecida como o dispositivo virtual local StorSimple ou o dispositivo virtual StorSimple) executando a versão de Visualização Pública v 1.1.1.0. Este tutorial descreve como executar a instalação inicial, registrar o servidor iSCSI do StorSimple, concluir a configuração do dispositivo e, em seguida, criar, montar, inicializar e formatar volumes em seu servidor iSCSI do dispositivo virtual StorSimple. As informações de implantação do StorSimple publicadas neste artigo se aplicam somente à StorSimple Virtual Array.

Os procedimentos descritos aqui levam um intervalo de aproximadamente 30 minutos a 1 hora para concluir. As informações publicadas nesse artigo aplicam-se somente a Matrizes Virtuais StorSimple.

>[AZURE.IMPORTANT]
>
>- A StorSimple Virtual Array está em visualização e é destinada para fins de planejamento de implantação e avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção. 
>- Se você tiver quaisquer problemas com a StorSimple Virtual Array, publique-os no [fórum do MSDN do StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

## Pré-requisitos de configuração

Antes de configurar e configurar o dispositivo virtual StorSimple, certifique-se de que:

- Você provisionou um dispositivo virtual e conectou-se a ele, conforme descrito em [Implantar StorSimple Virtual Array - provisionar uma matriz virtual em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar StorSimple Virtual Array - provisionar uma matriz virtual em VMware](storsimple-ova-deploy2-provision-vmware.md).

- Você tem a chave de registro do serviço StorSimple Manager que você criou para gerenciar dispositivos virtuais StorSimple. Para obter mais informações, veja **Etapa 2: obter a chave de registro do serviço** em [Implantar StorSimple Virtual Array - preparar o portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Se esse for o segundo dispositivo virtual ou dispositivo virtual subsequente que você está registrando com um serviço StorSimple Manager existente, você deve ter a chave de criptografia de dados do serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Se você perdeu essa chave, veja **Obter a chave de criptografia de dados de serviço** em [Usar a interface do usuário da Web para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## Configuração passo a passo 

Use as instruções passo a passo a seguir para preparar e configurar seu dispositivo virtual StorSimple:

-  [Etapa 1: concluir a configuração de interface do usuário da Web local e registrar seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Etapa 2: concluir a configuração obrigatória do dispositivo](#step-2-complete-the-required-device-setup)
-  [Etapa 3: adicionar um volume](#step-3-add-a-volume)
-  [Etapa 4: montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)  

## Etapa 1: concluir a configuração de interface do usuário da Web local e registrar seu dispositivo 

#### Para concluir a configuração e registrar o dispositivo

1. Abra uma janela do navegador e conecte-se à interface do usuário da Web, digitando:

    `https://<ip-address of network interface>`

    Use a URL de conexão observada na etapa anterior. Você verá um erro informando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**.

    ![erro de certificado de segurança](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Entre na interface do usuário da Web do seu dispositivo virtual como **StorSimpleAdmin**. Digite a senha do administrador do dispositivo que você alterou na Etapa 3: iniciar o dispositivo virtual em [Implantar StorSimple Virtual Array - provisionar um dispositivo virtual em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar StorSimple Virtual Array - provisionar um dispositivo virtual em VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Página de entrada](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Você será conduzido à **Página Inicial**. Esta página descreve as várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço StorSimple Manager. Observe que **Configurações de rede**, **Configurações de proxy da Web** e **Configurações de hora** são opcionais. As únicas configurações obrigatórias são as **Configurações do dispositivo** e **Configurações de nuvem**.

    ![Página inicial](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Na página **Configurações de rede**, em **Interfaces de rede**, DATA 0 será configurado automaticamente para você. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Assim, um endereço IP, a sub-rede e gateway serão atribuídos automaticamente (tanto para IPv4 quanto para IPv6).

    Já que você planeja implantar o dispositivo como um servidor iSCSI (para provisionar armazenamento em bloco), recomendamos que você desabilite a opção **Obter endereço IP automaticamente** e configure endereços IP estáticos.

    ![Página de configurações de rede](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui.

5. Os servidores DNS são necessários porque eles são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento de nuvem, ou então para resolver seu dispositivo por nome, se ele estiver configurado como um servidor de arquivos. Na página **Configurações de rede**, em **Servidores DNS**:

    1. Um servidor DNS primário e um secundário serão configurados automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.

    2. Clique em **Aplicar**. Isso aplicará e validará as configurações de rede.

6. Na página **Configurações do dispositivo**:

    1. Atribua um **Nome** exclusivo a seu dispositivo. Esse nome pode ter de 1 a 15 caracteres e pode conter letras, números e hifens.

    2. No **servidor iSCSI**, clique no ícone ![Ícone do servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para o **tipo** do dispositivo que você está criando. Um servidor iSCSI permitirá a você provisionar armazenamento em bloco.

    3. Especifique se deseja que este dispositivo seja ingressado no domínio. Se o dispositivo é um servidor iSCSI, ingressar no domínio é opcional. Se você optar por não incluir o servidor iSCSI em um domínio, clique em **Aplicar**, aguarde até que as configurações sejam aplicadas e, em seguida, vá para a próxima etapa.

        Se você quiser adicionar o dispositivo a um domínio. Insira um **Nome de Domínio** (mostrado abaixo).

    4. Clique em **Aplicar**.

    5. Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.

        ![credenciais](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Clique em **Aplicar**. Isso aplicará e validará as configurações do dispositivo.
 
7. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui.

    ![configurar o proxy Web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Na página **Proxy Web**:

    1. Forneça a **URL do proxy Web** neste formato: *endereço http://host-IP* ou *FDQN:Número da porta*. Observe que não há suporte para URLs HTTPS.

    2. Especifique **Autenticação** como **Básica**, **NTLM** ou **Nenhuma**.

    3. Se você estiver usando autenticação, você também precisará fornecer um **Nome de Usuário** e **Senha**.

    4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy Web definidas.
 
8. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.

    ![Configurações de hora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Na página **Configurações de hora**:

    1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    3. Opcionalmente, especifique um **Servidor NTP secundário** para seu dispositivo.

    4. Clique em **Aplicar**. Isso validará e aplicará as configurações de hora definidas.

9. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração de dispositivo local e, em seguida, registrará o dispositivo com o serviço StorSimple Manager.

    1. Insira a **Chave de registro do serviço** que você obteve na **Etapa 2: obter a chave de registro do serviço** em [Implantar StorSimple Virtual Array - Preparar o Portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Se não é o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a **Chave de criptografia de dados de serviço**. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço StorSimple Manager. Para obter mais informações, consulte [Obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua interface do usuário da Web local.

    3. Clique em **Registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar de 2 a 3 minutos até que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado à página de entrada.

       ![Registrar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Retorne ao portal clássico do Azure. Na página **Dispositivos**, verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve ser **Ativo**.

    ![Página Dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## Etapa 2: concluir a configuração obrigatória do dispositivo

Para concluir a configuração de dispositivo do seu dispositivo StorSimple, é necessário:

- Selecionar uma conta de armazenamento para associar ao seu dispositivo.

- Escolha as configurações de criptografia para os dados que são enviados para a nuvem.

Execute as etapas a seguir no portal clássico do Azure para concluir a configuração obrigatória do dispositivo.

#### Para concluir a configuração mínima do dispositivo

1. Na página **Dispositivos**, selecione o dispositivo que você acabou de criar. Este dispositivo apareceria como **Ativo**. Clique na seta próxima do nome do dispositivo e, em seguida, clique em **Início Rápido**.

    ![Página Dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Clique em **concluir a instalação do dispositivo** para iniciar o assistente Configurar dispositivo.

    ![Assistente Configurar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. No assistente Configurar dispositivo, na página **Configurações Básicas**, faça o seguinte:

   1. Especifique uma conta de armazenamento para ser usada com seu dispositivo. Nesta assinatura, você pode selecionar uma conta de armazenamento existente na lista suspensa, ou você pode especificar **Adicionar mais** para escolher uma conta de uma assinatura diferente.

   2. Defina as configurações de criptografia para todos os dados em repouso que serão enviados para a nuvem. (o StorSimple usa a criptografia AES-256). Para criptografar seus dados, selecione a caixa de seleção **Habilitar criptografia de armazenamento em nuvem**. Insira uma chave de criptografia de armazenamento em nuvem que contenha 32 caracteres. Redigite a chave para confirmá-la.

   3. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Configurações básicas](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    As configurações agora serão atualizadas. Depois que as configurações forem atualizadas com êxito, o botão concluir configuração de dispositivo estará indisponível. Você será retornado para a página **Início Rápido** do dispositivo.

>[AZURE.NOTE]Você pode modificar todas as outras configurações do dispositivo a qualquer momento acessando a página **Configurar**.

## Etapa 3: adicionar um volume

Execute as etapas a seguir no portal clássico do Azure para criar um volume.

#### Para criar um volume

1. No página **Início Rápido** do dispositivo clique em **Adicionar um volume**. Isso inicia o assistente Adicionar um volume.

2. No assistente Adicionar um volume, em **Configurações Básicas**, faça o seguinte:

    1. Fornecer um nome exclusivo para o volume. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.

    2. Forneça uma descrição para o volume. A descrição ajudará a identificar os proprietários de volume.

    3. Selecione um tipo de uso para o volume. O tipo de uso pode ser **Volume em camadas** ou **Volume fixado localmente.** (**Volume em camadas** é o padrão.) Para cargas de trabalho que exigem garantias locais, menos latências e um melhor desempenho, selecione **Volume** **fixado localmente**. Para todos os outros dados, selecione **Volume** **em camadas**.

        Um volume fixado localmente é provisionado estaticamente e garante que os dados primários no volume permaneçam como locais para o dispositivo e não sejam divulgados na nuvem. Se você criar um volume fixado localmente, o dispositivo verificará o espaço disponível nas camadas locais para provisionar um volume do tamanho solicitado. Criar um volume fixado localmente pode exigir a perda de dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume pode ser longo. O tempo total depende do tamanho do volume provisionado, da largura de banda disponível e dos dados no dispositivo.

        Um volume em camadas, por outro lado, é provisionado dinamicamente e pode ser criado bem rapidamente. Quando você cria um volume em camadas, aproximadamente 10% do espaço é provisionado na camada de local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionar um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que, se você ficar sem todo o espaço local no dispositivo, você não poderá provisionar um compartilhamento em camadas (porque 10% não estarão disponíveis).

    4. Especifique a capacidade provisionada para o seu volume. Observe que a capacidade especificada deve ser menor do que a capacidade disponível. Se você estiver criando um volume em camadas, o tamanho deve ser entre 500 GB e 20 TB. Para um volume fixado localmente, especifique um tamanho de volume entre 50 GB e 2 TB. Use a capacidade disponível como um guia para o provisionamento de um volume. Se a capacidade local disponível é 0 GB, você não terá permissão para provisionar um volume fixado localmente ou em camadas.

        ![Configurações básicas](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Clique no ícone de seta ![ícone de seta](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir para a próxima página.

3. Na página **Configurações adicionais**, adicione um novo registro de controle de acesso (ACR):

    1. Dê um **Nome** para o seu ACR.

    2. Em **Nome do Iniciador iSCSI**, forneça o iSCSI IQN (nome qualificado) do host Windows. Se você não tem o IQN, vá para [Apêndice A: obter o IQN de um host do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. É recomendável que você habilite um backup padrão ao marcar a caixa de seleção **Habilitar um backup padrão para este volume**. O backup padrão criará uma política que é executa às 22:30 todos os dias (hora do dispositivo) e cria um instantâneo de nuvem desse volume.

        ![configurações adicionais](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Isso inicia o trabalho de criação do volume. Você receberá uma mensagem de andamento semelhante à mostrada a seguir.

        ![mensagem sobre o andamento](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Será criado um volume com as configurações especificadas. Por padrão, monitoramento e backup estarão habilitados para o volume.

    5. Para confirmar se o volume foi criado com êxito, vá para a página **Volumes**. Você deve ver o volume listado.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## Etapa 4: montar, inicializar e formatar um volume

Execute as etapas a seguir para montar, inicializar e formatar os volumes StorSimple em um host do Windows Server.

#### Para montar, inicializar e formatar um volume

1. Inicie o iniciador iSCSI da Microsoft.

2. Na janela **Propriedades do Iniciador iSCSI** na guia **Descoberta**, clique em **Descobrir Portal**.

    ![descobrir portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Na caixa de diálogo **Descobrir Portal de Destino**, forneça o endereço IP da sua interface de rede habilitada para iSCSI e clique em **OK**.

    ![Endereço IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Na janela **Propriedades do Iniciador iSCSI** na guia **Destinos**, localize os **Destinos descobertos**. (Cada volume será um destino descoberto.) O status do dispositivo deve aparecer como **Inativo**.

    ![destinos descobertos](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Selecione um dispositivo de destino e depois clique em **Conectar**. Após o dispositivo ter sido conectado, o status deverá mudar para **Conectado**. (Para obter mais informações sobre o uso do iniciador iSCSI da Microsoft, consulte [Instalando e Configurando o Iniciador iSCSI da Microsoft][1]).

    ![selecionar dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. No host do Windows, aperte a tecla do logotipo do Windows + X e depois clique em **Executar**.

7. Na caixa de diálogo **Executar**, digite **Diskmgmt.msc**. Clique em **OK** e a caixa de diálogo **Gerenciamento de Disco** será exibida. O painel do lado direito exibirá os volumes do seu host.

8. Na janela **Gerenciamento de Disco**, os volumes montados serão exibidos conforme exibido na ilustração a seguir. Clique com o botão direito no volume descoberto (clique no nome do disco) e depois clique em **Online**.

    ![gerenciamento de disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Clique com o botão direito do mouse e selecione **Inicializar Disco**.

    ![inicializar disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Na caixa de diálogo, selecione os discos a inicializar e, em seguida, clique em **OK**.

    ![inicializar disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. O assistente de Novo Volume Simples é iniciado. Selecione um tamanho de disco e, em seguida, clique em **Próximo**.

    ![assistente de novo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Atribua uma letra da unidade ao volume e, em seguida, clique em **Próximo**.

    ![assistente de novo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Insira os parâmetros para formatar o volume. **No Windows Server, há suporte somente para NTFS.** Defina AUS como 64K. Forneça um rótulo para o volume. É uma melhor prática recomendada que esse nome seja idêntico ao nome do volume fornecido em seu dispositivo virtual StorSimple. Clique em **Próximo**.

    ![assistente de novo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Verifique os valores para o volume e, em seguida, clique em **Concluir**.

    ![assistente de novo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Os volumes serão exibidos como **Online** na página **Gerenciamento de Disco**.

    ![volumes online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## Próximas etapas

Aprenda como usar a interface do usuário da Web local para [administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## Apêndice A: obter o IQN de um host do Windows Server

Execute as etapas a seguir para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server 2012.

#### Para obter o IQN do host do Windows

1. Inicie o iniciador Microsoft iSCSI no host do Windows.

2. Na janela **Propriedades do Iniciador iSCSI**, na guia **Configuração** selecione e copie a cadeia de caracteres do campo **Nome do Iniciador**.

    ![Propriedades do iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Salve esta cadeia de caracteres.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

<!---HONumber=AcomDC_0121_2016-->
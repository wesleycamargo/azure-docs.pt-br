<properties 
   pageTitle="Implantar o StorSimple Snapshot Manager | Microsoft Azure"
   description="Saiba como baixar e instalar o StorSimple Snapshot Manager, um snap-in do MMC para gerenciar recursos de backup e de proteção de dados do StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/07/2016"
   ms.author="v-sharos" />

# Implantar o snap-in do MMC StorSimple Snapshot Manager

## Visão geral

O StorSimple Snapshot Manager é um snap-in do Console de Gerenciamento Microsoft (MMC) que simplifica a proteção de dados e o gerenciamento de backups em um ambiente do Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, é possível gerenciar o armazenamento em nuvem e local do Microsoft Azure StorSimple como se fosse um sistema de armazenamento totalmente integrado, simplificando assim os processos de backup e restauração e reduzindo os custos.

Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar, remover e atualizar o StorSimple Snapshot Manager.

>[AZURE.NOTE]
>
>- Você não pode usar o StorSimple Snapshot Manager para gerenciar as Matrizes Virtuais do Microsoft Azure StorSimple (também conhecido como dispositivos virtuais locais StorSimple).
>
>- Se você planeja instalar a Atualização 2 do StorSimple em seu dispositivo StorSimple, não se esqueça de baixar a versão mais recente do StorSimple Snapshot Manager e instalá-lo **antes de instalar a Atualização 2 do StorSimple**. A versão mais recente do StorSimple Snapshot Manager é compatível com versões anteriores e funciona com todas as versões do Microsoft Azure StorSimple. Se você está usando a versão anterior do StorSimple Snapshot Manager, você precisa atualizá-lo (não é necessário desinstalar a versão anterior antes de instalar a nova versão).

## Instalação do StorSimple Snapshot Manager

O StorSimple Snapshot Manager pode ser instalado em computadores que executam o sistema operacional Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2. Em servidores que executam o Windows 2008 R2, instale também o Windows Server 2008 SP1 e o Windows Management Framework 3.0.

Antes de instalar ou atualizar o snap-in StorSimple Snapshot Manager para o Console de Gerenciamento Microsoft (MMC), verifique se o servidor de host e o dispositivo do Microsoft Azure StorSimple estão configurados corretamente.

## Configurar pré-requisitos

As etapas a seguir oferecem uma visão geral de alto nível das tarefas de configuração que você deve concluir antes de instalar o StorSimple Snapshot Manager. Para obter informações de instalação e configuração completas do Microsoft Azure StorSimple, incluindo requisitos de sistema e instruções passo a passo, consulte [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Antes de começar, releia a [Lista de verificação de implantação](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) e os [Pré-requisitos de implantação](storsimple-deployment-walkthrough.md#deployment-prerequisites) em [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md).<br>
 
### Antes de instalar o StorSimple Snapshot Manager

1. Desempacote, monte e conecte o dispositivo do Microsoft Azure StorSimple conforme descrito em [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Verifique se o computador host está executando um dos seguintes sistemas operacionais:

    - Windows Server 2008 R2 (em servidores executando o Windows 2008 R2, você também deverá instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Para um dispositivo virtual StorSimple, o host deve ser uma Máquina Virtual do Microsoft Azure.

3. Certifique-se de atender a todos os requisitos de configuração do Microsoft Azure StorSimple. Para obter detalhes, acesse [Pré-requisitos de implantação](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Conecte o dispositivo ao host e execute a configuração inicial. Para obter detalhes, acesse as [Etapas de implantação](storsimple-deployment-walkthrough.md#deployment-steps).

5. Crie volumes no dispositivo, atribua-os ao host e verifique se o host pode montá-los e usá-los. O StorSimple Snapshot Manager dá suporte aos seguintes tipos de volumes:

    - Volumes básicos
    - Volumes simples
    - Volumes dinâmicos
    - Volumes dinâmicos espelhados (RAID 1)
    - Volumes compartilhados de cluster
 
    Para obter informações sobre como criar volumes no dispositivo StorSimple ou no dispositivo virtual StorSimple, acesse a [Etapa 6: criar um volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume) em [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

## Instalar um novo StorSimple Snapshot Manager

Antes de instalar o StorSimple Snapshot Manager, certifique-se de que os volumes criados no dispositivo StorSimple ou dispositivo virtual StorSimple estejam montados, inicializados e formatados como descrito em [Configurar pré-requisitos](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Para um dispositivo virtual StorSimple, o host deve ser uma Máquina Virtual do Microsoft Azure. 
>
>- O host deve executar Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Se seu servidor executar o Windows Server 2008 R2, você também deverá instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0.
>
>- Você deve configurar uma conexão iSCSI do host para o dispositivo StorSimple antes de conectar o dispositivo ao StorSimple Snapshot Manager.

Siga estas etapas para concluir uma instalação nova do StorSimple Snapshot Manager. Se você estiver instalando uma atualização, acesse [Atualizar ou reinstalar o StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Etapa 1: Instalar o StorSimple Snapshot Manager 
- Etapa 2: Conectar o StorSimple Snapshot Manager a um dispositivo 
- Etapa 3: Verificar a conexão com o dispositivo 

###Etapa 1: Instalar o StorSimple Snapshot Manager

Use as etapas a seguir para instalar o StorSimple Snapshot Manager.

#### Para instalar o StorSimple Snapshot Manager

1. Baixe o software do StorSimple Snapshot Manager (acesse [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) no Centro de Download da Microsoft) e salve-o localmente no host.

2. No Explorador de Arquivos, clique com o botão direito do mouse na pasta compactada e em **Extrair todos**.

3. Na janela **Extrair pastas compactadas (zipadas)**, na caixa **Selecione um destino e extraia os arquivos**, digite ou procure o caminho onde você gostaria de extrair o arquivo.

       >[AZURE.IMPORTANT]Você deve instalar o StorSimple Snapshot Manager na unidade C:.
 
4. Marque a caixa de seleção **Mostrar arquivos extraído ao concluir** e clique em **Extrair**.

    ![Extrair a caixa de diálogo de arquivos](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. Quando a extração estiver concluída, a pasta de destino será aberta. Clique duas vezes no ícone de instalação do aplicativo que aparece na pasta de destino.

5. Quando a mensagem **Configuração Concluída com Êxito** aparecer, clique em **Fechar**. Você deve ver o ícone do StorSimple Snapshot Manager na área de trabalho.

    ![Ícone da área de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### Etapa 2: Conectar o StorSimple Snapshot Manager a um dispositivo

Use as seguintes etapas para conectar o StorSimple Snapshot Manager a um dispositivo StorSimple.

#### Para conectar o StorSimple Snapshot Manager a um dispositivo

1. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela do StorSimple Snapshot Manager aparece. A janela contém os painéis **Escopo**, **Resultados** e **Ações**. 

    ![Interface do usuário do StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - O painel **Escopo** (o painel esquerdo) contém uma lista de nós organizados em uma estrutura de árvore. Você pode expandir alguns nós para selecionar uma exibição ou dados específicos relacionados a esse nó. Clique no ícone de seta para expandir ou recolher um nó. Clique com o botão direito do mouse em um item no painel **Escopo** para ver uma lista de ações disponíveis para o item. 

    - O painel **Resultados** (o painel central) contém informações de status detalhadas sobre o nó, exibição ou dados que você selecionou no painel **Escopo**.

    - O painel **Ações** lista as operações que podem ser executadas no nó, exibição ou dados que você selecionou no painel **Escopo**.

    Para obter uma descrição completa da interface do usuário do StorSimple Snapshot Manager, consulte a [Interface de usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

2. No painel **Escopo**, clique com o botão direito do mouse no nó **Dispositivos** e clique em **Configurar um dispositivo**. A caixa de diálogo **Configurar um Dispositivo** é exibida.

    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. Na caixa de listagem **Dispositivo**, selecione o endereço IP do dispositivo ou dispositivo virtual do Microsoft Azure StorSimple. Na caixa de texto **Senha**, digite a senha do StorSimple Snapshot Manager que você criou para o dispositivo no portal clássico do Azure. Clique em **OK**.

4. O StorSimple Snapshot Manager procura o dispositivo que você identificou. Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma conexão. Você pode [verificar a conexão com o dispositivo](#to-verify-the-connection) para confirmar se a conexão foi adicionada com êxito.

    Se o dispositivo estiver indisponível por algum motivo, o StorSimple Snapshot Manager retorna uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e clique em **Cancelar** para fechar a caixa de diálogo **Configurar um Dispositivo**.

5. Ao conectar-se a um dispositivo, o StorSimple Snapshot Manager importa cada grupo de volumes configurado para esse dispositivo, desde que o grupo de volumes tenha backups associados. Grupos de volumes que não têm backups associados não são importados. Além disso, as políticas de backup que foram criadas para um grupo de volumes não são importadas. Para ver os grupos importados, clique com o botão direito do mouse no nó de **Grupos de volume** mais alto no painel **Escopo** e clique em **Alternar grupos importados**.

### Etapa 3: Verificar a conexão com o dispositivo

Use as etapas a seguir para verificar se o StorSimple Snapshot Manager está conectado ao dispositivo StorSimple.

#### Para verificar a conexão

1. No painel **Escopo**, clique no nó **Dispositivos**.

    ![Status do dispositivo StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. Verifique o painel **Resultados**:

   - Se um indicador verde aparecer no ícone de dispositivo e **Disponível** aparecer na coluna **Status**, o dispositivo está conectado. 

   - Se um indicador vermelho aparecer no ícone do dispositivo e Indisponível aparecer na coluna **Status**, o dispositivo não está conectado.

   - Se **Atualizando** aparecer na coluna **Status**, o StorSimple Snapshot Manager está recuperando grupos de volumes e backups associados para um dispositivo conectado.

## Atualizar ou reinstalar o StorSimple Snapshot Manager

Você deve desinstalar o StorSimple Snapshot Manager completamente antes de atualizar ou reinstalar o software.

Antes de reinstalar o StorSimple Snapshot Manager, faça backup do banco de dados existente do StorSimple Snapshot Manager no computador host. Isso salva as políticas de backup e informações de configuração para que você possa restaurar facilmente esses dados de backup.

Siga essas etapas se você estiver atualizando ou reinstalando o StorSimple Snapshot Manager:

- Etapa 1: Desinstalar o StorSimple Snapshot Manager 
- Etapa 2: Fazer backup do banco de dados do StorSimple Snapshot Manager 
- Etapa 3: Reinstalar o StorSimple Snapshot Manager e restaurar o banco de dados 

### Etapa 1: Desinstalar o StorSimple Snapshot Manager

Use as etapas a seguir para desinstalar o StorSimple Snapshot Manager.

#### Para desinstalar o StorSimple Snapshot Manager

1. No computador host, abra o **Painel de Controle**, clique em **Programas** e clique em **Programas e Recursos**.

2. No painel esquerdo, clique em **Desinstalar ou alterar um programa**.

3. Clique com o botão direito do mouse em **StorSimple Snapshot Manager** e clique em **Desinstalar**.

4. Isso abre o programa de instalação do StorSimple Snapshot Manager. Clique em **Modificar Configuração** e clique em **Desinstalar**.

    >[AZURE.NOTE]Se houver algum processo do MMC em execução em segundo plano, como o StorSimple Snapshot Manager ou o Gerenciamento de Disco, a desinstalação falhará e você receberá uma mensagem para fechar todas as instâncias do MMC antes de tentar desinstalar o programa. Selecione **Fechar aplicativos automaticamente e tentar reiniciá-los após a conclusão da instalação** e clique em **OK**.
 
5. Quando o processo de desinstalação for concluído, uma mensagem **Configuração concluída com êxito** é exibida. Clique em **fechar**

### Etapa 2: Fazer backup do banco de dados do StorSimple Snapshot Manager

Use as etapas a seguir para criar e salvar uma cópia do banco de dados do StorSimple Snapshot Manager.

#### Para fazer backup do banco de dados

1. Parar o Serviço de Gerenciamento do Microsoft StorSimple:

   1. Inicie o Gerenciador do Servidor.

   2. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.

   3. Na página **Serviços**, selecione **Serviço de Gerenciamento do Microsoft StorSimple**.

   4. No painel direito, em **Serviço de Gerenciamento Microsoft StorSimple**, clique em **Parar o serviço**.

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Navegue para C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

    >[AZURE.NOTE]ProgramData é uma pasta oculta.

3. Localize o arquivo XML do catálogo, copie o arquivo e armazene a cópia em um local seguro ou na nuvem.

    ![Arquivo de catálogo de backup do StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Reinicie o Serviço de Gerenciamento do Microsoft StorSimple:

    1. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.

    2. Na página **Serviços**, selecione o **Microsoft StorSimple Management Service**.

    3. No painel direito, em **Serviço de Gerenciamento do Microsoft StorSimple**, clique em **Reiniciar o serviço**.

### Etapa 3: Reinstalar o StorSimple Snapshot Manager e restaurar o banco de dados

Para reinstalar o StorSimple Snapshot Manager, siga as etapas em [Instalar um novo StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Em seguida, use o procedimento a seguir para restaurar o banco de dados do StorSimple Snapshot Manager.

#### Para restaurar o banco de dados

1. Parar o Serviço de Gerenciamento do Microsoft StorSimple:

    1. Inicie o Gerenciador do Servidor.

    2. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.

    3. Na página **Serviços**, selecione **Serviço de Gerenciamento do Microsoft StorSimple**.

    4. No painel direito, em **Serviço de Gerenciamento Microsoft StorSimple**, clique em **Parar o serviço**.

2. Navegue para C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

     >[AZURE.NOTE]ProgramData é uma pasta oculta.

3. Exclua o arquivo XML do catálogo e substitua-o pela versão que foi salva anteriormente.

4. Reinicie o Serviço de Gerenciamento do Microsoft StorSimple:

    1. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.

    2. Na página **Serviços**, selecione **Microsoft StorSimple Management Service**.

    3. No painel direito, em **Microsoft StorSimple Management Service**, clique em **Reiniciar o serviço**.

## Próximas etapas

- Para saber mais sobre o StorSimple Snapshot Manager, acesse [O que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

- Para saber mais sobre a interface do usuário do StorSimple Snapshot Manager, acesse a [Interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

- Para saber mais sobre como usar o StorSimple Snapshot Manager, acesse [Usar o StorSimple Snapshot Manager para administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).

<!---HONumber=AcomDC_0114_2016-->
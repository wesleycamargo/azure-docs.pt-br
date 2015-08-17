<properties 
   pageTitle="Usar o StorSimple Snapshot Manager para exibir e gerenciar volumes | Microsoft Azure"
   description="Descreve como usar o snap-in StorSimple Snapshot Manager MMC para exibir e gerenciar volumes."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Usar o StorSimple Snapshot Manager para exibir e gerenciar volumes

## Visão geral

Você pode usar o nó **Volumes** do StorSimple Snapshot Manager (no painel **Escopo**) para selecionar volumes e exibir informações sobre eles. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo host. O nó **Volumes** mostra volumes locais e tipos de volumes que têm suporte pelo Azure StorSimple, incluindo volumes descobertos por meio do uso de iSCSI e um dispositivo.

Para saber mais sobre os volumes compatíveis, vá até [Suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de Volumes no painel de Resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Figura 1: Nó Volumes do StorSimple Snapshot Manager**

O nó **Volumes** também permite examinar novamente ou excluir volumes após o StorSimple Snapshot Manager descobri-los.

Este tutorial explica como montar, inicializar e formatar volumes e usar o StorSimple Snapshot Manager para:

- Exibir informações sobre volumes 
- Excluir volumes
- Examinar volumes novamente 
- Configurar um volume básico e fazer seu backup
- Configurar um volume dinâmico espelhado e fazer seu backup

>[AZURE.NOTE]Todas as ações do nó **Volume** também estão disponíveis no painel **Ações**.
 
## Montar volumes

Use o procedimento a seguir para montar, inicializar e formatar volumes do Azure StorSimple.

#### Para montar volumes

1. No computador host, inicie o Microsoft iSCSI.

2. Forneça um dos endereços IP da interface como o portal de destino ou endereço IP de descoberta e conecte-se ao dispositivo. Após o dispositivo ser conectado, os volumes ficarão acessíveis para o seu sistema Windows. Para saber mais sobre o uso do iniciador Microsoft iSCSI, vá para a seção "Conectando a um dispositivo de destino iSCSI", em [Instalando e configurando o iniciador Microsoft iSCSI][1].

3. Use qualquer uma das opções a seguir para iniciar o snap-in de Gerenciamento de Disco:

    - Digite Diskmgmt.msc na caixa **Executar**.

    - Inicie o Gerenciador do Servidor, expanda o nó **Armazenamento** e selecione **Gerenciamento de Disco**.

    - Inicie as **Ferramentas Administrativas**, expanda o nó **Gerenciamento do Computador** e selecione **Gerenciamento de Disco**.

    >[AZURE.NOTE]Você precisa usar privilégios de administrador para executar o Gerenciamento de Disco.
 
4. Coloque os volumes online:

   1. No Gerenciamento de Disco, clique com o botão direito em qualquer volume marcado como **Offline**.

   2. Clique em **Reativar Disco**. O disco deve ser marcado como **Online** após o disco ser reativado.

5. Inicializar os volumes:

   1. Clique com botão direito nos volumes descobertos.

   2. No menu, selecione **Inicializar Disco**.

   3. Na caixa de diálogo **Inicializar Disco**, selecione os discos que você deseja inicializar e clique em **OK**.

6. Formate os volumes simples:

   1. Clique com o botão direito em um volume que você deseja formatar.

   2. No menu, selecione **Novo Volume Simples**.

   3. Use o assistente de Novo Volume Simples para formatar o volume:

      - Especifique o tamanho do volume.
      - Forneça uma letra da unidade.
      - Selecione o sistema de arquivos NTFS.
      - Especifique um tamanho de unidade de alocação com 64 KB.
      - Realize uma formatação rápida.

7. Formate os volumes com várias partições. Para obter instruções, vá até a seção "Partições e Volumes" em [Implementando o Gerenciamento de Disco](https://msdn.microsoft.com/library/dd163556.aspx).

## Exibir informações sobre os volumes

Use o procedimento a seguir para exibir informações sobre volumes locais e do Azure StorSimple.

#### Para exibir informações de volume

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 

2. No painel **Escopo**, clique no nó **Volumes**. Uma lista de volumes locais e montados, incluindo todos os volumes do Azure StorSimple, aparece no painel **Resultados**. As colunas no painel **Resultados** são configuráveis. (Clique com o botão direito no nó **Volumes**, selecione **Exibir** e selecione **Adicionar/Remover Colunas**.)

    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Coluna de resultados | Descrição 
    :--------------|:-------------
    Nome | A coluna **Nome** contém a letra da unidade atribuída a cada volume descoberto.
    Dispositivo | A coluna **Dispositivo** contém o endereço IP do dispositivo conectado ao computador host.
    Nome do Volume do Dispositivo | A coluna **Nome do Volume do Dispositivo** contém o nome do volume do dispositivo ao qual o volume selecionado pertence. Esse é o nome do volume definido no Portal de Gerenciamento do Azure para o volume específico.
    Caminhos de acesso | A coluna **Caminhos de Acesso** exibe o caminho de acesso ao volume. Esta é a letra da unidade ou ponto de montagem no qual o volume está acessível para o computador host.
 
## Excluir um volume

Use o procedimento a seguir para excluir um volume do StorSimple Snapshot Manager.

>[AZURE.NOTE]Você não pode excluir um volume se ele fizer parte de um grupo de volumes. (A opção de exclusão não está disponível para volumes que fazem parte de um grupo de volumes). Você deve excluir o grupo de volumes inteiro para excluir o volume.<br>
#### Para excluir um volume

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique no nó **Volumes**.

3. No painel **Resultados**, clique no volume que você deseja excluir.

4. No menu, clique em **Excluir**.

    ![Excluir um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png)

5. A caixa de diálogo **Excluir Volume** é exibida. Digite **Confirmar** na caixa de texto e clique em **OK**.

6. Por padrão, o StorSimple Snapshot Manager faz backup de um volume antes de excluí-lo. Essa precaução pode proteger você contra perda de dados se a exclusão tiver sido involuntária. O StorSimple Snapshot Manager exibe uma mensagem de progresso do **Instantâneo Automático** enquanto faz backup do volume.

    ![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png)

## Examinar volumes novamente

Use o procedimento a seguir para examinar novamente os volumes conectados ao StorSimple Snapshot Manager.

#### Para examinar novamente os volumes

1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.

2. No painel **Escopo**, clique com o botão direito em **Volumes** e clique em **Examinar volumes novamente**.

    ![Examinar volumes novamente](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Este procedimento sincroniza a lista de volumes com o StorSimple Snapshot Manager. Quaisquer alterações, como novos volumes ou volumes excluídos, serão refletidas nos resultados.

## Configurar e fazer backup de um volume básico

Use o procedimento a seguir para configurar um backup de um volume básico e, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

### Pré-requisitos

Antes de começar:

- Certifique-se de que o computador host e o dispositivo StorSimple estejam configurados corretamente. Para obter mais informações, vá até [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

- Instale e configure o StorSimple Snapshot Manager. Para saber mais, vá até [Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### Para configurar o backup de um volume básico

1. Crie um volume básico no dispositivo StorSimple.

2. Monte, inicialize e formate o volume, conforme descrito em [Montar volumes](#mount-volumes).

3. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela do StorSimple Snapshot Manager aparece.

4. No painel **Escopo**, clique com o botão direito no nó **Volumes** e selecione **Examinar volumes novamente**. Quando a verificação for concluída, uma lista de volumes deverá aparecer no painel **Resultados**.

5. No painel **Resultados**, clique com o botão direito no volume e selecione **Criar Grupo de Volumes**.

    ![Criar grupo de volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png)

6. Na caixa de diálogo **Criar Grupo de Volumes**, digite um nome para o grupo de volumes, atribua volumes a ele e clique em **OK**.

7. No painel **Escopo**, expanda o nó **Grupos de Volumes**. O novo grupo de volumes deve aparecer no nó **Grupos de Volumes**.

8. Clique com o botão direito no nome do grupo de volumes.

    - Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 

    - Para agendar um backup automático, clique em **Criar Política de Backup**. Na página **Geral**, selecione um grupo de volumes da lista. Na página **Agenda**, insira os detalhes da agenda. Quando tiver terminado, clique em **OK**.

9. Para confirmar que o trabalho de backup foi iniciado, expanda o nó **Trabalhos** no painel **Escopo** e clique no nó **Executando**. A lista de trabalhos em execução no momento aparece no painel **Resultados**.

## Configurar e fazer backup de um volume espelhado dinâmico

Conclua as seguintes etapas para configurar o backup de um volume dinâmico espelhado:

- Etapa 1: Usar o Gerenciamento de Disco para criar um volume espelhado dinâmico 

- Etapa 2: Usar o StorSimple Snapshot Manager para configurar o backup

### Pré-requisitos

Antes de começar:

- Certifique-se de que o computador host e o dispositivo StorSimple estejam configurados corretamente. Para obter mais informações, vá até [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

- Instale e configure o StorSimple Snapshot Manager. Para saber mais, vá até [Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

- Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **Disco 1** e **Disco 2**.)

### Etapa 1: Usar o Gerenciamento de Disco para criar um volume espelhado dinâmico

O Gerenciamento de Disco é um utilitário do sistema para gerenciar discos rígidos e os volumes ou partições que eles contêm. Para obter mais informações sobre o Gerenciamento de Disco, vá até [Gerenciamento de Disco](https://technet.microsoft.com/library/cc770943.aspx) no site da Microsoft TechNet.

#### Para criar um volume espelhado dinâmico

1. Use qualquer uma das opções a seguir para iniciar o Gerenciamento de Disco: 

   - Abra a caixa **Executar**, digite **Diskmgmt.msc** e pressione Enter.

   - Inicie o Gerenciador do Servidor, expanda o nó **Armazenamento** e selecione **Gerenciamento de Disco**.

   - Inicie as **Ferramentas Administrativas**, expanda o nó **Gerenciamento do Computador** e selecione **Gerenciamento de Disco**.

2. Certifique-se de que você tenha dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **Disco 1** e **Disco 2**.) 

3. Na janela Gerenciamento de Disco, na coluna à direita do painel inferior, clique com o botão direito em **Disco 1** e selecione **Novo Volume Espelhado**.

    ![Novo Volume Espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png)

4. Na página do assistente **Novo Volume Espelhado**, clique em **Avançar**.

5. Na página **Selecionar Discos**, selecione o **Disco 2** no painel **Selecionado**, clique em **Adicionar** e clique em **Avançar**.

6. Na página **Atribuir Letra da Unidade ou Caminho**, aceite os padrões e clique em **Avançar**.

7. Na página **Formatar Volume**, na caixa **Tamanho da Unidade de Alocação**, selecione **64K**. Marque a caixa de seleção **Executar uma formatação rápida** e clique em **Avançar**.

8. Na página **Concluindo o Novo Volume Espelhado**, examine as configurações e clique em **Concluir**.

9. Será exibida uma mensagem indicando que o disco básico será convertido em um disco dinâmico. Clique em **Sim**.

    ![Mensagem de conversão de disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png)

10. No gerenciamento de disco, verifique se os discos 1 e 2 são mostrados como volumes dinâmicos espelhados. **Dinâmico** deve aparecer na coluna de status, e a cor da barra de capacidade deve mudar para vermelho, indicando um volume espelhado.

    ![Discos dinâmicos espelhados do Gerenciamento de Disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png)
 
### Etapa 2: Usar o StorSimple Snapshot Manager para configurar o backup

Use o procedimento a seguir para configurar um volume espelhado dinâmico, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

#### Para configurar o backup de um volume espelhado dinâmico

1. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela do StorSimple Snapshot Manager aparece. 

2. No painel **Escopo**, clique com o botão direito no nó **Volumes** e selecione **Examinar volumes novamente**. Quando a verificação for concluída, uma lista de volumes deverá aparecer no painel **Resultados**. O volume dinâmico espelhado é listado como um único volume.

3. No painel **Resultados**, clique no volume dinâmico espelhado e clique em **Criar Grupo de Volumes**.

4. Na caixa de diálogo **Criar Grupo de Volumes**, digite um nome para o grupo de volumes, atribua o volume dinâmico espelhado a esse grupo e clique em **OK**.

5. No painel **Escopo**, expanda o nó **Grupos de Volumes**. O novo grupo de volumes deve aparecer no nó **Grupos de Volumes**.

6. Clique com o botão direito no nome do grupo de volumes.

    - Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 

    - Para agendar um backup automático, clique em **Criar Política de Backup**. Na página **Geral**, selecione o grupo de volumes na lista. Na página **Agenda**, insira os detalhes da agenda. Quando tiver terminado, clique em **OK**.

7. Você pode monitorar o trabalho de backup enquanto ele é executado. No painel **Escopo**, expanda o nós **Trabalhos** e clique em **Em execução**. Os detalhes do trabalho aparecem no painel **Resultados**. Quando o trabalho de backup é concluído, os detalhes são transferidos para a lista de trabalhos **Últimas 24 horas**.

## Próximas etapas

[Usar o StorSimple Snapshot Manager para gerenciar grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx

<!---HONumber=August15_HO6-->
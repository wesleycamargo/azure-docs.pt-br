<properties
   pageTitle="Gerenciar seus volumes do StorSimple | Microsoft Azure"
   description="Explica como adicionar, modificar, monitorar e excluir volumes do StorSimple e como colocá-los offline, se necessário."
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
   ms.date="09/15/2015"
   ms.author="v-sharos" />

# Usar o serviço StorSimple Manager para gerenciar volumes

## Visão geral

Este tutorial explica como usar o serviço StorSimple Manager para criar e gerenciar volumes no dispositivo StorSimple e dispositivo virtual StorSimple.

O serviço StorSimple Manager é uma extensão do Portal do Azure que permite gerenciar a solução do StorSimple em uma única interface da Web. Além de gerenciar volumes, você pode usar o serviço StorSimple Manager para criar e gerenciar serviços do StorSimple, exibir e gerenciar dispositivos, exibir alertas, exibir e gerenciar políticas de backup e o catálogo de backup.

> [AZURE.NOTE]O Azure StorSimple pode criar somente volumes pequenos de provisionamento. Não é possível criar volumes parcialmente ou totalmente provisionados em um sistema do Azure StorSimple.
>
> Provisionamento dinâmico é uma tecnologia de virtualização no qual o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o Azure StorSimple usa o provisionamento dinâmico para alocar espaço suficiente para atender às necessidades atuais. A natureza elástica do armazenamento em nuvem facilita essa abordagem porque o Azure StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às diferentes demandas.

## A página Volumes

A página **Volumes** página permite que você gerencie os volumes de armazenamento provisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Ela exibe a lista de volumes no seu dispositivo StorSimple.

 ![Página Volumes](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Um volume consiste em uma série de atributos:

- **Nome** – Um nome descritivo que deve ser exclusivo e ajuda a identificar o volume. Esse nome também é usado em relatórios de monitoramento ao filtrar um volume específico.

- **Status** – Pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm permissão de acesso para usar o volume.

- **Capacidade** – Especifica quão grande é o volume, como visto pelo iniciador (servidor). Capacidade especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Os volumes são provisionados de forma dinâmica e ocorre a eliminação de duplicação de dados. Isso implica que o seu dispositivo não aloca previamente capacidade de armazenamento físico internamente ou em nuvem de acordo com a capacidade de volume configurada. A capacidade de volume é alocada e consumida por demanda.

- **Acesso** – Especifica os iniciadores (servidores) que podem acessar este volume. Os iniciadores que não são membros do registro de controle de acesso (ACR) que está associado com o volume não verão o volume.

- **Monitoramento** – Especifica se um volume está sendo monitorado. Um volume terá o monitoramento ativado por padrão quando ele é criado. O monitoramento será, no entanto, desabilitado para um clone de volume. Para habilitar o monitoramento para um volume, siga as instruções no Monitor de um volume.

As tarefas mais comuns associadas a um volume são:

- Adicionar um volume 
- Modificar um volume 
- Excluir um volume 
- Colocar um volume offline 
- Monitorar um volume 

## Adicionar um volume

Você [criou um volume](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) durante a implantação da solução StorSimple. Adicionar um volume é um procedimento semelhante.

### Para adicionar um volume

1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.

2. Selecione um contêiner de volume e clique na seta na linha correspondente para acessar os volumes associados ao contêiner.

3. Clique em **Adicionar** na parte inferior da página. Isso inicia o assistente Adicionar um volume.

     ![Configurações básicas do assistente para Adicionar volume](./media/storsimple-manage-volumes/HCS_AddVolume1M.png)

4. No assistente Adicionar um volume, em **Configurações Básicas**, faça o seguinte:

  1. Digite um **Nome** para o seu volume.
  2. Especifique a **Capacidade Provisionada** para o seu volume em GB ou TB. A capacidade deve ser entre 1 GB e 64 TB para um dispositivo físico. A capacidade máxima que pode ser provisionada para um volume em um dispositivo virtual StorSimple é de 30 TB.
  3. Na lista suspensa, selecione o **Tipo de Uso** para o seu volume. Escolha **Volume de arquivamento** se você estiver trabalhando com dados de arquivamento acessados com menos frequência. Para todos os outros casos de uso, selecione **Volume em camadas**. (Volumes em camadas eram chamados anteriormente de volumes primários).
  5. Clique no ícone de seta ![Ícone de seta](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)para ir para a página **Configurações Adicionais**.

     ![Configurações adicionais do assistente para Adicionar volume](./media/storsimple-manage-volumes/HCs_AddVolume2M.png)
   
5. Em **Configurações Adicionais**, adicione um novo registro de controle de acesso (ACR):
  
  1. Selecione um registro de controle de acesso (ACR) na lista suspensa. Como opção, você também pode abrir um novo ACR. ACRs determinam quais hosts podem acessar os volumes fazendo a correspondência do IQN do host com aqueles listados no registro.
  2. Em **Backup padrão para este volume**, recomendamos que você habilite um backup padrão marcando a caixa de seleção **Habilitar**.
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes/HCS_CheckIcon.png) para criar o volume com as configurações especificadas.

O seu novo volume agora está pronto para uso.

## Modificar um volume

Modifica um volume quando você precisa expandi-lo ou alterar os hosts que acessam o volume.

> [AZURE.IMPORTANT]
>
> - Se você modificar o tamanho do volume no dispositivo, o tamanho do volume precisa ser alterado no host também. 
> - As etapas do lado do host descritas aqui servem para o Windows Server 2012 (2012R2). Procedimentos para Linux ou para outros sistemas operacionais host serão diferentes. Consulte as instruções do sistema operacional host ao modificar o volume em um host que executa outro sistema operacional. 

### Para modificar um volume

1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêiner de Volume**. Esta página lista, em formato de tabela, todos os contêineres de volume associados ao dispositivo.

2. Selecione um contêiner de volume e clique nele para exibir a lista de todos os volumes dentro do contêiner.

3. Na página **Volumes** selecione um volume e clique em **Modificar**.

4. No assistente Adicionar um volume, em **Configurações Básicas**, você pode fazer o seguinte:

  - Editar o **Nome** e o **Tipo de Aplicativo**.
  - Aumentar a **Capacidade Provisionada**. A **Capacidade Provisionada** só pode ser aumentada. Não é possível reduzir um volume depois que ele é criado.

    > [AZURE.NOTE]Você não pode alterar o contêiner de volume depois que ele é atribuído a um volume.

5. Em **Configurações Adicionais**, você pode fazer o seguinte:

  - Modificar os ACRs, desde que o volume esteja offline. Se o volume estiver online, você precisará colocá-lo offline primeiro. Consulte as etapas em [Colocar um volume offline](#take-a-volume-offline) antes de modificar o ACR.
  - Modificar a lista de ACRs depois que o volume está offline.
 
    > [AZURE.NOTE]Você não pode alterar a opção **Habilitar um backup padrão** para o volume.

6. Salve suas alterações, clicando no ícone de verificação ![check-icon](./media/storsimple-manage-volumes/HCS_CheckIcon.png). O portal exibirá uma mensagem de atualização do volume. Ele exibirá uma mensagem de êxito quando o volume for atualizado com êxito.

7. Se estiver expandindo um volume, conclua as seguintes etapas no computador host do Windows:

   1. Acesse **Gerenciamento do Computador** ->**Gerenciamento de Disco**.
   2. Clique com o botão direito do mouse em **Gerenciamento de Disco** e selecione **Examinar Discos Novamente**.
   3. Na lista de discos, selecione o volume que você atualizou, clique com o botão direito do mouse e selecione **Estender Volume**. O Assistente para Estender Volume é iniciado. Clique em **Próximo**.
   4. Conclua o assistente com a aceitação dos valores padrão. Depois que o assistente for concluído, o volume deve mostrar o tamanho aumentado.

![Vídeo disponível](./media/storsimple-manage-volumes/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como expandir um volume, clique [aqui](http://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume).

## Colocar um volume offline

Talvez seja necessário colocar um volume offline quando você estiver planejando modificá-lo ou excluí-lo. Quando um volume está offline, não está disponível para acesso de leitura / gravação. Você precisará colocar o volume offline no host e no dispositivo. Execute as seguintes etapas para colocar um volume offline.

### Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em uso antes de colocá-lo offline.

2. Coloque o volume offline no host primeiro. Isso elimina qualquer risco de corrupção de dados no volume. Para etapas específicas, consulte as instruções do sistema operacional do host.

3. Depois que o host estiver offline, coloque o volume no dispositivo offline executando as seguintes etapas:

  1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**. A guia **Contêineres de Volume** lista todos os contêineres de volume associados ao dispositivo em formato de tabela.
  2. Selecione um contêiner de volume e clique nele para exibir a lista de todos os volumes dentro do contêiner.
  3. Selecione um volume e clique em **Colocar offline**.
  4. Quando solicitado a confirmar, clique em **Sim**. Agora, o volume deve estar offline.

    Depois que um volume está offline, a opção **Colocar Online** ficará disponível.

> [AZURE.NOTE]O comando **Colocar Offline** envia uma solicitação para o dispositivo para colocar o volume offline. Se os hosts ainda estiverem usando o volume, ocorrerão conexões interrompidas, mas não ocorrerá nenhuma falha ao colocar o volume offline.

## Excluir um volume

> [AZURE.IMPORTANT]Você pode excluir um volume apenas se ele estiver offline.

Conclua as seguintes etapas para excluir um volume.

### Para excluir um volume

1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.

2. Selecione o contêiner de volume que possui o volume que você deseja excluir. Clique no contêiner de volume para acessar a página **Volumes**.

3. Todos os volumes associados a este contêiner são exibidos em formato de tabela. Verifique o status do volume que deseja excluir. Se o volume que você deseja excluir não estiver offline, coloque-o offline em primeiro lugar, seguindo as etapas em [Colocar um volume offline](#take-a-volume-offline).

4. Depois que o volume estiver offline, clique em **Excluir** na parte inferior da página.

5. Quando solicitado a confirmar, clique em **Sim**. O volume será excluído e a página **Volumes** mostrará a lista atualizada de volumes dentro do contêiner.

## Monitorar um volume

O monitoramento de volume permite coletar estatísticas de E/S para um volume. O monitoramento é habilitado por padrão para os primeiros 32 volumes que você criar. O monitoramento de volumes adicionais é desabilitado por padrão. Monitoramento de volumes clonados também será desabilitado por padrão.

Execute as seguintes etapas para habilitar ou desabilitar o monitoramento para um volume.

### Para habilitar ou desabilitar o monitoramento de volume

1. Na página **Dispositivos**, selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia **Contêineres de Volume**.

2. Selecione o contêiner de volume em que reside o volume e, em seguida, clique no contêiner de volume para acessar a página **Volumes**.

3. Todos os volumes associados a este contêiner são listados em formato de tabela. Clique e selecione o volume ou o clone do volume.

4. Na parte inferior da página, clique em **Modificar**.

5. No Assistente Modificar Volume, em **Configurações Básicas**, selecione **Habilitar** ou **Desabilitar** da lista suspensa **Monitoramento**.

    ![Modificar as Configurações Básicas de um volume](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)


## Próximas etapas

- Saiba como [Clonar um volume StorSimple](storsimple-clone-volume.md).
- Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=Oct15_HO3-->
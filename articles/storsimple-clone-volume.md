<properties
   pageTitle="Clonar seu volume StorSimple"
   description="Descreve os tipos diferentes de clone e quando usá-los, e explica como você pode usar um conjunto de backups para clonar um volume individual."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/13/2015"
   ms.author="v-sharos" />

# Clonar um volume StorSimple

## Visão geral

A página **Catálogo de Backup** exibe todos os conjuntos de backup criados quando são realizados backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

  ![backup-catalog](./media/storsimple-clone-volume/HCS_BackupCatalog.png)

Este tutorial descreve como você pode usar um conjunto de backups para clonar um volume individual. Ele também explica a diferença entre os clones *transitório* e *permanente*.

## Criar clone de um volume

Você pode criar um clone no mesmo dispositivo, em outro dispositivo ou mesmo em uma máquina virtual usando um instantâneo local ou na nuvem.

## Para clonar um volume

1. Na página do serviço Gerenciador StorSimple, clique no **Catálogo de backup** e selecione um conjunto de backups.

2. Expanda o conjunto de backups para exibir os volumes associados. Clique e selecione um volume no conjunto de backups.

     ![Clone](./media/storsimple-clone-volume/HCS_Clone.png)

3. Clique em **Clonar** para começar a clonar o volume selecionado.

4. No assistente Clonar Volume, em **Especificar nome e local**:

  1. Identificar um dispositivo de destino. Esse é o local onde o clone será criado. Você pode escolher o mesmo dispositivo ou especificar outro dispositivo.

        >  [AZURE.NOTE] Certifique-se de que a capacidade exigida para o clone é inferior a capacidade disponível no dispositivo de destino.
  2. Especifique um nome de volume exclusivo para o clone. O nome deve conter entre 3 e 127 caracteres.
  3. Clique no ícone de seta ![ícone-de-seta](./media/storsimple-clone-volume/HCS_ArrowIcon.png)para prosseguir para a próxima página.

5. Em **Especificar os hosts que podem usar este volume**:

  1. Especificar um registro de controle de acesso (ACR) para o clone. Você pode adicionar um novo ACR ou escolher na lista existente.
  2. Clique no ícone de verificação ![ícone-de-verificação](./media/storsimple-clone-volume/HCS_CheckIcon.png)para concluir a operação.

6. Um trabalho de clone será iniciado e você será notificado quando o clone for criado com êxito. Clique em **Exibir Trabalho** para monitorar o trabalho de clone na página **Trabalhos**.

7. Depois do trabalho de clone ser concluído:

  1. Vá para a página **Dispositivos** e selecione a guia **Contêineres do Volume**.
  2. Selecione o contêiner do volume associado ao volume de origem clonado. Na lista de volumes, você deve ver o clone que acabou de criar.

>[AZURE.NOTE]O monitoramento e o backup padrão são desabilitados automaticamente em um volume clonado.

Um clone criado dessa maneira é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [Clones transitórios versus permanentes](#transient-vs.-permanent-clones).

O clone é agora um volume normal e qualquer operação que é possível em um volume estará disponível para o clone. Você precisará configurar este volume para qualquer backup.

## Clones transitórios versus permanentes

Você pode clonar um volume específico a partir de um conjunto de backups. Um clone criado dessa maneira é um clone *transitório*. O clone transitório terá referências para o volume original e usará esse volume para ler durante a gravação local. Isso poderá resultar em baixo desempenho, especialmente se o volume clonado for grande.

Depois de fazer um instantâneo de nuvem de um clone transitório, o clone resultante será um clone *permanente*. O clone permanente é independente e não tem nenhuma referência para o volume original do qual foi clonado. Para melhorar o desempenho, é recomendável que você crie clones permanentes.

## Cenários para os clones transitórios e permanentes

As seções a seguir descrevem as situações de exemplo nas quais os clones transitórios e permanentes podem ser usados.

### Recuperação ao nível do item com um clone transitório

Você precisa recuperar um arquivo de apresentação do Microsoft PowerPoint com um ano. O administrador de TI identifica o backup específico desse intervalo de tempo, em seguida, filtra o volume. Então, o administrador clona o volume, localiza o arquivo que você está procurando e fornece a você. Nesse cenário, é usado um clone transitório.
 
### Testando no ambiente de produção com um clone permanente

Você precisa verificar um bug de teste no ambiente de produção. Você cria um clone do volume no ambiente de produção. Para melhorar o desempenho, você precisa fazer um instantâneo de nuvem do clone. O volume clonado agora é independente, o que resulta em um desempenho mais rápido. Nesse cenário, é usado um clone permanente.

## Próximas etapas
Saiba como [restaurar um volume StorSimple a partir de um conjunto de backups](storsimple-restore-from-backup-set.md).



<!--HONumber=52-->

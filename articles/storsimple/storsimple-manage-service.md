<properties 
   pageTitle="Implante seu serviço do StorSimple Manager | Microsoft Azure"
   description="Explica como criar e excluir o serviço StorSimple Manager no Portal de Gerenciamento, além de descrever como gerenciar a chave de registro de serviço."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="v-sharos" />

# Implantar o serviço StorSimple Manager

## Visão geral

O serviço StorSimple Manager é executado no Microsoft Azure e se conecta a vários dispositivos StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar os dispositivos no Portal de Gerenciamento do Microsoft Azure em execução em um navegador. Isso permite monitorar todos os dispositivos que estão conectados ao serviço StorSimple Manager de um local único e central, minimizando a sobrecarga administrativa.

A página de aterrissagem do StorSimple Manager lista todos os serviços StorSimple Manager que você pode usar para gerenciar os dispositivos de armazenamento do StorSimple. Para cada serviço StorSimple Manager, as informações a seguir são apresentadas na página do StorSimple Manager:

- **Nome**: o nome que foi atribuído ao serviço StorSimple Manager quando ele foi criado. O nome do serviço não pode ser alterado depois que o serviço é criado.

- **Status**: o status do serviço, que pode ser **Ativo**, **Criando** ou **Online**.

- **Local**: a localização geográfica em que o dispositivo StorSimple será implantado.

- **Assinatura**: a assinatura de cobrança associada ao seu serviço.

As tarefas comuns que podem ser executadas pela página do StorSimple Manager são:

- Criar um serviço
- Excluir um serviço
- Obtenha a chave de registro do serviço
- Regenerar a chave de registro do serviço

Este tutorial descreve como executar cada uma dessas tarefas.

## Criar um serviço

Use a opção **Criação Rápida** para criar um serviço StorSimple Manager se deseja implantar seu dispositivo StorSimple. Para criar um serviço, você precisa ter:

- Uma assinatura com um Enterprise Agreement
- Uma conta de armazenamento ativa do Microsoft Azure
- As informações de cobrança que são usadas para gerenciamento de acesso

Também é possível optar por gerar uma conta de armazenamento padrão ao criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação.

Execute as etapas a seguir para criar um serviço.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## Excluir um serviço

Antes de excluir um serviço, verifique se nenhum dispositivo conectado está usando ele. Se o serviço estiver em uso, desative os dispositivos conectados. A operação de desativação desfaz a conexão entre o dispositivo e o serviço, mas preserva os dados do dispositivo na nuvem.

[AZURE.IMPORTANT]Depois que um serviço é excluído, a operação não pode ser revertida. Qualquer dispositivo que estava usando o serviço precisará ser redefinida para as configurações de fábrica para que possa ser usado com outro serviço. Nesse cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.

Execute as etapas a seguir para excluir um serviço.

### Para excluir um serviço

1. Na página **Serviço StorSimple Manager**, selecione o serviço que deseja excluir.

1. Clique em **Excluir** na parte inferior da página.

1. Clique em **Sim** na notificação de confirmação. Pode levar alguns minutos para que o serviço seja excluído.

## Obtenha a chave de registro do serviço

Depois de ter criado um serviço com êxito, você precisará registrar o dispositivo StorSimple no serviço. Para registrar seu primeiro dispositivo StorSimple, será necessária a chave de registro do serviço. Para registrar dispositivos adicionais em um serviço StorSimple existente, serão necessárias a chave de registro e a chave de criptografia dos dados de serviço (que é gerada durante o registro do primeiro dispositivo). Para obter mais informações sobre a chave de criptografia dos dados de serviço, consulte [Segurança do StorSimple](storsimple-security.md). Você pode obter a chave de registro acessando **Chave de Registro** na página **Serviços**.

Execute as etapas a seguir para obter a chave de registro do serviço.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Mantenha a chave de registro do serviço em local seguro. Você precisará dessa chave, bem como da chave de criptografia dos dados de serviço, para registrar dispositivos adicionais nesse serviço. Depois de obter a chave de registro do serviço, você precisará configurar o dispositivo usando o Windows PowerShell para interface do StorSimple.

Para obter detalhes sobre como usar essa chave de registro, consulte [Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-2-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## Regenerar a chave de registro do serviço

Você precisará regenerar uma chave de registro de serviço se tiver que executar a rotação de chave ou se a lista de administradores de serviço tiver mudado. Quando você regenera a chave, a nova chave é usada somente para registrar dispositivos subsequentes. Os dispositivos que já foram registrados não serão afetados por esse processo.

Execute as etapas a seguir para regenerar uma chave de registro de serviço.

### Para regenerar a chave de registro de serviço

1. Na página **Serviço StorSimple Manager**, clique em **Chave de Registro**.

1. Na caixa de diálogo **Chave de Registro de Serviço**, clique em **Regenerar**.

1. Você verá uma mensagem de confirmação. Clique em **OK** para continuar com a regeneração.

1. Uma nova chave de registro de serviço será exibida.

1. Copie essa chave e salve-a para registrar todos os novos dispositivos nesse serviço.

1. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-service/HCS_CheckIcon.png) para fechar essa caixa de diálogo.


## Próximas etapas

- Saiba mais sobre o [processo de implantação do StorSimple](storsimple-deployment-walkthrough.md).

- [Saiba mais sobre como gerenciar sua conta de armazenamento do StorSimple](storsimple-manage-storage-accounts.md).

- Saiba mais sobre como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=Sept15_HO3-->
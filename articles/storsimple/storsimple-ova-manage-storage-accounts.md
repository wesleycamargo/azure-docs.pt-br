<properties 
   pageTitle="Gerenciar sua conta de armazenamento do StorSimple | Microsoft Azure"
   description="Explica como você pode usar a página Configurar do StorSimple Manager para adicionar, editar, excluir ou alternar entre as chaves de uma conta de armazenamento associadas ao StorSimple Virtual Array."
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
   ms.date="03/02/2016"
   ms.author="alkohli" />

# Usar o serviço StorSimple Manager para gerenciar sua conta de armazenamento

## Visão geral

A página **Configurar** apresenta os parâmetros de serviços globais que podem ser criados no serviço StorSimple Manager. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

- Contas de armazenamento 
- Registros de controle de acesso 

Este tutorial explica como você pode usar a página **Configurar** para adicionar, editar ou excluir contas de armazenamento para sua Matriz Virtual StorSimple. As informações neste tutorial aplicam-se apenas ao StorSimple Virtual Array que executa o software lançado em março de 2016 GA.

 ![Configurar página](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)

As contas de armazenamento contém as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, essas credenciais podem ser o nome da conta e a chave de acesso primário, por exemplo.

Na página **Configurar**, todas as contas de armazenamento que são criadas para a assinatura de cobrança são exibidas em formato de tabela contendo as seguintes informações:

- **Nome** – O nome exclusivo atribuído à conta quando a mesma foi criada.
- **SSL habilitado** – Se o SSL está ativado e a comunicação do dispositivo para a nuvem é feita pelo canal seguro.

As tarefas mais comuns relacionadas a contas de armazenamento que podem ser executadas na página **Configurar** são:

- Adicionar uma conta de armazenamento 
- Editar uma conta de armazenamento 
- Excluir uma conta de armazenamento 


## Tipos de contas de armazenamento

Há três tipos de contas de armazenamento que podem ser usadas com o dispositivo StorSimple.

- **Contas de armazenamento geradas automaticamente** – Como o nome sugere, esse tipo de conta de armazenamento é gerada automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como essa conta de armazenamento é criada, confira [Criar um novo serviço](storsimple-ova-manage-service.md#create-a-service). 
- **Contas de armazenamento na assinatura do serviço** – Essas são as contas de armazenamento do Azure que estão associadas com a mesma assinatura que o serviço. Para saber mais sobre como essas contas de armazenamento são criadas, consulte [Sobre Contas de Armazenamento do Azure](../storage/storage-create-storage-account.md). 
- **Contas de armazenamento fora do serviço de assinatura** – Essas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e que provavelmente existiam antes da criação do serviço.

## Adicionar uma conta de armazenamento

Você pode adicionar uma conta de armazenamento à configuração de serviço do StorSimple Manager fornecendo um nome amigável exclusivo e credenciais de acesso que são vinculadas à conta de armazenamento. Você também tem a opção de habilitar o modo secure sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem específico. Enquanto a conta de armazenamento está sendo salvo, o serviço tenta se comunicar com o seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu serão autenticados neste momento. Uma conta de armazenamento será criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

O procedimento para adicionar uma conta de armazenamento é detalhado abaixo.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## Editar uma conta de armazenamento

Você pode editar uma conta de armazenamento usada pelo seu dispositivo. Se você editar uma conta de armazenamento que esteja atualmente em uso, os campos disponíveis a serem modificados são a chave de acesso e o modo SSL da conta. Você pode fornecer a nova chave de acesso de armazenamento ou modificar a seleção de **Habilitar modo SSL** e salvar as configurações atualizadas.

#### Para editar uma conta de armazenamento

1. Na página inicial do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, clique em **Configurar**.

2. Clique em **Adicionar/Editar Contas de Armazenamento**.

3. Na caixa de diálogo **Adicionar/Editar conta de armazenamento**:

  1. Na lista suspensa de **Contas de Armazenamento**, escolha uma conta existente que você deseja modificar.
  2. Se necessário, você poderá modificar a seleção em **Habilitar modo SSL**.
  3. Você pode escolher gerar novamente as chaves de acesso da conta de armazenamento. Para obter mais informações, confira [Regenerar as chaves da conta de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys). Forneça a nova chave de conta de armazenamento. Para uma conta de armazenamento do Azure, essa é a chave de acesso principal. 
  4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-manage-storage-accounts/checkicon.png) para salvar as configurações. As configurações serão atualizadas na página **Configurar**. 
  5. Na parte inferior da página, clique em **Salvar** para salvar as configurações recentemente atualizadas. 

     ![Editar uma conta de armazenamento](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## Excluir uma conta de armazenamento

> [AZURE.IMPORTANT] Será possível excluir uma conta de armazenamento somente se ela não estiver em uso. Se uma conta de armazenamento estiver em uso, você será notificado.

#### Para excluir uma conta de armazenamento

1. Na página inicial do serviço StorSimple Manager, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, clique em **Configurar**.

2. Na lista de contas de armazenamento em formato de tabela, passe o mouse sobre a conta que você deseja excluir.

3. Um ícone de exclusão (**x**) será exibido na coluna mais à direita para essa conta de armazenamento. Clique no ícone **x** para excluir as credenciais.

4. Quando solicitado a confirmar, clique em **Sim** para continuar com a exclusão. A listagem de tabela será atualizada para refletir as alterações.

5. Na parte inferior da página, clique em **Salvar** para salvar as configurações recentemente atualizadas.


## Próximas etapas

- Aprenda como [administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0309_2016-->
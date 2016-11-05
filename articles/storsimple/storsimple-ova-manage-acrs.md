---
title: Gerenciar registros de controle de acesso para a matriz Virtual StorSimple | Microsoft Docs
description: Descreve como gerenciar registros de controle de acesso (ACRs) para determinar quais hosts podem se conectar a um volume na matriz Virtual StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2016
ms.author: alkohli

---
# Use o serviço StorSimple Manager para gerenciar registros de controle de acesso para o StorSimple Virtual Array
## Visão geral
Registros de controle de acesso (ACRs) permitem que você especifique quais hosts podem se conectar a um volume na matriz de StorSimple Virtual (também conhecida como o local no dispositivo virtual StorSimple). Os ACRs são definidos para um volume específico e contêm os IQNs (Nomes Qualificados iSCSI) dos hosts. Quando um host tenta se conectar a um volume, o dispositivo verifica o nome do IQN no ACR associado a esse volume e, se houver correspondência, a conexão será estabelecida. A seção de **registros do controle de acesso** na página **Configurar** exibe todos os registros de controle de acesso com o IQN correspondente dos hosts.

Este tutorial explica as seguintes tarefas comuns relacionadas ao ACR:

* Obter o IQN
* Adicionar um registro de controle de acesso 
* Editar um registro de controle de acesso 
* Excluir um registro de controle de acesso 

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume. 
> * Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.
> 
> 

## Obter o IQN
Execute as seguintes etapas para obter o IQN de um host do Windows que está executando o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## Adicionar um ACR
Use a página **Configuração** do serviço StorSimple Manager para adicionar ACRs. Normalmente, você associará um ACR a um volume.

Para obter informações sobre como associar um ACR a um volume, vá para [Adicionar um volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume.
> 
> 

Execute as etapas a seguir para adicionar um ACR.

#### Para adicionar um ACR
1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configuração**.
   
    ![guia de configuração](./media/storsimple-ova-manage-acrs/acr1.png)
2. Na listagem de tabela em **Registros de controle de acesso**, forneça um **Nome** para seu ACR.
3. Forneça o nome IQN do host do Windows em **Nome do Iniciador iSCSI**.
4. Clique em **Salvar** na parte inferior da página para salvar o ACR recentemente criado. Você verá a seguinte mensagem de confirmação.
   
    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr2.png)
5. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem de tabela será atualizada para refletir essa adição.

## Editar um ACR
Você usa a página **Configuração** no portal clássico do Azure para editar ACRs.

> [!NOTE]
> Você deve modificar somente os ACRs que não estejam em uso no momento. Para editar um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.
> 
> 

Execute as etapas a seguir para editar um ACR.

#### Para editar um ACR
1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configuração**.
2. Na listagem de tabela dos registros de controle de acesso, passe o mouse sobre o ACR que deseja modificar.
3. Forneça um novo nome e/ou IQN para o ACR.
4. Clique em **Salvar** na parte inferior da página para salvar o ACR modificado. Você verá uma mensagem de confirmação.
5. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem de tabela será atualizada para refletir essa alteração.

## Excluir um registro de controle de acesso
Use a página **Configuração** no Portal clássico do Azure para excluir ACRs.

> [!NOTE]
> * Você deve excluir somente os ACRs que não estejam em uso no momento. Para excluir um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.
> * Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.
> 
> 

Execute as etapas a seguir para excluir um registro de controle de acesso.

#### Para excluir um registro de controle de acesso
1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configuração**.
2. Na listagem de tabela dos ACRs (registros de controle de acesso), passe o mouse sobre o ACR que deseja excluir.
3. Um ícone de exclusão (**x**) será exibido na coluna mais à direita do ACR selecionado. Clique no ícone **x** para excluir o ACR. Você verá a seguinte mensagem de confirmação.
   
    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr3.png)
4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem de tabela será atualizada para refletir a exclusão.

## Próximas etapas
* Saiba mais sobre [Adicionar volumes e configurar ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

<!---HONumber=AcomDC_0511_2016-->
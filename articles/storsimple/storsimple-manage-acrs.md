<properties 
   pageTitle="Gerenciar registros de controle de acesso no StorSimple | Microsoft Azure"
   description="Descreve como usar os ACRs (registros de controle de acesso) para determinar quais hosts podem se conectar a um volume no dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="alkohli" />

# Usar o serviço StorSimple Manager para gerenciar registros de controle de acesso

## Visão geral

Os ACRs (registros de controle de acesso) permitem especificar quais hosts podem se conectar a um volume no dispositivo StorSimple. Os ACRs são definidos para um volume específico e contêm os IQNs (Nomes Qualificados iSCSI) dos hosts. Quando um host tenta se conectar a um volume, o dispositivo verifica o nome do IQN no ACR associado a esse volume e, se houver correspondência, a conexão será estabelecida. A seção de registros do controle de acesso na página **Configurar** exibe todos os registros de controle de acesso com o IQN correspondente dos hosts.

Este tutorial explica as seguintes tarefas comuns relacionadas ao ACR:

- Adicionar um registro de controle de acesso 
- Editar um registro de controle de acesso 
- Excluir um registro de controle de acesso 

> [AZURE.IMPORTANT] 
> 
> - Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume. 
> - Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.

## Adicionar um registro de controle de acesso

Use a página **Configurar** do serviço StorSimple Manager para adicionar ACRs. Normalmente, você associará um ACR a um volume.

Execute as etapas a seguir para adicionar um ACR.

#### Para adicionar um registro de controle de acesso

1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configurar**.

2. Na listagem de tabela em **Registros de controle de acesso**, forneça um **Nome** para seu ACR.

3. Forneça o nome IQN do host do Windows em **Nome do Iniciador iSCSI**. Para obter o IQN do host do Windows Server, siga este procedimento:

   - Inicie o iniciador Microsoft iSCSI no host do Windows.
   - Na janela **Propriedades do Iniciador iSCSI**, na guia **Configuração** selecione e copie a cadeia de caracteres do campo **Nome do Iniciador**.
   - Cole essa cadeia de caracteres no campo **Nome do Iniciador iSCSI** na tabela de ACRs no Portal clássico do Azure.

4. Clique em **Salvar** para salvar o ACR recentemente criado. A listagem de tabela será atualizada para refletir essa adição.

## Editar um registro de controle de acesso

Use a página **Configurar** no Portal clássico do Azure para editar ACRs.

> [AZURE.NOTE] Você pode modificar somente os ACRs que não estejam em uso no momento. Para editar um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.

Execute as etapas a seguir para editar um ACR.

#### Para editar um registro de controle de acesso

1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configurar**.

2. Na listagem de tabela dos registros de controle de acesso, passe o mouse sobre o ACR que deseja modificar.

3. Forneça um novo nome e/ou IQN para o ACR.

4. Clique em **Salvar** para salvar o ACR modificado. A listagem de tabela será atualizada para refletir essa alteração.

## Excluir um registro de controle de acesso

Use a página **Configurar** no Portal clássico do Azure para excluir ACRs.

> [AZURE.NOTE] Você pode excluir somente os ACRs que não estejam em uso no momento. Para excluir um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.

Execute as etapas a seguir para excluir um registro de controle de acesso.

#### Para excluir um registro de controle de acesso

1. Na página de aterrissagem do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e clique na guia **Configurar**.

2. Na listagem de tabela dos ACRs (registros de controle de acesso), passe o mouse sobre o ACR que deseja excluir.

3. Um ícone de exclusão (**x**) será exibido na coluna mais à direita do ACR selecionado. Clique no ícone **x** para excluir o ACR.

4. Quando a confirmação é solicitada, clique em **Sim** para continuar com a exclusão. A listagem de tabela será atualizada para refletir a exclusão.

## Próximas etapas

- Saiba mais sobre [como gerenciar volumes do StorSimple](storsimple-manage-volumes.md).

- Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_0218_2016-->
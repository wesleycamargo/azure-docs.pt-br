---
title: Definir configurações de uso em laboratórios de sala de aula dos Serviços de Laboratório do Azure | Microsoft Docs
description: Saiba como configurar o número de usuários para o laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM e muito mais.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964807"
---
# <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
Este artigo descreve como adicionar usuários ao laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM, entre outros. 


## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório
Se você tiver o **Restringir acesso** habilitado, adicione usuários (endereços de email) à lista.

1. Selecione **Usuários** no menu à esquerda.
2. Selecione **Adicionar usuários** na barra de ferramentas. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários**, insira endereços de email dos usuários em linhas separadas ou em uma única linha e separados por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Clique em **Salvar**. Você vê os endereços de email de usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Enviar link de registro para estudantes
O procedimento a seguir contém etapas para enviar um link de registro para os usuários. Se a opção **Restringir o acesso** estiver habilitada para o laboratório, somente os usuários na lista de usuários poderão usar o link de registro para se registrar no laboratório. 

1. Alterne para a exibição **Usuários** selecionando **usuários** no menu à esquerda. 
2. Selecione o bloco **Obter link de registro**.

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de email e envie um email para o aluno. 

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registro de usuário**, selecione **Fechar**. 
4. Compartilhe o link de registro com um aluno para que ele possa se registrar para a aula. Caso a configuração **Restringir opção** esteja habilitada e você tenha uma lista de usuários na lista, faça as seguintes ações:
    1. Selecione o **endereço de email** do usuário na lista. 
    2. Você vê uma janela do seu programa de email padrão com o endereço do campo **PARA** preenchido. 
    3. Cole a **URL de registro** que você copiou anteriormente. 
    4. Envie o **email**. 

## <a name="view-users-registered-with-the-lab"></a>Exibir usuários registrados no laboratório

Selecione **usuários** no menu à esquerda para ver a lista de usuários registrados com o laboratório. 

![Lista de usuários registrados no laboratório](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Definir cotas por usuário
Defina cotas por usuário usando as seguintes etapas: 

1. Selecione **Usuários** no menu à esquerda.
2. Selecione **Cota por usuário: ilimitado** na barra de ferramentas. 
3. Na página **Cota por usuário**, selecione uma das seguintes opções: 
    1. **None**. Os usuários podem usar as máquinas virtuais apenas durante o horário agendado ou quando um proprietário de laboratório ativa máquinas virtuais por eles.
    2. **Ilimitado (padrão)**. Os usuários podem usar suas máquinas virtuais sem nenhuma restrição de horário.
    3. **Especifique o número de horas por usuário**. Os usuários podem usar suas máquinas virtuais durante o número definido de horas (especificado abaixo), além do horário agendado. Se você selecionar essa opção, insira o **número de horas** na caixa de texto. 

        ![Número de horas por usuário](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Clique em **Salvar**. 
5. Agora você vê os valores alterados na barra de ferramentas: **Cota por usuário: &lt;número de horas&gt;**. 

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> O [tempo de execução agendado de VMs](how-to-create-schedules.md) não conta com a cota alocada a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários ao carregar um arquivo CSV
Você também pode adicionar usuários ao carregar um arquivo CSV com endereços de email dos usuários.

1. Crie um arquivo CSV com endereços de email de usuários em uma coluna.

    ![Cota por usuário](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na página **Usuários** do laboratório, selecione **Carregar CSV** na barra de ferramentas.

    ![Botão Carregar CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o arquivo CSV com endereços de email do usuário. Quando você seleciona **Abrir** após selecionar o arquivo CSV, você vê a seguinte janela **Adicionar usuários**. A lista de endereço de email é preenchida com endereços de email do arquivo CSV. 

    ![Adicionar janela de usuários populada com endereços de email do arquivo CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **Salvar** na janela **Adicionar usuários**. 
5. Confirme que você vê os usuários na lista de usuários. 

    ![Lista de usuários adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gerenciar VMs do usuário
Após o registro dos alunos no Azure Lab Services usando o link de registro fornecido, você verá as VMs atribuídas aos alunos na guia **Máquinas virtuais**. 

![Máquinas virtuais atribuídas aos alunos](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

É possível executar as seguintes tarefas em uma VM de aluno: 

- Pare uma VM, se ela estiver em execução. 
- Inicie uma VM, se ela estiver parada. 
- Conecte-se à VM. 
- Exclua a VM. 
- Veja o número de horas que os usuários usaram a máquina virtual. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Atualizar o número de máquinas virtuais no laboratório
Para atualizar o número de máquinas virtuais no laboratório, execute as seguintes etapas da página **Máquinas Virtuais**:

1. Selecione **Máquinas virtuais** no menu à esquerda. 
2. Selecione **Capacidade do laboratório: &lt;número&gt; de computadores** na barra de ferramentas. 
3. Insira o **número** de máquinas virtuais.
4. Clique em **Salvar**.

    ![Máquinas virtuais no laboratório](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

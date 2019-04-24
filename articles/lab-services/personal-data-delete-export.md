---
title: Como excluir e exportar dados pessoais do Azure DevTest Labs | Microsoft Docs
description: Saiba como excluir e exportar dados pessoais do serviço Azure DevLast Labs para dar suporte às obrigações em conformidade com o GDPR (Regulamento Geral sobre a Proteção de Dados).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394848"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou excluir dados pessoais do Azure DevTest Labs
Este artigo fornece etapas para excluir e exportar dados pessoais do serviço Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quais dados pessoais o DevTest Labs coleta?
O DevTest Labs coleta duas partes principais de dados pessoais do usuário. São elas: o endereço de email do usuário e a ID de objeto de usuário. Essas informações são fundamentais para que o serviço forneça recursos em serviço a administradores de laboratório e usuários de laboratório.

### <a name="user-email-address"></a>Endereço de email do usuário
O DevTest Labs usa o endereço de email do usuário para enviar notificações de email de desligamento automático para usuários do laboratório. O email notifica os usuários do computador sendo desligado. Os usuários podem adiar ou ignorar o desligamento, se quiserem fazer isso. Você configura o endereço de email no nível de laboratório ou no nível de VM.

**Configurar email no laboratório:**

![Configurar email no nível de laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Configurar email na VM:**

![Configurar email no nível de VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID de objeto de usuário
O DevTest Labs usa a ID de objeto de usuário para mostrar as tendências de custo mês a mês e o custo das informações do recurso aos administradores de laboratório. Isso permite controlar custos e gerenciar limites do Lab. 

**Tendência de custo estimado para o mês atual:**
![Tendência de custo estimado para o mês atual](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Custo estimado do mês por recurso:**
![Custo estimado do mês por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que esses dados pessoais são necessários?
O serviço do DevTest Labs usa os dados pessoais para fins operacionais. Esses dados são fundamentais para que o serviço forneça recursos importantes. Se você definir uma política de retenção no endereço de email do usuário, os usuários do laboratório não receberão notificações por email de desligamento automático após o endereço de email ser excluído do sistema. Da mesma forma, o administrador de laboratório não poderá exibir as tendências de custo mês a mês e o custo por recurso dos computadores nos laboratórios, se as IDs de objeto de usuário forem excluídas com base em uma política de retenção. Portanto, esses dados precisam ser retidos enquanto o recurso do usuário estiver ativo no Lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como é possível excluir os dados pessoais do sistema?
Como usuário de laboratório, se você quiser excluir esses dados pessoais, poderá excluí-los do Lab. O serviço DevTest Labs tornará os dados pessoais excluídos anônimos, 30 dias após terem sido excluídos pelo usuário.

Por exemplo, se você excluir a VM ou remover o endereço de email, o serviço DevTest Labs tornará esses dados anônimos 30 dias após o recurso ser excluído. A política de retenção de 30 dias após a exclusão é garantir que forneça uma projeção de custos de mês a mês precisa ao administrador de laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como é possível solicitar uma exportação nos dados pessoais?
Como usuário de laboratório, você pode solicitar uma exportação nos dados pessoais armazenados pelo serviço DevTest Labs. Para solicitar uma exportação, navegue até a opção **Dados pessoais** na página de **Visão geral** do laboratório. Selecione o botão **Solicitar exportação** para iniciar a criação de um arquivo do Excel para download na conta de armazenamento do administrador do Lab. Você pode contatar o administrador do laboratório para exibir esses dados.

1. Selecione **Dados pessoais** no menu esquerdo. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Escolha o grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione a **conta de armazenamento** no grupo de recursos.
4. Na página **Conta de armazenamento**, selecione **Blobs**.

    ![Selecionar o bloco Blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o contêiner nomeado **labresourceusage** na lista de contêineres.

    ![Selecionar o contêiner de blob](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione a **pasta** com o nome do laboratório. Você localizará arquivos **csv** para **discos** e **máquinas virtuais** do laboratório nesta pasta. É possível baixar esses arquivos csv, filtrar o conteúdo para o usuário de laboratório que estiver solicitando acesso e compartilhá-los.

    ![Baixar arquivo CSV](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Próximos passos
Confira os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)

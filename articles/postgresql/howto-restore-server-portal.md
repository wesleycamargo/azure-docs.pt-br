---
title: Como restaurar um servidor no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7607a3e60eec39de61c785b8ff75a9f11fa02d0c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
O backup do Banco de Dados do Azure para servidores PostgreSQL é feito periodicamente para habilitar os recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="set-backup-configuration"></a>Definir configuração de backup

Escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor, na janela **Tipo de Preço**.

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geográfica ou local, não pode ser alternado.
>

Ao criar um servidor por meio do portal do Azure, a janela **Tipo de Preço** é onde você seleciona backups **Com Redundância Local** ou **Com Redundância Geográfica** para o servidor. Essa janela também é onde você seleciona o **Período de Retenção de Backup**: quanto tempo (em número de dias) você deseja que os backups de servidor sejam armazenados.

   ![Tipo de preço - Escolher redundância de backup](./media/howto-restore-server-portal/pricing-tier.png)

Para saber mais sobre como definir esses valores de durante a criação, confira o [guia de início rápido do Banco de Dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

O período de retenção de backup de um servidor pode ser alterado por meio das seguintes etapas:
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL. Essa ação abre a página **Visão geral** do runbook.
3. Selecione **Tipo de Preço** no menu, em **CONFIGURAÇÕES**. Usando o controle deslizante, você pode alterar o **Período de Retenção de Backup** entre 7 e 35 dias, conforme a sua preferência.
Na captura de tela abaixo, ele foi aumentado para 34 dias.
![Período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique em **OK** para confirmar a alteração.

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. A Restauração pontual é descrita mais detalhadamente na seção a seguir. 

## <a name="point-in-time-restore-in-the-azure-portal"></a>Restauração pontual no portal do Azure
O Banco de Dados do Azure para PostgreSQL permite a restauração do servidor em um ponto anterior no tempo e em uma nova cópia do servidor. Você pode usar esse novo servidor para recuperar seus dados ou fazer seu aplicativo cliente apontar para esse novo servidor.

Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, você poderá restaurar em um momento logo antes do meio-dia e recuperar a tabela e os dados dessa nova cópia do servidor. A restauração pontual está no nível do servidor, não no nível do banco de dados.

As etapas a seguir restauram o exemplo de servidor para um ponto anterior:
1. No Portal do Azure, selecione o servidor do Banco de Dados do Azure para PostgreSQL. 

2. Na barra de ferramentas da página **Visão geral** do servidor, selecione **Restaurar**.

   ![Banco de Dados do Azure para PostgreSQL - Visão geral - botão Restaurar](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário Restaurar com as informações necessárias:

   ![Banco de Dados do Azure para PostgreSQL - Informações de restauração ](./media/howto-restore-server-portal/3-restore.png)
  - **Ponto de restauração**: selecione o ponto para o qual você deseja restaurar.
  - **Servidor de destino**: forneça um nome para o novo servidor.
  - **Local**: não é possível selecionar a região. Por padrão, é o mesmo que o servidor de origem.
  - **Tipo de preço**: você não pode alterar esses parâmetros ao fazer uma restauração pontual. Ele é igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor em um ponto anterior. 

5. Após a conclusão da restauração, localize o novo servidor criado para verificar se os dados foram restaurados conforme o esperado.

>[!Note]
>O novo servidor criado pela restauração pontual tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no ponto escolhido. Você pode alterar a senha na página **Visão geral** do novo servidor.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [backups](concepts-backup.md) do serviço.
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md).

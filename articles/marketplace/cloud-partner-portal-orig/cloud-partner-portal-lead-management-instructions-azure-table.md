---
title: Tabela do Azure | Microsoft Docs
description: Configure o gerenciamento de clientes potenciais para a tabela do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805318"
---
<a name="lead-management-instructions-for-azure-table"></a>Instruções de gerenciamento de leads em tabelas do Azure
============================================

Este artigo descreve como configurar a Tabela do Azure para armazenar clientes potenciais de vendas. A Tabela do Azure permite que você armazene e personalize informações do cliente.

## <a name="to-configure-azure-table"></a>Para configurar a tabela do Azure

1.  Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2.  Após a sua conta do Azure, entre no [portal do Azure](https://portal.azure.com).
3.  No portal do Azure, crie uma conta de armazenamento. A próxima captura de tela mostra como criar uma conta de armazenamento. Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copie a cadeia de conexão da conta de armazenamento para a chave e cole-a no campo **Cadeia de Conexão da Conta de Armazenamento** no Portal do Cloud Partner. Um exemplo de uma cadeia de caracteres de conexão é `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Você pode usar o [gerenciador de armazenamento do Azure](http://azurestorageexplorer.codeplex.com/) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na Tabela do Azure.
dados.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Opcional)**  Para usar o Azure Functions com uma tabela do Azure

Se você quer personalizar como recebe clientes potenciais, use [Azure Functions](https://azure.microsoft.com/services/functions/) com uma tabela do Azure. O serviço Azure Functions permite automatizar o processo de geração de cliente potencial.

As etapas a seguir mostram como criar uma função do Azure que usa um temporizador. A cada cinco minutos, a função procura na tabela do Azure novos registros e, em seguida, usa o serviço SendGrid para enviar uma notificação por email.


1.  [Criar](https://portal.azure.com/#create/SendGrid.SendGrid) uma conta de serviço gratuita do SendGrid na sua assinatura do Azure.

    ![Criar SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Criar uma chave de API do SendGrid 
    - Selecione **Gerenciar** para ir para a interface do usuário do SendGrid
    - Selecione **Configurações**, **Chaves de API** e, em seguida, crie uma chave que tenha Envio de Email –\> Acesso Completo
    - Salvar a chave de API


    ![Chave de API do SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Criar](https://portal.azure.com/#create/Microsoft.FunctionApp) um aplicativo de Função do Azure usando a opção de Plano de Hospedagem chamado "Plano de Consumo".

    ![Criar um Aplicativo de funções do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Crie uma nova definição de função.

    ![Criar definição de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Para obter a função para enviar uma atualização em um horário específico, selecione **TimerTrigger-CSharp** como a opção de início.

     ![Opção de gatilho de tempo de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Substitua o código de "Desenvolver" pelo seguinte exemplo de código. Edite os endereços de email com os endereços que você deseja usar para o remetente e o destinatário.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Snippet de código de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Selecione **Integrar** e em **Entradas** para definir a conexão de Tabela do Azure.

    ![Integração de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Insira o nome da tabela e defina a cadeia de conexão selecionando **novo**.


    ![Conexão de tabela de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Agora, defina a saída como SendGrid e mantenha todos os padrões.

    ![Saída do SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Padrões de saída do SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Adicione a Chave de API do SendGrid às Configurações do Aplicativo de Funções usando o nome "SendGridApiKey" e o valor obtido de Chaves de API na Interface do Usuário do SendGrid

    ![Gerenciar SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![Chave para gerenciar SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Depois de terminar de configurar a função, o código na seção Integrar deve se parecer com o exemplo a seguir.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. A etapa final é navegar para a interface do usuário de Desenvolvimento e selecionar **Executar** para iniciar o temporizador. Agora, você receberá uma notificação sempre que um novo lead entrar.

---
title: "Instruções de gerenciamento de leads em tabelas do Azure no Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece orientações passo a passo aos fornecedores sobre como configurar o gerenciamento de leads com o armazenamento de tabelas do Azure."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 45aa2e0b178969215df9ae305d0a13a350ae0fda
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-azure-table"></a>Instruções de gerenciamento de leads em tabelas do Azure

Este documento fornece instruções sobre como configurar a Tabela do Azure para que a Microsoft possa fornecer vendas potenciais. A tabela do Azure será a melhor escolha padrão se você quiser fazer algo personalizado como armazenar leads.

1. Se você não tiver uma conta do Azure, primeiro [crie uma](https://azure.microsoft.com/pricing/free-trial/).

2. Quando a conta do Azure estiver pronta, faça logon no [Portal do Azure](https://portal.azure.com) e crie uma conta de armazenamento. Consulte a captura de tela abaixo para obter instruções e clique aqui para [saber mais sobre preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/). <br/>
  ![Imagem de fluxo de criação de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

3. Em seguida, copie a cadeia de conexão da chave e cole-a no campo <b>Cadeia de Conexão da Conta de Armazenamento</b> no Portal do Cloud Partner. <br/> 
  ![Imagem da chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

  Amostra final da cadeia de conexão: <br/>
`{"connectionString":"DefaultEndpointsProtocol=https;AccountName=leadaccount;AccountKey=ObS0EW7tDmXrC8oNeG6IRHpx2IUioBQTQynQcR/MUMqrNqQ/RC6zctP8HfucNJO+ond7dJHTROO9ziiPNspjEg=="}`

Você pode usar o [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/) (aplicativo de terceiros) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento ou exportar os dados.

### <a name="optional-azure-functions--azure-table"></a>**(Opcional)** Azure Functions + Tabela do Azure
Para personalizar a forma de receber esses leads, o serviço [Azure Functions](https://azure.microsoft.com/services/functions/) fornece máxima flexibilidade para automatizar o processo de geração de leads. Abaixo está um exemplo de como criar uma função do Azure com um temporizador executado a cada cinco minutos, procura novos registros na tabela do Azure e usa a camada gratuita do serviço SendGrid para enviar uma simples notificação por email.

1. [Criar](https://portal.azure.com/#create/SendGrid.SendGrid) uma conta de serviço gratuita do SendGrid na sua assinatura do Azure.
  
    ![Criar SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)
  
2. Crie uma chave de API do SendGrid (clique em Gerenciar Chave para ir para a interface do usuário do SendGrid, clique em Configurações, Chaves de API e crie uma chave que tem Envio de Email-> Acesso Completo e salve a chave de API).
 
    ![Chave de API do SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)

3. [Criar](https://portal.azure.com/#create/Microsoft.FunctionApp) um Aplicativo de funções do Azure usando a opção de Plano de Hospedagem chamado "Plano de Consumo". 
  
    ![Criar função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)

4. Crie uma nova definição de função. 
  
    ![Criação de definição de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)

5. Aqui, para obter a função para enviar uma atualização em um horário específico, selecione TimerTrigger-CSharp como a opção de início.
  
    ![Opção de gatilho de tempo de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)
  
6. Substitua o código de "Desenvolvimento" pelo da tabela abaixo. Edite endereços de email para corresponder com o que você quer que sejam para remetente e destinatário.
Isso é apenas o código de exemplo, você pode alterá-lo para torná-lo melhor, se necessário.
  
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
   ![Trecho de código de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)

7. Clique em "Integração" e em "Entradas" para definir a conexão de tabela do Azure:
  
    ![Integração de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)

8. Insira o nome da tabela e defina a cadeia de conexão, clicando em "novo".
  
    ![Conexão de tabela de função do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9. Agora, defina a saída como SendGrid e mantenha todos os padrões.
  
    ![Saída de SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)
    ![Padrões de saída do SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Adicionar chave de API do SendGrid às configurações do Aplicativo de funções usando o nome "SendGridApiKey" e o valor obtido de chaves de API na interface do usuário do SendGrid
  
    ![Gerenciar SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![managekey SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Depois de tudo configurado, a seção Integrar deve conter um código como este:

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
  
Finalmente, volte à interface do usuário de desenvolvimento da função e clique em **Executar** para iniciar o temporizador. Agora você preparado para obter notificações sempre que um novo lead entrar.


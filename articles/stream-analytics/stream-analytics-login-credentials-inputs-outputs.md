---
title: "Stream Analytics: alternar as credenciais de logon para entradas e saídas | Microsoft Docs"
description: "Saiba como atualizar as credenciais para as entradas e saídas do Stream Analytics."
keywords: credenciais de logon
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/11/2018
ms.author: sngun
ms.openlocfilehash: c1aded8fefc7b56acd2e9ff36bb2c9641665db76
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Fazer a rotação de credenciais de logon para entradas e saídas de um trabalho do Stream Analytics

Sempre que você gerar novamente as credenciais para uma entrada ou saída de um trabalho do Stream Analytics, você deve atualizar o trabalho com as novas credenciais. Você deve interromper o trabalho antes de atualizar as credenciais, você não pode substituir as credenciais enquanto o trabalho estiver em execução. Para reduzir a latência entre interromper e reiniciar o trabalho, o Stream Analytics dá suporte para retomar um trabalho de sua última saída. Este tópico descreve o processo de rotação das credenciais de logon e reinicialização do trabalho com as novas credenciais.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Regenerar novas credenciais e atualizar o seu trabalho com as novas credenciais 

Nesta seção, vamos orientar você a regenerar as credenciais para o armazenamento de Blob, Hubs de Eventos, Banco de Dados SQL do Microsoft Azure e o Armazenamento de Tabelas. 

### <a name="blob-storagetable-storage"></a>Armazenamento de blob/Armazenamento de tabela
1. Entre no portal do Azure > procure a conta de armazenamento que você usou como entrada/saída para o trabalho do Stream Analytics.    
2. Na seção de configurações, abra **Chaves de acesso**. Entre as duas chaves padrão (key1, key2), selecione aquela que não é usada por seu trabalho e a regenere:  
   ![Regenerar chaves para a conta de armazenamento](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Copie a chave recém-gerada.    
4. No portal do Azure, procure seu trabalho do Stream Analytics > selecione **Parar** e aguarde o trabalho ser interrompido.    
5. Localize a entrada/saída de blob/tabela de armazenamento para o/a qual você deseja atualizar as credenciais.    
6. Localize o campo **Chave da Conta de Armazenamento** e cole sua chave recém-gerada > clique em **Salvar**.    
7. Um teste de conexão será iniciado automaticamente quando você salvar as alterações, você pode exibi-lo na guia notificações. Há duas notificações - uma corresponde a salvar a atualização e outra corresponde a testar a conexão:  
   ![Notificações após a edição da chave](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Vá para a seção [iniciar seu trabalho a partir da hora da última interrupção] (#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Hubs de Eventos

1. Entre no portal do Azure > procure o Hub de eventos que você usou como entrada/saída para o trabalho do Stream Analytics.    
2. Na seção de configurações, abra **políticas de acesso compartilhado** e selecione a política de acesso necessária. Entre a **Chave Primária** e a **Chave Secundária**, escolha a que não é usada por seu trabalho e a regenere:  
   ![Regenerar chaves para o Hub de eventos](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Copie a chave recém-gerada.    
4. No portal do Azure, procure seu trabalho do Stream Analytics > selecione **Parar** e aguarde o trabalho ser interrompido.    
5. Localize a entrada/saída de Hubs de Evento para o qual você deseja atualizar as credenciais.    
6. Localize o campo **Política de Hub de Eventos** e cole sua chave recém-gerada > clique em **Salvar**.    
7. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.    
8. Vá para a seção [iniciar o trabalho a partir da hora da última interrupção](#start-your-job-from-the-last-stopped-time).

### <a name="sql-database"></a>Banco de dados SQL

Você precisa se conectar ao banco de dados SQL para atualizar as credenciais de logon de um usuário existente. Você pode atualizar as credenciais usando o portal do Azure ou uma ferramenta do cliente, como o SQL Server Management Studio. Esta seção demonstra o processo de atualização de credenciais usando o portal do Azure.

1. Entre no portal do Azure > procure o banco de dados SQL que você usou como saída para o trabalho do Stream Analytics.    
2. No **Explorador de Dados**, faça o logon/se conecte ao seu banco de dados > selecione o tipo de Autorização como **Autenticação do servidor SQL** > digite seus detalhes de **Logon** e  **Senha** > Selecione **Ok**.  
   ![Regenerar as credenciais para o banco de dados SQL](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Na guia de consulta, altere a senha para um dos seus usuários executando a consulta a seguir (certifique-se de substituir `<user_name>` por seu nome de usuário e `<new_password>` pela sua nova senha):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anote a nova senha.    
5. No portal do Azure, procure seu trabalho do Stream Analytics > selecione **Parar** e aguarde o trabalho ser interrompido.    
6. Localize a saída do banco de dados SQL para o qual você deseja girar as credenciais. Atualize a senha e salve as alterações.    
7. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.    
8. Vá para a seção [iniciar o trabalho a partir da hora da última interrupção](#start-your-job-from-the-last-stopped-time).

### <a name="power-bi"></a>Power BI
1. Entre no portal do Azure > procure seu trabalho do Stream Analytics > selecione **Parar** e aguarde o trabalho ser interrompido.    
2. Localize a saída do Power BI para a qual você deseja renovar as credenciais > clique em **Renovar autorização** (você verá uma mensagem de êxito) > **Salvar** as alterações.    
3. Um teste de conexão será automaticamente iniciado quando você salvar as alterações, verifique se ele é aprovado.    
4. Vá para a seção [iniciar o trabalho a partir da hora da última interrupção](#start-your-job-from-the-last-stopped-time).

## <a name="start-your-job-from-the-last-stopped-time"></a>Iniciar o trabalho a partir da hora da última interrupção

1. Navegue até o painel **Visão geral** do trabalho > selecione **Iniciar** para iniciar o trabalho.    
2. Selecione **Na última interrupção** > clique em **Iniciar**. Observe que a opção "Na última interrupção" só aparece se você tiver executado o trabalho anteriormente e tiver algumas saídas geradas. O trabalho é reiniciado com base na hora do último valor de saída.
   ![Iniciar o trabalho](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

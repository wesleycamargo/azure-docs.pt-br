---
title: "Portal do Azure: Configurar a detecção de ameaças – Banco de Dados SQL | Microsoft Docs"
description: "Configurar e gerenciar a detecção de ameaças com o Portal do Azure"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 0222847321b82c9572e361cc5e578b779d859792
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-azure-sql-database-threat-detection-in-the-azure-portal"></a>Configurar e gerenciar a detecção de ameaças do Banco de Dados SQL do Azure com o Portal do Azure

Este tópico mostra como configurar a detecção de ameaças e explorar atividades anormais de banco de dados. Para obter uma visão geral da detecção de ameaças do Banco de Dados SQL do Azure, consulte [Visão geral de detecção de ameaças](sql-database-threat-detection.md).

## <a name="set-up-threat-detection-for-your-database"></a>Configurar a detecção de ameaças para seu banco de dados
1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a folha de configuração do Banco de Dados SQL que você deseja monitorar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.
   
    ![Painel de navegação][1]
3. Na folha de configuração **Auditoria e Detecção de Ameaças**, **ATIVE** a auditoria, o que exibirá as configurações de detecção de ameaças.
   
    ![Painel de navegação][2]
4. **ATIVE** a detecção de ameaças.
5. Configure a lista de emails que receberão os alertas de segurança após a detecção das atividades anormais do banco de dados.
6. Clique em **Salvar** na folha **Auditoria e detecção de ameaças** para salvar a auditoria nova ou atualizada e a política de detecção de ameaças.
   
    ![Painel de navegação][3]

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades anormais do banco de dados na detecção de um evento suspeito
1. Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. <br/>
   O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, nome do banco de dados, nome do servidor e a hora do evento. Além disso, ele fornecerá informações sobre as possíveis causas e ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.<br/>
   
    ![Painel de navegação][4]
2. No email, clique no link **Log de Auditoria do SQL do Azure** , que iniciará o portal do Azure e mostrará os registros de Auditoria relevantes na época do evento suspeito.
   
    ![Painel de navegação][5]
3. Clique nos registros de auditoria para exibir mais detalhes sobre as atividades suspeitas do banco de dados, como a instrução SQL, motivo da falha e IP do cliente.
   
    ![Painel de navegação][6]
4. Na folha Registros de Auditoria, clique em **Abrir no Excel** para abrir um modelo pré-configurado do Excel para importar e executar uma análise mais profunda do log de auditoria na época do evento suspeito.<br/>
   **Observação:** no Excel 2010 ou posterior, a configuração **Combinação Rápida** e o Power Query são necessários.
   
    ![Painel de navegação][7]
5. Para definir a configuração **Combinação Rápida**: na guia de faixa de opções **POWER QUERY**, selecione **Opções** para exibir a caixa de diálogo Opções. Selecione a seção Privacidade e escolha a segunda opção - 'Ignorar os Níveis de Privacidade e melhorar potencialmente o desempenho':
   
    ![Painel de navegação][8]
6. Para carregar os logs de auditoria do SQL, verifique se os parâmetros na guia de configurações estão definidos corretamente e, em seguida, selecione a faixa de opções 'Dados' e clique no botão 'Atualizar Tudo'.
   
    ![Painel de navegação][9]
7. Os resultados aparecem na folha **Logs de Auditoria do SQL** , que permite executar uma análise mais profunda das atividades anormais detectadas e minimizar o impacto do evento de segurança em seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral da detecção de ameaças do Banco de Dados SQL, consulte [Detecção de ameaças](sql-database-threat-detection.md).
* Para obter uma visão geral da auditoria de Banco de Dados SQL, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


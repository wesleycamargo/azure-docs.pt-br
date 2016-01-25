<properties
   pageTitle="Introdução à Detecção de Ameaças do SQL Data Warehouse"
   description="Introdução à Detecção de Ameaças do SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# Introdução à detecção de ameaças do SQL Data Warehouse

## Visão geral

A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Detecção de ameaças está em visualização e tem suporte para o SQL Data Warehouse.

A Detecção de Ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança nas atividades anormais. Os usuários podem explorar os eventos suspeitos usando [Azure SQL Data Warehouse Auditing]sql-data-warehouse-auditing-get-started.md) para determinar se eles são provenientes de uma tentativa de acesso, violação ou exploração de dados no data warehouse. A Detecção de Ameaças torna simples lidar com possíveis ameaças no data warehouse sem a necessidade de ser uma especialista em segurança ou gerenciar sistemas avançados de monitoramento de segurança.

Por exemplo, a Detecção de Ameaças detecta determinadas atividades anormais do banco de dados que indicam possíveis tentativas de injeção de SQL. A injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos controlados por dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, para violar ou modificar os dados no banco de dados.


## Configurar a detecção de ameaças para seu banco de dados

1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

2. Navegue até a folha de configuração do SQL Data Warehouse que você deseja monitorar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

	![Painel de navegação][1]

3. Na folha de configuração **Auditoria e Detecção de Ameaças** **ATIVE** a auditoria, que exibirá as configurações de Detecção de ameaças.

	![Painel de navegação][2]

4. **ATIVE** a Detecção de ameaças.

5. Configure a lista de emails que receberão alertas de segurança após a detecção de atividades de depósito de dados anormais.

6. Clique em **Salvar** na folha de configuração **Auditoria e detecção de ameaças** para salvar a auditoria nova ou atualizada e a política de detecção de ameaças.

	![Painel de navegação][3]


## Explore as atividades de depósito de dados anômalos após a detecção de um evento suspeito

1. Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. <br/> O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, nome do banco de dados, nome do servidor e a hora do evento. Além disso, ele fornecerá informações sobre as possíveis causas e ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.<br/>

	![Painel de navegação][4]

2. No email, clique no link **Log de Auditoria do SQL do Azure**, que iniciará o portal clássico do Azure e mostrará os registros de Auditoria relevantes na época do evento suspeito.

	![Painel de navegação][5]

3. Clique nos registros de auditoria para exibir mais detalhes sobre as atividades suspeitas do banco de dados, como a instrução SQL, motivo da falha e IP do cliente.

	![Painel de navegação][6]

4. Na folha Registros de Auditoria, clique em **Abrir no Excel** para abrir um modelo pré-configurado do Excel para importar e executar uma análise mais profunda do log de auditoria na época do evento suspeito.<br/> **Observação:** no Excel 2010 ou posterior, o Power Query e a configuração **Combinação Rápida** são necessários

	![Painel de navegação][7]

5. Para definir a configuração **Combinação Rápida** na guia da faixa de opções **POWER QUERY**, selecione **Opções** para exibir a caixa de diálogo Opções. Selecione a seção Privacidade e escolha a segunda opção - 'Ignorar os Níveis de Privacidade e melhorar potencialmente o desempenho':

	![Painel de navegação][8]

6. Para carregar os logs de auditoria do SQL, verifique se os parâmetros na guia de configurações estão definidos corretamente e, em seguida, selecione a faixa de opções 'Dados' e clique no botão 'Atualizar Tudo'.

	![Painel de navegação][9]

7. Os resultados aparecem na folha **Logs de Auditoria do SQL**, que permite executar uma análise mais profunda das atividades anormais detectadas e minimizar o impacto do evento de segurança em seu aplicativo.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png

<!---HONumber=AcomDC_0114_2016-->
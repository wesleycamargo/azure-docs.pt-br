<properties
   pageTitle="Habilitar a auditoria em servidores SQL na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Habilitar a auditoria em servidores SQL**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Habilitar a auditoria em servidores SQL na Central de Segurança do Azure

A Central de Segurança do Azure recomendará que você ative a auditoria para todos os bancos de dados em seus servidores SQL do Azure se isso não tiver sido feito. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

Depois de ativar a auditoria, você poderá definir as configurações de Detecção de Ameaças e emails para receber alertas de segurança. A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Isso permite que você detecte e reaja a possíveis ameaças à medida que elas ocorrem.

Essa recomendação se aplica apenas ao serviço do SQL do Azure. Ela não inclui o servidor SQL em execução nas máquinas virtuais nos Serviços de Infraestrutura do Azure (Azure IaaS).

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Habilitar Auditoria em servidores SQL**. Isso abre a folha **Habilitar Auditoria em servidores SQL**. ![Habilitar auditoria em servidores SQL][1]

2. Selecione um servidor SQL onde deseja habilitar a auditoria. Isso abre a folha **Configurações de Auditoria**. ![Configurações de auditoria][2]
3. Na folha **Configurações de Auditoria**, selecione **ATIVAR** em **Auditoria**. ![Ativar configurações de auditoria][3]

4. Siga as etapas em [Introdução à Auditoria do Banco de Dados SQL](../sql-database/sql-database-auditing-get-started.md) para configurar o local onde os logs de auditoria serão armazenados. A conta de armazenamento da assinatura para coleta de dados é a padrão.

5. Siga as etapas em [Introdução à Detecção de Ameaças do Banco de Dados SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a Detecção de Ameaças e configurar a lista de emails que receberá alertas de segurança após a detecção de atividades anormais.

## Próximas etapas

Este artigo mostrou como implementar a recomendação "Habilitar auditoria em servidores SQL" da Central de Segurança. Para saber mais sobre como proteger seu banco de dados SQL, veja:

- [Protegendo o Banco de Dados SQL](../sql-database/sql-database-security.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciamento e resposta a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png

<!---HONumber=AcomDC_0720_2016-->
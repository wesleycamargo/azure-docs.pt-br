<properties
   pageTitle="Protegendo o serviço do SQL Azure na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento endereça as recomendações na Central de Segurança do Azure que ajudam a proteger o serviço do SQL Azure e cumprir as políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protegendo o serviço do SQL Azure na Central de Segurança do Azure

A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários. As recomendações se aplicam aos tipos de recursos do Azure: máquinas virtuais (VMs), rede, aplicativos e SQL.

Este artigo endereça as recomendações que se aplicam ao serviço do SQL Azure. As recomendações do serviço do SQL Azure giram em torno de como ativar a auditoria para os bancos de dados e os servidores SQL do Azure e como habilitar a criptografia para os bancos de dados SQL. Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis do serviço SQL e o que cada uma delas fará se você as aplicar.

## Recomendações disponíveis do serviço SQL

|Recomendações|Descrição|
|-----|-----|
|[Habilitar a auditoria do servidor SQL](security-center-enable-auditing-on-sql-servers.md)|Recomenda que você habilite auditoria para servidores Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|[Habilitar auditoria para Banco de Dados SQL](security-center-enable-auditing-on-sql-databases.md)|Recomenda que você habilite auditoria para bancos de dados do Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|[Habilitar Transparent Data Encryption em bancos de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL).|

## Confira também

Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

- [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.

<!---HONumber=AcomDC_0810_2016-->
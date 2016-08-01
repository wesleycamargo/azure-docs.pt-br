<properties
   pageTitle="Habilitar Transparent Data Encryption na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Habilitar Transparent Data Encryption**."
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

# Habilitar Transparent Data Encryption na Central de Segurança do Azure

A Central de Segurança do Azure recomendará habilitar a TDE (Transparent Data Encryption) em bancos de dados SQL se já não estiver habilitada. A TDE protege seus dados e ajuda a cumprir os requisitos de conformidade, criptografando seu banco de dados, backups associados e arquivos de log de transações em repouso, sem exigir alterações no seu aplicativo. Para saber mais, consulte [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096).

Essa recomendação se aplica apenas ao serviço do SQL Azure, não incluindo o SQL em execução em máquinas virtuais.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Habilitar Transparent Data Encryption**. ![Habilitar Transparent Data Encryption][1]

2. Isso abre a folha **Habilitar Transparent Data Encryption em bancos de dados SQL**. Selecione um banco de dados SQL no qual você deseja habilitar a TDE. ![Selecionar o banco de dados SQL no qual habilitar a TDE][2]
3. Na folha **Transparent data encryption**, selecione **ATIVAR** em Criptografia de dados e selecione **Salvar** na faixa de opções superior da folha. ![Ativar TDE][3]

  Quando a TDE for habilitada no banco de dados SQL selecionado, o **Status da criptografia** será alterado para **Criptografado**.

  ![Status de criptografia][4]

## Próximas etapas

Este artigo mostrou como implementar a recomendação da Central de Segurança "Habilitar a Transparent Data Encryption". Para saber mais sobre a TDE do SQL, consulte o seguinte:

- [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096)
- [Introdução ao Transparent Data Encryption (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciamento e resposta a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]: ./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png

<!---HONumber=AcomDC_0720_2016-->
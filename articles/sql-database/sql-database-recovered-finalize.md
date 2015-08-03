<properties
   pageTitle="Finalizar seu Banco de Dados SQL do Azure recuperado"
   description="Ponto de restauração pontual, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, Portal de Gerenciamento do Azure, portal do Azure"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Finalizar seu Banco de Dados SQL do Azure recuperado

## Visão geral

Este artigo fornece uma lista de verificação das tarefas que precisam ser realizadas antes de colocar um Banco de Dados SQL do Azure recentemente recuperado de volta em produção. Esta lista de verificação se aplica aos bancos de dados recuperados do failover de Replicação Geográfica, Restauração do Banco de Dados Excluído, Restauração Pontual ou Restauração Geográfica.

## Atualizar cadeias de conexão

Verifique se as cadeias de conexão do aplicativo estão apontando para o banco de dados recentemente recuperado. Atualize as cadeias de conexão no caso de uma das situações abaixo:

  + O banco de dados recuperado usa um nome diferente do nome do banco de dados de origem
  + O banco de dados recuperado está em um servidor diferente do servidor de origem

Para saber mais sobre como alterar as cadeias de conexão, confira [Diretrizes sobre como se conectar de modo programático com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336282.aspx) e [Conexões com o Banco de Dados SQL do Azure: principais recomendações](sql-database-connect-central-recommendations.md).
 
## Modificar regras de firewall
Verifique as regras de firewall no nível de servidor e de banco de dados para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas. Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx) e [Como configurar as definições de firewall (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Verificar logons do servidor e usuários do banco de dados

Verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. Para saber mais, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

Verifique se cada usuário no banco de dados recuperado está associado a um logon de servidor válido. Use a instrução ALTER USER para mapear o usuário para o logon de servidor válido. Para saber mais, confira [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


## Configurar alertas de telemetria

Verifique se as configurações de regra de alerta existentes foram mapeadas para o banco de dados recuperado. Atualize a configuração no caso de uma das situações abaixo:

  + O banco de dados recuperado usa um nome diferente do nome do banco de dados de origem
  + O banco de dados recuperado está em um servidor diferente do servidor de origem

Para saber mais sobre regras de alerta do banco de dados, confira [Como receber notificações de alerta e gerenciar regras de alerta no Azure](https://msdn.microsoft.com/library/azure/dn306638.aspx).


## Habilitar a auditoria

Se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Um bom indicador de auditoria é obrigatório, pois os aplicativos cliente usam cadeias de conexão seguras em um padrão *.database.secure.windows.net. Para saber mais, confira [Introdução à auditoria do banco de dados SQL](sql-database-auditing-get-started.md). 
 

<!---HONumber=July15_HO4-->
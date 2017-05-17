---
title: "Migrar seu banco de dados MySQL usando despejo e restauração no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Apresenta a migração do Banco de Dados do Azure para MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c09a6fa947d235189ab0137b074b6d7d9c925827
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar seu banco de dados MySQL para o Banco de Dados do Azure para MySQL usando despejo e restauração
Este artigo descreve duas maneiras comuns de fazer backup e restaurar bancos de dados no seu Banco de Dados do Azure para MySQL
- Fazendo backup e restauração a partir da linha de comando (usando mysqldump) 
- Fazendo backup e restaurando usando PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de começar
Para acompanhar este guia de instruções, você precisa do seguinte:
- [Criar Banco de Dados do Azure para servidor MySQL - portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilitário de linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado em um computador
- MySQL Workbench [Baixar MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat ou qualquer ferramenta de terceiros do MySQL

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas comuns, como MySQL Workbench, mysqldump, Toad ou Navicat para se conectar remotamente e restaurar dados para o Banco de Dados do Azure para MySQL. Use essas ferramentas no computador cliente com uma conexão de Internet para se conectar ao Banco de Dados do Azure para MySQL. Use uma conexão SSL criptografada para práticas recomendadas de segurança, consulte também [Configurar conectividade SSL no Banco de Dados do Azure para MySQL](concepts-ssl-connection-security.md). Você não precisa mover os seus arquivos de despejo para nenhum local específico na nuvem ao migrar para o Banco de Dados do Azure para MySQL. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um arquivo de backup a partir da linha de comando usando mysqldump
Para fazer backup de um banco de dados MySQL existente localmente ou em uma VM, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os seguintes parâmetros devem ser fornecidos:
- [uname] Seu nome de usuário do banco de dados 
- [pass] A senha do banco de dados (observe que não há nenhum espaço entre -p e a senha) 
- [dbname] O nome do banco de dados 
- [backupfile.sql] O nome do arquivo para o backup do banco de dados 
- [-opt] A opção mysqldump 

Por exemplo, para fazer backup de um banco de dados chamado “testdb” com o nome de usuário “testuser” e nenhuma senha para um arquivo testdb_backup.sql, use o comando a seguir. Esse comando fará backup de banco de dados “testdb” em um arquivo chamado testdb_backup.sql que irá conter todas as instruções SQL necessárias para recriar o banco de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas em seu banco de dados para backup, liste os nomes de tabela, separados por espaços. Por exemplo, para fazer backup somente das tabelas table1 e table2 de “testdb”, siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer backup de mais de um banco de dados ao mesmo tempo, use a opção --database e liste os nomes dos banco de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para fazer backup de todos os bancos de dados no servidor ao mesmo tempo, você deve usar a opção --all-databases.
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="upload-files"></a>Carregar arquivos
Com o WinSCP você pode facilmente carregar e gerenciar a importação ou despejo dos arquivos existentes no seu ambiente MySQL (Azure ou não Azure) Local sobre o protocolo SFTP ou FTPS para fins de exportação.

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>Criar um banco de dados no servidor MySQL do Azure
Você deve criar um banco de dados vazio no Banco de Dados do Azure de destino para o servidor MySQL para o qual você deseja migrar os dados usando o MySQL Workbench, Toad, Navicat ou qualquer ferramenta de terceiros para MySQL. O banco de dados pode ter o mesmo nome que o banco de dados que contém os dados de despejo ou você pode criar um banco de dados com um nome diferente.

![Banco de Dados do Azure para Cadeia de Conexão do MySQL](./media/concepts-migrate-import-export/p5.png)

![Cadeia de Conexão do MySQL Workbench](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar o banco de dados MySQL usando a linha de comando ou o MySQL Workbench
Depois de criar o banco de dados de destino, você pode usar o comando mysql ou o MySQL Workbench para restaurar os dados no banco de dados recém-criado do arquivo de despejo.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, iremos restaurar os dados para o banco de dados recém-criado testdb3 no servidor de destino.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar usando PHPMyAdmin
Para exportar, você pode usar a ferramenta phpMyAdmin que você pode já ter instalado localmente em seu ambiente. Para exportar o banco de dados MySQL usando PHPMyAdmin:
- Abra o phpMyAdmin.
- Selecione o seu banco de dados clicando no nome do banco de dados na lista à esquerda da tela. 
- Clique no link Exportar. Isso deve abrir uma nova tela que diz Visualizar despejo do banco de dados. 
- Na área de Exportação, clique no link Selecionar tudo para selecionar todas as tabelas no banco de dados. 
- Na área de opções do SQL, clique nas opções à direita. 
- Clique na opção Salvar como arquivo e na opção de compactação e, em seguida, clique no botão “Ir”. Uma caixa de diálogo deve aparecer solicitando que você salve o arquivo localmente.

## <a name="import-using-phpmyadmin"></a>Importar usando PHPMyAdmin
Importar o banco de dados é semelhante à exportação. As seguintes ações ocorrem:
- Abra o phpMyAdmin. 
- Crie um banco de dados com o nome adequado e selecione-o clicando no nome do banco de dados na lista à esquerda da tela. Se você deseja reescrever a importação sobre um banco de dados existente, em seguida, clique no nome do banco de dados, selecione todas as caixas de seleção ao lado dos nomes de tabela e selecione Soltar para excluir todas as tabelas existentes no banco de dados. 
- Clique no link SQL. Isso deve abrir uma nova tela onde você pode digitar comandos SQL ou carregar o arquivo SQL. 
- Use o botão de procurar para localizar o arquivo do banco de dados. 
- Clique no botão Ir. Isso irá exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="next-steps"></a>Próximas etapas

[Criar um banco de dados para o servidor MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
[Criar um Banco de Dados para o servidor MySQL usando a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)


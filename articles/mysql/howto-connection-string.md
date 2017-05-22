---
title: Como conectar aplicativos ao Banco de Dados do Azure para MySQL | Microsoft Docs
description: "Este documento lista todas as cadeias de conexão com suporte no momento para conexão de aplicativos com o Banco de Dados do Azure para MySQL"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como conectar aplicativos ao Banco de Dados do Azure para MySQL
Este documento lista todos os tipos de cadeia de conexão com suporte pelo Banco de Dados para MySQL, juntamente com modelos e exemplos. Você pode ter parâmetros e configurações diferentes em sua cadeia de conexão.

- Confira o documento [Como configurar o SSL](./howto-configure-ssl.md) para obter o certificado
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

Por exemplo, supondo que o nome do servidor seja **wpdemo**, o nome do banco de dados seja **wpdb**, o nome de usuário seja **WPAdmin**, a senha será **orcas!2**.

Depois, a cadeia de conexão deverá ser:

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Obter a cadeia de conexão do portal
Em [Portal do Azure](https://portal.azure.com), acesse o Banco de Dados do Azure para MySQL e clique em **Cadeias de conexão** para obter a lista de cadeia de caracteres para sua instância: ![cadeias de conexão no portal](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, servidor e outros parâmetros de conexão de banco de dados. Copie a cadeia de conexão em sua demanda e modifique-a com seus próprios parâmetros, como nome do banco de dados, senha etc. Depois, use essa cadeia de caracteres para se conectar ao servidor.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre a biblioteca de conexão, veja [Conceitos - Bibliotecas de conexão](./concepts-connection-libraries.md)


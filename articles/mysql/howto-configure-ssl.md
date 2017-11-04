---
title: "Configurar a conectividade SSL para se conectar com segurança ao Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Instruções sobre como configurar apropriadamente o Banco de Dados do Azure para MySQL e aplicativos associados a fim de usar as conexões SSL corretamente"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 079bb22aa76b8354f79400ced4e04dc971ea249a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="step-1-obtain-ssl-certificate"></a>Etapa 1: Obter um certificado SSL
Baixe o certificado necessário para comunicação via SSL com o Banco de Dados do Azure para o servidor MySQL de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e salve o arquivo de certificado no disco local (com este tutorial, usamos c:\ssl).
**Para o Microsoft Internet Explorer e Microsoft Edge:** depois que o download for concluído, renomeie o certificado para BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Etapa 2: associar o SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Conectar-se ao servidor usando a o MySQL Workbench sobre SSL
Configurar o MySQL Workbench para se conectar com segurança por SSL. Na caixa de diálogo Configurar Nova Conexão, navegue até a guia **SSL**. No campo **Arquivo de AC SSL**:, insira o local do arquivo de **BaltimoreCyberTrustRoot.crt.pem**. 
![salvar bloco personalizado](./media/howto-configure-ssl/mysql-workbench-ssl.png) Para as conexões existentes, você pode associar o SSL clicando com o botão direito do mouse no ícone de conexão e escolha Editar. Em seguida, navegue até a guia **SSL** e associe o arquivo de certificado.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conectar-se ao servidor usando a CLI do MySQL sobre SSL
Outra maneira de associar o certificado SSL é usar a interface de linha de comando do MySQL, executando o seguinte comando:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Etapa 3: impor conexões SSL no Azure 
### <a name="using-the-azure-portal"></a>Usando o portal do Azure
Usando o Portal do Azure, visite seu servidor de Banco de Dados do Azure para MySQL e, em seguida, clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL** e, em seguida, clique em **Salvar**. A Microsoft recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores Enabled ou Disabled respectivamente na CLI do Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Etapa 4: Verificar a conexão SSL
Execute o comando **status** de mysql para verificar se você se conectou ao servidor MySQL usando o SSL:
```dos
mysql> status
```
Confirme que a conexão é criptografada, revisando a saída, que deve mostrar: **SSL: A criptografia em uso é AES256 SHA** 

## <a name="sample-code"></a>Exemplo de código
Para estabelecer uma conexão segura com o Banco de Dados do Azure para MySQL sobre SSL de seu aplicativo, veja os exemplos de código a seguir.
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>Próximas etapas
Analise as várias opções de conectividade do aplicativo em [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

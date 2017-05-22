---
title: Leitura de Python do Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: "Supondo o não conhecimento prévio, este artigo explica como executar um exemplo de código Python para gravar e ler dados de uma tabela em um Banco de Dados do Azure para PostgreSQL."
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Leitura de Python do Banco de Dados do Azure para PostgreSQL


Este artigo fornece um pequeno programa em Python que usa uma instrução SQL SELECT para ler uma linha de uma tabela. O artigo também explica como localizar e instalar todos os pré-requisitos necessários para executar o programa em Python.

Nosso exemplo de programa em Python, e as ferramentas descritas relacionadas a Python, se aplicam igualmente a várias plataformas, incluindo Windows, Linux e Mac. 


## <a name="install-the-python-interpreter"></a>Instalar o intérprete de Python


O exemplo de código Python neste artigo foi escrito para a versão 2.7 do intérprete de Python, e não pode ser executado pela versão 3.x.

Baixe e instale a versão 2.7 do intérprete de Python em:

- [Baixar o intérprete de Python em python.org](https://www.python.org/downloads/)

Após a instalação, comprove você pode localizar e executar o intérprete em uma linha de comando. Use um comando como:

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Instalar pip.exe, o instalador do módulo Python


A instalação do intérprete de Python também pode ter instalado o pip.exe, talvez em um subdiretório chamado *Scripts/*. O Pip.exe instala os módulos de Python especializados. Prove que você pode localizar e executar o pip.exe.

`pip.exe`

Se você não puder executar o pip.exe, veja se você tem o arquivo de programa do utilitário Python chamado **get-pip.py**. Se você tiver o get-pip.py, poderá executá-lo para obter o pip.exe:

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>Instalar psycopg, o módulo de conexão


Nosso programa em Python precisa de um módulo que saiba como conectar nosso programa ao Banco de Dados do Azure para o servidor PostgreSQL. O nome do módulo conexão é **psycopg2**. Se você quiser mais detalhes, confira:

- [Site do psycopg2](http://initd.org/psycopg/)

Instale o psycopg2 usando o comando de instalação de pip.exe:

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL


Se você ainda não tiver acesso a um servidor de Banco de Dados do Azure para PostgreSQL, confira esta documentação que explica como você pode criar um servidor:

- [Criar um Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](quickstart-create-server-database-portal.md)
- [Criar um Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>Obter os valores de cadeia de conexão


No Portal do Azure, você pode obter os valores de cadeia de conexão valores de cadeia para seu Banco de Dados do Azure para o servidor PostgreSQL. Você precisa dos valores de parâmetro, conforme descrito na tabela a seguir.

- A coluna *Nome* &ndash; exibe os identificadores de parâmetro exigidos pelo psycopg2.
- A coluna *-Symbol* &ndash; exibe os identificadores de parâmetro exigidos pelo exemplo de programa em Python, *PythonDriver.py*.


| Nome | -Symbol | Exemplo de valor |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| usuário | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />*(Todos os servidores do PostgreSQL têm um banco de dados chamado **postgres**.)* |
| porta | -p | 5432 *(Provavelmente esse valor específico de 5432.)* |
| Senha | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>O programa de exemplo do Python


Esta seção fornece o código-fonte para o nosso exemplo de programa em Python. Você executará o programa posteriormente neste artigo.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>Linhas de comando para executar o exemplo de programa


Esta seção exibe as linhas de comando usadas para testar o exemplo de programa em Python, juntamente com a saída de confirmação real.

Para os parâmetros descritos anteriormente, substitua os valores de exemplo exibidos aqui por seus valores reais de cadeia de conexão.

A sintaxe exata para execução do exemplo de programa em Python anterior pode variar um pouco. A sintaxe exata depende de seu sistema operacional e do tipo de console usado.


#### <a name="windows-cmdexe-console"></a>Console cmd.exe do Windows


O bloco de código a seguir exibe uma execução de teste real do exemplo de programa em Python. Uma linha de comando cmd.exe simples foi usada. Após cada caractere de continuação de linha '^' ser digitado:

1. A tecla Enter foi pressionada.
2. A frase **More?** foi exibida.
3. Outra parte da linha inteira foi digitada e assim por diante.

Essa técnica de continuação de linha foi usada para evitar que o exemplo se tornasse tão grande que dificultaria sua exibição aqui, ou na impressão.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

Você pode ver a linha **SUCCESS** como confirmação de que o programa foi executado.

Como uma técnica alternativa, a linha de comando inteira pode ser colocada em um arquivo .bat. O arquivo .bat pode ser executado na linha de comando do cmd.exe.


#### <a name="powershell-file"></a>Arquivo do PowerShell


No console do PowerShell, a linha de comando não oferece suporte ao caractere de continuação de linha. Portanto, nesta seção do PowerShell, colocamos os comandos em um arquivo do PowerShell. Depois, executamos o arquivo na linha de comando do PowerShell.

Copie o seguinte código em um arquivo chamado *PythonDriverRun.ps1*.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

Execute PythonDriverRun.ps1, conforme mostrado a seguir. Veja a linha **SUCCESS** que o programa imprime como confirmação.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

Se você preferir executar PythonDriverRun.ps1 diretamente na linha de comando do PowerShell, coloque um caractere '&' e um espaço. Sem os '&' inicial, a mensagem de confirmação desaparecerá muito rapidamente, dificultando sua visualização.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>Instalar pgAdmin para inspecionar o servidor


Quando o programa PythonDriverRun.ps1 terminar, ele executará sua própria limpeza descartando a tabela testpy1 criada pelo programa. Você tem a opção de usar '#' para comentar a linha do código fonte que emite a instrução **DROP TABLE**. Essa opção manteria a tabela para que você possa inspecionar a tabela posteriormente.

A ferramenta pgAdmin permite que você inspecione qualquer servidor PostgreSQL, e os objetos dentro do servidor. Usuários do Microsoft SQL Server ou do Banco de Dados SQL do Azure, veriam semelhanças entre o SSMS (SQL Server Management Studio) e o pgAdmin.

Se quiser, instale **pgAdmin** para inspecionar o servidor e sua tabela **testpy1**.


#### <a name="1-install-pgadmin"></a>1. Instalar pgAdmin


As instruções de instalação para pgAdmin estão disponíveis em:

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

**pgAdmin4.exe** pode ser o nome do arquivo executável, não simplesmente pgAdmin.exe.

Para executar pgAdmin4.exe em um computador com Windows, insira algo parecido com o seguinte comando na linha de comando:

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2. Conectar o pgAdmin ao seu servidor

 
Após a exibição da interface de usuário do pgAdmin, localize o painel **Browser**. Em seguida, clique com botão direito em **Servers** > **Create** > **Servers**. Aqui o termo *Create* significa criar uma *conexão* com qualquer servidor PostgreSQL existente, incluindo qualquer servidor de Banco de Dados do Azure para PostgreSQL.

Quando a conexão é feita, uma árvore de objetos é exibida no painel **Browser**.


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. Navegue na árvore de pgAdmin até sua tabela


Para ver a tabela, expanda os elementos da árvore da seguinte maneira:

- **Servers** &gt; *[SeuServidorAqui]* &gt; **Databases** &gt; postgres &gt; **Schemas** &gt; public &gt; **tables** &gt; testpy1

![pgAdmin mostrando a tabela testpy1 na árvore](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. Descartar a tabela testpy1


Para a limpeza final, clique com o botão direito no nó testpy1 e clique em **Delete/Drop**.


## <a name="next-steps"></a>Próximas etapas

- [Conexão do Python com o Banco de Dados SQL do Azure](../sql-database/sql-database-connect-query-python.md)
- [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md)


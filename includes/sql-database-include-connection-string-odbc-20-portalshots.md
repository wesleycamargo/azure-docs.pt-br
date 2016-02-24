<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Obter a cadeia de conexão no portal do Azure


Use o [portal de visualização do Azure](https://portal.azure.com/) para obter a cadeia de conexão necessária para o seu programa cliente interagir com o Banco de Dados SQL do Azure:


1. Clique em **PROCURAR** > **bancos de dados SQL**.

    ![Selecionar SQL][1-select-sql]

2. Insira o nome do banco de dados na caixa de texto de filtro próximo ao canto superior esquerdo da folha dos **bancos de dados SQL**.

    ![Selecionar um banco de dados][2-select-database]]

3. Clique na linha do banco de dados.

4. Depois que a folha aparecer para o banco de dados, para conveniência visual, você pode clicar nos controles padrão de minimização para recolher as folhas usadas para navegação e filtragem do banco de dados.

5. Na folha do banco de dados, clique em **Mostrar cadeias de conexão de banco de dados**.

6. Se você pretende usar a biblioteca de conexão ODBC, copie a cadeia de caracteres rotulada **ODBC (inclui o Node.js)**.

	![Copie a cadeia de conexão ODBC do banco de dados][3-get-connection-string]

7. Cole as informações da cadeia de conexão no código do programa cliente. Você precisará substituir {your\_password\_here} pela sua senha real.



<!--
Could not find a good link for ODBC

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-odbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0128_2016-->
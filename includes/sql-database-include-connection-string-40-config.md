
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### Exemplo de arquivo de configuração para segurança da cadeia de conexão


Não faz sentido colocar a cadeia de conexão como literais em seu código do C#. É melhor colocar a cadeia de conexão em um arquivo de configuração. Lá você pode editar a cadeia de caracteres a qualquer momento, sem precisar recompilar.

Vamos supor que o seu programa compilado no C# chama-se **ConsoleApplication1.exe** e que esse .exe reside em um diretório **bin\\debug**.

Neste exemplo, a maioria das partes da cadeia de conexão é armazenada em um arquivo de configuração chamado exatamente **ConsoleApplication1.exe.config**. Este arquivo de configuração também deve residir em **bin\\debug**.

No XML do arquivo de configuração a seguir, você verá uma cadeia de conexão chamada **ConnectionString4NoUserIDNoPassword**. O código do C# procura essa cadeia de caracteres.

Você deve editar os nomes reais nos espaços reservados:

- {seu\_nomeServidor\_aqui}
- {seu\_nomeBancoDados\_aqui}



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



Para esta ilustração, escolhemos omitir dois parâmetros:

- User ID={seu\_nomeUsuário\_aqui};
- Password={sua\_senha\_aqui};


Você pode incluí-los, mas às vezes é melhor obter esses valores do programa com a entrada de teclado do usuário. Isso depende.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->
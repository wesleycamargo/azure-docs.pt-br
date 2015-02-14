###Instalar por meio do Composer

1. [Instalar o Git][install-git].

 
	> [AZURE.NOTE] No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Baixe o **[composer.phar][composer-phar]** na raiz do seu projeto.

4. Abra um prompt de comando e execute esta função na raiz do projeto

		php composer.phar install

###Instalar manualmente

Para baixar e instalar as Bibliotecas de Cliente PHP para o Azure manualmente, siga estas etapas:

1. Baixar um arquivo. zip que contém as bibliotecas de [GitHub][php-sdk-github]. Como alternativa, divida o repositório e clone-o para sua máquina local. (A última opção requer uma conta do GitHub e ter o Git instalado localmente).

	
	> [AZURE.NOTE] As Bibliotecas de Cliente PHP para o Azure têm uma dependência de pacotes PEAR [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime), e [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) . A maneira recomendada para resolver essas dependências é instalando esses pacotes com o [gerenciador de pacotes PEAR](http://pear.php.net/manual/en/installation.php).


2. Copie o diretório do `WindowsAzure` do arquivo baixado para a estrutura de diretórios de aplicativo.

Para obter mais informações sobre como instalar as Bibliotecas de Clientes PHP para o Azure (incluindo informações sobre instalar como um pacote PEAR), consulte [Baixar o SDK do Azure para PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar
<!--HONumber=42-->

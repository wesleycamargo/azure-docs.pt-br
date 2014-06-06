###Instalar por meio do Composer

1. [instalar o Git][install-git]. 

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.</p>
	</div>

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código nele:

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

Para baixar e instalar as Bibliotecas de Clientes do PHP para Azure manualmente, siga estas etapas:

1. Fazer o download de um arquivo. zip que contém as bibliotecas de [GitHub][php-sdk-github]. Como alternativa, divida o repositório e clone-o para sua máquina local. (A última opção requer uma conta do GitHub e ter o Git instalado localmente).

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>As Bibliotecas de Clientes do PHP para Azure têm uma dependência dos pacotes PEAR <a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>, <a href="http://pear.php.net/package/Mail_mime">Mail_mime</a> e <a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a>. A maneira recomendada para resolver essas dependências é instalar esses pacotes usando o <a href="http://pear.php.net/manual/en/installation.php">Gerenciador de pacotes PEAR</a>.</p> 
	</div>


2. Copie o diretório do `WindowsAzure` do arquivo baixado para a estrutura de diretórios de aplicativo.

Para obter mais informações sobre como instalar as Bibliotecas de Clientes PHP para Azure (incluindo informações sobre instalar como um pacote PEAR), consulte [Baixar o SDK do Azure para PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar


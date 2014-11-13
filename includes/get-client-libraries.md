### Instalar por meio do Composer

1.  [instalar o Git][instalar o Git].

    <div class="dev-callout">

    **Observação**
    No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

    </div>

2.  Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

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

3.  Baixe o **[composer.phar][composer.phar]** na raiz do seu projeto.

4.  Abra um prompt de comando e execute esta função na raiz do projeto

        php composer.phar install

### Instalar manualmente

Para baixar e instalar as Bibliotecas de Cliente PHP para o Azure manualmente, siga estas etapas:

1.  Fazer o download de um arquivo. zip que contém as bibliotecas de [GitHub][GitHub]. Como alternativa, divida o repositório e clone-o para sua máquina local. (A última opção requer uma conta do GitHub e ter o Git instalado localmente).

    <div class="dev-callout">

    **Observação**
    As Bibliotecas de Cliente PHP para o Azure têm uma dependência de pacotes PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] e [Mail\_mimeDecode][Mail\_mimeDecode]. A maneira recomendada de se resolver essas dependências é instalar esses pacotes usando o [gerenciador de pacotes PEAR][gerenciador de pacotes PEAR].

    </div>

2.  Copie o diretório do `WindowsAzure` do arquivo baixado para a estrutura de diretórios de aplicativo.

Para obter mais informações sobre como instalar as Bibliotecas de Clientes PHP para o Azure (incluindo informações sobre instalar como um pacote PEAR), consulte [Baixar o SDK do Azure para PHP][Baixar o SDK do Azure para PHP].

  [instalar o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [gerenciador de pacotes PEAR]: http://pear.php.net/manual/en/installation.php
  [Baixar o SDK do Azure para PHP]: ../php-download-sdk/

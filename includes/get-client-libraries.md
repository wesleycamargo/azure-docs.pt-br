### Instalar por meio do Composer
1. [instalar o Git][install-git]. Observe que no Windows, também será necessário adicionar o Git executável à variável de ambiente PATH. 
2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:
   
    ```
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. Baixe o **[composer.phar][composer-phar]** na raiz do seu projeto.
4. Abra um prompt de comando e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=AcomDC_0601_2016-->
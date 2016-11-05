Há algumas maneiras diferentes de instalar os módulos: a Galeria do PowerShell e o Web Platform Installer. O resultado final é praticamente o mesmo, embora a maneira selecionada para fazer a instalação determinará onde os módulos serão instalados por padrão em seu computador.

Quando você instalar da Galeria do PowerShell, seus arquivos serão colocados por padrão em *%ProgramFiles%\\WindowsPowerShell\\Modules*. Quando você instalar do Web Platform Installer, seus arquivos serão colocados por padrão em *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*. Por isso, você deseja continuar com um ou outro para evitar erros ao atualizar seus cmdlets no futuro. O Web Platform Installer recebe os cmdlets atualizados mensalmente. A Galeria recebe versões atualizadas dos cmdlets no momento em que elas são lançadas. Por esse motivo, algumas pessoas preferem usar a Galeria.

Para obter informações adicionais sobre a instalação do Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md).

**Para instalar os módulos da Galeria do PowerShell**

1. Para instalar o módulo do gerenciador de recursos diretamente da Galeria, abra o Windows PowerShell como administrador e digite o seguinte:
   
        Install-Module AzureRM
        Install-AzureRM
2. Depois de instalar os módulos, você precisará importá-los para usá-los:
   
        Import-AzureRM

**Para instalar os módulos usando o Web Platform Installer**

* Você pode instalar os módulos usando o [Web Platform Installer](http://aka.ms/webpi-azps). Quando você clicar no link, isso iniciará o instalador.
* Se você obtiver erros ao usar o Web Platform Installer, talvez já tenha instalado uma versão anterior dos cmdlets usando a Galeria. Confira esta [Postagem de blog](https://azure.microsoft.com/blog/azps-1-0/) que pode ajudá-lo a remover versões anteriores dos módulos e a voltar a trabalhar. Normalmente, os erros acontecerão quando você tiver usado o Web Platform Installer e está mudando para a Galeria, ou o caminho inverso. A remoção dos módulos instalados anteriormente acaba com esse problema e você poderá, então, instalar do novo local.

<!----HONumber=AcomDC_0218_2016-->
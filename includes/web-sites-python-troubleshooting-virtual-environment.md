O script de implantação vai ignorar a criação do ambiente virtual no Azure, caso detecte que um ambiente virtual compatível já existe.  Isso pode acelerar consideravelmente a implantação.  Pacotes que já estão instalados serão ignorados pelo pip.

Em determinadas situações, talvez você queira excluir forçadamente esse ambiente virtual.  Você desejará fazer isso se decidir incluir um ambiente virtual como parte do seu repositório.  Também convém fazer isso se você precisar se livrar de determinados pacotes ou testar alterações ao requirements.txt.

Há algumas opções para gerenciar o ambiente virtual existente no Azure:

### Opção 1: Usar o FTP

Com um cliente FTP, conecte-se ao servidor e você poderá excluir a pasta env.  Observe que alguns clientes FTP (como navegadores da web) podem ser somente leitura e não permitirão que você exclua pastas, portanto você deve certificar-se de usar um cliente FTP com esse recurso.  O usuário e nome do host do FTP são exibidos na página de painel para seu site no portal do Azure.

### Opção 2: Alternar tempo de execução

Aqui está uma alternativa que tira proveito do fato de que o script de implantação excluirá a pasta env quando esta não corresponder à versão desejada do Python.  Isso excluirá efetivamente o ambiente existente e criará um novo.

1. Alterne para uma versão diferente do Python (via runtime.txt ou página de configuração do site)
1. use git push e faça algumas mudanças (ignore quaisquer eventuais erros de instalação de pip)
1. Alterne de volta para a versão inicial do Python
1. use git push e faça algumas mudanças novamente

### Opção 3: Personalize o script de implantação

Se você personalizou o script de implantação, você pode alterar o código em deploy.cmd para forçá-lo para excluir a pasta env.
<!--HONumber=42-->

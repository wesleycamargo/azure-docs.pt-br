## <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação  

Para o FTP e o Git local, é necessário ter um usuário de implantação configurado no servidor para autenticar sua implantação.

> [!NOTE]
> Um usuário de implantação é necessário para a implantação do FTP e do Git Local em um Aplicativo Web.
> O `username` e o `password` são no nível da conta. Eles são diferentes das credenciais da sua conta de assinatura do Azure.
>

Execute o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar suas credenciais de implantação.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

O nome de usuário deve ser exclusivo e a senha deve ser forte. Se receber um erro ` 'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte.

Você só precisa criar esse usuário de implantação de uma vez; é possível usá-lo para todas as implantações do Azure.

Tome nota do nome de usuário e da senha, pois eles serão usados mais tarde, em uma etapa posterior, quando você implantar o aplicativo.
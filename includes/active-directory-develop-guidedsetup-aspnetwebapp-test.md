## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo no Visual Studio, pressione **F5** para executar seu projeto. O navegador abre para o local http://<span></span>localhost:{port} e você visualiza o botão **Entrar com a conta da Microsoft**. Selecione o botão para iniciar o processo de entrada.

Quando estiver pronto para executar o teste, use uma conta do Microsoft Azure AD (Microsoft Azure Active Directory) (conta corporativa ou de estudante) ou uma conta pessoal da Microsoft (<span>live.</span>com ou <span>outlook.</span>com) para entrar.

![Entrar com a Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Entre na sua conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Veja os resultados de aplicativo
Depois de entrar, o usuário será redirecionado para a home page do seu site. A home page é a URL HTTPS especificada nas informações de registro do aplicativo no Portal de Registro de Aplicativos da Microsoft. A home page inclui uma mensagem de boas-vindas "Olá \<Usuário>," um link para sair e um link para exibir as declarações de usuário. O link para as declarações de usuário navega até o controlador **Autorizar** criado anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Navegue para ver as declarações de usuário
Para ver as declarações de usuário, selecione o link para navegar até a exibição do controlador disponível somente para usuários autenticados.

#### <a name="view-the-claims-results"></a>Exibir os resultados de declarações
Depois de navegar para a exibição do controlador, você deverá visualizar uma tabela que contém as propriedades básicas para o usuário:

|Propriedade |Valor |DESCRIÇÃO |
|---|---|---|
|**Nome** |Nome completo do usuário | O nome e sobrenome do usuário.
|**Nome de Usuário** |usuário<span>@domain.com</span> | O nome de usuário que é usado para identificar o usuário.
|**Assunto** |Assunto |Uma cadeia de caracteres que identifica de forma exclusiva o usuário na Web.|
|**ID do locatário** |Guid | Um **guid** que representa exclusivamente a organização do Microsoft Azure Active Directory do usuário.|

Além disso, você deve ver uma tabela de todas as declarações que estão na solicitação de autenticação. Para obter mais informações, consulte a [lista de declarações que estão em um Token de ID do Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tenha um atributo Autorizar (opcional)
Para testar o acesso ao **Autorizar** para declarações de usuário como usuário anônimo, execute essas etapas:
1. Selecione o link para desconectar o usuário e concluir o processo de saída.
2. No seu navegador, digite http://<span></span>localhost:{port}/authenticated para acessar o controlador que está protegido com o atributo **Autorizar**.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido
Você será solicitado a autenticar para usar a exibição do controlador protegido.

## <a name="additional-information"></a>Informações adicionais

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteja todo o seu site
Para proteger todo o seu site, no arquivo **Global.asax**, adicione o atributo **AuthorizeAttribute** ao filtro **GlobalFilters** no método **Application_Start**:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Limite o acesso de entrada para seu aplicativo
Por padrão, contas pessoais como outlook.com, live.com e outros podem entrar no seu aplicativo. As contas corporativas ou de estudante em organizações integradas com o Microsoft Azure Active Directory também podem entrar, por padrão.

Para restringir acesso de entrada de usuário para seu aplicativo, há várias opções disponíveis.

#### <a name="restrict-access-to-a-single-organization"></a>Restringir o acesso a uma única organização
É possível restringir o acesso de entrada para o seu aplicativo apenas a contas de usuários que estão em uma única organização doMicrosoft Azure Active Directory:
1. No arquivo **web.config**, altere o valor para o parâmetro **Locatário**. Altere o valor de **Common** para o nome do locatário da organização, como **contoso.onmicrosoft.com**.
2. Na classe **OWIN Startup**, defina o argumento **ValidateIssuer** para **true**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Restringir o acesso a uma lista de organizações
É possível restringir acesso de entrada apenas a contas de usuários que estão em uma organização do Microsoft Azure Active Directory que esteja na lista de organizações permitidas:
1. No arquivo **web.config**, na classe **OWIN Startup**, defina o argumento **ValidateIssuer** para **true**.
2. Defina o valor do parâmetro **ValidIssuers** para a lista de organizações permitidas.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Usar um método personalizado para validar emissores
É possível implementar um método personalizado para validar emissores usando o parâmetro **IssuerValidator**. Para obter mais informações sobre como usar esse parâmetro, leia sobre a [classe TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) no MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

Alterne para a janela do navegador que contém o portal do Azure.

#### <a name="add-hostname"></a>Adicionar nome do host

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>Validar nome de host

Digite o nome de domínio totalmente qualificado para o qual você configurou o registro CNAME anteriormente (por exemplo, `www.contoso.com`), depois clique em **Validar**.

Selecione a opção **CNAME (www.example.com ou qualquer subdomínio)** no cabeçalho do **Tipo de registro de nome de host**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.
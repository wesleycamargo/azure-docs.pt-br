
O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## <a id="certificates"></a>Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1. Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2. Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente do Certificado** e clique em **Solicitar um certificado de um autoridade do certificado...**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se a opção **Salvo em disco** está selecionada e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e então clique em **Salvar**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

## <a id="register"></a>Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Provisionamento do iOS</a> no Apple Developer Center, faça logon com a sua Apple ID, clique em **Identificadores**, em seguida, clique em **IDs de Aplicativo** e, finalmente, clique no sinal de **+** para registrar um novo aplicativo.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE] Se você optar por fornecer um <strong>identificador de pacote</strong> com valor diferente de <i>mobileservices. QuickStart</i>, você deve também atualizar o valor do identificador de pacote em seu projeto Xcode. É recomendável que você use o valor exato do identificador de pacote já usado no seu projeto de início rápido.

2. Digite um nome para o seu aplicativo em **Descrição**, insira o valor MobileServices.Quickstart em **Identificador de Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativo" e depois clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   	Isso gerará sua ID do aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


   	Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. Localize a ID de aplicativo que acabou de criar e clique na respectiva linha.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   	Clicar na ID do aplicativo faz com que os detalhes sobre o aplicativo e a ID do aplicativo sejam exibidos. Clique no botão **Configurações**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   	Isso exibirá o assistente "Adicionar Certificado de iOS".

    > [AZURE.NOTE] Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5. Clique em **Selecionar arquivo**, vá até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Depois que o certificado for criado pelo portal, clique no botão **Download** e clique em **Concluído**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   	Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [AZURE.NOTE] Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7. Clique duas vezes no certificado de push baixado, **aps_development.cer**.

   	Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [AZURE.NOTE] O nome em seu certificado pode ser diferente, mas ele será prefixado como **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

## <a id="profile"></a>Criar um perfil de provisionamento para o aplicativo

1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de provisionamento iOS</a>, selecione **perfis de provisionamento**, selecione **Tudo** e, em seguida, clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. Na tela **Selecionar certificados**, selecione o certificado criado anteriormente e clique em **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Finalmente, selecione um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e, em seguida, clique em **Concluído**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	Isso criará um novo perfil de provisionamento.

7. No Xcode, abra o Organizador, selecione a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e clique no botão **Atualizar** localizado na parte inferior do painel central.

8. Como alternativa, no menu Xcode, selecione **Preferências** e depois **Contas**. Selecione sua ID de Desenvolvedor da Apple no painel esquerdo. Clique no botão **Exibir Detalhes** à direita. Na janela pop-over, clique no botão circular **Atualizar**. Isso atualiza a lista de perfis de provisionamento. Esse processo pode levar alguns minutos. Recomendamos que você clique em **Atualizar** 2 ou 3 vezes até ver o novo perfil de provisionamento. Além disso, confirme se o identificador de pacote deste projeto Xcode é idêntico ao identificador de pacote associado à ID do aplicativo e o perfil de provisionamento que você criou até agora.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. Em **Destinos**, clique em **Quickstart**, expanda **Identidade de Assinatura de Código** e, em seguida, selecione o novo perfil em **Depurar**. Isso garantirá que o projeto Xcode use o novo perfil para a assinatura de código. Em seguida, carregue o certificado no Azure.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)
<!--HONumber=42-->

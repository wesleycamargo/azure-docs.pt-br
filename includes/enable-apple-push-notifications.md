O APNS (Serviço de Notificação por Push da Apple) usa certificados para autenticar seu serviço móvel. Siga estas instruções para criar os certificados necessários e carregá-los no seu Serviço Móvel. Para a documentação oficial do recurso APNS, consulte [Serviço de Notificação por Push da Apple][Serviço de Notificação por Push da Apple].

##Gerar o arquivo de Solicitação de Assinatura de Certificado

Primeiro, gere o arquivo CSR (Solicitação de Assinatura de Certificado), que é usado pela Apple para gerar um certificado assinado.

1.  Na pasta Utilities, execute a ferramenta Acesso do Conjunto de Chaves.

2.  Clique em **Acesso do Conjunto de Chaves**, expanda **Assistente de Certificado** e clique em **Solicitar um Certificado de uma Autoridade de Certificação...**.

    ![][0]

3.  Selecione seu **Endereço de Email de Usuário** e seu **Nome Comum**, verifique se **Salvo em disco** está selecionado e, em seguida, clique em **Continuar**. Deixe o campo **Endereço de Email de CA** em branco, pois ele não é necessário.

    ![][1]

4.  Digite um nome para o arquivo CSR (Solicitação de Assinatura de Certificado) em **Salvar como**, selecione o local em **Onde** e, em seguida, clique em **Salvar**.

    ![][2]

    Isso salvará o arquivo CSR no local selecionado; o local padrão está situado na Área de Trabalho. Lembre-se do local escolhido para esse arquivo.

Em seguida, você registrará seu aplicativo na Apple, habilitará as notificações por push e carregará esse CSR exportado para criar um certificado de push.

##Registrar seu aplicativo para notificações por push

Para poder enviar notificações por push para um aplicativo iOS dos serviços móveis, você deve registrar seu aplicativo na Apple e também registrar para notificações por push.

1.  Se você ainda não registrou seu aplicativo, navegue até o [Portal de Provisionamento do iOS][Portal de Provisionamento do iOS] no Centro de Desenvolvedores da Apple. Faça logon com sua ID da Apple, clique em **Identificadores**, em **IDs do Aplicativo.** Finalmente, clique no sinal **+** para registrar um novo aplicativo.

    ![][3]

2.  Digite um nome para o seu aplicativo em **Descrição**, insira o valor *MobileServices.Quickstart* em **Identificador do Pacote**, marque a opção "Notificações por Push" na seção "Serviços de Aplicativos" e clique em **Continuar**. Este exemplo usa a ID **MobileServices.Quickstart**, mas você não pode reutilizar essa mesma ID, pois as IDs de aplicativo devem ser exclusivas para todos os usuários. Portanto, recomenda-se acrescentar o nome completo ou as iniciais após o nome do aplicativo.

    ![][4]

    Isso gerará sua ID de aplicativo e solicitará a você para **Enviar** as informações. Clique em **Enviar**.

    ![][5]

    Após clicar em **Enviar**, você verá a tela **Registro concluído**, conforme mostrado abaixo. Clique em **Concluído**.

    ![][6]

    > [WACOM.NOTE] Se optar por fornecer um valor de <strong>Identificador do Pacote</strong> diferente de <i>MobileServices.Quickstart</i>, você também deverá atualizar o valor do identificador do pacote em seu projeto Xcode.

3.  Localize a ID de aplicativo que acabou de criar e clique na respectiva linha.

    ![][7]

    Clicar na ID do aplicativo faz com que os detalhes sobre o aplicativo e a ID do aplicativo sejam exibidos. Clique no botão **Configurações**.

    ![][8]

4.  Role até a parte inferior da tela e clique no botão **Criar Certificado...** na seção **Certificado SSL por Push para Desenvolvimento**.

    ![][9]

    Isso exibirá o assistente "Adicionar Certificado de iOS".

    > [WACOM.NOTE] Este tutorial usa um certificado de desenvolvimento. O mesmo processo é usado para registrar um certificado de produção. Verifique apenas se você definiu o mesmo tipo de certificado ao carregar o certificado para os Serviços Móveis.

5.  Clique em **Escolher Arquivo**, navegue até o local em que salvou o arquivo CSR criado na primeira tarefa e clique em **Gerar**.

    ![][10]

6.  Depois que o certificado for criado pelo portal, clique no botão **Baixar** e, em seguida, clique em **Concluído**.

    ![][11]

    Isso baixará o certificado de assinatura e salvará esse certificado no seu computador, na pasta Downloads.

    ![][12]

    > [WACOM.NOTE] Por padrão, o arquivo baixado, um certificado de desenvolvimento, é denominado **aps_development.cer**.

7.  Clique duas vezes no certificado de push baixado, **aps_development.cer**.

    Isso instalará o novo certificado no Conjunto de Chaves, conforme mostrado abaixo:

    ![][13]

    > [WACOM.NOTE] O nome em seu certificado pode ser diferente, mas ele terá o prefixo **Serviços de Notificação por Push do iOS para Desenvolvimento da Apple:**.

Posteriormente, você usará esse certificado para gerar um arquivo. p12 e carregá-lo para os Serviços Móveis para habilitar a autenticação com APNS.

##Criar um perfil de provisionamento para o aplicativo

1.  De volta ao [Portal de Provisionamento do iOS][Portal de Provisionamento do iOS], selecione **Perfis de Provisionamento**, escolha **Tudo** e clique no botão **+** para criar um novo perfil. Isso iniciará o Assistente **Adicionar Perfil de Provisionamento do iOS**

    ![][14]

2.  Selecione **Desenvolvimento de Aplicativos do iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e clique em **Continuar**

    ![][15]

3.  Em seguida, selecione a ID para o aplicativo Quickstart de Serviços Móveis na lista suspensa **ID do Aplicativo** e clique em **Continuar**

    ![][16]

4.  Na tela **Selecionar certificados**, selecione o certificado criado anteriormente e clique em **Continuar**

    ![][17]

5.  Em seguida, selecione os **Dispositivos** a serem usados no teste e clique em **Continuar**

    ![][18]

6.  Finalmente, selecione um nome para o perfil em **Nome do Perfil**, clique em **Gerar** e, em seguida, clique em **Concluído**

    ![][19]

    Isso criará um novo perfil de provisionamento.

7.  No Xcode, abra o Organizador, selecione a exibição Dispositivos, selecione **Perfis de Provisionamento** na seção **Biblioteca** no painel esquerdo e clique no botão **Atualizar** localizado na parte inferior do painel central.

    ![][20]

8.  Em **Destinos**, clique em **Quickstart**, expanda **Identidade de Assinatura de Código** e, em seguida, selecione o novo perfil em **Depurar**.

    ![][21]

Isso garantirá que o projeto Xcode use o novo perfil para a assinatura do código. Em seguida, carregue o certificado no Azure.

  [Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  [0]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png

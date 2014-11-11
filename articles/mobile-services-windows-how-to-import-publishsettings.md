<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Importar seu arquivo de configurações de assinatura no Visual Studio 2013

Antes de criar o serviço móvel, você deve importar o arquivo de configurações de publicação de sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome.

1.  No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito no projeto, clique em **Adicionar** e, em seguida, em **Serviço Conectado**.

    ![adicionar serviço conectado][adicionar serviço conectado]

2.  No diálogo Gerenciador de Serviços, clique em **Criar serviço...**, selecione **\<Importar...\>** em **Assinatura** no diálogo Criar Serviço Móvel.

    ![criar um novo serviço móvel do VS 2013][criar um novo serviço móvel do VS 2013]

3.  Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, faça logon em sua conta do Azure (se necessário) e clique em **Salvar** quando o navegador solicitar que você salve o arquivo.

    ![baixe o arquivo de inscrição no VS][baixe o arquivo de inscrição no VS]

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>A janela de logon &eacute; exibida no navegador, que pode estar por tr&aacute;s da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Voc&ecirc; poder&aacute; ignorar esta etapa se o seu projeto j&aacute; estiver conectado &agrave; sua assinatura do Azure.</p></div>

4.  Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e então em **Importar**.

    ![importar assinatura no VS][importar assinatura no VS]

    O Visual Studio importa os dados necessários para conectar-se à sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o de seguran&ccedil;a</strong> <p>Depois de importar as configura&ccedil;&otilde;es de publica&ccedil;&atilde;o, considere a possibilidade de excluir o arquivo .publishSettings baixado, pois ele cont&eacute;m informa&ccedil;&otilde;es que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se voc&ecirc; pretende mant&ecirc;-lo para uso em outros projetos de aplicativo conectado.</p></div>

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [adicionar serviço conectado]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [criar um novo serviço móvel do VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [baixe o arquivo de inscrição no VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importar assinatura no VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png

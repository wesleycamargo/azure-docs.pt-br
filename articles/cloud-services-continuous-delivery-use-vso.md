<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publicando com o TFS" pageTitle="Entrega contínua com o Visual Studio Online no Azure" metaKeywords="" description="Saiba como configurar seus projetos de equipe do Visual Studio Online para criação e implantação automáticas em sites ou serviços de nuvem do Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Entrega contínua no Azure usando o Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />




# Entrega contínua no Azure usando o Visual Studio Online

O Visual Studio Online (anteriormente Team Foundation Service) é uma versão de serviço hospedada na nuvem do software popular Team Foundation Server (TFS) da Microsoft que fornece código-fonte altamente personalizável e gerenciamento de compilação, desenvolvimento ágil e fluxo de trabalho de processo de equipe, acompanhamento de problemas e itens de trabalho e muito mais.  Você pode configurar seus projetos de equipe do Visual Studio Online para compilação e implantação automática em sites ou serviços de nuvem do Azure.  Para obter informações sobre como configurar uma compilação contínua e implantar um sistema usando um Team Foundation Server local, consulte [Entrega contínua de serviços de nuvem no Azure](../cloud-services-dotnet-continuous-delivery).

Este tutorial pressupõe que você possui o Visual Studio 2013 e o SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Online, siga estas etapas:

-   [Etapa 1: Inscrever-se no Visual Studio Online.][]

-   [Etapa 2: Fazer check-in em um projeto para controle do código-fonte.][]

-   [Etapa 3: Conectar o projeto ao Azure.][]

-   [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.][]

-   [Etapa 5: Reimplantar uma compilação anterior (opcional)][]

-   [Etapa 6: Alterar a implantação na produção (somente serviços de nuvem)][]

<h2> <a name="step1"></a><span class="short-header">Inscrever-se no Visual Studio Online</span>Etapa 1: Inscrever-se no Visual Studio Online</h2>

1. Crie uma conta no Visual Studio Online navegando para [http://www.visualstudio.com](http://www.visualstudio.com). Clique no link **Entrar**.
  Você precisará entrar usando uma conta da Microsoft. Se esta for a primeira vez que você entra, você será solicitado a fornecer algumas informações, como seu nome e endereço de email.
![][0]
  
2. Se esta não for a primeira vez que você entra, você verá esta tela ao entrar. Clique no link **Criar uma conta gratuita agora**.<br/>
![][36]

3. Crie uma URL de conta para seu novo projeto. Sua conta terá o formato: https://&lt;accountname&gt;.visualstudio.com.<br/>
![][37]
 
4. Agora você pode criar o seu primeiro projeto. Digite o nome e a descrição do projeto. Escolha o sistema de controle de versão que você deseja usar. O Team Foundation Version Control (TFVC) ou o Git são suportados.  Você pode encontrar mais informações sobre essas opções em [Usar controle de versão](http://go.microsoft.com/fwlink/?LinkId=324037). Este passo a passo pressupõe que você está usando o TFVC. Em seguida, escolha o modelo de processo que sua organização usa e selecione o botão **Criar Projeto**. Para obter mais informações sobre modelos de processo, consulte [Trabalhar com artefatos de projeto de equipe, escolher um modelo de processo](http://go.microsoft.com/fwlink/?LinkId=324035).<br/>
![][1]

5. Quando a criação do projeto for concluída, clique no botão **Abrir com o Visual Studio para conectar** para iniciar automaticamente o Visual Studio conectado ao seu projeto de equipe. Se você vir as caixas de diálogo de segurança, selecione Permitir.<br/>
![][2]

<h2><a name="step2"> </a><span class="short-header">Fazer check-in em um projeto para controle do código-fonte.</span>Etapa 2: Fazer check-in em um projeto para controle do código-fonte</h2>

1. No Visual Studio, abra a solução que você deseja implantar ou crie uma nova.
Você pode implantar um site ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo.
Se você desejar criar uma nova solução, crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC e escolha o aplicativo da Internet para a função web. Quando solicitado, escolha **Aplicativo da Internet**.
Se você desejar criar um site, escolha o modelo de projeto de Aplicativo Web ASP.NET e escolha MVC. Consulte [Introdução ao Azure e ao ASP.NET](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-dotnet-get-started/).

2. Abra o menu de contexto da solução e selecione **Adicionar solução ao controle do código-fonte**.<br/>
![][5]

3. Aceite ou altere os padrões e escolha o botão **OK**. Quando o processo estiver concluído, os ícones do controle do código-fonte aparecerão no Gerenciador de Soluções.<br/>
![][6]

4. Abra o menu de atalho da solução e escolha **Fazer Check-In**.<br/>
![][7]

5. Na área Alterações Pendentes do Team Explorer, digite um comentário para o check-in e escolha o botão **Fazer Check-In**.<br/>
![][8]

<br/>
Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se desejar que as alterações sejam excluídas, escolha o link **Incluir Tudo**.<br/>
![][9]

<h2> <a name="step3"> </a><span class="short-header">Conectar o projeto ao Azure</span>Etapa 3: Conectar o projeto ao Azure</h2>

1. Agora que você possui um projeto de equipe do VSO com algum código-fonte nele, você está pronto para conectar seu projeto de equipe ao Azure.  No [Portal do Azure](http://manage.windowsazure.com), selecione o serviço de nuvem ou site ou crie um novo selecionando o ícone + na parte inferior esquerda e escolhendo **Serviço de Nuvem** ou **Site** e, em seguida, **Criação Rápida**. Escolha o link **Configurar publicação com o Visual Studio Online**.<br/>
![][10]

2. No assistente, digite o nome de sua conta do Visual Studio Online na caixa de texto e clique no link **Autorizar Agora**. Você pode ser solicitado a entrar.<br/>
![][11]

3. Na caixa de diálogo pop-up OAuth, escolha **Aceitar** para autorizar o Azure a configurar seu projeto de equipe no VSO.<br/>
![][12]

4. Quando a autorização for bem-sucedida, você verá uma caixa suspensa contendo uma lista de seus projetos de equipe do Visual Studio Online.  Selecione o nome do projeto de equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.<br/>
![][13]

5. Quando seu projeto estiver vinculado, você verá algumas instruções para fazer check-in das alterações em seu projeto de equipe do Visual Studio Online.  Em seu próximo check-in, o Visual Studio Online irá compilar e implantar seu projeto no Azure.  Tente isso agora clicando no link **Fazer Check-In no Visual Studio** e no link **Iniciar o Visual Studio** (ou o botão **Visual Studio** equivalente na parte inferior da tela do portal).<br/>
![][14]

<h2><a name="step4"> </a><span class="short-header">Disparar uma recompilação</span>Etapa 4: Disparar uma recompilação e reimplantar seu projeto</h2>

1. No Team Explorer do Visual Studio, clique no link **Gerenciador de Controle do Código-Fonte**.<br/>
![][15]

2. Navegue para seu arquivo de solução e abra-o.<br/>
![][16]

3. No Gerenciador de Soluções, abra um arquivo e altere-o. Por exemplo, altere o arquivo _Layout.cshtml na pasta Views\Shared em uma função web do MVC.<br/>
![][17]

4. Edite o logotipo do site e pressione Ctrl+S para salvar o arquivo.<br/>
![][18]

5. No Team Explorer, escolha o link **Alterações Pendentes**.<br/>
![][19]

6. Digite um comentário e escolha o botão **Fazer Check-In**.<br/>
![][20]

7. Escolha o botão Início para retornar à home page do Team Explorer.<br/>
![][21]

8. Escolha o link **Compilações** para exibir as compilações em andamento.<br/>
![][22]
<br/>
O Team Explorer mostra que uma compilação foi disparada para seu check-in.<br/>
![][23]

9. Clique duas vezes no nome da compilação em andamento para exibir um log detalhado enquanto a compilação está em andamento.<br/>
![][24]

10. Enquanto a compilação estiver em andamento, examine a definição da compilação que foi criada quando você vinculou o TFS no Azure usando o assistente.  Abra o menu de atalho da definição da compilação e escolha **Editar Definição da Compilação**.<br/>
![][25]
<br/>
Na guia **Disparador**, você verá que a definição da compilação está definida, por padrão, para compilar em cada check-in.<br/>
![][26]
<br/>
Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou site. Se estiver trabalhando com sites, as propriedades que você verá serão diferentes daquelas mostradas aqui.<br/>
![][27]
<br/>
Especifique valores para as propriedades se você desejar valores diferentes dos padrões.
A tabela a seguir mostra os valores padrão das propriedades de um serviço de nuvem:
	<table>
<tr><td><b>Propriedade</b></td><td><b>Valor Padrão</b></td></tr>
<tr><td>Permitir Atualização</td><td>True</td></tr>
<tr><td>Ambiente de Serviço de Nuvem</td><td>Preparo</td></tr>
<tr><td>Nome do Serviço de Nuvem</td><td>O nome do serviço ao qual você está conectado</td></tr>
<tr><td>Rótulo de Implantação</td><td>O mesmo que o nome do serviço</td></tr>
<tr><td>Configuração do Serviço</td><td>ServiceConfiguration.Cloud.cscfg</td></tr>
<tr><td>Nome da Conta de Armazenamento</td><td>Em branco, o que significa tentar localizar uma conta de armazenamento.</td></tr>
<tr><td>Perfil de Publicação</td><td>O arquivo .azurePubxml. Se você fez check-in em um, poderá escolhê-lo aqui.</td></tr>
</table>
<br/>
Se a propriedade da conta de armazenamento for deixada em branco, o Azure pesquisará uma. Se houver uma conta de armazenamento com o mesmo nome do serviço de nuvem, ela será usada. Caso contrário, o Azure usará outra conta de armazenamento ou, se não houver nenhuma conta de armazenamento, ele criará uma. A conta de armazenamento fornece um local no Azure para armazenamento de arquivos e outros dados. Para obter mais informações, consulte [O que é uma conta de armazenamento? (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/storage-whatis-account).

11. A essa altura, sua compilação deve estar concluída com êxito.<br/>
![][28]

12. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste associado aos projetos de teste de unidade.<br/>
![][29]

13. No [Portal do Azure](http://manage.windowsazure.com), você poderá exibir a implantação associada na guia Implantações quando o ambiente de preparo estiver selecionado.<br/>
![][30]

14.	Navegue até a URL do site. Para um site, basta clicar no botão Procurar na barra de comandos. Para um serviço de nuvem, escolha a URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo de um serviço de nuvem. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade Ambiente de Serviço de Nuvem Alternativo para a produção. Esta captura de tela mostra onde a URL do site está na página do painel do serviço de nuvem: <br/>
![][31]
<br/>
Uma nova guia do navegador será aberta para revelar seu site em execução.<br/>
![][32]

15.	Para serviços de nuvem, se você fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. A última marcada como Ativa.<br/>
![][33]

<h2> <a name="step5"> </a><span class="short-header">Reimplantar uma compilação anterior</span>Etapa 5: Reimplantar uma compilação anterior</h2>

Esta etapa se aplica aos serviços de nuvem e é opcional. No Portal de Gerenciamento, selecione uma implantação anterior e clique no botão **Reimplantar** para retroceder seu site para um check-in anterior.  Observe que isso irá disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.<br/>
![][34]

<h2> <a name="step6"> </a><span class="short-header">Alterar a implantação de produção</span>Etapa 6: Alterar a implantação de produção</h2>

Esta etapa se aplica somente aos serviços de nuvem, não aos sites. Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo o botão Permutar no Portal de Gerenciamento. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.<br/>
![][35]

Para obter mais informações, consulte [Visual Studio Online (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=253861). Se estiver usando o Git, consulte [Compartilhar seu código no Git (a página pode estar em inglês)](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Publicando no controle do código-fonte para sites do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-publish-source-control).

[Etapa 1: Inscrever-se no Visual Studio Online.]: #step1
[Etapa 2: Fazer check-in em um projeto para controle do código-fonte.]: #step2
[Etapa 3: Conectar o projeto ao Azure.]: #step3
[Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]: #step4
[Etapa 5: Reimplantar uma compilação anterior (opcional)]: #step5
[Etapa 6: Alterar a implantação na produção (somente serviços de nuvem)]: #step6
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG


<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with Visual Studio Online" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen"></tags>

# Entrega contínua no Azure usando o Visual Studio Online

Você pode configurar seus projetos de equipe do Visual Studio Online para compilação e implantação automática em sites ou serviços de nuvem do Azure. (Para obter informações sobre como configurar uma compilação contínua e implantar um sistema usando um Team Foundation Server *local*, consulte [Entrega contínua de serviços de nuvem no Azure][Entrega contínua de serviços de nuvem no Azure].)

Este tutorial pressupõe que você possui o Visual Studio 2013 e o SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com][www.visualstudio.com]. Instale o SDK do Azure [aqui][aqui].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Visual Studio Online:</h5>
<p>Voc&ecirc; pode <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">abrir uma conta no Visual Studio Online gratuitamente</a>.</p>
</div>

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Online, siga estas etapas:

-   [Etapa 1: Crie um projeto em equipe.][Etapa 1: Crie um projeto em equipe.]

-   [Etapa 2: Fazer check-in em um projeto para o controle do código-fonte.][Etapa 2: Fazer check-in em um projeto para o controle do código-fonte.]

-   [Etapa 3: Conectar o projeto no Azure.][Etapa 3: Conectar o projeto no Azure.]

-   [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.][Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]

-   [Etapa 5: Reimplantar uma compilação anterior (opcional)][Etapa 5: Reimplantar uma compilação anterior (opcional)]

-   [Etapa 6: Alterar a implantação da produção (somente serviços de nuvem)][Etapa 6: Alterar a implantação da produção (somente serviços de nuvem)]

-   [Etapa 7: Executar testes de unidade (opcional)][Etapa 7: Executar testes de unidade (opcional)]

## <a name="step1"></a><span class="short-header">Crie um projeto em equipe</span>Etapa 1: Crie um projeto em equipe

Siga as instruções contidas [aqui][1] para criar seu projeto de equipe e vinculá-lo ao Visual Studio. Este passo a passo pressupõe que você está usando oo Controle de Versão do Team Foundation (TFVC). Se quiser usar o Git para controle de versão, consulte [a versão do Git neste passo a passo][a versão do Git neste passo a passo].

## <a name="step2"> </a><span class="short-header">Fazer check-in em um projeto para o controle do código-fonte.</span>Etapa 2: Fazer check-in em um projeto para o controle do código-fonte

1.  No Visual Studio, abra a solução que você deseja implantar ou crie uma nova. Você pode implantar um site ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo. Se você desejar criar uma nova solução, crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC e escolha o aplicativo da Internet para a função web. Quando solicitado, escolha **Aplicativo da Internet**. Se você desejar criar um site, escolha o modelo de projeto de Aplicativo Web ASP.NET e escolha MVC. Consulte [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET].

2.  Abra o menu de contexto da solução e selecione **Adicionar solução ao controle do código-fonte**.

    ![][]

3.  Aceite ou altere os padrões e escolha o botão **OK**. Quando o processo estiver concluído, os ícones do controle do código-fonte aparecerão no Gerenciador de Soluções.

    ![][2]

4.  Abra o menu de atalho da solução e escolha **Fazer Check-In**.

    ![][3]

5.  Na área Alterações Pendentes do Team Explorer, digite um comentário para o check-in e escolha o botão **Fazer Check-In**.

    ![][4]

Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se desejar que as alterações sejam excluídas, escolha o link **Incluir Tudo**.

![][5]

## <a name="step3"> </a><span class="short-header">Conectar o projeto ao Azure</span>Etapa 3: Implantar o projeto no Azure

1.  Agora que você possui um projeto de equipe do VSO com algum código-fonte nele, você está pronto para conectar seu projeto de equipe ao Azure. No [Portal do Azure][Portal do Azure], selecione o serviço de nuvem ou site ou crie um novo selecionando o ícone + na parte inferior esquerda e escolhendo **Serviço de Nuvem** ou **Site** e, em seguida, **Criação Rápida**. Escolha o link **Configurar publicação com o Visual Studio Online**.

    ![][6]

2.  No assistente, digite o nome de sua conta do Visual Studio Online na caixa de texto e clique no link **Autorizar Agora**. Você pode ser solicitado a entrar.

    ![][7]

3.  Na caixa de diálogo pop-up OAuth, escolha **Aceitar** para autorizar o Azure a configurar seu projeto de equipe no VSO.

    ![][8]

4.  Quando a autorização for bem-sucedida, você verá uma caixa suspensa contendo uma lista de seus projetos de equipe do Visual Studio Online. Selecione o nome do projeto de equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.

    ![][9]

5.  Quando seu projeto estiver vinculado, você verá algumas instruções para fazer check-in das alterações em seu projeto de equipe do Visual Studio Online. Em seu próximo check-in, o Visual Studio Online irá compilar e implantar seu projeto no Azure. Tente isso agora clicando no link **Fazer Check-In no Visual Studio** e no link **Iniciar o Visual Studio** (ou o botão **Visual Studio** equivalente na parte inferior da tela do portal).

    ![][10]

## <a name="step4"> </a><span class="short-header">Disparando uma recompilação</span>Etapa 4: Disparar uma recompilação e reimplantar seu projeto

1.  No Team Explorer do Visual Studio, clique no link **Gerenciador de Controle do Código-Fonte**.

    ![][11]

2.  Navegue para seu arquivo de solução e abra-o.

    ![][12]

3.  No Gerenciador de Soluções, abra um arquivo e altere-o. Por exemplo, mude a pasta do arquivo \_Layout.cshtml na pasta Views\\Shared em uma função web do MVC.

    ![][13]

4.  Edite o logotipo do site e pressione Ctrl+S para salvar o arquivo.

    ![][14]

5.  No Team Explorer, escolha o link **Alterações Pendentes**.

    ![][15]

6.  Digite um comentário e escolha o botão **Fazer Check-In**.

    ![][16]

7.  Escolha o botão Início para retornar à home page do Team Explorer.

    ![][17]

8.  Escolha o link **Compilações** para exibir as compilações em andamento.

    ![][18]

    O Team Explorer mostra que uma compilação foi disparada para seu check-in.

    ![][19]

9.  Clique duas vezes no nome da compilação em andamento para exibir um log detalhado enquanto a compilação está em andamento.

    ![][20]

10. Enquanto a compilação estiver em andamento, examine a definição da compilação que foi criada quando você vinculou o TFS no Azure usando o assistente. Abra o menu de atalho da definição da compilação e escolha **Editar Definição da Compilação**.

    ![][21]

    Na guia **Disparador**, você verá que a definição da compilação está definida, por padrão, para compilar em cada check-in.

    ![][22]

    Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou site. Se você estiver trabalhando com sites, as propriedades que você verá serão diferentes daquelas mostradas aqui.

    ![][23]

    Valores específicos para as propriedades, se você quiser valores diferentes dos valores padrão. As propriedades para a publicação do Azure estão na seção de Implantação.
    A tabela seguinte mostra as propriedades disponíveis na seção Implantação:

    <table>

    <tr>
    <td>
    <strong>Propriedade</strong>

    </td>
    <td>
    <strong>Valor Padrão</strong>

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
     Permitir certificados não confiáveis
    
     </td>
    > <td>
     Se falso, os certificados SSL deve ser assinados por uma autoridade raiz.
    
     </td>
    > </tr>
    > <tr>
    > <td>
     Permitir Atualização
    
     </td>
    > <td>
     Permite que uma implantação atualize uma implantação existente, ao invés de criar uma nova. Preserve o endereço IP.
    
     </td>
    > </tr>
    > <tr>
    > <td>
     Não exclua
    
     </td>
    > <td>
     Se verdadeiro, não substitua uma implantação não relacionada existente (a atualização é permitida).
    
     </td>
    > </tr>
    > <tr>
    > <td>
     Caminho para as configurações de implantação
    
     </td>
    > <td>
     O caminho para seu arquivo .pubxml para um site, relativo à pasta raiz do repositório. Ignorado para serviços em nuvem.
    
     </td>
    > </tr>
    > <tr>
    > <td>
     Ambiente de implantação do Sharepoint
    
     </td>
    > <td>
     O mesmo que o nome do serviço
    
     </td>
    > </tr>
    > <tr>
    > <td>
     Ambiente de implantação do Windows Azure:
    
     </td>
    > <td>
     O site ou nome do serviço de nuvem
    
     </td>
    > </tr>
    > </table>

Se está usando configurações de serviço múltipla (arquivos .cscfg), você pode especificar a configuração do serviço desejado na configuração **Compilar, Avançado argumentos MSBuild**. Por exemplo, para usar ServiceConfiguration.Test.cscfg, defina opções de linha de argumentos MSBuild /p:TargetProfile=Test.

![][24]

1.  A essa altura, sua compilação deve estar concluída com êxito.

    ![][25]

2.  Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste associado aos projetos de teste de unidade.

    ![][26]

3.  No [Portal do Azure][Portal do Azure], você poderá exibir a implantação associada na guia Implantações quando o ambiente de preparo estiver selecionado.

    ![][27]

4.  Navegue até a URL do site. Para um site, basta clicar no botão Procurar na barra de comandos. Para um serviço de nuvem, escolha a URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo de um serviço de nuvem. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade Ambiente de Serviço de Nuvem Alternativo para a produção. Esta captura de tela mostra onde a URL do site está na página do painel do serviço de nuvem:

    ![][28]

    Uma nova guia do navegador será aberta para revelar seu site em execução.

    ![][29]

5.  Para serviços de nuvem, se você fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. A última marcada como Ativa.

    ![][30]

## <a name="step5"> </a><span class="short-header">Reimplantar uma compilação anterior</span>Etapa 5: Reimplantar uma compilação anterior

Esta etapa se aplica aos serviços de nuvem e é opcional. No Portal de Gerenciamento, selecione uma implantação anterior e clique no botão **Reimplantar** para retroceder seu site para um check-in anterior. Observe que isso irá disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.

![][31]

## <a name="step6"> </a><span class="short-header">Alterar a implantação de produção</span>Etapa 6: Alterar a implantação de produção

Esta etapa se aplica somente aos serviços de nuvem, não aos sites. Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo o botão Permutar no Portal de Gerenciamento. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.

![][32]

## <a name="step7"> </a><span class="short-header">Executar testes de unidade</span>Etapa 7: Executar testes de unidade

Para colocar um portão de qualidade em suas implantações dinâmicas ou de preparo, você pode executar testes da unidade e se eles falhares, você pode parar a implantação.

1.  No Visual Studio, adicione um projeto de teste de unidade.

    ![][33]

2.  Adicione as referências do projeto aos projetos que deseja testar.

    ![][34]

3.  Adicione alguns testes de unidade. Para começar, tente um teste fictício que sempre passará.

        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }

4.  Edite a definição da compilação, escolha a guia Processar e expanda o nó de Teste.

5.  Defina o **Compilação falha na falha de teste** para Verdadeiro. Isso significa que a implantação não ocorre, a menos que passe no teste.

    ![][35]

6.  Fila de uma nova compilação.

    ![][36]

    ![][37]

7.  Enquanto a compilação está em processamento, verifique seu progresso.

    ![][38]

    ![][39]

8.  Quando a compilação está concluída, verifique os resultados do teste.

    ![][40]

    ![][41]

9.  Tente criar um teste que falhará. Adicione um novo teste ao copiar o primeiro, renomeá-lo e comentar na linha de código que gere o NotImplementedException.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Faça check-in das alterações para a fila de uma nova compilação.

    ![][42]

11. Exina os resultados de teste para ver detalhes sobre a falha.

    ![][43]

    ![][44]

Para saber mais sobre o teste de unidade no Visual Studio Online, consulte [Executar testes de unidade em sua compilação][Executar testes de unidade em sua compilação].

Para obter mais informações, consulte [Visual Studio Online (a página pode estar em inglês)][Visual Studio Online (a página pode estar em inglês)]. Se estiver usando o Git, consulte [Compartilhar seu código no Git][Compartilhar seu código no Git] e [Publicando no controle do código-fonte para sites do Azure][Publicando no controle do código-fonte para sites do Azure].

  [Entrega contínua de serviços de nuvem no Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [aqui]: http://go.microsoft.com/fwlink/?LinkId=239540
  [abrir uma conta no Visual Studio Online gratuitamente]: http://go.microsoft.com/fwlink/p/?LinkId=512979
  [Etapa 1: Crie um projeto em equipe.]: #step1
  [Etapa 2: Fazer check-in em um projeto para o controle do código-fonte.]: #step2
  [Etapa 3: Conectar o projeto no Azure.]: #step3
  [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]: #step4
  [Etapa 5: Reimplantar uma compilação anterior (opcional)]: #step5
  [Etapa 6: Alterar a implantação da produção (somente serviços de nuvem)]: #step6
  [Etapa 7: Executar testes de unidade (opcional)]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=512980
  [a versão do Git neste passo a passo]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Introdução ao Azure e ao ASP.NET]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-dotnet-get-started/
  []: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [Portal do Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [44]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Executar testes de unidade em sua compilação]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Compartilhar seu código no Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publicando no controle do código-fonte para sites do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-publish-source-control

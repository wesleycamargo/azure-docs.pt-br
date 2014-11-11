<properties linkid="dev-net-common-tasks-profiling-in-compute-emulator" urldisplayname="Team Foundation Service" headerexpose="" pageTitle="Profiling a Cloud Service Locally in the Compute Emulator" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="Testing the Performance of a Cloud Service Locally in the Azure Compute Emulator Using the Visual Studio Profiler" authors="ghogen" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Testando o desempenho de um serviço de nuvem localmente no emulador de computação do Azure usando o criador de perfis do Visual Studio

Várias técnicas e ferramentas estão disponíveis para testar o desempenho de serviços de nuvem.
Quando publica um serviço de nuvem no Azure, você pode fazer com que o Visual Studio colete
dados de criação de perfil e, em seguida, os analise localmente, conforme descrito em [Criar o perfil de um aplicativo do Azure][Criar o perfil de um aplicativo do Azure].
Você também pode usar o diagnóstico para acompanhar vários
contadores de desempenho, conforme descrito em [Usando contadores de desempenho no Azure][Usando contadores de desempenho no Azure].
Também convém criar o perfil de seu aplicativo localmente no emulador de computação antes de implantá-lo na nuvem.

Este artigo aborda o método de Amostragem de CPU de criação de perfil, que pode ser executado localmente no emulador. A Amostragem de CPU é um método de criação de perfil que não é muito invasivo. Em um intervalo de amostragem designado, o criador de perfis tira um instantâneo da pilha de chamadas. Os dados são coletados durante um período de tempo e mostrados em um relatório. Esse método de criação de perfis tende a indicar onde está sendo feita a maior parte do trabalho em um aplicativo que utiliza muitos recursos de computação. Isso oferece a oportunidade de focalizar o "afunilamento" onde seu aplicativo está gastando mais tempo.

## Pré-requisitos

É possível executar o criador de perfis localmente apenas se você tiver o Visual Studio Premium ou o Visual Studio Ultimate.

## Neste artigo:

-   [Etapa 1: Configurar o Visual Studio para criação de perfis][Etapa 1: Configurar o Visual Studio para criação de perfis]

-   [Etapa 2: Anexar a um processo][Etapa 2: Anexar a um processo]

-   [Etapa 3: Exibir relatórios de criação de perfil][Etapa 3: Exibir relatórios de criação de perfil]

-   [Etapa 4: Fazer alterações e comparar o desempenho][Etapa 4: Fazer alterações e comparar o desempenho]

-   [Solucionar problemas][Solucionar problemas]

-   [Próximas etapas][Próximas etapas]

## <a name="step1"> </a>Etapa 1: Configurar o Visual Studio para criação de perfis

Em primeiro lugar, há algumas opções de configuração do Visual Studio que podem ser úteis ao criar perfis. Para compreender os relatórios de criação de perfis, você precisará de símbolos (arquivos .pdb) para seu aplicativo e também de símbolos para as bibliotecas do sistema. Você deve certificar-se de fazer referência aos servidores de símbolo disponíveis. Para fazer isso, no menu **Ferramentas** do Visual Studio, escolha **Opções**, escolha **Depuração** e, em seguida, **Símbolos**. Verifique se os Servidores de Símbolo da Microsoft estão listados em **Locais do arquivo de símbolos (.pdb)**. Você também pode fazer referência a <http://referencesource.microsoft.com/symbols>, que pode ter arquivos de símbolos adicionais.

![][0]

Se desejar, você pode simplificar os relatórios que o criador de perfis gera definindo Apenas Meu Código. Com a opção Apenas Meu Código habilitada, as pilhas de chamadas de função são simplificadas de maneira que as chamadas inteiramente internas às bibliotecas e ao .Net Framework sejam ocultados dos relatórios. No menu **Ferramentas**, escolha **Opções**. Em seguida, expanda o nó **Ferramentas de Desempenho** e escolha **Geral**. Marque a caixa de seleção **Habilitar Apenas Meu Código para relatórios do criador de perfis**.

![][1]

Você pode usar essas instruções com um projeto existente ou com um novo projeto. Se você criar um novo projeto para testar as técnicas descritas a seguir, escolha um projeto C# do **Serviço de Nuvem do Azure** e selecione uma **Função Web** e uma **Função de Trabalho**.

![][2]

Para fins de exemplo, adicione um código ao seu projeto que demore
muito tempo e demonstre alguns problemas óbvios de desempenho. Por exemplo, adicione o seguinte código a um projeto de função de trabalho:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Chame esse código no método Run na classe RoleEntryPoint-derived da função de trabalho.

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
    }

Compile e execute seu serviço de nuvem localmente com a configuração da solução definida como **Versão**. Isso garante que todos os arquivos e pastas sejam
criados para executar o aplicativo localmente e garante que todos os emuladores estejam iniciados.

## <a name="step2"> </a>Etapa 2: Anexar a um processo

Em vez de criar o perfil do aplicativo iniciando-o no IDE do Visual Studio 2010, você deve anexar o criador de perfis a um processo em execução.

Para anexar o criador de perfis a um processo, no menu **Analisar**, escolha **Criador de Perfis** e **Anexar/Desanexar**.

![][3]

Para uma função de trabalho, localize o processo WaWorkerHost.exe.

![][4]

Se a pasta de seu projeto estiver em uma unidade de rede, o criador de perfis solicitará que você forneça outro local para salvar os relatórios de criação de perfis.

Também é possível conectar-se a uma função web, conectando-se ao WaIISHost.exe.
 Se houver vários processos de função de trabalho em seu aplicativo, você precisará usar o processID para diferenciá-los. Você pode consultar o processID programaticamente acessando o objeto Process. Por exemplo, adicionando este código ao método Run da classe RoleEntryPoint-derived em uma função, você pode examinar o
log na interface do usuário do Emulador de Computação para saber a qual processo conectar-se.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para exibir o log, inicie a interface do usuário do Emulador de Computação.

![][5]

Abra a janela do console do log da função de trabalho na interface do usuário do Emulador de Computação clicando na barra de títulos da janela do console. Você pode ver a ID do processo no log.

![][6]

Depois de conectar-se, execute as etapas na interface do usuário do seu aplicativo (se necessário) para reproduzir o cenário.

Quando desejar parar a criação de perfis, escolha o link **Parar Criação de Perfis**.

![][7]

## <a name="step3"> </a>Etapa 3: Exibir relatórios de criação de perfil

O relatório de desempenho de seu aplicativo é exibido.

Nesse ponto, o criador de perfis interromperá a execução, salvará os dados em um arquivo .vsp e exibirá um relatório
que mostra uma análise dos dados.

![][8]

Se você vir String.wstrcpy no Afunilamento, clique em Apenas Meu Código para alterar a exibição para mostrar somente o código do usuário. Se você vir String.Concat, tente pressionar o botão Mostrar Todo o Código.

Você verá o método Concatenated e o String.Concat tomando uma grande parte do
tempo de execução.

![][9]

Se você adicionou o código de concatenação de cadeia de caracteres deste artigo, verá um aviso na Lista de Tarefas por isso. Você também poderá ver um aviso de que há uma quantidade excessiva de coleta de lixo, devido ao número de cadeias de caracteres que são descartadas.

![][10]

## <a name="step4"> </a>Etapa 4: Fazer alterações e comparar o desempenho

Você também pode comparar o desempenho antes e depois de uma alteração no código. Edite o código para substituir a operação de concatenação de cadeia de caracteres pelo uso de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Realize outra execução de desempenho e, em seguida, compare o desempenho. No Gerenciador de Desempenho, se as execuções forem na mesma sessão, você poderá selecionar os dois relatórios, abrir o menu de atalho e escolher **Comparar Relatórios de Desempenho**. Se desejar comparar com uma execução em outra sessão de desempenho, abra o menu **Analisar** e escolha **Comparar Relatórios de Desempenho**. Especifique os dois arquivos na caixa de diálogo que é exibida.

![][11]

Os relatórios destacam as diferenças entre as duas execuções.

![][12]

Parabéns! Você começou a usar o criador de perfis.

## <a name="troubleshooting"> </a> Solucionar problemas

-   Verifique se você está criando o perfil de uma compilação de versão.

-   Se a opção Conectar/Desconectar não estiver habilitada no menu Criador de Perfis, execute o Assistente de Desempenho.

-   Use a interface do usuário do Emulador de Computação para exibir o status do seu aplicativo.

-   Se tiver problemas para iniciar aplicativos no emulador ou para conectar o criador de perfis, desligue e reinicie o emulador de computação. Se isso não resolver o problema, tente reinicializar. Esse problema pode ocorrer se você usar o Emulador de Computação para suspender e remover implantações em execução.

-   Se você tiver usado qualquer um dos comandos de criação de perfil na
    linha de comando, especialmente as configurações globais, verifique se VSPerfClrEnv / globaloff foi chamado e se VsPerfMon.exe foi desligado.

-   Se durante a amostragem você vir a mensagem "PRF0025: nenhum dado foi coletado", verifique se o processo ao qual você se conectou tem atividade de CPU. Os aplicativos que não estão fazendo nenhum trabalho de computação podem não produzir nenhum dado de amostragem. Também é possível que o processo tenha sido encerrado antes de qualquer amostra ter sido feita. Verifique se o método Run para uma função para a qual você esteja criando um perfil não é encerrado.

## <a name="nextSteps"> </a>Próximas etapas

A instrumentação de binários do Azure no emulador não tem suporte no criador de perfis do Visual Studio 2010, mas para testar a alocação de memória, você poderá escolher essa opção ao criar um perfil. Você também pode escolher a criação de perfis simultânea, que ajuda a determinar se os threads estão perdendo tempo competindo por bloqueios, ou a criação de perfis de interação de camadas, que ajuda a rastrear problemas de desempenho ao interagir entre camadas de um aplicativo, mais frequentemente entre a camada de dados e uma função de trabalho. Você pode exibir as consultas do banco de dados que seu aplicativo gera e usar os dados da criação de perfis para melhorar o uso do banco de dados. Para obter informações sobre a criação de perfis de interação de camadas, consulte [Passo a passo: usando o criador de perfis de interação de camadas no Visual Studio Team System 2010.][Passo a passo: usando o criador de perfis de interação de camadas no Visual Studio Team System 2010.].

  [Criar o perfil de um aplicativo do Azure]: http://msdn.microsoft.com/pt-BR/library/windowsazure/hh369930.aspx
  [Usando contadores de desempenho no Azure]: http://www.windowsazure.com/pt-BR/develop/net/common-tasks/performance-profiling
  [Etapa 1: Configurar o Visual Studio para criação de perfis]: #step1
  [Etapa 2: Anexar a um processo]: #step2
  [Etapa 3: Exibir relatórios de criação de perfil]: #step3
  [Etapa 4: Fazer alterações e comparar o desempenho]: #step4
  [Solucionar problemas]: #troubleshooting
  [Próximas etapas]: #nextSteps
  [0]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
  [1]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
  [2]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
  [3]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
  [4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
  [5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
  [6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
  [7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
  [8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
  [9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
  [10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png
  [11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
  [12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
  [Passo a passo: usando o criador de perfis de interação de camadas no Visual Studio Team System 2010.]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx

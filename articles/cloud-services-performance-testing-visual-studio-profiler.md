<properties 
	urldisplayname="Team Foundation Service" 
	headerexpose="" 
	pageTitle="Criando um perfil de serviço de nuvem localmente no emulador de computação" 
	metakeywords="" 
	footerexpose="" 
	description="Saiba como testar o desempenho de um serviço de nuvem no Emulador de computação do Azure local usando o criador de perfil do Visual Studio" 
	umbraconavihide="0" 
	disquscomments="1" 
	authors="kempb" 
	manager="douge" 
	editor="tglee" 
	services="cloud-services" 
	documentationCenter=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>

# Testando o desempenho de um serviço de nuvem localmente no emulador de computação do Azure usando o criador de perfis do Visual Studio

Várias técnicas e ferramentas estão disponíveis para testar o desempenho de serviços de nuvem.
Quando você publica um serviço de nuvem no Azure, você pode ter o Visual Studio coletando os dados da criação de perfil
e os analisando localmente, conforme descrito em [criação de perfil de um aplicativo do Azure][1].
Você também pode usar o diagnóstico para controlar uma variedade de contadores de desempenho
, conforme descrito em [Usando os contadores de desempenho no Azure][2].
Também convém criar o perfil de seu aplicativo localmente no emulador de computação antes de implantá-lo na nuvem.

Este artigo aborda o método de Amostragem de CPU da criação de perfil, que pode ser executado localmente no emulador. A Amostragem de CPU é um método de criação de perfil que não é muito invasivo. Em um intervalo de amostragem designado, o criador de perfis faz um instantâneo da pilha de chamadas. Os dados são coletados durante um período de tempo e mostrados em um relatório. Esse método de criação de perfis tende a indicar onde está sendo feita a maior parte do trabalho em um aplicativo que utiliza muitos recursos de computação.  Isso oferece a oportunidade de focalizar o "afunilamento" onde seu aplicativo está gastando mais tempo.


## Pré-requisitos

É possível executar o criador de perfis localmente apenas se você tiver o Visual Studio Premium ou o Visual Studio Ultimate. 

## Neste artigo:

-   [Etapa 1: Configurar o Visual Studio para criação de perfis][]

-   [Etapa 2: Anexar a um processo][]

-   [Etapa 3: Exibir relatórios de criação de perfil][]

-   [Etapa 4: Fazer alterações e comparar o desempenho][]

-   [Solução de problemas][]

-   [Próximas etapas][]

## <a name="step1"> </a> Etapa 1: Configurar o Visual Studio para criação de perfis

Em primeiro lugar, há algumas opções de configuração do Visual Studio que podem ser úteis ao criar perfis. Para compreender os relatórios de criação de perfis, você precisará de símbolos (arquivos .pdb) para seu aplicativo e também de símbolos para as bibliotecas do sistema. Você deve certificar-se de fazer referência aos servidores de símbolo disponíveis. Para fazer isso, no menu **Ferramentas** do Visual Studio, escolha **Opções**, escolha **Depuração** e, em seguida, **Símbolos**. Verifique se os Servidores de Símbolo da Microsoft estão listados em **Locais do arquivo de símbolos (.pdb)**.  Você também pode fazer referência a http://referencesource.microsoft.com/symbols, que pode ter arquivos de símbolos adicionais.

![][4]

Se desejar, você pode simplificar os relatórios que o criador de perfis gera definindo Apenas Meu Código. Com a opção Apenas Meu Código habilitada, as pilhas de chamadas de função são simplificadas de maneira que as chamadas inteiramente internas às bibliotecas e ao .Net Framework sejam ocultados dos relatórios. No menu **Ferramentas**, escolha **Opções**. Em seguida, expanda o nó **Ferramentas de Desempenho** e escolha **Geral**. Marque a caixa de seleção **Habilitar Apenas Meu Código para relatórios do criador de perfis**.

![][17]

Você pode usar essas instruções com um projeto existente ou com um novo projeto.  Se você criar um novo projeto para testar as técnicas descritas a seguir, escolha um projeto C# do **Serviço de Nuvem do Azure** e selecione uma **Função web** e uma **Função de Trabalho**.

![][5]

Para fins de exemplo, adicione algum código ao seu projeto que leve
muito tempo e apresente algum problema de desempenho óbvio. Por exemplo, adicione o seguinte código a um projeto de função de trabalho:

	public class Concatenator
	{
	    public static string Concatenate(int number)
	    {
	        int count;
	        string s = "";
	        for (count = 0; count < number; count++)
	        {
	            s += "\n" + count.ToString();
	        }
	        return s;
	    }
	}

Chame esse código do método RunAsync na classe derivada de RoleEntryPoint da função de trabalho. (Ignore o alerta relacionado ao método estar em execução de modo sincronizado.)

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Compile e execute seu serviço de nuvem localmente sem depuração (Ctrl+F5), com a configuração da solução definida como **Versão**. Isso garante que todos os arquivos e pastas sejam criados para executar o aplicativo localmente e garante que todos os emuladores estejam iniciados. Inicie a UI do Emulador de Computação na barra de tarefas para confirmar que sua função de trabalho esteja em execução.

## <a name="step2"> </a> Etapa 2: Anexar a um processo

Em vez de criar o perfil do aplicativo iniciando-o no IDE do Visual Studio 2010, você deve anexar o criador de perfis a um processo em execução. 

Para anexar o criador de perfis a um processo, no menu **Analisar**, escolha **Criador de Perfis** e **Anexar/Desanexar**.

![][6]

Para uma função de trabalho, localize o processo WaWorkerHost.exe.

![][7]

Se a pasta de seu projeto estiver em uma unidade de rede, o criador de perfis solicitará que você forneça outro local para salvar os relatórios de criação de perfis.

 Também é possível conectar-se a uma função web, conectando-se ao WaIISHost.exe.
 Se houver vários processos de função de trabalho em seu aplicativo, você precisará usar o processID para diferenciá-los. Você pode consultar o processID programaticamente acessando o objeto Process. Por exemplo, se você adicionar este código ao método Executar da classe RoleEntryPoint-derived em uma função, você pode examinar no
logon na IU do emulador de computação para saber a qual processo se conectar.

	var process = System.Diagnostics.Process.GetCurrentProcess();
	var message = String.Format("Process ID: {0}", process.Id);
	Trace.WriteLine(message, "Information");

Para exibir o log, inicie a interface do usuário do Emulador de Computação.

![][8]

Abra a janela do console do log da função de trabalho na interface do usuário do Emulador de Computação clicando na barra de título da janela do console. Você pode ver a ID do processo no log.

![][9]

Depois de conectar-se, execute as etapas na interface do usuário do seu aplicativo (se necessário) para reproduzir o cenário.

Quando desejar parar a criação de perfis, escolha o link **Parar Criação de Perfis**.

![][10]

## <a name="step3"> </a> Etapa 3: Exibir relatórios de desempenho

O relatório de desempenho de seu aplicativo é exibido.

Nesse ponto, o criador de perfis interromperá a execução, salvará os dados em um arquivo .vsp e exibirá um relatório
que mostra uma análise dos dados.

![][11]


Se você vir String.wstrcpy no Afunilamento, clique em Apenas Meu Código para alterar a exibição para mostrar somente o código do usuário.  Se você vir String.Concat, tente pressionar o botão Mostrar Todo o Código.

Você verá o método Concatenated e o String.Concat tomando uma grande parte
do tempo de execução.

![][12]

Se você adicionou o código de concatenação de cadeia de caracteres deste artigo, verá um aviso na Lista de Tarefas por isso. Você também poderá ver um aviso de que há uma quantidade excessiva de coleta de lixo, devido ao número de cadeias de caracteres que são criadas e descartadas.

![][14]

## <a name="step4"> </a> Etapa 4: Fazer alterações e comparar o desempenho

Você também pode comparar o desempenho antes e depois de uma alteração no código.  Interrompa o processo em execução e edite o código para substituir a operação de concatenação de cadeia de caracteres pelo uso de StringBuilder:

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

![][15]

Os relatórios destacam as diferenças entre as duas execuções.

![][16]

Parabéns! Você começou a usar o criador de perfis.

## <a name="troubleshooting"> </a> Solução de problemas

- Verifique se você está criando o perfil de uma compilação de versão e inicie sem depurar.

- Se a opção Anexar/Desanexar não estiver habilitada no menu Criador de Perfis, execute o Assistente de Desempenho.

- Use a interface do usuário do Emulador de Computação para exibir o status do seu aplicativo. 

- Se tiver problemas para iniciar aplicativos no emulador ou para conectar o criador de perfis, desligue e reinicie o emulador de computação. Se isso não resolver o problema, tente reinicializar. Esse problema pode ocorrer se você usar o Emulador de Computação para suspender e remover implantações em execução.

- Se você tiver usado qualquer um dos comandos de criação de perfil na
linha de comando, especialmente as configurações globais, verifique se VSPerfClrEnv/globaloff foi chamado e se VsPerfMon.exe foi desligado.

- Se, durante a amostragem, você vir a mensagem: "PRF0025: Nenhum dado foi coletado", verifique se o processo ao qual você se conectou tem atividade de CPU. Os aplicativos que não estão fazendo nenhum trabalho de computação podem não produzir nenhum dado de amostragem.  Também é possível que o processo tenha sido encerrado antes de qualquer amostragem ter sido feita. Verifique se o método Run para uma função para a qual você esteja criando um perfil não é encerrado.

## <a name="nextSteps"> </a> Próximas etapas

A instrumentação de binários do Azure no emulador não tem suporte no criador de perfis do Visual Studio, mas para testar a alocação de memória, você poderá escolher essa opção ao criar um perfil. Você também pode escolher a criação de perfis simultânea, que ajuda a determinar se os threads estão perdendo tempo competindo por bloqueios, ou a criação de perfis de interação de camadas, que ajuda a rastrear problemas de desempenho ao interagir entre camadas de um aplicativo, mais frequentemente entre a camada de dados e uma função de trabalho.  Você pode exibir as consultas do banco de dados que seu aplicativo gera e usar os dados da criação de perfis para melhorar o uso do banco de dados. Para obter informações sobre a criação de perfis de interação de camadas, consulte [Passo a passo: Usando o criador de perfis de interação de camadas no Visual Studio Team System 2010][3].


[Etapa 1: Configurar o Visual Studio para criação de perfis]: #step1
[Etapa 2: Anexar a um processo]: #step2
[Etapa 3: Exibir relatórios de criação de perfil]: #step3
[Etapa 4: Fazer alterações e comparar o desempenho]: #step4
[Solução de problemas]: #troubleshooting
[Próximas etapas]: #nextSteps

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png

<!--HONumber=45--> 

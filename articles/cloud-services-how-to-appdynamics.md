<properties urlDisplayName="Monitor with AppDynamics" pageTitle="Como usar o AppDynamics com o Azure" metaKeywords="" description="Saiba como usar o AppDynamics para Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Como usar o AppDynamics para Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Como usar o AppDynamics para Azure

Este tópico descreve como começar a usar o AppDynamics para Azure.

## Sumário

-   [O que é AppDynamics?][O que é AppDynamics?]
-   [Pré-requisitos][Pré-requisitos]
-   [Registrar-se para uma conta do AppDynamics][Registrar-se para uma conta do AppDynamics]
-   [Baixar o agente .NET do AppDynamics][Baixar o agente .NET do AppDynamics]
-   [Adicionar o Agente do .NET às funções do Azure e modificar a inicialização][Adicionar o Agente do .NET às funções do Azure e modificar a inicialização]
-   [Publicar o aplicativo instrumentado pelo AppDynamics para Azure][Publicar o aplicativo instrumentado pelo AppDynamics para Azure]
-   [Monitorar seu aplicativo][Monitorar seu aplicativo]

## <span id="what"></span></a>O que é AppDynamics?

O AppDynamics é uma solução do monitoramento de desempenho do aplicativo que ajuda você a:

-   Identificar problemas, como solicitações lentas e paradas do usuário e erros em um ambiente de produção

-   Solucionar problemas e isolar a causa raiz desses problemas

Há dois componentes no AppDynamics:

-   Agente do servidor de aplicativos: O agente do servidor de aplicativos .NET coleta dados dos servidores. Você executa um agente separado em cada instância de função que deseja monitorar. Você pode baixar o Agente no portal do AppDynamics.

-   Controlador do AppDynamics: O agente envia as informações a um serviço hospedado do controlador do AppDynamics no Windows Azure. Usando um console baseado em navegador da Web, você faz logon no Controlador para monitorar, analisar e solucionar problemas de seu aplicativo.

    ![Diagrama do AppDynamics][Diagrama do AppDynamics]

## <span id="prereq"></span></a>Pré-requisitos

-   Visual Studio 2010 ou mais recente
-   Uma solução do Visual Studio a ser monitorada
-   SDK do Azure
-   Conta do Azure

## <span id="register"></span></a>Registrar-se para uma conta do AppDynamics

Para registrar-se para uma conta AppDynamics para Azure:

1.  Clique em **Teste gratuitamente** ou **Inscrever-se** para AppDynamics Windows Azure Marketplace em <https://datamarket.azure.com/browse/Applications>.

    Se escolher **Inscrever-se**, você receberá uma versão gratuita do AppDynamics Pro para Azure com funcionalidade completa, que é reduzida após 30 dias para uma versão gratuita do AppDynamics Lite para Azure com funcionalidade limitada. Você não precisa fornecer um cartão de crédito para essa opção. Você pode atualizar para o AppDynamics Pro para Azure a qualquer momento.

    Se escolher **Teste gratuitamente**, você receberá uma versão gratuita do AppDynamics Pro para Azure com funcionalidade completa. Você precisará fornecer um cartão de crédito para essa opção. Após 30 dias, haverá uma cobrança na sua conta de crédito pelo uso contínuo do AppDynamics Pro para Azure, a menos que você cancele sua assinatura.

    Você precisa de uma licença de agente para cada instância de função que deseja monitorar. Por exemplo, um site executando duas instâncias de função Web e duas instâncias de função de trabalho requer quatro licenças de agente.

2.  Na página de registro, forneça suas informações de usuário, uma senha, o endereço de email, o nome da empresa e o nome do aplicativo que você está monitorando ao publicá-lo com o Azure.

3.  Clique em **Registrar agora**.

    Você receberá suas credenciais do AppDynamics e a URL do Controlador do AppDynamics (host e porta) atribuídas à sua conta em um email enviado para o endereço fornecido na página de inscrição. Salve essas informações.

    Se você já tiver credenciais do AppDynamics de outro produto, você se conectar usando-as.

    Você também receberá uma página inicial da conta do AppDynamics.

    Você será levado para sua página inicial da conta do AppDynamics.

    Sua página inicial da conta do AppDynamics inclui:

    -   A URL do controlador: da qual você faz logon em sua conta no serviço hospedado do controlador do AppDynamics

    -   Credenciais do AppDynamics: nome da conta e chave de acesso

    -   Link para o site de download do AppDynamics: do qual você pode baixar o agente .NET do AppDynamics

    -   Número de dias restantes em sua versão Pro

    -   Links para os vídeos e para a documentação inicial do AppDynamics

    Você pode acessar sua página inicial da conta do AppDynamics a qualquer momento inserindo a URL no navegador e entrando com suas credenciais do AppDynamics.

## <span id="download"></span></a>Baixar o agente .NET do AppDynamics

1.  Navegue até o site de download do AppDynamics. A URL está em seu email de boas-vindas e em sua página inicial da conta do AppDynamics.

2.  Entre com seu nome da conta e chave de acesso do AppDynamics.

3.  Baixe o arquivo chamado AppDynamicsdotNetAgentSetup64.msi. Não execute o arquivo.

## <span id="addagent"></span></a>Adicionar o Agente do .NET às funções do Azure e modificar a inicialização

Essa etapa implementa as funções em sua solução do Visual Studio para o monitoramento pelo AppDynamics. Não existe um procedimento de instalação no estilo tradicional de assistente do Windows necessário para usar o AppDynamics para o Azure.

1.  Crie um novo projeto do Azure no Visual Studio ou abra um projeto do Azure existente.

2.  Se você criou um novo projeto, adicione os projetos da função Web e/de trabalho à solução.

3.  Para cada projeto de função Web ou de trabalho que você desejar monitorar, adicione o arquivo .msi do agente .NET baixado.

    Observe que embora cada *projeto de função* tenha um único .msi do Agente do .NET, cada *instância de função* no projeto precisa de uma licença separada do Agente do .NET.

4.  Para projeto de função Web e de trabalho você deseja monitorar, adicione um arquivo de texto chamado startup.cmd e cole as seguintes linhas nele:

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Para cada função Web e de trabalho que você deseja para monitorar, defina a propriedade **Copiar para Diretório de Saída** para o arquivo .msi do agente do AppDynamics e, para o arquivo startup.cmd, como **Copiar sempre**.

    ![Copiar sempre][Copiar sempre]

6.  No arquivo ServiceDefinition.csdef do projeto do Azure, adicione um elemento de Tarefa de Inicialização que invoque startup.cmd com parâmetros para cada elemento WorkerRole e WebRole.

    Adicione as linhas a seguir:

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    onde:

    -   *o host do controlador* e a *porta do controlador* são o host e a porta do Controlador atribuídos à sua conta, e o *nome da conta* e a *chave de acesso* são as credenciais atribuídas a você pelo AppDynamics. Essas informações são fornecidas no email enviado após o registro com o AppDynamics e também em sua página inicial do AppDynamic. Consulte [Registrar-se para uma conta do AppDynamics][Registrar-se para uma conta do AppDynamics].

    -   *o nome do aplicativo* é o nome escolhido para o aplicativo. Esse nome identificará o aplicativo na interface do Controlador do AppDynamics.

    O arquivo ServiceDefinition.csdef terá essa aparência:

    ![Definição do serviço][Definição do serviço]

## <a name="publish"></a>Publicar o aplicativo instrumentado pelo AppDynamics para Azure

Para cada projeto de função instrumentada do AppDynamics:

1.  No Visual Studio, selecione o projeto da função.

2.  Selecione Publicar no Azure.

## <a name="monitor"></a>Monitorar seu aplicativo

1.  Faça logon no controlador do AppDynamics na URL especificada em seu email de boas-vindas e na página inicial da conta do AppDynamics.

2.  Envie algumas solicitações ao seu aplicativo para que haja tráfego para monitorar e aguarde alguns minutos.

3.  No controlador do AppDynamics, selecione seu aplicativo.

4.  Monitore o seu aplicativo.

## <a name="learn"></a>Saiba mais

Consulte sua página inicial da conta AppDynamics para obter os links para documentação e vídeos.

[As atualizações mais recentes deste documento estão na versão do wiki em][As atualizações mais recentes deste documento estão na versão do wiki em]http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure.

  [O que é AppDynamics?]: #what
  [Pré-requisitos]: #prereq
  [Registrar-se para uma conta do AppDynamics]: #register
  [Baixar o agente .NET do AppDynamics]: #download
  [Adicionar o Agente do .NET às funções do Azure e modificar a inicialização]: #addagent
  [Publicar o aplicativo instrumentado pelo AppDynamics para Azure]: #publish
  [Monitorar seu aplicativo]: #monitor
  [Diagrama do AppDynamics]: ./media/cloud-services-how-to-appdynamics/addiagram.png
  [Copiar sempre]: ./media/cloud-services-how-to-appdynamics/adcopyalways.png
  [Definição do serviço]: ./media/cloud-services-how-to-appdynamics/adscreen.png
  [As atualizações mais recentes deste documento estão na versão do wiki em]: http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure

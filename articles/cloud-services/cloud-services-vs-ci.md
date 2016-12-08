---
title: "Entrega contínua para serviços de nuvem com o Visual Studio Online | Microsoft Docs"
description: "Saiba como configurar a entrega contínua para aplicativos de nuvem do Azure sem salvar a chave de armazenamento de diagnóstico para os arquivos de configuração de serviço"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Salvar com segurança a chave de armazenamento de diagnóstico dos Serviços de Nuvem e configure a integração contínua e a implantação no Azure usando o Visual Studio Online
 Hoje, é uma prática comum para projetos de software livre. Salvar segredos do aplicativo nos arquivos de configuração não é mais uma prática segura, já que vulnerabilidades de segurança são expostas a partir de segredos vazados de controles de origem pública. O armazenamento do segredo como texto sem formatação em um pipeline de Integração Contínua não é seguro porque os servidores de compilação poderiam ser recursos compartilhados no ambiente de Nuvem. Este artigo explica como o Visual Studio e o Visual Studio Online minimizam as preocupações de segurança durante o desenvolvimento e o processo de Integração Contínua.

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>Remover o segredo de chave de armazenamento de diagnóstico em arquivo de configuração de projeto
A extensão de diagnóstico dos Serviços de Nuvem requer o armazenamento do Azure para salvar os resultados do diagnóstico. Anteriormente, a cadeia de conexão de armazenamento era especificada nos arquivos de configuração (.cscfg) dos Serviços de Nuvem e você podia fazer check-in deles para o controle de origem. Na última versão do SDK do Azure, alteramos o comportamento para armazenar somente uma cadeia de conexão parcial somente com a chave substituída por um token. As etapas a seguir descrevem o funcionamento das novas ferramentas dos Serviços de Nuvem:

### <a name="1-open-the-role-designer"></a>1. Abra o Designer de função
* Clique duas vezes ou clique com o botão direito do mouse em uma função dos Serviços de Nuvem para abrir o Designer de função

![Abrir o designer de função][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2. Na seção de diagnóstico, uma nova caixa de seleção "Não remover o segredo da chave de armazenamento projeto" é adicionada
* Se você estiver usando o emulador de armazenamento local, essa caixa de seleção estará desabilitada porque não há nenhum segredo a ser gerenciado para a cadeia de conexão local, que é UseDevelopmentStorage=true.

![A cadeia de conexão do emulador de armazenamento local não é um segredo][1]

* Se você estiver criando um novo projeto, por padrão, essa caixa de seleção estará desmarcada. Isso resulta na seção de armazenamento de chave de armazenamento da cadeia de conexão selecionada que está sendo substituída por um token. O valor do token será encontrado na pasta AppData Roaming do usuário atual, por exemplo: C:\Usuários\contosouser\AppData\Roaming\Microsoft\CloudService

> Observe que a pasta user\AppData tem Acesso Controlado pela entrada do usuário e é considerada como um local seguro para armazenar os segredos de desenvolvimento.
> 
> 

![A chave de armazenamento é salva na pasta de perfil de usuário][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3. Selecionar uma conta de armazenamento de diagnóstico
* Selecione uma conta de armazenamento do diálogo iniciado quando você clica em “...” . Observe como a cadeia de conexão de armazenamento gerada não terá a chave da conta de armazenamento.
* Por exemplo: DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4-debugging-the-project"></a>4.    Depuração do projeto
* Pressione F5 para iniciar a depuração no Visual Studio. Tudo deve funcionar da mesma maneira como antes.
  ![Iniciar a depuração no local][3]

### <a name="5-publish-project-from-visual-studio"></a>5. Publicar o projeto do Visual Studio
* Inicie o diálogo de publicação e prossiga com as instruções de entrada para publicar o aplicativo no Azure.

### <a name="6-additional-information"></a>6. Informações adicionais
> Observação: o painel Configurações no designer de função permanecerá como está agora. Se você quiser usar a funcionalidade de gerenciamento de informações de segredos para diagnóstico, vá para a guia Configurações.
> 
> 

![Adicionar configurações][4]

> Observação: se habilitada, a chave do Application Insights será armazenada como texto sem formatação. A chave só será usada para carregar conteúdo de forma que nenhum dado confidencial corra o risco de ser comprometido.
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Criar e publicar um projeto dos Serviços de Nuvem usando modelos de tarefa do Visual Studio Online
* As etapas a seguir ilustram como configurar a Integração Contínua para o projeto dos Serviços de Nuvem usando tarefas do Visual Studio Online:
  ### <a name="1-obtain-a-vso-account"></a>1.    Obter uma conta do VSO
* [Criar conta do Visual Studio Online][Criar conta do Visual Studio Online] se você não tiver uma
* [Criar projeto de equipe][Criar projeto de equipe] na sua conta do Visual Studio Online

### <a name="2-setup-source-control-in-visual-studio"></a>2.    Configurar o controle de origem no Visual Studio
* Conectar-se a um projeto de equipe

![Conectar-se ao projeto de equipe][5]

![Selecione o projeto de equipe ao qual você se conectará][6]

* Adicionar seu projeto ao controle do código-fonte

![Adicionar projeto ao controle do código-fonte][7]

![Mapear projeto para uma pasta de controle de origem][8]

* Fazer check-in do seu projeto no Team Explorer

![Fazer check-in em um projeto para o controle do código-fonte][9]

### <a name="3-configure-build-process"></a>3.    Configurar o processo de compilação
* Navegar até seu projeto de equipe e adicionar um novo processo de compilação Modelos

![Adicionar uma nova compilação][10]

* Selecionar tarefa de compilação

![Adicionar uma tarefa de compilação][11]

![Selecionar o modelo de tarefa Compilação do Visual Studio][12]

* Edite a entrada da tarefa de compilação. Personalizar os parâmetros de compilação de acordo com a sua necessidade

![Configurar a tarefa de compilação][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* Configurar variáveis de compilação

![Configurar variáveis de compilação][14]

* Adicionar uma tarefa para carregar o local de compilação

![Escolher publicar tarefa de local de compilação][15]

![Configurar publicar tarefa de compilação de local de publicação][16]

* Executar a compilação

![Enfileirar Nova Compilação][17]

![Exibir o resumo da compilação][18]

* Se a compilação for bem-sucedida, você verá um resultado semelhante a este

![Resultado da compilação][19]

### <a name="4-configure-release-process"></a>4.    Configurar o processo de lançamento
* Criar uma nova versão

![Criar nova versão][20]

* Selecionar a tarefa Implantação dos Serviços de Nuvem do Azure

![Selecionar a tarefa de implantação dos Serviços de Nuvem do Azure][21]

* Se você não fizer o check-in da chave da conta de armazenamento no controle de origem, precisaremos especificar a chave secreta para a configuração de extensões de diagnóstico. Expanda a seção **Opções Avançadas para a Criação de Novo Serviço** e edite a entrada do parâmetro **Chaves de Conta de Armazenamento de Diagnóstico**. Essa entrada obtém várias linhas do par de valor de chave no formato **[RoleName]:$(StorageAccountKey)**

> Observação: se a sua conta de armazenamento de diagnóstico estiver sob a mesma assinatura em que você publicará o aplicativo dos Serviços de Nuvem, não será necessário inserir a chave na entrada da tarefa de implantação; a implantação obterá programaticamente as informações de armazenamento de sua assinatura
> 
> 

![Configurar a tarefa de implantação dos Serviços de Nuvem][22]

* Use as variáveis de compilação secretas para salvar as Chaves de armazenamento. Para mascarar uma variável como segredo, clique no ícone de bloqueio no lado direito da entrada Variáveis

![Salvar chaves de armazenamento em variáveis de compilação secretas][23]

* Criar uma versão e implantar seu projeto no Azure

![Criar nova versão][24]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como configurar extensões de diagnóstico para os Serviços de Nuvem do Azure, veja [Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell][Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell]

[Criar conta do Visual Studio Online]:https://www.visualstudio.com/team-services/
[Criar projeto de equipe]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->



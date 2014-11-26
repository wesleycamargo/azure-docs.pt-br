<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Trabalhar com um serviço móvel de back-end .NET

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um projeto Visual Studio de back-end .NET que define seu serviço móvel em Serviços Móveis do Azure. Este tópico é dividido nas seguintes seções:

-   [Introdução][Introdução]
-   [Operações de tabela][Operações de tabela]

    -   [Como: Registro para operações de tabela][Como: Registro para operações de tabela]
    -   [Como: Substituir a resposta padrão][Como: Substituir a resposta padrão]
    -   [Como: Substituir o êxito da execução][Como: Substituir o êxito da execução]
    -   [Como: Substituir o gerenciamento de erros padrão][Como: Substituir o gerenciamento de erros padrão]
    -   [Como: Adicionar parâmetros personalizados][Como: Adicionar parâmetros personalizados]
    -   [Como: Trabalhar com usuários de tabela][Como: Trabalhar com usuários de tabela]
-   [API personalizada][API personalizada]

    -   [Como: Definir uma API personalizada][Como: Definir uma API personalizada]
    -   [Como: Implementar métodos HTTP][Como: Implementar métodos HTTP]
    -   [Como: Enviar e receber dados como XML][Como: Enviar e receber dados como XML]
    -   [Como: Trabalhar com usuários e cabeçalhos em uma API personalizada][Como: Trabalhar com usuários e cabeçalhos em uma API personalizada]
    -   [Como: Definir várias rotas em uma API personalizada][Como: Definir várias rotas em uma API personalizada]
-   [Agendador de Trabalhos][Agendador de Trabalhos]

    -   [Como: Definir scripts de trabalho agendado][Agendador de Trabalhos]
-   [Controle do código-fonte, código compartilhado e funções do auxiliar][Controle do código-fonte, código compartilhado e funções do auxiliar]

    -   [Como: Carregar módulos Node.js][Como: Carregar módulos Node.js]
    -   [Como: Usar funções de auxiliar][Como: Usar funções de auxiliar]
    -   [Como: Compartilhar código usando controle de código-fonte][Como: Compartilhar código usando controle de código-fonte]
    -   [Como: Trabalhar com configurações do aplicativo][Como: Trabalhar com configurações do aplicativo]
-   [Usando a ferramenta de linha de comando][Usando a ferramenta de linha de comando]
-   [Trabalhando com tabelas][Trabalhando com tabelas]

    -   [Como: Acessar tabelas dos scripts][Como: Acessar tabelas dos scripts]
    -   [Como: Executar inserções em massa][Como: Executar inserções em massa]
    -   [Como: Mapear tipos de JSON para tipos de banco de dados][Como: Mapear tipos de JSON para tipos de banco de dados]
    -   [Usando o Transact-SQL para acessar tabelas][Usando o Transact-SQL para acessar tabelas]
-   [Depuração e solução de problemas][Depuração e solução de problemas]

    -   [Como: Gravar saída para logs de serviço móvel][Como: Gravar saída para logs de serviço móvel]

## <a name="intro"></a> Introdução

Em um serviço móvel de back-end .NET, você pode definir lógica de negócios personalizada como código JavaScript que é armazenado e executado no servidor. Esse código de script de servidor é atribuído a uma das seguintes funções do servidor:

-   [Operações de inserção, leitura, atualização ou exclusão em uma determinada tabela][Operações de tabela].
-   [Trabalhos agendados][Agendador de Trabalhos].
-   [Métodos HTTP definidos em uma API personalizada][API personalizada].

A assinatura da função principal no script de servidor depende do contexto em que o script é usado. Você também pode definir códigos de script comuns como módulos nodes.js compartilhados entre scripts. Para obter mais informações, consulte [Controle do código-fonte e código compartilhado][Controle do código-fonte, código compartilhado e funções do auxiliar]

Para obter descrições de objetos de script de servidor individual e funções, consulte [Referência de script de servidor dos Serviços Móveis].



  [Introdução]: #intro
  [Operações de tabela]: #table-scripts
  [Como: Registro para operações de tabela]: #register-table-scripts
  [Como: Substituir a resposta padrão]: #override-response
  [Como: Substituir o êxito da execução]: #override-success
  [Como: Substituir o gerenciamento de erros padrão]: #override-error
  [Como: Adicionar parâmetros personalizados]: #access-headers
  [Como: Trabalhar com usuários de tabela]: #work-with-users
  [API personalizada]: #custom-api
  [Como: Definir uma API personalizada]: #define-custom-api
  [Como: Implementar métodos HTTP]: #handle-methods
  [Como: Enviar e receber dados como XML]: #api-return-xml
  [Como: Trabalhar com usuários e cabeçalhos em uma API personalizada]: #get-api-user
  [Como: Definir várias rotas em uma API personalizada]: #api-routes
  [Agendador de Trabalhos]: #scheduler-scripts
  [Controle do código-fonte, código compartilhado e funções do auxiliar]: #shared-code
  [Como: Carregar módulos Node.js]: #modules-helper-functions
  [Como: Usar funções de auxiliar]: #helper-functions
  [Como: Compartilhar código usando controle de código-fonte]: #shared-code-source-control
  [Como: Trabalhar com configurações do aplicativo]: #app-settings
  [Usando a ferramenta de linha de comando]: #command-prompt
  [Trabalhando com tabelas]: #working-with-tables
  [Como: Acessar tabelas dos scripts]: #access-tables
  [Como: Executar inserções em massa]: #bulk-inserts
  [Como: Mapear tipos de JSON para tipos de banco de dados]: #JSON-types
  [Usando o Transact-SQL para acessar tabelas]: #TSQL
  [Depuração e solução de problemas]: #debugging
  [Como: Gravar saída para logs de serviço móvel]: #write-to-logs

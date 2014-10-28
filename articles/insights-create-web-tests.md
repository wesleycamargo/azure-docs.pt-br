<properties title="How to create web test" pageTitle="How to create web test" description="Learn how to create web tests in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# Testes da Web

Seu site do Azure ainda está em execução? Está respondendo corretamente e rápido o suficiente? Teste seu site em intervalos regulares configurando um teste da Web. Se o site se tornar inativo ou responder lenta ou incorretamente, você receberá um alerta por email. E você obterá gráficos que mostram a disponibilidade e a capacidade de resposta ao longo do tempo.

![Hub de procura][Hub de procura]

Você pode configurar o monitoramento da disponibilidade de qualquer site do Azure que esteja usando um plano Basic ou Standard. Você pode criar até três testes da Web e executar todos os testes em até três locais geográficos. Você não precisa alterar o site de nenhuma maneira.

Você também pode pausar testes da web durante as implantações ou paralisações conhecidas para que sua disponibilidade geral não seja afetada. A disponibilidade geral é calculada sobre todos os testes da web incluindo os diferentes locais selecionados.

## Como configurar um teste da web

1.  Para configurar um teste da web, primeiro certifique-se de que seu site é **Basic** ou **Standard**.
2.  Em seguida, escolha a parte **Teste da Web** na lâmina **Site**:<br />
    ![Configurar testes da Web][Configurar testes da Web]
3.  Na lâmina **Criar teste web**, dê um nome ao teste da web e especifique a URL na qual executar o teste.<br />
    ![Criar os testes da Web][Criar os testes da Web]
4.  Em seguida, escolha até três dos oito locais
5.  Especifique os critérios de sucesso, incluindo verificações de código de status HTTP ou cadeias de caracteres contidas no próprio site.
6.  Em seguida, escolha as configurações de alerta, inclusive a sensibilidade e para quem enviar o email.<br />
    ![Alertas][Alertas]
    -   A alta sensibilidade criará um alerta sempre que for detectada uma falha de teste em apenas um local.
    -   A sensibilidade média requer que pelo menos metade dos locais tenham uma falha em 10 minutos.
    -   A baixa sensibilidade requer que o teste em todos os locais tenha uma falha em 15 minutos.

Ao concluir, clique no botão **Criar**. Depois que seu teste da web for criado, ele será executado a cada cinco minutos no local especificado, portanto, pode demorar um pouco para que os dados sejam exibidos.

### Quais são os locais?

Enviamos uma solicitação ao site a partir desses locais, da mesma maneira como os usuários acessarão o site de diferentes partes do mundo. Se seu site ficar indisponível nos EUA, mas ainda estiver disponível na Europa, você saberá que o problema é um problema de rede, e não no servidor.

### Usando critérios de sucesso

Normalmente você desejará que o código de status HTTP seja igual a 200, o que indica que o servidor reconheceu o URI e retornou uma página.

Você não pode usar caracteres curinga na cadeia de caracteres de correspondência de conteúdo, mas pode testar qualquer texto sem formatação.

## Oh oh - meu site está desativado!

Se o teste da web não passar nos critérios de sucesso, ele será marcado como um teste com falha e reduzirá a disponibilidade geral do site. Testes com falha (e também com êxito) são mostrados em um gráfico de dispersão na lâmina de teste da web específica.

![Teste com falha][Teste com falha]

Testes com falha podem ser analisados para determinar porque falharam. Faça uma busca detalhada de um teste da web com falha e baixe e abra o Arquivo de Resultado do Teste da Web do Visual Studio para analisar e compreender por que o teste falhou.

![Abrir no VS][Abrir no VS]

  [Hub de procura]: ./media/insights-create-web-tests/Inisghts_WebTestBlade.png
  [Configurar testes da Web]: ./media/insights-create-web-tests/Insights_ConfigurePart.png
  [Criar os testes da Web]: ./media/insights-create-web-tests/Insights_CreateTest.png
  [Alertas]: ./media/insights-create-web-tests/Inisghts_AlertCreation.png
  [Teste com falha]: ./media/insights-create-web-tests/Insights_FailedWebTest.png
  [Abrir no VS]: ./media/insights-create-web-tests/Insights_OpenInVS.png

<properties 
	pageTitle="Como criar um teste na Web" 
	description="Aprenda a criar testes da web no Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-17" 
	ms.author="awills"/>

# Testes na Web para sites do Microsoft Azure
Seu site do Azure ainda está em execução? Está respondendo corretamente e rápido o suficiente? Teste seu site em intervalos regulares configurando um teste da Web. Se o site se tornar inativo ou responder lenta ou incorretamente, você receberá um alerta por email. E você obterá gráficos que mostram a disponibilidade e a capacidade de resposta ao longo do tempo.  

*Deseja testar qualquer outro site? Use a disponibilidade dos [Testes na Web do Application Insights][] para aplicativos Web não Azure.*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

Você pode configurar o monitoramento da disponibilidade de qualquer site do Azure que esteja usando um plano Basic ou Standard.  Você pode criar até três testes da Web e executar todos os testes em até três locais geográficos. Você não precisa alterar o site de nenhuma maneira.

Você também pode pausar testes da web durante as implantações ou paralisações conhecidas para que sua disponibilidade geral não seja afetada.  A disponibilidade geral é calculada sobre todos os testes da web incluindo os diferentes locais selecionados.

## Como configurar um teste da web
1. Para configurar um teste na Web, primeiro certifique-se de que seu site é **Basic** ou **Standard**.
2. Em seguida, escolha a parte **Teste na Web** na folha **Site**:  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. Na folha **Criar teste na Web**, dê um nome ao teste na web e especifique a URL na qual executar o teste.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. Em seguida, escolha até três dos oito locais
5. Especifique os critérios de sucesso, incluindo verificações de código de status HTTP ou cadeias de caracteres contidas no próprio site.
6. Em seguida, escolha as configurações de alerta, inclusive a sensibilidade e para quem enviar o email.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - A alta sensibilidade criará um alerta sempre que for detectada uma falha de teste em apenas um local.
    - A sensibilidade média requer que pelo menos metade dos locais tenham uma falha em 10 minutos.
    - A baixa sensibilidade requer que o teste em todos os locais tenha uma falha em 15 minutos.

Ao concluir, clique no botão **Criar**. Depois que seu teste na Web for criado, ele será executado a cada cinco minutos no local especificado, portanto, pode demorar um pouco para que os dados sejam exibidos.

### Quais são os locais?
Enviamos uma solicitação ao site desses locais, da mesma maneira como os usuários acessarão o site de diferentes partes do mundo. Se seu site ficar indisponível nos EUA, mas ainda estiver disponível na Europa, você saberá que o problema é um problema de rede, e não no servidor.

### Usando critérios de sucesso
Normalmente você desejará que o código de status HTTP seja igual a 200, o que indica que o servidor reconheceu o URI e retornou uma página.

Você não pode usar caracteres curinga na cadeia de caracteres de correspondência de conteúdo, mas pode testar qualquer texto sem formatação.

## Oh oh - meu site está desativado!
Se o teste na Web não passar nos critérios de sucesso, ele será marcado como um teste com falha e reduzirá a disponibilidade geral do site. Testes com falha (e também com êxito) são mostrados em um gráfico de dispersão na folha de teste na Web específica.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

Testes com falha podem ser analisados para determinar porque falharam.  Faça uma busca detalhada de um teste na Web com falha e baixe e abra o Arquivo de Resultado do Teste na Web do Visual Studio para analisar e compreender por que o teste falhou.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/

<!--HONumber=46--> 

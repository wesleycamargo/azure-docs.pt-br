<properties
   pageTitle="Usando o conector do Facebook no aplicativo lógico no Serviço de Aplicativo do Azure"
   description="Como usar o Conector do Facebook em um aplicativo lógico"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="andalmia"/>


# Conector do Facebook

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

- O gatilho do conector do Facebook recupera “Nova Postagem na Linha do Tempo do Usuário” ou “Nova Postagem na Página”. Quando um novo tweet é recuperado, ele dispara uma nova instância do fluxo e transmite os dados recebidos na solicitação para o fluxo de processamento.
- As ações de conector do Facebook permitem "Publicar Postagem", "Publicar Fotos" e assim por diante. Essas ações obtêm uma resposta e a disponibilizam para uso pelas ações no fluxo.

## Gatilhos e Ações

Gatilhos | Ações
--- | ---
<ul><li>Nova Postagem na Linha do Tempo do Usuário</li><li>Nova Postagem em Página</li></ul> | <ul><li>Publicar Postagem</li>Publicar foto<li></li></ul>



## Criar um conector do Facebook para o aplicativo lógico
Para usar o conector do Facebook, você precisa primeiro criar uma instância do aplicativo de API do Conector do Facebook. Isso pode ser feito da seguinte maneira:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Facebook", selecione-o e selecione **Criar**.
3. Insira o Nome, o Plano do Serviço de Aplicativo e outras propriedades: ![][1]
4.	Selecione **Criar**.


## Usando o Conector do Facebook em seu aplicativo lógico
Depois de criar seu aplicativo de API, você pode usar o Conector do Facebook como ação/gatilho para seu aplicativo lógico. Para fazer isso, você precisa:

1.	No aplicativo lógico, abra **Gatilhos e Ações** para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo: ![][3]
2.	O conector do Facebook está listado na galeria: ![][4]
3. Selecione o conector do Facebook para adicionar automaticamente no designer. Selecione **Autorizar**, insira as suas credenciais e selecione **Permitir**: ![][5] ![][6] ![][7] ![][8]
4.	Selecione um gatilho: ![][9]

Agora, você pode usar as postagens recuperadas do gatilho do Facebook em outras ações. No fluxo abaixo, sempre que ocorre uma nova postagem na linha de tempo do Facebook do usuário, a mesma postagem é tuitada na linha do tempo do Twitter do usuário: ![][10]

De maneira semelhante, você pode criar fluxos usando as ações do Conector do Facebook. O fluxo abaixo recupera novas mensagens postadas no grupo do Yammer e publica a mesma postagem na página do Facebook gerenciada pelo usuário: ![][11]

> [AZURE.TIP]Para obter a ID da Página do Facebook ou a ID do Grupo do Yammer, procure o código numérico na URL.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=August15_HO8-->
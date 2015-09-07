<properties
   pageTitle="Usando o Ouvinte e o Conector HTTP em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o ouvinte HTTP e o Conector de ação HTTP ou o aplicativo de API e usá-los em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
	ms.date="08/23/2015"
	ms.author="prkumar"/>


# Introdução ao ouvinte HTTP e à ação de HTTP e à adição deles ao seu Aplicativo Lógico
Conecte-se diretamente a recursos HTTP para ouvir solicitações HTTP e para configurar solicitações da Web HTTP. Existem alguns cenários em que você pode precisar trabalhar com conexões HTTP diretas, incluindo:

1.	Para desenvolver um aplicativo lógico que dá suporte a um front-end interativo de usuário móvel ou da Web.
2.	Para obter e processar dados de um serviço Web que não tem um conector pré-configurado.
3.	Para executar ações que já estão expostas como um serviço Web, mas não estão disponíveis como um aplicativo de API.

Nestes cenários, há duas opções:

1. **Ouvinte HTTP**: este conector atua como gatilho e escuta as solicitações HTTP em um ponto de extremidade configurado. Quando uma chamada é recebida no ponto de extremidade configurado, ele aciona uma nova instância do fluxo e transmite os dados recebidos na solicitação para o fluxo de processamento. Ele também pode ser configurado para responder automaticamente à solicitação de entrada quando o fluxo foi iniciado ou permitir que você construa uma resposta com base na execução de fluxo e envie uma resposta ao chamador.
2. **Ação de HTTP**: isso permite que você configure uma solicitação Web para qualquer ponto de extremidade disponível na Internet, obtém uma resposta, e a disponibiliza para consumo pelas ações adicionais no fluxo.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o conector HTTP a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Criando um ouvinte HTTP para seu aplicativo lógico
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Procure "HTTP", selecione o ouvinte HTTP e selecione **Criar**.
3.	Configure o ouvinte HTTP da seguinte maneira: ![][1]

4.	Ao definir as configurações de pacote, você verá a seguinte opção sobre se o ouvinte deve responder automaticamente ou exigir que você envie uma resposta explícita. Defina isso como **Falso** para enviar sua própria resposta: ![][2]

5.	Clique em **OK** para criar.
6.	Depois de criar a instância do aplicativo de API, abra as configurações para configurar a segurança. O ouvinte HTTP atualmente dá suporte à Autenticação básica. Você poderá configurar isso usando a opção Segurança ao abrir o ouvinte HTTP: ![][3]
  
	**Problema conhecido** * As configurações de segurança mostram “Nenhum” como o valor padrão, mas isso é indefinido. Você deve alterar a configuração básica e voltar para Nenhum antes de salvá-la para garantir que o ouvinte HTTP esteja configurado corretamente.*

7. Por fim, defina as configurações de segurança do aplicativo de API como Público (Anônimo) para permitir que clientes externos acessem o ponto de extremidade. Essa configuração está disponível em "Todas as configurações > Configurações do Aplicativo" do Aplicativo de API do ouvinte HTTP:![][10]

Depois disso, pode criar um aplicativo lógico para usar o ouvinte HTTP.

## Usando o ouvinte HTTP em seu aplicativo lógico
Depois de criar o aplicativo de API, você pode usar o ouvinte HTTP como gatilho para o aplicativo lógico. Para fazer isso, você precisa:

4.	Criar um novo Aplicativo lógico.
5.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. O Ouvinte HTTP está listado na galeria. Selecione-o.
6.	Agora, você pode definir o Método HTTP e a URL relativa em que você precisa do ouvinte para disparar o fluxo: ![][4] ![][5]

7.	Para obter o URI completo, clique duas vezes no ouvinte HTTP para exibir suas configurações e copie a URL para o "Host" de seu aplicativo de API: ![][6]
8.	Agora você pode usar os dados recebidos na solicitação HTTP em outras ações no fluxo da seguinte maneira: ![][7] ![][8]
9.	Por fim, para enviar uma resposta, adicione outro ouvinte HTTP e selecione a ação Enviar Resposta HTTP. Defina a ID da solicitação como a RequestID obtida do ouvinte HTTP e preencha o corpo da resposta e o status HTTP que você deseja retornar: ![][9]

## Usando a ação HTTP
A ação HTTP tem suporte nativo pelos aplicativos lógicos e não exige a criação de um aplicativo de API para ser usada. Você pode inserir uma ação HTTP a qualquer momento em seu aplicativo lógico e escolher o URI, os cabeçalhos e o corpo para a chamada. A ação HTTP dá suporte a várias opções para segurança do cliente. Para usá-las, analise o artigo [aqui](http://aka.ms/logicapphttpauth).

A saída da ação HTTP são os cabeçalhos e o corpo, que podem ser usados downstream no fluxo, de maneira semelhante a como as saídas de outras ações e conectores são usadas.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=August15_HO9-->
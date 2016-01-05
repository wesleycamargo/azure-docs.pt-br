<properties
 pageTitle="Introdução à utilização do Agendador do Azure no Portal do Azure | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Introdução à utilização do Agendador do Azure no portal clássico do Azure

## Introdução

É fácil criar trabalhos e coleções de trabalhos no Agendador do Azure. Este tutorial orienta você durante a criação da coleção de trabalhos você usará para armazenar trabalhos, a criação de um trabalho em uma coleção de trabalhos e uma visão geral das tarefas de gerenciamento e monitoramento do trabalho disponíveis. Não é necessário experiência anterior com o Azure para usar este tutorial.

Ao abrir o portal clássico do Azure pela primeira vez, você é levado automaticamente para a guia **TODOS OS ITENS**. As colunas da guia **TODOS OS ITENS** podem ser classificadas. Para exibir seus trabalhos do Agendador e coleções de trabalhos, clique na guia **AGENDADOR**.

![][1]

## Criar um conjunto de trabalhos e um trabalho

1.  Entre no [portal clássico do Azure](https://manage.windowsazure.com/).  

2.  Clique em **Serviços de Aplicativos** > **Criar Novo** > **Agendador** e clique em **Criação Personalizada**. <br /><br /> ![][2]

3.  Em **Coleção de Trabalhos**, selecione o nome da coleção de trabalho existente no menu suspenso **Coleção de Trabalhos**. Se você não tiver uma coleção de trabalhos existente à qual você gostaria de adicionar o trabalho, selecione **Criar novo** e digite um nome para identificar a nova coleção de trabalhos.<br /><br /> ![][3]

4.  Em **Região**, selecione a região geográfica para a coleção de trabalhos.

5.  Clique na seta para criar a coleção de trabalhos e prosseguir para a próxima fase – criando um trabalho.

6.  Vamos criar um trabalho que simplesmente visita http://www.microsoft.com/ com uma solicitação GET. Na tela **Ação do trabalho**, defina os seguintes valores para os campos solicitados do formulário:

    1.  **Nome:** ` getmicrosoft`  

    2.  **Tipo de ação:** ` HTTP`

    3.  **Método:** ` GET`

    4.  **URI:** ` http://www.microsoft.com`

   	![][4]

7.  Depois de criar um trabalho, defina uma agenda. O trabalho pode ser definido como um único trabalho, mas convém escolher uma agenda de recorrência. Algumas capturas de tela neste tutorial mostram uma recorrência de 1 minuto somente para fins ilustrativos, mas escolha uma recorrência de 12 horas.

    1.  **Repetir a cada:** ` 12 Hours`  

    2.  **Iniciando:** ` Now`

    3.  **Terminando em:** ` Select date 2 days after current day and any time`

   	![][5]

8.  Clique em **OK**. Pode levar algum tempo para criar o trabalho e a coleção de trabalhos. Para verificar o status, você pode monitorar as notificações na parte inferior do portal.

   	![][6]

   	Depois de criar o trabalho e a coleção de trabalhos, uma mensagem informa que o trabalho ou a coleção de trabalhos foi criada com êxito. O trabalho será listado na seção Trabalhos da seção Agendador e a coleção de trabalhos será listada na seção Coleções de trabalhos. Para configurar as configurações adicionais no trabalho, consulte a seção "Configurar um trabalho" abaixo.

   	![][7]

## Gerenciar e monitorar Coleções de trabalhos e Trabalhos

Quando uma Coleção de trabalhos é criada, ela aparece na tela principal de gerenciamento do Agendador.

![][8]

Clique em uma coleção de trabalhos para abrir uma nova janela com as seguintes opções:

1.  Painel  

2.  Escala

3.  Histórico

4.  Trabalhos

Os tópicos a seguir descrevem essas guias mais detalhadamente.

### Painel

Quando você clica no nome da coleção de trabalhos, a guia de **Painel** é exibida. O Painel exibe as seguintes informações:

![][9]

#### Visão geral de uso e visão geral de uso de execução do trabalho

Uma série de gráficos e uma tabela que mostram uma lista fixa de métricas. Essas métricas fornecem valores em tempo real relacionadas à integridade da sua coleção de trabalhos.

1.  Trabalhos atuais  

2.  Trabalhos concluídos

3.  Trabalhos com falha

4.  Trabalhos habilitados

5.  Trabalhos desabilitados

6.  Execuções de trabalho

#### Visão Rápida

Uma tabela que mostra uma lista fixa de status e métricas de configuração. Essas métricas fornecem valores em tempo real sobre o status e as configurações associadas à sua coleção de trabalhos, incluindo:

1.  Status  

2.  Região

3.  Número de erros

4.  Número de ocorrências com falha

5.  URI

### Escala

Na guia **Escala**, você pode alterar as configurações e a camada de serviço usada pelo Agendador.

![][10]

#### Geral

Isso indica se você está no plano **Gratuito** ou **Standard**.

#### Cotas

O Agendador do Azure implementa cotas com base em várias condições. Esta seção lista os limites de cota e permite que você altere-os. Por padrão, há um conjunto de cotas configuradas. Os limites dessas configurações de cota são restritos por seu plano e a alteração do plano pode afetar os preços.. As cotas podem ser alteradas para dimensionar o Agendador. As opções incluem:

1.  Máximo de trabalhos  

2.  Frequência máxima

3.  Intervalo máximo

### Histórico

A guia **Histórico** exibe as seguintes informações para o trabalho selecionado:

![][11]

#### Tabela de histórico

Uma tabela que exibe a métrica selecionada para cada execução do trabalho do sistema para o trabalho selecionado. Essas métricas fornecem valores em tempo real relacionados à integridade do seu Agendador.

#### Métricas disponíveis

As contagens/métricas de desempenho a seguir estão disponíveis:

1.  Status  

2.  Detalhes

3.  Tentativa de repetição

4.  Número de execuções (primeira, segunda, terceira, etc.)

5.  Carimbo de hora de execução

Você pode clicar em **Exibir detalhes de histórico** para examinar a resposta inteira para cada execução. Essa caixa de diálogo também permite que você copie a resposta para a área de transferência.

![][12]

### Trabalhos

A guia trabalhos exibe as informações a seguir para monitorar o histórico de execução de trabalhos:

![][13]

#### Tabela de trabalhos

Uma tabela que exibe a métrica selecionada para cada trabalho no sistema. Essas métricas fornecem valores em tempo real relacionados à integridade do seu Agendador.

#### Desabilitar, habilitar ou excluir um trabalho

Clicar no nome de um trabalho oferece a opção para habilitar, desabilitar ou excluir o trabalho. Trabalhos excluídos talvez não sejam recuperáveis.

#### Métricas disponíveis

As contagens/métricas de desempenho a seguir estão disponíveis:

1.  Nome  

2.  Última execução

3.  Próxima execução

4.  Status

5.  Frequência

6.  Falhas

7.  Falhas

8.  Execuções

9.  Tipo de ação

### Configurar um trabalho

Clicar em um trabalho na tela **Trabalhos** permite que você configure esse trabalho. Isso permite que você defina configurações avançadas adicionais além do que está disponível no assistente de criação rápida. Para configurar um trabalho, clique na seta à direita ao lado do nome do trabalho na tela **Trabalhos**.

A página de configuração de trabalho permite atualizar as configurações do trabalho. A página de configuração de trabalho é mostrada abaixo para trabalhos HTTP e HTTPS. Para tipos de ação do trabalho HTTP e HTTPS, você pode alterar o método para qualquer verbo HTTP permitido. Você também pode adicionar, excluir ou alterar os cabeçalhos e informações de autenticação básica.

![][14]

A página de configuração do trabalho aparece como mostrado abaixo para trabalhos de fila de armazenamento. Para tipos de ação de fila de armazenamento, você pode alterar a conta de armazenamento, nome da fila, token SAS e corpo. A seção "Agenda" (não mostrada abaixo) é idêntica à seção "Agenda" para tipos de ação do trabalho HTTP/HTTPS.

![][15]

Por fim, para todos os tipos de ação, você pode alterar o agendamento em si e seu comportamento de recorrência. Você pode alterar a data e hora de início, o agendamento de recorrência e a data e hora de término (se o trabalho é recorrente). Depois de fazer as alterações, você pode salvar as alterações, clicando em **Salvar** ou descartar as alterações, clicando em **Descartar**.

## Consulte também

 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/dn528946)

 [Referência de cmdlets do PowerShell do Agendador](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_1210_2015-->
<properties 
	pageTitle="Gerenciar aplicativos Web usando o Portal do Azure" 
	description="Visão geral das tarefas de gerenciamento de aplicativo Web no Microsoft Azure usando o Portal do Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Gerenciar aplicativos Web usando o Portal do Azure

No [Portal do Azure](https://portal.azure.com), gerencie seus aplicativos Web por meio de uma série de *folhas*, que são contêineres para os diferentes componentes. (Para obter uma visão geral do Portal do Azure, consulte o guia para alterações do [Portal](http://go.microsoft.com/fwlink/?LinkId=529715).)

Para exibir a folha de seu aplicativo Web, clique em **Página Inicial** e no nome do aplicativo ou clique em **Procurar** para ver todos os seus recursos do Azure.

![](./media/web-sites-manage/manage01.png)

A parte superior da folha tem controles para algumas ações comuns:

- **Configurações** para ver uma lista de todas as configurações de gerenciamento.

- **Procurar** para abrir o aplicativo Web na janela do navegador.

- Outros botões iniciar ou interromper o aplicativo, permutar slots de implantação, excluir o aplicativo e assim por diante.

**Informações gerais** lista informações básicas sobre o aplicativo, incluindo a URL, local, plano do [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) e status atual (em execução, parado etc).

Abaixo de **Informações gerais**, há várias partes que você pode usar para monitorar, gerenciar e implantar seu aplicativo. Para personalizar o layout da folha, clique com botão direito do mouse e selecione **Personalizar** ou **Adicionar Partes**. O restante deste tópico descreve as partes no layout padrão.

## Monitoramento

Em **Monitoramento**, há um gráfico que mostra as métricas para o seu aplicativo Web. Para configurar o gráfico, clique em **Editar**. Você pode selecionar o intervalo de tempo e quais métricas exibir. As métricas disponíveis incluem o número de solicitações, tempo médio de resposta, erros de servidor e tempo de CPU.

![](./media/web-sites-manage/manage02.png)

Para adicionar uma regra de alerta, clique no gráfico e, depois, em **Adicionar alerta**. Uma regra de alerta notifica você quando uma determinada métrica atinge um determinado limite. Por exemplo, você pode ser alertado quando o número de erros do servidor exceder um determinado valor em um período de 5 minutos.

**Monitoramento** também inclui partes para configurar dados analíticos, configurar o monitoramento de aplicativos e criar testes de disponibilidade. Para obter mais informações, consulte [Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md).

## Uso

![](./media/web-sites-manage/manage03.png)

- **Armazenamento de Sistema de Arquivos** mostra quanto espaço de armazenamento de arquivos seu aplicativo está usando.
- **Cotas** mostra como seu aplicativo está usando sua cota de uso de recursos. Clique nesta parte para ver detalhes.
- **Escala** permite dimensionar a contagem de instâncias e configurar o dimensionamento automático. Consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure](../how-to-scale-websites.md).  
- **Gasto estimado** mostra uma estimativa de custo para o aplicativo.
- **Camada de preços** permite que você altere a camada de preços.

## Operações

![](./media/web-sites-manage/manage04.png)

- **Eventos**. Clique para exibir logs de eventos.  
- **Regras de alerta**. Clique para exibir as regras de alerta e adicionar novos alertas.
- **Logs de streaming** (A). Clique para exibir logs de aplicativo. Para habilitar logs, vá para **Configurações** e abra a folha **Logs de diagnóstico**. 
- **Console**. Clique para abrir uma linha de comando que é executada dentro do portal. Você pode usá-la para executar comandos de comando como `mkdir` e `dir`.  
- **Gerenciador de processos** (B). Clique para exibir os processos em execução no aplicativo, incluindo o conjunto de trabalho e a contagem de threads.

## Implantação
 
![](./media/web-sites-manage/manage05.png)

- Configure a implantação contínua. Consulte [Usando Git para implantar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-publish-source-control.md)
- Slots de implantação. Consulte [Implantar em ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md)
- Defina credenciais de implantação para Git ou FTP. 

## Rede

- Conectar o aplicativo a uma rede virtual
- Adicionar conexões híbridas

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

- [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure](../how-to-scale-websites.md)
- [Executar tarefas em segundo plano com Trabalhos Web](web-sites-create-web-jobs.md)
- [Backups de aplicativos Web do Azure](web-sites-backup.md) e [restaurar](web-sites-restore.md)
- [Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->
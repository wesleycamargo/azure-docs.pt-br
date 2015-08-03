<properties 
	pageTitle="Gerenciar um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Links para recursos para gerenciar um aplicativo Web no Serviço de Aplicativo do Azure." 
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
	ms.date="04/23/2015" 
	ms.author="mwasson"/>

# Gerenciar um aplicativo Web no Serviço de Aplicativo do Azure

Este tópico contém links para recursos para gerenciar um aplicativo Web no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). O gerenciamento inclui todas as tarefas que mantém seu aplicativo Web em execução perfeitamente.

Durante o ciclo de vida de um aplicativo Web, você realizará diferentes tarefas de gerenciamento, a medida em que você move da implantação inicial à operação normal, manutenção e atualizações.

Muitas tarefas de gerenciamento de aplicativo Web podem ser realizadas no portal do Azure. Para obter mais informações, consulte [Gerenciar aplicativos Web usando o Portal do Azure](web-sites-manage.md).

## Antes de implantar seu aplicativo Web para produção

### Escolha uma faixa

O Serviço de Aplicativo do Azure é oferecido em cinco níveis: Gratuito, Compartilhado, Básico, Padrão e Premium. Para obter informações sobre os recursos e preços para cada faixa, consulte [Detalhes dos preços](/pricing/details/app-service/).

- Os [planos de Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) lhe permitem agrupar vários aplicativos Web na mesma camada.
- Você sempre pode [alternar as camadas](web-sites-scale.md) após criar seu aplicativo Web.

### Configuração

Use o [portal de visualização do Azure](https://portal.azure.com/) para definir várias opções de configuração. Para obter detalhes, consulte [Configurar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-configure.md). Aqui está uma lista de verificação rápida:

- Selecione **versões de tempo de execução** para .NET, PHP, Java ou Python, se necessário.
- Habilite **WebSockets** se seu aplicativo Web usa o protocolo WebSocket. (Isto inclui aplicativos que usam o [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md).)
- Você está executando trabalhos web contínuos? Se estiver, habilite **Sempre ativo**.
- Defina o **documento padrão**, como index.html.

Além desta definições de configurações básicas, talvez você queira configurar o seguinte:

- A criptografia **Secure Socket Layer (SSL)**. Para usar a SSL com um nome de domínio personalizado, você deve solicitar um certificado SSL e configurar seu aplicativo Web para usá-lo. Consulte [Habilitar HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-configure-ssl-certificate.md).
- **Nome de domínio personalizado.** Seu aplicativo Web tem automaticamente um subdomínio em azurewebsites.net. Você pode associar um nome de domínio personalizado, por exemplo, contoso.com. Consulte [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](web-sites-custom-domain-name.md).

Configuração específica de idioma:

- **PHP**: [configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure](web-sites-php-configure.md).
- **Python**: [configurando o Python com Aplicativos Web do Serviço de Aplicativo do Azure](web-sites-python-configure.md)


## Enquanto seu aplicativo Web está em execução

Enquanto seu aplicativo Web estiver em execução, verifique se ele está disponível e dimensionado para atender o tráfego do usuário. Você talvez precise solucionar erros.

### Monitoramento

- Por meio do portal de visualização do Azure, você pode [Adicionar métricas de desempenho](web-sites-monitor.md) como uso de CPU e o número de solicitações do cliente.
- Para uma percepção mais aprofundada, use o New Relic para monitorar e gerenciar o desempenho. Consulte [Aplicativo Web do .NET no Serviço de Aplicativo do Azure com o gerenciamento de desempenho de aplicativo New Relic](store-new-relic-web-sites-dotnet-application-performance-management.md).
- [Dimensione seu aplicativo Web](web-sites-scale.md) em resposta ao tráfego. Dependendo da sua faixa, você pode reduzir o número de VMs e/ou o tamanho das instâncias da VM. Nas camadas Padrão e Premium, você também pode configurar o dimensionamento automático, para que seu aplicativo Web seja dimensionado automaticamente, em uma agenda fixa ou em resposta à carga.  
 
### Backups

- Defina [backups automáticos](web-sites-backup.md) de seu aplicativo Web. Saiba mais sobre backups [neste vídeo](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Saiba sobre as opções para a [recuperação de banco de dados](http://msdn.microsoft.com/library/azure/hh852669.aspx) no Banco de dados SQL do Azure.

### Solucionar problemas

- Se algo der errado, você pode [solucionar no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), usando os logs de diagnóstico e depuração ao vivo na nuvem. 
- Fora do Visual Studio, há várias maneiras de coletar os logs de diagnóstico. Consulte [Habilitar o registro de log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-enable-diagnostic-log.md).
- Para aplicativos Node.js, consulte [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md).

### Restaurando dados

- [Restaure](web-sites-restore.md) um aplicativo Web no qual foi realizado o backup anteriormente.


## Ao atualizar seu aplicativo Web

Se você não habilitou os backups automáticos, você pode criar um [ backup manual](web-sites-backup.md).

Considere o uso de uma [implantação em estágios](web-sites-staged-publishing.md). Esta opção permite que você publique atualizações para uma implantação em estágios que executam lado a lado com a sua implantação de produção.

Se você usar o Visual Studio Online, é possível configurar a implantação contínua a partir do controle de origem:

- [Usando o TFVC (Controle de Versão do Team Foundation)](../cloud-services-continuous-delivery-use-vso.md) 
- [Usando Git](../cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  

<!---HONumber=July15_HO4-->
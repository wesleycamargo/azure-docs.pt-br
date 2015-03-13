<properties 
	pageTitle="Guia do recurso Insights operacionais" 
	description="Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de computador para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Guia do recurso Insights operacionais</h1>

Este guia ajudará você a compreender quais problemas o Insights operacionais pode ajudá-lo a resolver, do que consiste o ambiente Insights Operacionais e como você pode criar uma conta do Insights operacionais e entrar no serviço.

<h2 id="whatisaad">O que é Insights Operacionais?</h2>

Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de computador para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados.

Com o Insights operacionais, você pode transformar dados do computador em inteligência operacional. Especificamente, você pode:

- Gerenciar a capacidade da sua infraestrutura de servidor
- Atualizar servidores com atualizações do sistema
- Compreender as relações entre os dados nos arquivos de log do servidor
- Proteger servidores com antimalware
- Gerenciar riscos de segurança com garantia de segurança
- Rastrear alterações de configuração em servidores
- Otimizar o SQL Server
- Pesquisar logs de eventos e do IIS
- Instalar agentes em máquinas virtuais IaaS Microsoft Azure
- Usar informações operacionais do Operations Manager  

<h2 id="">Ambiente do Insights Operacionais</h2>

O ambiente do Insights operacionais é composto de:

- Espaços de trabalho hospedados no Microsoft Azure que são contêineres para contas do Azure
- o serviço Web do Insights operacionais, que está hospedado na nuvem
- qualquer um dos agentes separados que se conectam diretamente ao serviço Web
- ou, um serviço anexado ao System Center Operations Manager, mas não é necessário




Se você usou a versão anterior do Insights operacionais chamado System Center Advisor, você pode ter software Advisor instalado no seu ambiente local. No entanto, o software Advisor não tem suporte com o Insights Operacionais.

O uso do software Insights Operacionais como um serviço do Operations Manager consiste em um ou mais grupos de gerenciamento e pelo menos um agente por grupo de gerenciamento. Os agentes do Operations Manager coletam dados de seus servidores e os analisam usando pacotes de inteligência (semelhantes a um pacote de gerenciamento no System Center Operations Manager). Os dados analisados são enviados regularmente do Operations Manager para o serviço Web do Insights Operacionais usando um servidor proxy, ignorando os outros servidores, então não há nenhuma carga adicional colocada sobre eles.

Da mesma forma, os agentes instalados em computadores individuais podem se conectar diretamente ao serviço Web para enviar os dados coletados para processamento.

Os dados em cada pacote de inteligência são analisados e apresentados no portal do Insights operacionais. Você pode exibir todos os alertas e diretrizes de correções associadas, avaliações de configuração, problemas de capacidade de infraestrutura, status de atualização do sistema, avisos de antimalware e dados de log. Você também pode executar pesquisas detalhadas.

<h2 id="">Criar uma conta do Insights Operacionais e entrar</h2>

Use as seguintes informações que configurar uma conta para usar com o Insights Operacionais do Microsoft Azure e entrar no Insights operacionais.



<h3>Obtenha uma conta organizacional ou uma conta da Microsoft</h3>

Se você quiser criar uma conta organizacional, inscreva-se em <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">Conta Institucional Microsoft</a>. Em seguida, você precisa conceder acesso à sua conta organizacional para Insights Operacionais no <a href="http://aka.ms/Mr1dtz" target="_blank">Active Directory do Microsoft Azure</a>.


Se você quiser obter uma conta da Microsoft (anteriormente chamada de "Windows Live ID"), inscreva-se em <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">Inscreva-se - conta da Microsoft</a>. Acesso é concedido automaticamente ao Insights operacionais com uma conta da Microsoft.


<h3>Entrar no Insights operacionais</h3>

1. Vá para <a href="preview.opinsights.azure.com" target="_blank">Insights Operacionais do Microsoft Azure</a>  e escolha Entrar na parte superior da página.
2. Selecione **conta da Microsoft** ou **conta organizacional** e **entre** com suas credenciais.
3. Se solicitado, crie uma conta do Insights Operacionais para associar com suas informações de logon.
4. Selecione **Experimentar visualização** quando for solicitado para selecionar a versão do Insights Operacionais que você deseja usar.
5. Se solicitado, insira seu código de convite e clique em **Aplicar** para ingressar na visualização. Se você não tiver um código de convite, na parte inferior da tela, insira suas informações para obter um.

<!--HONumber=45--> 

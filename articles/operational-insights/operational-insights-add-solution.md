<properties
    pageTitle="Soluções de Insights Operacionais"
    description="Você pode acrescentar funcionalidade adicional aos Insights Operacionais com soluções"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Soluções de Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

O Insights Operacionais do Microsoft Azure inclui o módulo de avaliação de configuração base. No entanto, você pode obter mais funcionalidade com a adição de soluções da página Visão Geral.

![imagem do ícone de soluções](./media/operational-insights-add-solution/sol-gallery.png)

Depois de adicionar uma solução, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço Insights Operacionais. O processamento pelo serviço Insights Operacionais pode levar de alguns minutos a várias horas. Depois que o serviço processa os dados, você pode exibi-los no Insights Operacionais.

Você pode facilmente remover uma solução quando ela não for mais necessário. Quando você remove uma solução, seus dados não são enviados ao Insights Operacionais, o que reduz a quantidade de dados usados pela sua cota diária.

## Soluções com suporte no Microsoft Monitoring Agent

Neste momento, servidores conectados diretamente ao Insights Operacionais do Microsoft Azure usando o Microsoft Monitoring Agent podem usar a maioria das soluções disponíveis, incluindo:

- [Atualizações do Sistema](operational-insights-updates.md)

- [Gerenciamento de Log](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Controle de Alterações](operational-insights-change-tracking.md)

- [Avaliação do SQL e do Active Directory](operational-insights-assessment.md)

No entanto, as soluções a seguir *não* têm suporte com o Microsoft Monitoring Agent e exigem o SCOM (System Center Operations Manager).

- [Gerenciamento de Capacidade](operational-insights-capacity.md)

- [Gerenciamento de alertas](operational-insights-alerts.md)

- [Avaliação de Configuração](operational-insights-solutions.md#configuration-assessment)

Consulte [Considerações sobre o Operations Manager com Insights Operacionais](operational-insights-operations-manager.md) para obter diretrizes sobre como usar essas soluções com o Operations Manager.

Há suporte para coleta de log do IIS em computadores com:

- Windows Server 2012

- Windows Server 2012 R2

### Para adicionar uma solução


1. Na página Visão Geral no Insights Operacionais, clique no bloco **Galeria de Soluções**.


2. Na página Galeria de Soluções de Insights Operacionais, você pode aprender sobre cada solução disponível. Clique no nome da solução que deseja adicionar a Insights Operacionais.


3. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.


4. Na página Confirmação, clique em **Aceitar** para concordar com a política de privacidade e os termos de uso.


5. Um novo bloco para a solução que você adicionou aparece na visão geral da página em Insights Operacionais e você pode começar a usá-lo depois que o serviço de informações operacionais processar seus dados.




### Para remover uma solução



1. Na página Visão Geral no Insights Operacionais, clique no bloco **Galeria de Soluções**.


2. Na página da Galeria de Soluções do Insights Operacionais, sob a solução que deseja remover, clique em **Remover**.


3. Na página de confirmação, clique em **Sim** para remover a solução.

<!---HONumber=July15_HO4-->
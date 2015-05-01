<properties 
    pageTitle="Pacotes de inteligência do Insights Operacionais" 
    description="Você pode adicionar outras funcionalidades ao Insights Operacionais com pacotes de inteligência" 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Pacotes de inteligência do Insights Operacionais

O Insights Operacionais do Microsoft Azure inclui o módulo de avaliação de configuração base. No entanto, você pode obter mais funcionalidade com a adição de pacotes de inteligência da página Visão Geral.

![imagem do ícone de pacotes de inteligência](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

Depois de adicionar um pacote de inteligência, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço Insights Operacionais. O processamento pelo serviço Insights Operacionais pode levar de alguns minutos a várias horas. Depois que o serviço processa os dados, você pode exibi-los no Insights Operacionais.

Você pode facilmente remover um pacote de inteligência quando ele não for mais necessário. Quando você remove um pacote de inteligência, seus dados não são enviados ao Insights Operacionais, o que reduz a quantidade de dados usados pela sua cota diária.

## Pacotes de inteligência com suporte no Microsoft Monitoring Agent

Neste momento, servidores conectados diretamente ao Insights Operacionais do Microsoft Azure usando o Microsoft Monitoring Agent podem usar a maioria dos pacotes de inteligência disponíveis, incluindo:

- [Atualizações do Sistema](operational-insights-updates.md)

- [Gerenciamento de Log](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Controle de Alterações](operational-insights-change-tracking.md)

- [Avaliação do SQL e Active Directory](operational-insights-assessment.md)

No entanto, para os seguintes pacotes de inteligência  *não* há suporte com o Microsoft Monitoring Agent

- [Gerenciamento de Capacidade](operational-insights-capacity.md)

- [Avaliação de Configuração](operational-insights-configuration-assessment.md)

Há suporte para coleta de log do IIS em computadores com:

- Windows Server 2012

- Windows Server 2012 R2

### Para adicionar um pacote de inteligência


1. Na página Visão Geral do Insights Operacionais, clique no bloco **Pacotes de Inteligência**.


2. Na página Galeria de Pacotes de Inteligência do Insights Operacionais, você pode aprender sobre cada pacote de inteligência disponível. Clique no nome do pacote de inteligência que deseja adicionar a Insights Operacionais.


3. Na página para o pacote de inteligência que você escolheu, são exibidas informações detalhadas sobre o pacote de inteligência. Clique em **Adicionar**.


4. Na página de confirmação, clique em **Aceitar** para concordar com a política de privacidade e os termos de uso.


5. Um novo bloco para o pacote de inteligência que você adicionou aparece na visão geral da página em Insights Operacionais e você pode começar a usá-lo depois que o serviço de informações operacionais processar seus dados.




### Para remover um pacote de inteligência



1. Na página Visão Geral do Insights Operacionais, clique no bloco **Pacotes de Inteligência**.


2. Na página da Galeria de Pacotes de Inteligência do Insights Operacionais, sob o pacote de inteligência que deseja remover, clique em **Remover**.


3. Na página de confirmação, clique em **Sim** para remover o pacote de inteligência.




<!--HONumber=52-->
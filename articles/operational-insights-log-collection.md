<properties 
   pageTitle="Como usar o gerenciamento de Log" 
   description="Com o gerenciamento de log no Insights Operacionais do Microsoft Azure, você pode exibir eventos de log coletados dos servidores monitorados" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights" 
   ms.date="03/20/2015"
   ms.author="banders"/>

# Usar o gerenciamento de log 

Antes de poder usar o Gerenciamento de Log no Insights Operacionais do Microsoft Azure, você deve ter o pacote de inteligência instalado. Como esse pacote deve ser configurado e não coleta nada por padrão, ele está habilitado por padrão em cada novo espaço de trabalho. Se você o tiver removido, poderá adicioná-lo novamente da Galeria de pacotes de inteligência. Consulte [Usar a Galeria para adicionar ou remover pacotes de inteligência](operational-insights-add-intelligence-packs.md). 

Você pode adicionar novos logs para coletar eventos e escolher quais níveis de evento ou gravidade deseja coletar para os logs.
Depois de o gerenciamento de log estar configurado, ele enviará por push sua política de coleta e começará a coletar eventos.
Você pode acessar algumas falhas iniciais de eventos de log coletados dos servidores monitorados usando o bloco **Gerenciamento de log** na página **Visão Geral** do Insights Operacionais. 

![imagem do bloco Gerenciamento de Log](./media/operational-insights-log-collection/overview-log-mgt.png)

O bloco abre a página **Gerenciamento de Log**, em que é possível exibir um resumo dos eventos contidos nos logs.
Ou você pode usar a pesquisa diretamente, como para qualquer outro tipo de dados.


A página detalha as seguintes categorias:

- Logs de eventos do Windows
- Logs do IIS
- Outros logs que você adicionou

![imagem do painel Gerenciamento de Log](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![imagem do painel Gerenciamento de Log](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## Formato de arquivo de log do IIS

O único formato de log do IIS com suporte no momento é W3C. Não se preocupe - é o formato mais comum e o formato padrão no IIS 7 e no IIS 8. Assim, se você efetuar logon em formato nativo NCSA ou IIS, o Insights Operacionais não coletará esses logs de modo algum. Mesmo em formato W3C, você verá que nem todos os campos são registrados por padrão. Você pode ler mais sobre o formato em [Selecionar campos do W3C para o log (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx). 


> [AZURE.TIP] Para obter a melhor experiência de pesquisa, recomendamos selecionar todos os campos de log para cada site usando **Registrar em log** no IIS. Também é recomendável alterar a agenda de **Substituição de Arquivo de Log** para novos logs para **Por Hora** - assim, os arquivos menores serão carregados para a nuvem, economizando largura de banda.


## Coletar logs de eventos do Windows do Operations Manager ou de agentes conectados diretamente

1. Na página **Visão Geral**, clique no bloco **Gerenciamento de Log**. 

2. No painel **Gerenciamento de Log**, clique no bloco **Configurar**.
 
3. Digite o nome do log de eventos do qual deseja coletar informações. Se não tiver certeza de qual nome usar, selecione as propriedades do log de eventos do Windows no **Visualizador de Eventos**, copie o nome no campo **FullName** e cole-o na caixa **Coletar Eventos dos Seguintes Logs de Eventos**.

4. Clique em **+** para adicionar o log.

5. Selecione os níveis de eventos ou a gravidade que deseja coletar para o log. **Não há suporte para Eventos de Sucesso da Auditoria** e **Falha na Auditoria** nesta versão.

6. Repita as etapas anteriores para cada log do qual deseja coletar informações e, em seguida, clique em **Salvar**.

7. Os eventos devem aparecer no Insights Operacionais em alguns minutos e, em seguida, você poderá pesquisar os dados. 



## Para coletar logs do IIS do Operations Manager ou agentes conectados diretamente

1. Na página **Visão Geral**, clique no bloco **Gerenciamento de Log**. 

2. No painel **Gerenciamento de Log**, clique no bloco **Configurar**.
 
3. Em **LOGS do IIS**, selecione **Coletar Logs do Operations Manager**.


## Coletar logs do IIS e/ou eventos do Windows do Diagnóstico do Azure
Isso pode ser configurado no Portal de Gerenciamento do Azure, não no portal do Insights Operacionais. No seu espaço de trabalho, vá para a guia **Armazenamento** e habilite a coleta de log nessa conta de armazenamento.


## Trabalhar com arquivos de log
 
1. Na página **Visão Geral**, clique no bloco **Gerenciamento de Log**.

2. No painel **Gerenciamento de Log**, exiba os logs de eventos e escolha um com o qual trabalhar.
  
3. Clique em qualquer item para exibir informações detalhadas sobre ele na página de **Pesquisa**.

4. Você pode executar pesquisas adicionais com base nos resultados iniciais e então analisar e correlacionar os logs.

 
# Recursos adicionais
- [Requisitos de formato de Log do IIS no Insights Operacionais do Azure](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Veja que outras fontes de dados e tipos de logs a comunidade está nos pedindo para implementar no [Fórum de comentários](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).


<!--HONumber=52-->
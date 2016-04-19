<properties
   pageTitle="Obter percepções de dados da Central de Segurança do Azure com o Power BI | Microsoft Azure"
   description="O pacote de conteúdo do Power BI da Central de Segurança do Azure facilita a localização de alertas de segurança, recomendações, recursos atacados e tendências, com base em um conjunto de dados que foi criado para o relatório."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/11/2016"
   ms.author="yurid"/>

# Obtenha percepções de dados da Central de Segurança do Azure com o Power BI
O [Painel do Power BI](http://aka.ms/azure-security-center-power-bi) para a Central de Segurança do Azure o habilita a visualizar, analisar e filtrar recomendações e alertas de segurança de qualquer lugar, incluindo seu dispositivo móvel. Use o painel do Power BI para revelar tendências e padrões, exibir alertas de segurança por recurso ou endereço IP de origem e riscos de segurança não tratados por recurso ou idade. Você pode também combinar as recomendações da Central de Segurança e alertas de segurança com outros dados de maneiras interessantes, por exemplo, com os [Logs de Auditoria do Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e a [Auditoria do Banco de Dados SQL do Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), que oferecem painéis do Power BI, ou pode exportar esses dados para o Excel para facilmente produzir relatórios sobre o estado de segurança de seus recursos da nuvem.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## Como explorar seus dados da Central de Segurança do Azure com serviços do Power BI
Conecte-se ao [pacote de conteúdo da Central de Segurança do Azure](https://app.powerbi.com/groups/me/getdata/services/azure-security-center) no Power BI e siga as etapas abaixo:

1\.Clique em **Conectar** no bloco da Central de Segurança do Azure para continuar.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\.A janela **Conectar-se à Central de Segurança do Azure** será aberta. No campo **ID da Assinatura do Azure**, digite sua assinatura do Azure e clique em **Avançar**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\.Na lista suspensa **Método de autenticação**, escolha **oAuth2** e clique em **Entrar**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\.Você será redirecionado para uma página de autenticação onde deverá digitar as credenciais que está usando para se conectar à Central de Segurança do Azure. Após o processo de autenticação ser concluído, o Power BI iniciará a importação de dados para criar seus relatórios. Durante esse período, você verá a seguinte mensagem no canto direito do navegador:

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\.Quando o processo for concluído, o painel do Power BI da Central de Segurança do Azure carregará os relatórios, conforme mostrado abaixo:

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig5.png)

De uma só vez, você pode ver o número de alertas de segurança e recomendações, além do número de VMs, bancos de dados SQL do Azure e recursos de rede que estão sendo monitorados pela Central de Segurança do Azure.

Um link para a Central de Segurança do Azure o redirecionará para o portal do Azure. Os gráficos facilitam a visualização de informações sobre recomendações de segurança e alertas, incluindo:

- Integridade da Segurança de Recursos
- Recomendações Pendentes Gerais
- Recomendações de VM
- Alertas ao Longo do Tempo
- Recursos Atacados
- IPs Atacados

Por trás de cada gráfico, há percepções adicionais. Selecione um bloco para obter mais informações. Por exemplo, o bloco de Integridade da Segurança de Recursos mostra detalhes adicionais sobre recomendações pendentes por recursos, conforme mostrado abaixo:

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Ao clicar em uma linha desse gráfico, as outras ficarão esmaecidas e você se concentrará apenas na linha selecionada. Para retornar ao painel, clique em **Central de Segurança do Azure** na opção **Painéis** no painel esquerdo dessa página.

> [AZURE.NOTE] Edite o relatório se quiser personalizar seus relatórios, adicionando campos ou alterando elementos visuais existentes. Leia [Interagir com um relatório no Modo de Edição no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) para saber mais.

Os blocos **Alertas ao Longo do Tempo**, **Recursos Atacados** e **IPs Atacados** terão resultados semelhante ao clicar em cada um deles. Isso ocorre porque o relatório agrega informações sobre essas três variáveis e as chama de **Recursos sob Ataque**, conforme mostrado abaixo:

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig7.png)

Nesse ponto, você pode também salvar uma cópia do relatório, imprimi-lo ou publicá-lo na Web usando as opções disponíveis no menu **Arquivo**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##Como usar o painel da Central de Segurança do Azure para acessar o Power BI
Você também pode usar o painel da Central de Segurança do Azure para acessar relatórios do Power BI. Siga as etapas abaixo para executar essa tarefa:

1\.No painel **Central de Segurança do Azure**, clique no botão **Explorar no Power BI**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig9-new.png)

2\.A folha **Explorar no Power BI** será aberta no lado direito, conforme mostrado abaixo:

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\.Na lista suspensa **Escolher uma assinatura para explorar no Power BI**, selecione a assinatura que você deseja usar.

4\.No campo **Copiar a ID da assinatura**, clique no botão Copiar. 5.Clique no botão **Ir para o Power BI**. 6.A janela **Conectar-se à Central de Segurança do Azure** será aberta. No campo **Id da Assinatura do Azure**, digite sua assinatura do Azure e clique em **Avançar**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\.Na lista suspensa **Método de autenticação**, selecione **oAuth2** e clique em **Entrar**.

![Conectar-se a Central de Segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\.Você será redirecionado para uma página de autenticação, em que deverá digitar as credenciais que está usando para se conectar à Central de Segurança do Azure. Após o processo de autenticação ser concluído, o Power BI iniciará a importação de dados para criar seus relatórios.

> [AZURE.NOTE] Uma atualização do relatório é agendada para ocorrer diariamente. Se houver uma falha dessa atualização, leia [Possíveis problemas de atualização com o Power BI da Central de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) para saber mais sobre como solucionar problemas.

## Próximas etapas
Neste documento, você aprendeu a usar o Power BI na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança na Central de Segurança do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0413_2016-->
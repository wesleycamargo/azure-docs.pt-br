<properties
   pageTitle="Configuração das políticas de segurança na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a configurar políticas de segurança na Central de segurança do Azure."
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
   ms.date="08/24/2016"
   ms.author="yurid"/>

# Configuração de políticas de segurança na Central de segurança do Azure
Este documento ajuda a configurar as políticas de segurança na Central de Segurança, guiando você nas etapas necessárias para executar essa tarefa.

## Quais são políticas de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas para o grupo de recursos ou as assinaturas do Azure de acordo com as necessidades de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de segurança do Azure determinam as recomendações de segurança e o monitoramento para ajudá-lo a identificar vulnerabilidades potenciais e atenuar as ameaças. Leia o [Guia de Planejamento e Operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como decidir qual é a opção mais adequada para você.

## Como definir políticas de segurança para assinaturas

É possível configurar políticas de segurança para cada grupo de recursos ou assinatura. Para modificar uma política de segurança, você deve ser um proprietário ou colaborador daquela assinatura. Acesse o portal do Azure e siga as etapas anteriores para configurar as políticas de segurança na Central de Segurança:

1. Clique no bloco **Política** no painel Central de Segurança.

2. Na folha **Política de Segurança - Definir política por assinatura ou grupo de recursos** que é aberta no lado direito, selecione a assinatura desejada para habilitar a política de segurança. Se você preferir habilitar a Política de Segurança para um grupo de recursos em vez da assinatura inteira, role para baixo até a próxima seção onde falamos sobre como configurar políticas de segurança para Grupos de Recursos.

    ![Definir a política](./media/security-center-policies/security-center-policies-fig1-ga.png)

3. A folha **política de segurança** para essa assinatura é aberta com um conjunto de opções semelhantes ao seguinte:

    ![Habilitar coleta de dados](./media/security-center-policies/security-center-policies-fig2-ga.png)

4. Certifique-se de que a opções **Coletar dados de máquinas virtuais** esteja **Ativa**. Essa opção habilita a coleta de log automático para recursos novos e existentes.

    >[AZURE.NOTE] É recomendável que você ative a coleta de dados para cada uma das suas assinaturas para assegurar que o monitoramento de segurança esteja disponível para todas as VMs novas e existentes. Habilitar a coleta de dados instala o agente de monitoramento. Se não desejar ativar a coleta de dados agora desse local, você pode fazer isso posteriormente dos modos de exibição integridade e recomendações. Você também pode habilitar a coleta de dados somente para a assinatura ou para VMs selecionadas. Consulte as [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) para saber mais sobre as VMs com suporte.

5. Se sua conta de armazenamento ainda não estiver configurada, você poderá ver um aviso quando abrir a **Política de Segurança**, semelhante ao seguinte:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig2.png)

6. Se você vir esse aviso, clique nessa opção e selecione a região, como mostrado na seguinte tela:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig3-ga.png)

7. Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Após decidir a região que você pretende usar, selecione a região e, em seguida, selecione a conta de armazenamento.

8. Na folha **Escolher contas de armazenamento**, clique em **OK**.

    > [AZURE.NOTE] Se preferir, você pode agregar dados de máquinas virtuais em várias regiões em uma conta de armazenamento central. Consulte as [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) para obter mais informações.

9. Na folha **Política de segurança**, clique em **Ativar** para habilitar as recomendações de segurança que você deseja usar nesta assinatura. Clique na opção **Política de prevenção**. A folha **Política de Segurança** será aberta, conforme mostrado na seguinte imagem:

	![Selecionar as políticas de segurança](./media/security-center-policies/security-center-policies-fig4-ga.png)

Use a tabela a seguir como referência para entender o que cada opção fará:

| Política | Quando o estado for Ativado |
|----- |-----|
| Atualizações do Sistema | Diariamente, recupera uma lista de atualizações críticas e de segurança disponíveis no Windows Update ou WSUS, dependendo de qual serviço está configurado para a máquina virtual e recomenda que as atualizações ausentes sejam aplicadas. Verifica as atualizações mais recentes nos sistemas Linux usando o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar quais pacotes têm atualizações disponíveis. Também verifica a segurança e as atualizações críticas das máquinas virtuais dos [Serviços de Nuvem](./cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do sistema operacional | Diariamente, analisa as configurações do SO que podem tornar a máquina virtual mais vulnerável a ataques e recomenda as alterações de configuração para endereçar essas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas que estão sendo monitoradas. |
| Proteção do Ponto de Extremidade | Recomenda que seja fornecida uma proteção do ponto de extremidade para todas as máquinas virtuais do Windows para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados.|
| Criptografia de disco | Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. 
| Grupos de segurança de rede | Recomenda que os [NSGs (Grupos de Segurança da Rede)](../virtual-network/virtual-networks-nsg.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os NSGs configurados para uma sub-rede serão herdados por todas as interfaces de rede de máquina virtual, a menos que especificado o contrário. Além de verificar se um NSG foi configurado, essa opção avalia as regras de segurança de entrada para identificar se há regras que permitem o tráfego de entrada. |
| Firewall do Aplicativo Web | Recomenda que um Firewall do Aplicativo Web seja fornecido nas máquinas virtuais quando o [ILPIP (IP Público no Nível da Instância)](../virtual-network/virtual-networks-instance-level-public-ip.md) é usado e as Regras de Segurança de Entrada do NSG Associadas são configuradas para permitir o acesso à porta 80/443. O IP de balanceamento de carga (VIP) é usado e o balanceamento de carga associado e as regras NAT de entrada estão configuradas para permitir acesso à porta 80/443 (para obter mais informações, consulte [Suporte do Gerenciador de Recursos do Azure para Balanceador de Carga](../load-balancer/load-balancer-arm.md)) |
| Firewall da Próxima Geração | Isso estende as proteções da rede para além dos Grupos de Segurança da Rede, que são internos no Azure. A Central de Segurança descobrirá as implantações para as quais o Firewall da Próxima Geração é recomendado e irá habilitá-lo para fornecer um dispositivo virtual. |
| Auditoria SQL | Recomenda que a auditoria de acesso a Servidores SQL do Azure e bancos de dados esteja habilitada para conformidade, detecção avançada e fins de investigação. |
| Transparent Data Encryption do SQL | Recomenda que a criptografia em repouso seja habilitada para arquivos de log de bancos de dados SQL do Azure, backups associados e transação para que mesmo se os seus dados forem violados, eles não possam ser lidos. |
	
Quando você terminar de configurar todas as opções, clique em **OK** na folha **Política de Segurança** com as recomendações e clique em **Salvar** na folha **Política de Segurança** com as configurações iniciais.

## Como definir políticas de segurança para grupos de recursos

Se preferir configurar as políticas de segurança por grupo de recursos, as etapas são semelhantes às usadas para configurar políticas de segurança para assinaturas. A principal diferença é que você precisará expandir o nome da assinatura e selecionar o grupo de recursos desejado para configurar a política de segurança exclusiva:

![Seleção de grupo de recursos](./media/security-center-policies/security-center-policies-fig5-ga.png)

Após selecionar o grupo de recursos, a folha **Política de segurança** será aberta. Por padrão, a opção **Herança** está habilitada, significando que todas as políticas de segurança desse grupo de recursos são herdadas do nível da assinatura. Você pode alterar essa configuração caso deseje uma política de segurança personalizada por grupo de recursos. Nesse caso, você precisa selecionar **Exclusiva** e fazer alterações na opção **Política de prevenção**.

![Política de segurança por grupo de recursos](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [AZURE.NOTE] No caso de um conflito entre a política de nível de assinatura e a política de nível de grupo de recursos, a política de nível de recursos tem precedência.


## Confira também

Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Guia de Operações e Planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) - saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0824_2016-->
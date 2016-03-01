<properties
   pageTitle="Introdução ao Centro de Segurança do Azure | Microsoft Azure"
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
   ms.date="02/22/2016"
   ms.author="yurid"/>

# Configuração de políticas de segurança na Central de segurança do Azure
Este documento ajuda você a configurar políticas de segurança na Central de Segurança do Azure, guiando você pelas etapas necessárias para executar essa tarefa.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## Quais são políticas de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura especificada. Na Central de segurança do Azure, você define as políticas para as assinaturas do Azure de acordo com as necessidades de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de segurança do Azure determinarão as recomendações de segurança e o monitoramento para ajudá-lo a identificar vulnerabilidades potenciais e atenuar as ameaças.

## Configurando políticas de segurança

As políticas de segurança são configuradas para cada assinatura. Para modificar uma política de segurança, você deve ser um proprietário ou colaborador daquela assinatura. Siga as etapas abaixo para configurar as políticas de segurança na Central de segurança do Azure:

1. Clique no bloco **Política de segurança** no painel Central de segurança do Azure.

2. Na folha **Política de segurança - definir política por assinatura** que aparece no lado direito, selecione a assinatura que você deseja para habilitar a política de segurança.

    ![Habilitar coleta de dados](./media/security-center-policies/security-center-policies-fig0.png)

3. A folha **Política de segurança** para essa assinatura será aberta com um conjunto de opções semelhantes para o mostrado abaixo:

    ![Habilitar coleta de dados](./media/security-center-policies/security-center-policies-fig1.png)

4. Certifique-se de que a opções **Coletar dados de máquinas virtuais** esteja **Ativa**. Essa opção habilita a coleta de log automático para recursos novos e existentes.

    >[AZURE.NOTE] É altamente recomendável que você ative a coleta de dados para cada uma das suas assinaturas porque isso garantirá que o monitoramento de segurança esteja disponível para todas as máquinas virtuais novas e existentes. Habilitar a coleta de dados instala o agente de monitoramento. Se não desejar ativar a coleta de dados agora desse local, você pode fazer isso posteriormente dos modos de exibição integridade e recomendações. Você também pode habilitar a coleta de dados somente para a assinatura ou para VMs selecionadas. Consulte as [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) para saber mais sobre as VMs com suporte.

5. Se sua conta de armazenamento ainda não estiver configurada, você poderá ver um aviso semelhante mostrado na figura a seguir quando você abre a **Política de segurança**:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig2.png)

6. Se você vir esse aviso, clique nessa opção e selecione a região, como mostrado na figura a seguir:

    ![Seleção de armazenamento](./media/security-center-policies/security-center-policies-fig3.png)

7. Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Após decidir a região que você pretende usar, selecione a região e, em seguida, selecione a conta de armazenamento.

8. Na folha **Escolher contas de armazenamento**, clique em **OK**.

    > [AZURE.NOTE] Se preferir, você pode agregar dados de máquinas virtuais em várias regiões em uma conta de armazenamento central. Consulte as [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) para obter mais informações.

9. Na folha **Política de segurança**, clique em **Ativar** para habilitar as recomendações de segurança que você deseja usar nesta assinatura. Use a tabela a seguir como uma referência para entender o que cada opção fará:

| Política | Quando o estado for Ativado |
|----- |-----|
| Atualizações do Sistema | Recupera uma lista de atualizações disponíveis do Windows Update ou WSUS, dependendo de qual serviço está configurado para a máquina virtual, a cada 12 horas, e recomenda que as atualizações críticas e de segurança ausentes sejam instaladas nas máquinas virtuais do Windows. |
| Regras de linha de base | Analisa todas as máquinas virtuais com suporte a cada 12 horas para identificar quaisquer configurações de sistema operacional que podem tornar a máquina virtual mais vulnerável a ataques e recomenda as alterações de configuração para tratar essas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas que estão sendo monitoradas. |
| Antimalware | Recomenda que o antimalware seja provisionado para todas as máquinas virtuais do Windows para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados. |
| Lista de controle de acesso nos pontos de extremidade | Recomenda que uma ACL [(Lista de controles de acesso)](virtual-machines-set-up-endpoints.md) seja configurada para limitar o acesso a pontos de extremidade de máquina virtual Clássica. Isso normalmente seria usado para garantir que somente os usuários que estão conectados à rede corporativa possam acessar as máquinas virtuais. |
| Grupos de segurança de rede | Recomenda que NSGs [(Grupos de segurança de rede)](virtual-networks-nsg.md) sejam configurados para controlar o tráfego de entrada e saída para sub-redes e interfaces de rede para máquinas virtuais de Gerenciador de Recursos. Os NSGs configurados para uma sub-rede serão herdados por todas as interfaces de rede de máquina virtual, a menos que especificado o contrário. Além de verificar se um NSG foi configurado, essa opção avalia as regras de segurança de entrada para identificar se há regras que permitem o tráfego de entrada. |
| Firewall do Aplicativo Web | Recomenda que um Firewall do aplicativo Web seja fornecido em máquinas virtuais quando: ILPIP [(IP público de nível de instância)](virtual-networks-instance-level-public-ip.md) é usado e as regras de segurança de entrada de NSG associado estão configuradas para permitir o acesso à porta 80/443. O IP de balanceamento de carga (VIP) é usado e o balanceamento de carga associado e as regras NAT de entrada estão configuradas para permitir acesso à porta 80/443 (para obter mais informações, consulte [Suporte do Gerenciador de Recursos do Azure para Balanceador de Carga](load-balancer-arm.md)) |
| Auditoria SQL | Recomenda que a auditoria de acesso a Servidores SQL do Azure e bancos de dados esteja habilitada para conformidade, detecção avançada e fins de investigação. |
| Transparent Data Encryption do SQL | Recomenda que a criptografia em repouso seja habilitada para arquivos de log de bancos de dados SQL do Azure, backups associados e transação para que mesmo se os seus dados forem violados, eles não possam ser lidos. |

Ao terminar de configurar todas as opções, clique em **Salvar** para confirmar as alterações.

## Próximas etapas

Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0224_2016-->
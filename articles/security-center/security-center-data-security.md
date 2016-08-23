<properties
   pageTitle="Segurança de Dados da Central de Segurança do Azure | Microsoft Azure"
   description="Este documento explica como os dados são gerenciados e protegidos na Central de Segurança do Azure."
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
   ms.date="08/08/2016"
   ms.author="yurid"/>

# Segurança dos Dados da Central de Segurança do Azure
Para ajudar os clientes a evitar, detectar e responder a ameaças, a Central de Segurança do Azure coleta e processa dados sobre os recursos do Azure, incluindo informações da configuração, metadados, logs de eventos, arquivos de despejo corrompidos e mais. Estamos comprometidos com a proteção da privacidade e da segurança dos dados. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Este artigo explica como os dados são gerenciados e protegidos na Central de Segurança do Azure.

## Fontes de dados
A Central de Segurança do Azure analisa os dados das seguintes fontes:

- Serviços do Azure: Lê as informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.
- Tráfego da Rede: Lê os metadados do tráfego da rede de exemplo a partir da infraestrutura da Microsoft, como origem/IP de destino/porta, tamanho do pacote e protocolo da rede.
- Soluções de Parceiros: Coleta alertas de segurança das soluções de parceiros integradas, como firewalls e soluções antimalware. Esses dados são colocados no armazenamento da Central de Segurança do Azure, atualmente localizado nos Estados Unidos.
- Suas Máquinas Virtuais: A Central de Segurança do Azure pode coletar informações da configuração e informações sobre os eventos de segurança, como eventos do Windows e logs de auditoria, logs do IIS, mensagens do syslog e arquivos de despejo corrompidos a partir de suas máquinas virtuais usando agentes de coleta de dados. Consulte a seção "Gerenciamento da Coleta de Dados" abaixo para obter detalhes adicionais.

Além disso, as informações sobre os alertas de segurança, recomendações e status de integridade da segurança são colocadas no armazenamento da Central de Segurança do Azure, atualmente localizado nos Estados Unidos. Essas informações podem incluir informações de configuração relacionadas e eventos de segurança coletados de suas máquinas virtuais conforme o necessário para fornecer um alerta de segurança, recomendação ou status de integridade da segurança.

## Proteção de dados
**Segregação dos dados**: os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço. Além disso, os dados coletados das máquinas virtuais são colocados em sua(s) conta(s) de armazenamento.

**Acesso a dados**: para fornecer recomendações de segurança e investigar as possíveis ameaças de segurança, os funcionários da Microsoft podem acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo os arquivos de despejo de falha. Os arquivos de despejo corrompidos e os eventos de criação do processo podem incluir, sem querer, Dados do Cliente ou dados pessoais de suas máquinas virtuais. Seguimos os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Política de Privacidade](https://www.microsoft.com/privacystatement/pt-BR/OnlineServices/Default.aspx), que determinam que a Microsoft não usará os Dados do Cliente nem obterá as informações para fins comerciais ou de propaganda semelhantes. Somente usamos os Dados do Cliente conforme o necessário para fornecer os serviços do Azure, inclusive para fins compatíveis com o fornecimento desses serviços. Você mantém todos os direitos dos Dados do Cliente.

**Uso dos dados**: A Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://www.microsoft.com/privacystatement/pt-BR/OnlineServices/Default.aspx).

**Localização dos dados**: Uma conta de armazenamento é especificada para cada região onde as máquinas virtuais estão em execução. Isso permite que você armazene os dados na mesma região da máquina virtual na qual os dados são coletados. Esses dados, incluindo os arquivos de despejo corrompidos, serão armazenados de modo permanente em sua conta de armazenamento. O serviço também coloca informações sobre os alertas de segurança, incluindo alertas das soluções de parceiros integradas, recomendações e status de integridade da segurança, no armazenamento da Central de Segurança do Azure, atualmente localizado nos Estados Unidos.

## Gerenciamento da Coleta de Dados das Máquinas Virtuais

Quando você escolhe habilitar a Central de Segurança do Azure, a coleta de dados é ativada para cada uma de suas assinaturas. Você pode desativar a coleta de dados na seção "Política de Segurança" do painel da Central de Segurança do Azure. Quando a Coleta de dados é ativada, a Central de Segurança do Azure provisiona o Agente de Monitoramento do Azure em todas as máquinas virtuais existentes com suporte e as novas criadas. A extensão do Monitoramento de Segurança do Azure examina várias configurações e eventos relacionados à segurança nos rastreamentos [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(Rastreamento de Eventos para Windows). Além disso, o sistema operacional irá gerar eventos do log de eventos no decorrer da execução da máquina. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise.

Uma conta de armazenamento é especificada para cada região na qual você tem máquinas virtuais em execução, onde os dados coletados das máquinas virtuais nessa mesma região são armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Você pode configurar contas de armazenamento para cada região na seção "Política de Segurança" do painel da Central de Segurança do Azure.

O Agente de Monitoramento do Azure também copia os arquivos de despejo corrompidos para sua conta de armazenamento. A Central de Segurança do Azure coleta as cópias transitórias dos seus arquivos de despejo corrompidos e analisa-as para obter evidências das tentativas de exploração e comprometimentos bem-sucedidos. A Central de Segurança do Azure executa essa análise dentro da mesma região geográfica da conta de armazenamento e exclui as cópias transitórias quando a análise termina.

Você pode desabilitar a coleta de dados das máquinas virtuais a qualquer momento, o que removerá os Agentes de Monitoramento instalados anteriormente pela Central de Segurança do Azure.


## Próximas etapas

Neste documento, você aprendeu como os dados são gerenciados e protegidos na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, consulte:

- [Guia de Operações e Planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0817_2016-->
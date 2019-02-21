---
title: Definições de diretiva do Azure monitoradas na Central de Segurança do Azure | Microsoft Docs
description: Definições de política do Azure monitoradas na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: e83ff9f3bf02a134919d497662744028e1b9acda
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343010"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Políticas de Central de Segurança do Azure monitoradas pelo Azure Security Center
Este artigo fornece uma lista de definições de políticas do Azure Policy que podem ser monitoradas na Central de Segurança. Para obter mais informações sobre políticas de segurança, confira [Trabalhando com políticas de segurança](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Políticas de segurança disponíveis

Para reconhecer políticas internas monitoradas pela Central de Segurança do Azure, consulte a tabela a seguir:

| Política | O que faz a política |
| --- | --- |
|Auditoria de habilitação de logs de diagnósticos no Service Fabric e nos Conjuntos de Dimensionamento de Máquinas Virtuais|Recomenda-se habilitar os Logs para que a trilha de atividades possa ser recriada quando investigações forem necessárias no caso de um incidente ou um comprometimento.|
|Auditoria de regras de autorização em namespaces do Hub de Eventos|Os clientes do Hub de Eventos não devem usar uma política de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica.|
|Auditar a existência de regras de autorização em entidades do Hub de Eventos|Auditar a existência de regras de autorização em entidades do Hub de Eventos para conceder acesso com privilégios mínimos.|
|Auditar o acesso irrestrito à rede para contas de armazenamento|Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Em vez disso, configure as regras de rede de forma que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da internet, o acesso pode ser concedido para o tráfego de redes virtuais do Azure específicas ou para intervalos de endereços IP públicos da internet.|
|Auditar o uso de regras personalizadas do RBAC|Auditar funções internas, como 'Proprietário, Colaborador, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças.|
|Auditar a ativação de logs de diagnóstico no Azure Stream Analytics|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.|
|Auditar transferência segura para contas de armazenamento|Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|Provisionamento de auditoria de um administrador do Azure Active Directory Domain Services para o SQL Server|Audite o provisionamento de um administrador do Active Directory Domain Services do Azure para o seu servidor SQL para habilitar a autenticação do AD do Azure. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.|
|Regras de autorização de auditoria em namespaces de Barramento de Serviço do Microsoft Azure|Os clientes do Service Bus não devem usar uma diretiva de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica.|
|Auditoria de ativação de logs de diagnóstico no Barramento de Serviço|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.|
|Auditar a configuração da propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção (Nenhum, Sinal e EncryptAndSign) para comunicação de nó a nó usando um certificado de cluster principal. Defina o nível de proteção para garantir que todas as mensagens de nó a nó sejam criptografadas e assinadas digitalmente.| 
|Auditar o uso do Azure Active Directory para autenticação de cliente no Service Fabric|Auditar o uso da autenticação do cliente somente por meio do Active Directory do Azure no Service Fabric| 
|Auditoria de habilitação de logs de diagnóstico para o serviço de pesquisa|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditoria de habilitação de somente conexões seguras ao Cache Redis do Azure|Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão| 
|Auditoria de Habilitação de logs de diagnóstico em Aplicativo Lógico do Azure|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditoria de ativação de logs de diagnóstico no Key Vault|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.|
|Auditoria de habilitação de logs de diagnóstico no Hub de Eventos|Auditoria habilitando de logs e retê-los até um ano. Isso permite a recriação de trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditoria de ativação de logs de diagnóstico no Azure Data Lake Store|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditar a habilitação dos logs de diagnóstico no Data Lake Analytics|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditar o uso de contas de armazenamento clássicas|Use o Azure Resource Manager para suas contas de armazenamento para fornecer aprimoramentos de segurança, como: RBAC (controle de acesso mais forte), melhor auditoria, implantação e controle com base no Azure Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves por segredos, autenticação baseada no Microsoft Azure Active Directory e suporte para tags e grupos de recursos para facilitar o gerenciamento de segurança.| 
|Auditar o uso de máquinas virtuais clássicas|Use o Azure Resource Manager para suas máquinas virtuais para fornecer aprimoramentos de segurança, como: controle de acesso mais forte (RBAC), melhor auditoria, implantação e controle com base no Azure Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves por segredos, autenticação baseada no Microsoft Azure Active Directory e suporte para tags e grupos de recursos para facilitar o gerenciamento de segurança.| 
|Auditar a configuração de regras de alerta de métrica em contas do lote|Auditar a configuração das regras de alerta de métrica na conta do Lote para ativar a métrica necessária.| 
|Auditar a configuração de regras de alerta de métrica em contas do lote|Auditar a configuração das regras de alerta de métrica na conta do Lote para ativar a métrica necessária.| 
|Auditar a ativação de logs de diagnóstico em contas em lote|Auditoria habilitando de logs e retê-los até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida.| 
|Auditar a habilitação de criptografia de variáveis de conta de automação do Azure|É importante habilitar a criptografia dos ativos variáveis da conta de Automação do Azure ao armazenar dados confidenciais.| 
|Auditoria de habilitação de logs de diagnóstico no Serviço de Aplicativo|Auditoria de ativação de logs de diagnóstico no aplicativo. Isso permite recriar trilhas de atividades para fins de investigação se ocorrer um incidente de segurança ou se sua rede estiver comprometida.| 
|Auditar o status da Transparent Data Encryption|Auditoria de status de criptografia de dados transparente para bancos de dados SQL.| 
|Auditar configurações de Auditoria de nível do servidor SQL|Audita a existência da Auditoria SQL no nível do servidor.| 
|[Versão prévia]: monitora o banco de dados SQL não criptografado na Central de Segurança do Azure|Servidores ou bancos de dados SQL não criptografados serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora o banco de dados SQL não auditado na Central de Segurança do Azure|Os servidores e bancos de dados SQL que não tiverem a auditoria SQL ativada serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora atualizações de sistemas ausentes na Central de Segurança do Azure|A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: audita a criptografia de blob ausente para contas de armazenamento|Esta política audita as contas de armazenamento sem criptografia blob. Aplica-se apenas aos tipos de recursos Microsoft.Storage e não aos outros provedores de armazenamento. Possível rede O acesso Just In Time será monitorado pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora o possível acesso Just In Time (JIT) da rede na Central de Segurança do Azure|Rede possível O acesso Just In Time (JIT) será monitorado pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora a possível lista de permissões de aplicativo na Central de Segurança do Azure|A configuração possível da Whitelist de aplicativo será monitorada pela Central de Segurança do Azure.| 
|[Versão prévia]: monitora o acesso permissivo à rede na Central de Segurança do Azure|Grupos de segurança de rede com regras muito permissivas serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora a vulnerabilidades do sistema operacional na Central de Segurança do Azure|Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora o Endpoint Protection ausente na Central de Segurança do Azure|Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora discos não criptografados da VM na Central de Segurança do Azure|As VMs sem uma criptografia de disco ativada serão monitoradas pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora vulnerabilidades de máquinas virtuais na Central de Segurança do Azure|Monitora as vulnerabilidades detectadas pela solução de Avaliação de Vulnerabilidade e VMs sem uma solução de Avaliação de Vulnerabilidade na Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora o aplicativo Web desprotegido na Central de Segurança do Azure|Os aplicativos da Web sem uma proteção do Firewall de Aplicativos da Web serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: monitora pontos de extremidade de rede desprotegidos na Central de Segurança do Azure|Endpoints de rede sem a proteção de um firewall de próxima geração serão monitorados pela Central de Segurança do Azure como recomendações.| 
|[Versão prévia]: Monitora os resultados da avaliação de vulnerabilidade do SQL na Central de Segurança do Azure|Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados.| 
|[Versão prévia]: audita o número máximo de proprietários de uma assinatura|Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido.| 
|[Versão prévia]: audita o número mínimo de proprietários da assinatura|Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador.| 
|[Versão prévia]: audita contas com permissões de proprietário que não são habilitadas para MFA em uma assinatura|A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos.| 
|[Versão prévia]: audita contas com permissões de gravação que não são habilitadas para MFA em uma assinatura|A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos.| 
|[Versão prévia]: audita contas com permissões de leitura que não são habilitadas para MFA em uma assinatura|A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos.| 
|[Versão prévia]: audita contas descontinuadas com permissões de proprietário em uma subscrição|Contas descontinuadas com permissões de proprietário devem ser removidas da sua assinatura. Contas descontinuadas são contas que foram impedidas de fazer login.| 
|[Versão prévia]: audita contas descontinuadas em uma subscrição|Contas descontinuadas devem ser removidas de suas assinaturas. Contas descontinuadas são contas que foram impedidas de fazer login.| 
|[Versão prévia]: audita contas externas com permissões de proprietário em uma assinatura|Contas externas com permissões de proprietário devem ser removidas da sua assinatura para evitar o acesso não monitorado.| 
|[Versão prévia]: audita contas externas com permissões de gravação em uma assinatura|Contas externas com privilégios de gravação devem ser removidas da sua assinatura para evitar o acesso não monitorado.| 
|[Versão prévia]: audita contas externas com permissões de leitura em uma assinatura|Contas externas com privilégios de leitura devem ser removidas da sua assinatura para evitar o acesso não monitorado.| 




## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Obtenha respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.

Para saber mais sobre o Azure Policy, confira [O que é o Azure Policy?](../governance/policy/overview.md)

<properties
   pageTitle="Integração de alertas da Central de Segurança do Azure com a integração de log do Azure (Visualização) | Microsoft Azure"
   description="Este artigo ajuda você a integrar alertas da Central de Segurança com a integração de log do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# Integração de alertas da Central de Segurança com a integração de log do Azure (Visualização)

Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM (Gerenciamento de eventos e informações de segurança) como ponto de partida para separação e investigação de alertas de segurança. Com a integração de log do Azure, os clientes podem sincronizar os alertas da Central de Segurança do Azure, juntamente com os eventos de segurança da máquina virtual coletados pelo Diagnóstico do Azure e Logs de Auditoria do Azure, com sua análise de log ou solução SIEM quase em tempo real.

A integração do log do Azure funciona com a HP ArcSight, Splunk, IBM Qradar e outros.

## Quais logs posso integrar?

Azure produz um log abrangente para cada serviço. Esses logs são categorizados como:

- **Logs de controle/ gerenciamento** que ofereçam visibilidade nas operações CREATE, UPDATE e DELETE do Azure Resource Manager.
- **Logs do Plano de Dados** que oferecem visibilidade nos eventos gerados ao usar um recurso do Azure. Um exemplo é o Log de Eventos do Windows - os logs de segurança e do aplicativo em uma máquina virtual.

A integração do log do Azure atualmente suporta a integração de:

- Logs da VM do Azure
- Logs de Auditoria do Azure
- Alertas da Central de Segurança do Azure

## Instalar a integração do log do Azure

Baixe a [integração do log do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração do log do Azure coleta os dados de telemetria do computador no qual está instalado. Os dados de telemetria coletados são:

- Exceções que ocorrem durante a execução da integração do log do Azure
- Métricas sobre o número de consultas e eventos processados
- Estatísticas sobre quais opções da linha de comando do Azlog.exe estão sendo usadas

> [AZURE.NOTE] Você pode desativar a coleta dos dados de telemetria desmarcando essa opção.

## Integrar os Logs de Auditoria do Azure e os alertas da Central de Segurança

1. Abra o prompt de comando e **cd** em **c:\\Arquivos de Programa\\Microsoft Azure Log Integration**.

2. Execute o comando **azlog createazureid** para criar uma [Entidade de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) nos locatários do Azure Active Directory (AD) que hospedam as assinaturas do Azure.

    Você será solicitado a fazer o logon do Azure.

    > [AZURE.NOTE] Você deve ser o Proprietário da assinatura ou um Coadministrador da assinatura.

    A autenticação do Azure é feita por meio do AD do Azure. Criar uma entidade de serviço para a integração do log do Azure criará a identidade do AD do Azure que terá o acesso de leitura a partir das assinaturas do Azure.

3. Execute o comando **azlog authorize <IDAssinatura>** para atribuir acesso de Leitor na assinatura para a entidade de serviço criada na etapa 2. Se você não especificar uma **IDAssinatura**, a entidade de serviço será atribuída à função de Leitor para todas as assinaturas às quais você tem acesso.

    > [AZURE.NOTE] Você poderá ver avisos se executar o comando **authorize** imediatamente após o comando **createazureid** porque há uma latência entre quando a conta do AD do Azure é criada e quando a conta está disponível para o uso. Se você esperar cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **authorize**, então não verá esses avisos.

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de auditoria existem:

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. Verifique as seguintes pastas para confirmar se os alertas da Central de Segurança existem:

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. Aponte o conector de encaminhamento de arquivos SIEM padrão para a devida pasta para enviar os dados para a instância SIEM. Consulte [Configurações SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) em sua configuração do SIEM.

Se você tiver dúvidas sobre a integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Próximas etapas

Para saber mais sobre os Logs de Auditoria do Azure e as definições das propriedades, consulte:

- [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)
- [Lista os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx): para recuperar os eventos do log de auditoria.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
- [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!---HONumber=AcomDC_0921_2016-->
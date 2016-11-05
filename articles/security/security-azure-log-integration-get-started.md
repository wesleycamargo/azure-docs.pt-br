---
title: Introdução à integração de log do Azure | Microsoft Docs
description: Saiba como instalar o serviço de integração do log do Azure e como integrar logs do armazenamento do Azure, dos Logs de Auditoria do Azure e dos alertas da Central de Segurança do Azure.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear

ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2016
ms.author: TomSh

---
# Introdução à integração de log do Azure (visualização)
A integração do log do Azure permite que você integre registros brutos de recursos do Azure em seus sistemas de SIEM (Segurança da Informação e Gerenciamento de Evento) locais. Essa integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança associados aos aplicativos.

Este tutorial orienta você durante a instalação da integração de log do Azure e na integração dos Logs de Armazenamento do Azure, dos Logs de Auditoria do Azure e dos alertas da Central de Segurança do Azure. O tempo estimado para concluir este tutorial é de uma hora.

## Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Um computador (local ou na nuvem) para instalar o serviço de integração de log do Azure. Esse computador deve executar um sistema operacional Windows de 64 bits com o .NET 4.5.1 instalado. Esse computador é chamado de **Integrador do Azlog**.
* Assinatura do Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).
* Diagnóstico do Azure habilitado para as máquinas virtuais (VMs) do Azure. Para habilitar o diagnóstico para Serviços de Nuvem, veja [Habilitando o Diagnóstico do Azure nos Serviços de Nuvem do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para habilitar o diagnóstico para uma VM do Azure que esteja executando o Windows, veja [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
* A conectividade do Integrador do Azlog ao armazenamento do Azure para autenticar e autorizar a assinatura do Azure.
* Para logs de VM do Azure, o agente SIEM (por exemplo, Splunk Universal Forwarder, HP ArcSight Windows Event Collector agent ou IBM QRadar WinCollect) deve estar instalado no Integrador do Azlog.

## Considerações de implantação
Você poderá executar várias instâncias do Integrador do Azlog se o volume de eventos for alto. O balanceamento de carga de contas de armazenamento do Diagnóstico do Azure para Windows *(WAD)* e o número de assinaturas a serem fornecidas às instâncias devem se basear em sua capacidade.

Em uma máquina com oito processadores (núcleos), uma única instância do Integrador do Azlog pode processar cerca de 24 milhões de eventos por dia (~1M/hora).

Em um computador com quatro processadores (núcleos), uma única instância do integrador Azlog pode processar cerca de 1,5 milhão de eventos por dia (~62,5K/hora).

## Instalar a integração do log do Azure
Baixe a [integração do log do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração do log do Azure coleta os dados de telemetria do computador no qual está instalado. Os dados de telemetria coletados são:

* Exceções que ocorrem durante a execução da integração do log do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre quais opções da linha de comando do Azlog.exe estão sendo usadas

> [!NOTE]
> Você pode desativar a coleta dos dados de telemetria desmarcando essa opção.
> 
> 

## Integrar os logs de VM do Azure de suas contas de armazenamento do Diagnóstico do Azure
1. Verifique os pré-requisitos listados acima para garantir que sua conta de armazenamento do WAD esteja coletando logs antes de continuar a integração de log do Azure. Não execute as etapas a seguir se sua conta de armazenamento do WAD não estiver coletando logs.
2. Abra o prompt de comando e **cd** em **c:\\Arquivos de Programa\\Microsoft Azure Log Integration**.
3. Executar o comando
   
        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>
   
      Substitua StorageAccountName pelo nome da conta de armazenamento do Azure configurada para receber eventos de diagnóstico da VM.
   
        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==
   
      Se você quiser que a id da assinatura apareça no XML do evento, acrescente a ID da assinatura ao nome amigável:
   
        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>
4. Aguarde de 30 a 60 minutos (pode levar até uma hora) e exiba os eventos extraídos da conta de armazenamento. Para exibir, abra **Visualizador de Eventos > Logs do Windows > Eventos Encaminhados** no Integrador do Azlog.
5. Verifique se o conector SIEM padrão instalado no computador está configurado para selecionar eventos da pasta **Eventos Encaminhados** e canalize-os para sua instância SIEM. Examine a configuração específica de SIEM para configurar e ver os logs de integração.

## E se os dados não estiverem sendo mostrados na pasta Eventos Encaminhados?
Se depois de uma hora os dados não estiverem sendo mostrados na pasta **Eventos Encaminhados**:

1. Verifique o computador e confirme se ele pode acessar o Azure. Para testar a conectividade, tente abrir o [portal do Azure](http://portal.azure.com) do navegador.
2. Verifique se a conta de usuário **azlog** tem permissão de gravação na pasta **users\\azlog**.
3. Verifique se a conta de armazenamento adicionada no comando **azlog source add** é listada quando você executa o comando **azlog source list**.
4. Vá para **Visualizador de Eventos > Logs do Windows > Aplicativo** para ver se há erros relatados da integração de log do Azure.

Se você ainda não vir os eventos, então:

1. Baixe o [Microsoft Azure Storage Explorer](http://storageexplorer.com/).
2. Conecte-se à conta de armazenamento adicionada no comando **azlog source add**.
3. No Microsoft Azure Storage Explorer, navegue até a tabela **WADWindowsEventLogsTable** para ver se há algum dado. Caso contrário, então o diagnóstico na VM não está configurado corretamente.

## Integrar os logs de auditoria do Azure e alertas da Central de Segurança
1. Abra o prompt de comando e **cd** em **c:\\Arquivos de Programa\\Microsoft Azure Log Integration**.
2. Executar o comando
   
        azlog createazureid
   
      Esse comando solicitará o logon do Azure. O comando cria uma [Entidade de Serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um Administrador, um Coadministrador ou um Proprietário. O comando falhará se o usuário conectado for apenas um usuário Convidado no Locatário do Azure AD. A autenticação do Azure é feita por meio do Azure Active Directory (AD). Criar uma entidade de serviço para a Integração do Azlog criará a identidade do Azure AD que terá o acesso de leitura a partir das assinaturas do Azure.
3. Executar o comando
   
        azlog authorize <SubscriptionID>
   
      Isso atribui acesso de leitor na assinatura para a entidade de serviço criado na etapa 2. Se você não especificar uma SubscriptionID, ela tentará atribuir a função de leitor de entidade de serviço a todas as assinaturas às quais você tem acesso.
   
        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328
   
   > [!NOTE]
   > Você poderá ver avisos se executar o comando **authorize** imediatamente após o comando **createazureid**. Há alguma latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você esperar cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **authorize**, então não verá esses avisos.
   > 
   > 
4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de auditoria existem:
   
   * **c:\\Users\\azlog\\AzureResourceManagerJson**
   * **c:\\Users\\azlog\\AzureResourceManagerJsonLD**
5. Verifique as seguintes pastas para confirmar se os alertas da Central de Segurança existem:
   
   * **c:\\Users\\azlog\\ AzureSecurityCenterJson**
   * **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**
6. Aponte o conector de encaminhamento de arquivos SIEM padrão para a devida pasta para enviar os dados para a instância SIEM. Talvez sejam necessários alguns mapeamentos de campo com base no produto SIEM que você está usando.

Se você tiver dúvidas sobre a integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Próximas etapas
Neste tutorial, você aprendeu a instalar a integração de log do Azure e a integrar logs do armazenamento do Azure. Para saber mais, consulte os seguintes:

* [Integração de log do Microsoft Azure para os logs do Azure (visualização)](https://www.microsoft.com/download/details.aspx?id=53324) – visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à integração de log do Azure](security-azure-log-integration-overview.md) – este documento apresenta a integração do registro do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com o Log Analytics ou com a solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de Auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.

<!---HONumber=AcomDC_0921_2016-->
---
title: Perguntas frequentes sobre a integração de log do Azure | Microsoft Docs
description: Este artigo de perguntas frequentes responde às perguntas sobre a integração de log do Azure.
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
ms.date: 08/23/2016
ms.author: TomSh

---
# Perguntas frequentes sobre a integração do log do Azure
Este artigo de perguntas frequentes responde às perguntas sobre a integração do log do Azure, um serviço que permite integrar logs brutos de recursos do Azure a seus sistemas locais de SIEM (Gerenciamento de Eventos e Informações de Segurança). Essa integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança associados aos aplicativos.

## Como posso ver as contas de armazenamento das quais a integração de log do Azure está extraindo os logs da VM do Azure?
Execute o comando **azlog source list**.

## Como atualizo a configuração de proxy?
Se a configuração de proxy não permitir acesso ao armazenamento do Azure diretamente, abra o arquivo **AZLOG.EXE.CONFIG** em **c:\\Arquivos de Programas\\Integração de Log do Microsoft Azure**. Atualize o arquivo para incluir a seção **defaultProxy** com o endereço do proxy da sua organização. Depois que a atualização for concluída, pare e inicie o serviço usando os comandos **net stop azlog** e **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## Como posso ver as informações de assinatura nos eventos do Windows?
Acrescente **subscriptionid** ao nome amigável ao adicionar a origem.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

O evento XML tem os metadados, como mostrado abaixo, incluindo a id da assinatura.

![Evento XML][1]

## Mensagens de erro
### Ao executar o comando **azlog createazureid**, por que obtenho o erro a seguir?
Erro:

  *Falha ao criar aplicativo AAD - Locatário 72f988bf-86f1-41af-91ab-2d7cd011db37 - Motivo = 'Proibido' - Mensagem = 'Privilégios insuficientes para concluir a operação.'*

**Azlog createazureid** tenta criar uma entidade de serviço em todos os locatários do Azure AD para as assinaturas nas quais o logon do Azure tem acesso. Se o logon do Azure for apenas um usuário Convidado nesse locatário do Azure AD, o comando falhará com 'Privilégios insuficientes para concluir a operação'. Solicite ao administrador do Locatário para adicionar sua conta como um usuário no locatário.

### Ao executar o comando **azlog authorize**, por que obtenho o erro a seguir?
Erro:

  *Aviso ao criar Atribuição de Função - AuthorizationFailed: O cliente janedo@microsoft.com' com a id de objeto 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' além do escopo '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

O comando **Azlog authorize** tenta criar a função de Leitor para a entidade de serviço do Azure AD (criada com **Azlog createazureid**) para as assinaturas fornecidas. Se o logon do Azure não for um coadministrador nem um proprietário da assinatura, ele falhará com a mensagem de erro 'Falha na Autorização'. O RBAC (Controle de Acesso Baseado em Função) do coadministrador ou proprietário é necessário para concluir essa ação.

## Onde posso encontrar a definição das propriedades no log de auditoria?
Consulte:

* [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)
* [Listar os eventos de gerenciamento em uma assinatura na API REST do Azure Insights](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## Onde posso encontrar detalhes sobre alertas da Central de Segurança do Azure?
Confira [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## Como posso modificar o que é coletado com o diagnóstico da VM?
Confira [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) para obter detalhes sobre como obter, modificar e definir o Diagnóstico do Azure na configuração *(WAD)* do Windows. Veja o exemplo a seguir:

### Obter a configuração WAD
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### Modificar a configuração WAD
O exemplo a seguir é uma configuração onde apenas a EventID 4624 e a EventID 4625 são coletadas do log de eventos de segurança. Os eventos do Microsoft Antimalware são coletados no log de eventos do sistema. Confira [Consumindo eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85) para obter detalhes sobre o uso das expressões XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### Definir a configuração WAD
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de fazer alterações, verifique a conta de armazenamento para garantir que os eventos corretos sejam coletados.

Se você tiver dúvidas sobre a integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->
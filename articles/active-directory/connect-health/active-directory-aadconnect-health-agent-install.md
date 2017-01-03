---
title: "Instalação do Agente do Azure AD Connect Health | Microsoft Docs"
description: "Esta é a página do Azure AD Connect Health que descreve a instalação do agente para o AD FS e Sincronização."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 99cd66d0fc4fc96c0b49e1ce3e3e2095fbe62395


---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do Agente do Azure AD Connect Health
Este documento explica como instalar e configurar os Agentes do Azure AD Connect Health. Você pode baixar os agentes [daqui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisitos
A tabela a seguir é uma lista de requisitos para o uso do Azure AD Connect Health.

| Requisito | Descrição |
| --- | --- |
| AD Premium do Azure |O Azure AD Connect Health é um recurso do Azure AD Premium e requer o Azure AD Premium. </br></br>Para saber mais, consulte [Introdução ao AD Premium do Azure](../active-directory-get-started-premium.md) </br>Para iniciar uma avaliação gratuita de 30 dias, confira [Iniciar uma avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Você deve ser um administrador global do Azure AD para começar a usar o Azure AD Connect Health |Por padrão, somente os administradores globais podem instalar e configurar os agentes de integridade para começar, acessar o portal e realizar operações no Azure AD Connect Health. Para saber mais, consulte [Administrar seu diretório do Azure AD](../active-directory-administer.md). <br><br>  Usando o Controle de Acesso com Base em Funções, você pode permitir acesso ao Azure AD Connect Health para outros usuários em sua organização. Para saber mais, confira [Controle de Acesso com Base em Função para o Azure AD Connect Health.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importante:** a conta usada ao instalar os agentes deve ser uma conta corporativa ou de estudante. Não pode ser uma conta da Microsoft. Para saber mais, confira [Inscrever-se no Azure como uma organização](../sign-up-organization.md) |
| O agente do Azure AD Connect Health é instalado em cada servidor de destino |O Azure AD Connect Health requer a instalação de um agente nos servidores de destino para fornecer os dados que serão exibidos no portal. </br></br>Por exemplo, para obter dados da sua infraestrutura do AD FS local, o agente deve ser instalado nos servidores do AD FS, do Proxy do AD FS e do Proxy de Aplicativo Web. Da mesma forma, para obter dados da infraestrutura do AD DS local, o agente deve ser instalado nos controladores de domínio. </br></br>**Importante:** a conta usada ao instalar os agentes deve ser uma conta corporativa ou de estudante. Não pode ser uma conta da Microsoft. Para saber mais, confira [Inscrever-se no Azure como uma organização](../sign-up-organization.md) |
| Conectividade de saída para os pontos de extremidade de serviço do Azure |Durante a instalação e o tempo de execução, o agente requer conectividade com os pontos de extremidade de serviço do Azure AD Connect Health. Se a conectividade de saída estiver bloqueada, verifique se os pontos de extremidade a seguir foram adicionados à lista de permissões: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
| Portas de firewall no servidor que executa o agente. |O agente requer que as seguintes portas de firewall estejam abertas para que o agente se comunique com os pontos de extremidade de serviço do Azure AD Health.</br></br><li>Porta TCP/UDP 443</li><li>Porta TCP/UDP 5671</li> |
| Permita os sites a seguir se a segurança reforçada do IE estiver habilitada |Se a Segurança Aprimorada do IE estiver habilitada, os sites a seguir precisarão receber permissão no servidor no qual o agente será instalado.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>O servidor de federação da sua organização confiável pelo Azure Active Directory. Por exemplo: https://sts.contoso.com</li> |

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalando o Agente do Azure AD Connect Health para AD FS
Para iniciar a instalação do agente, dê um clique duplo no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Um prompt de comando será iniciado, seguido pelo PowerShell, que executará Register-AzureADConnectHealthADFSAgent. Quando solicitado a entrar no Azure, continue e entre.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Depois de entrar, o PowerShell continuará. Depois de concluir, feche o PowerShell e a configuração estará concluída.

Neste ponto, os serviços devem ser iniciados automaticamente, permitindo que o agente monitore e colete dados. Se você não tiver atendido a todos os pré-requisitos descritos nas seções anteriores, serão exibidos avisos na janela do PowerShell. Não se esqueça de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar se o agente foi instalado, procure os serviços a seguir no servidor. Se você tiver concluído a configuração, eles já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

* Serviço de diagnóstico do AD FS do Azure AD Connect Health
* Serviço do Insights do AD FS do Azure AD Connect Health
* Serviço de Monitoramento do AD FS do Azure AD Connect Health

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalação do agente em servidores do Windows Server 2008 R2
Etapas para servidores Windows Server 2008 R2:

1. Certifique-se de que o servidor esteja em execução no Service Pack 1 ou superior.
2. Desative a ESC do IE para instalação do agente:
3. Instale o Windows PowerShell 4.0 em cada um dos servidores antes de instalar o agente de integridade do AD. Para instalar o Windows PowerShell 4.0:
   * Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando o link a seguir para baixar o instalador offline.
   * Instale o PowerShell ISE (de recursos do Windows)
   * Instale o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Instale o Internet Explorer versão 10 ou superior no servidor. (Necessário para que o serviço de integridade faça sua autenticação usando suas credenciais de administrador do Azure).
4. Para saber mais sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo no wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Habilitar a auditoria do AD FS
> [!NOTE]
> Esta seção se aplica apenas aos servidores de Federação do AD FS.
> 
> 

Para que o recurso de Análise de Uso colete e analise dados, o agente do Azure AD Connect Health precisa das informações nos Logs de auditoria do AD FS. Esses logs não estão habilitados por padrão. Use os procedimentos a seguir para habilitar a auditoria do AD FS e localizar os logs de auditoria do AD FS em seus servidores do AD FS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Para habilitar a auditoria do AD FS 2.0
1. Clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e clique em **Política de Segurança Local**.
2. Navegue até a pasta **Configurações de segurança\Políticas locais\Gerenciamento de direitos de usuário** e, em seguida, clique duas vezes em Gerar auditorias de segurança.
3. Na guia **Configuração de Segurança Local** , verifique se a conta de serviço do AD FS 2.0 está listada. Se ela não estiver presente, clique em **Adicionar Usuário ou Grupo** e adicione-a à lista e clique em **OK**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Feche a Política de segurança Local e, em seguida, abra o snap-in de Gerenciamento. Para abrir o snap-in de Gerenciamento, clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e clique em Gerenciamento do AD FS 2.0.
6. No painel de ações, clique em Editar propriedades do serviço de federação.
7. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique na guia **Eventos**.
8. Marque as caixas de seleção **Auditorias com êxito** e **Auditorias com falha**.
9. Clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar a auditoria do AD FS no Windows Server 2012 R2
1. Abra **Política de Segurança Local** abrindo o **Gerenciador do Servidor** na tela Iniciar ou o Gerenciador do Servidor na barra de tarefas na área de trabalho e, em seguida, clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até a pasta **Configurações de Segurança\Políticas Locais\Atribuição de Direitos de Usuário** e clique duas vezes em **Gerar auditorias de segurança**.
3. Na guia **Configuração de Segurança Local** , verifique se a conta de serviço do AD FS está listada. Se ela não estiver presente, clique em **Adicionar Usuário ou Grupo** e adicione-a à lista e clique em **OK**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Feche **Política de Segurança Local** e abra o snap-in de **Gerenciamento do AD FS** (no Gerenciador do Servidor, clique em Ferramentas e selecione Gerenciamento do AD FS).
6. No painel Ações, clique em **Editar Propriedades do Serviço de Federação**.
7. Na caixa de diálogo Propriedades do Serviço de Federação, clique na guia **Eventos** .
8. Marque as caixas de seleção **Auditorias com êxito e Auditorias com falha** e clique em **OK**.

#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar os logs de auditoria do AD FS
1. Abra o **Visualizador de Eventos**.
2. Vá para Logs do Windows e selecione **Segurança**.
3. À direita, clique em **Filtrar Logs Atuais**.
4. Em Origem do Evento, selecione **Auditoria do AD FS**.

![Logs de auditoria do AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Se existir uma política de grupo que esteja desabilitando a auditoria do AD FS, o agente do Azure AD Connect Health não poderá coletar informações. Certifique-se de que você não tem uma política de grupo que pssa desativar a auditoria.
> 
> 

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalando o agente do Azure AD Connect Health para sincronização
O agente do Azure AD Connect Health para sincronização é instalado automaticamente na compilação mais recente do Azure AD Connect. Para usar o Azure AD Connect para sincronização, você precisará baixar a versão mais recente do Azure AD Connect e instalá-lo. Você pode baixar a versão mais recente [aqui](http://www.microsoft.com/download/details.aspx?id=47594).

Para verificar se o agente foi instalado, procure os serviços a seguir no servidor. Se você tiver concluído a configuração, eles já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

* Serviço de Informações do Azure AD Connect Health para Sincronização
* Serviço de Monitoramento do Azure AD Connect Health para Sincronização

![Verificar sincronização do Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Lembre-se de que usar o Azure AD Connect Health requer o Azure AD Premium. Se você não tiver o Azure AD Premium, você não poderá concluir a configuração no portal do Azure. Para saber mais, confira a [página de requisitos](active-directory-aadconnect-health-agent-install.md#requirements).
> 
> 

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Registro manual do Azure AD Connect Health para Sincronização
Se o registro do agente do Azure AD Connect Health para Sincronização falhar após a instalação bem-sucedida do Azure AD Connect, você poderá usar o comando a seguir do PowerShell para registrar manualmente o agente.

> [!IMPORTANT]
> O uso desse comando do PowerShell só será necessário se o registro do agente falhar após a instalação do Azure AD Connect.
> 
> 

O comando a seguir do PowerShell é necessário somente quando o registro do agente de integridade falha mesmo após a instalação e a configuração bem-sucedidas do Azure AD Connect. Os serviços do Azure AD Connect Health não serão iniciados até que o agente seja registrado com êxito.

Você pode registrar manualmente o agente do Azure AD Connect Health para sincronização usando o seguinte comando do PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando usa os seguintes parâmetros:

* AttributeFiltering: $true (padrão) - se o Azure AD Connect não estiver sincronizando o conjunto de atributos padrão e tiver personalizado para usar um conjunto de atributos filtrados. Caso contrário, o valor será $false.
* StagingMode: $false (padrão) - se o servidor do Azure AD Connect NÃO estiver no modo de preparo; o valor será $true se o servidor estiver configurado para o modo de preparo.

Quando for solicitado a realizar a autenticação, você deverá usar a mesma conta de administrador global (como admin@domain.onmicrosoft.com)) que foi usada para configurar o Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalando o Agente do Azure AD Connect Health para AD DS
Para iniciar a instalação do agente, dê um clique duplo no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Um prompt de comando será iniciado, seguido pelo PowerShell, que executará Register-AzureADConnectHealthADDSAgent. Quando solicitado a entrar no Azure, continue e entre.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Depois de entrar, o PowerShell continuará. Depois de concluir, feche o PowerShell e a configuração estará concluída.

Neste ponto, os serviços devem ser iniciados automaticamente, permitindo que o agente monitore e colete dados. Se você não tiver atendido a todos os pré-requisitos descritos nas seções anteriores, serão exibidos avisos na janela do PowerShell. Não se esqueça de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verificar o Azure AD Connect Health para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente foi instalado, procure os serviços a seguir no controlador de domínio.

* Serviço do Insights do AD DS do Azure AD Connect Health
* Serviço de Monitoramento do AD DS do Azure AD Connect Health

Se você tiver concluído a configuração, esses serviços já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Instalar o Agente do Azure AD Connect Health Agent para o AD DS no Server Core.
Depois de instalar o arquivo .exe, você pode concluir o processo de registro usando o seguinte comando do PowerShell:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar agentes do Azure AD Connect Health para usar HTTP Proxy
Você pode configurar agentes do Azure AD Connect Health para trabalhar com um HTTP Proxy.

> [!NOTE]
> * Não há suporte para "Netsh WinHttp set ProxyServerAddress", pois o agente usa System.Net para fazer solicitações da web em vez do Microsoft Windows HTTP Services.
> * O endereço do Http Proxy configurado é usado para mensagens de passagem criptografadas de Https.
> * Não há suporte para proxies autenticados (usando HTTPBasic).
> 
> 

### <a name="change-health-agent-proxy-configuration"></a>Alterar a configuração de proxy do agente de integridade
Você tem as seguintes opções para configurar o agente do Azure AD Connect Health para usar um HTTP Proxy.

> [!NOTE]
> Todos os serviços do agente do Azure AD Connect Health devem ser reiniciados para que as configurações de proxy sejam atualizadas. Execute o comando a seguir:<br>
>  Restart-Service AdHealth*
> 
> 

#### <a name="import-existing-proxy-settings"></a>Importar configurações de proxy existentes
##### <a name="import-from-internet-explorer"></a>Importar do Internet Explorer
As configurações de proxy HTTP do Internet Explorer podem ser importadas para serem usadas por agentes do Azure AD Connect Health. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar do WinHTTP
As configurações de proxy WinHTTP podem ser importadas para serem usadas por agentes do Azure AD Connect Health. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar endereços de Proxy manualmente
Você pode executar manualmente um servidor proxy em cada um dos servidores que estejam executando o Agente de Integridade ao executar o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

* "address" pode ser um nome do servidor DNS que pode ser resolvido ou um endereço IPv4
* "port" pode ser omitida. Se omitida, 443 é escolhida como a porta padrão.

#### <a name="clear-existing-proxy-configuration"></a>Limpar a configuração de proxy existente
Você pode limpar a configuração de proxy existente executando o comando a seguir:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Ler configurações de proxy atuais
Você pode ler as configurações de proxy atuais ao executar este comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade com o serviço Azure AD Connect Health
É possível haver problemas que façam com que o agente do Azure AD Connect Health perca a conectividade com o serviço Azure AD Connect Health. Isso inclui problemas de rede, problemas de permissão, entre vários outros.

Se o agente não puder enviar dados para o serviço do Azure AD Connect Health por mais de duas horas, o seguinte alerta será indicado no portal: "Os dados do Serviço de Integridade não estão atualizados." Você pode confirmar se o agente do Azure AD Connect Health afetado consegue carregar dados no serviço Azure AD Connect Health ao executar o seguinte comando do PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

O parâmetro de função usa os seguintes valores:

* ADFS
* Sincronizar
* ADICIONA

Você pode usar o sinalizador -ShowResults no comando para exibir logs detalhados. Use o seguinte exemplo:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> Para usar a ferramenta de conectividade, você deve primeiro concluir o registro do agente. Se conseguir concluir o registro do agente, verifique se você atende a todos os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) do Azure AD Connect Health. Esse teste de conectividade é executado por padrão durante o registro do agente.
> 
> 

## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Dec16_HO3-->



<properties 
    pageTitle="Usando o redirecionamento no RemoteApp do Azure" 
    description="Saiba como configurar e usar o redirecionamento no RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2015" 
    ms.author="elizapo" />

# Usando o redirecionamento no RemoteApp do Azure

O redirecionamento de dispositivo permite que os usuários interajam com aplicativos remotos usando os dispositivos conectados ao seu computador local, telefone ou tablet. Por exemplo, se você tiver fornecido o Skype por meio do RemoteApp do Azure, o usuário precisará da câmera instalada no PC para trabalhar com o Skype. Isso também é verdadeiro para impressoras, alto-falantes, monitores e uma variedade de periféricos conectados por USB.

O RemoteApp utiliza o protocolo RDP e RemoteFX para fornecer redirecionamento.

## Qual redirecionamento está habilitado por padrão?
Quando você usa o RemoteApp, os redirecionamentos a seguir são habilitados por padrão. As informações entre parênteses mostram a configuração do RDP.

- Reproduzir sons no computador local (**Reproduzir neste computador**). (audiomode:i:0)
- Capturar áudio do computador local e enviar para o computador remoto (**Gravar deste computador**). (audiocapturemode:i:1)
- Imprimir em impressoras locais (redirectprinters:i:1)
- Portas COM (redirectcomports:i:1)
- Dispositivo de cartão inteligente (redirectsmartcards:i:1)
- Área de transferência (capacidade de copiar e colar) (redirectclipboard:i:1)
- Suavização de fonte clear type (permitir suavização de fonte: i:1)
- Redirecionar todos os dispositivos Plug and Play com suporte. (devicestoredirect:s: *)

## Quais são os outros redirecionamentos disponíveis?
Duas opções de redirecionamento estão desabilitadas por padrão:

- Redirecionamento de unidade (mapeamento de unidade): as unidades do computador local se tornam unidades mapeadas na sessão remota. Isso permite que você salve ou abra arquivos de suas unidades locais enquanto trabalha na sessão remota. 
- Redirecionamento de USB: você pode usar os dispositivos USB conectados ao computador local na sessão remota.

## Alterar as configurações de redirecionamento no RemoteApp
Você pode alterar as configurações de redirecionamento de dispositivos para uma coleção usando o PowerShell do Microsoft Azure com o SDK. Depois de instalar o novo PowerShell e o SDK, primeiro configure-o para gerenciar sua assinatura, como descrito em [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Em seguida, use um comando semelhante ao seguinte para definir as propriedades personalizadas de RDP:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"
    
(Observe que *`n* é usado como um delimitador entre propriedades individuais).

Para obter uma lista de quais propriedades personalizadas de RDP estão configuradas, execute o cmdlet a seguir. Observe que somente as propriedades personalizadas são mostradas como resultados de saída e não as propriedades padrão:

    Get-AzureRemoteAppCollection -CollectionName <collection name> 
 
Quando você definir propriedades personalizadas, deverá especificar todas elas a cada vez; caso contrário, a configuração será revertida para desabilitada.

### Exemplos comuns
Use o seguinte cmdlet para habilitar o redirecionamento de unidade:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Usar este cmdlet para habilitar o redirecionamento de USB e de unidade:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Use este cmdlet para desabilitar o compartilhamento da área de transferência:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT]Não se esqueça de fazer logoff completo de todos os usuários na coleção (e não apenas desconectá-los) antes de testar a alteração. Para garantir que os usuários estejam totalmente desconectados, vá para a guia **Sessões** na coleção no portal do Azure e faça logoff de todos os usuários desconectados ou conectados. Às vezes, pode levar vários segundos para que as unidades locais sejam mostradas no Explorer na sessão.

## Alterar as configurações de redirecionamento de USB no cliente do Windows

Se você quiser usar o redirecionamento de USB em um computador que se conecta ao RemoteApp, há duas ações que devem ser realizadas. 1 - O administrador precisa habilitar o redirecionamento de USB no nível da coleção usando o PowerShell do Azure. 2 - Em cada dispositivo em que você deseja usar o redirecionamento de USB, será necessário habilitar uma política de grupo que permita isso. Esta etapa precisará ser concluída para cada usuário que queira usar o redirecionamento de USB.
   
> [AZURE.NOTE]O redirecionamento de USB com o RemoteApp do Azure só tem suporte para computadores com Windows.

### Habilitar o redirecionamento de USB para a coleção do RemoteApp
Use o cmdlet a seguir para habilitar o redirecionamento de USB no nível da coleção:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Habilitar o redirecionamento de US para o computador cliente

Para definir as configurações de redirecionamento de USB em seu computador:

1. Abra o Editor de Política de Grupo Local (GPEDIT.MSC). (Execute gpedit.msc no prompt de comando).
2. Abra **Configuração do Computador\\Políticas\\Modelos Administrativos\\Componentes do Windows\\Serviços da Área de Trabalho Remota\\Cliente de Conexão da Área de Trabalho Remota\\Redirecionamento de Dispositivo USB RemoteFX**.
3. Clique duas vezes em **Permitir redirecionamento de RDP de outros dispositivos USB RemoteFX com suporte deste computador**.
4. Selecione **Habilitado** e, em seguida, selecione **Administradores e Usuários nos Direitos de Acesso de Redirecionamento de USB RemoteFX**.
5. Abra um prompt de comando com permissões administrativas e execute o seguinte comando: 

		gpupdate /force
6. Reinicie o computador.

Você também pode usar a ferramenta Gerenciamento de Política de Grupo para criar e aplicar a política de redirecionamento de USB a todos os computadores no seu domínio:

1. Faça logon no controlador de domínio como o administrador de domínio.
2. Abra o Console de Gerenciamento de Política de Grupo. (Clique em **Iniciar > Ferramentas Administrativas > Gerenciamento de Política de Grupo**).
3. Navegue até o domínio ou a unidade organizacional para o qual você deseja criar a política.
4. Clique com o botão da direita do mouse em **Política de Domínio Padrão** e, em seguida, clique em **Editar**.
5. Abra **Configuração do Computador\\Políticas\\Modelos Administrativos\\Componentes do Windows\\Serviços da Área de Trabalho Remota\\Cliente de Conexão da Área de Trabalho Remota\\Redirecionamento de Dispositivo USB RemoteFX**.
6. Clique duas vezes em **Permitir redirecionamento de RDP de outros dispositivos USB RemoteFX com suporte deste computador**.
7. Selecione **Habilitado** e, em seguida, selecione **Administradores e Usuários nos Direitos de Acesso de Redirecionamento de USB RemoteFX**.
8. Clique em **OK**.  

<!---HONumber=Oct15_HO3-->
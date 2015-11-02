<properties 
    pageTitle="Como redirecionar dispositivos USB no Azure RemoteApp? | Microsoft Azure" 
    description="Saiba como usar o redirecionamento para dispositivos USB no Azure RemoteApp." 
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
    ms.date="10/19/2015" 
    ms.author="elizapo" />



# Como redirecionar dispositivos USB no Azure RemoteApp?

O redirecionamento de dispositivo permite que os usuários usem os dispositivos USB conectados ao seu computador ou tablet com os aplicativos no Azure RemoteApp. Por exemplo, se você compartilhou o Skype por meio do Azure RemoteApp, os usuários terão de conseguir usar as câmeras de seus dispositivos.

Antes de continuar, lembre-se de ler as informações de redirecionamento de USB em [Usando o redirecionamento no Azure RemoteApp](remoteapp-redirection.md). No entanto, o nusbdevicestoredirect:s:* recomendado não funcionará para webcams USB e pode não funcionar para algumas impressoras USB ou dispositivos USB multifuncionais. Por motivos de design e de segurança, o administrador do Azure RemoteApp precisa habilitar o redirecionamento por GUID de classe de dispositivo ou por ID de instância de dispositivo antes que seus usuários possam usar esses dispositivos.

Embora este artigo fale sobre o redirecionamento da webcam, você pode usar uma abordagem semelhante para redirecionar impressoras USB e outros dispositivos USB multifuncionais que não são redirecionados pelo comando **nusbdevicestoredirect:s:***.

## Opções de redirecionamento para dispositivos USB
O Azure RemoteApp usa mecanismos muito semelhantes para redirecionar dispositivos USB como os que estão disponíveis para os Serviços de Área de Trabalho Remota. A tecnologia subjacente permite que você escolha o método de redirecionamento correto para um determinado dispositivo, para obter o melhor do redirecionamento de dispositivo USB de alto nível e RemoteFX usando o comando **usbdevicestoredirect:s:**. Há quatro elementos neste comando:

| Ordem de processamento | Parâmetro | Descrição |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | Seleciona todos os dispositivos que não são captados pelo redirecionamento de alto nível. Observação: por design, o * não funciona para webcams USB. |
| | {GUID de classe do dispositivo} | Seleciona todos os dispositivos que correspondem à classe de instalação do dispositivo especificado. |
| | USB\\InstanceID | Seleciona um dispositivo USB especificado para a ID de instância indicada. |
| 2 | -USB\\Instance ID | Remove as configurações de redirecionamento para o dispositivo especificado. |

## Redirecionando um dispositivo USB usando o GUID de classe do dispositivo
Há duas maneiras para encontrar o GUID de classe do dispositivo que pode ser usado no redirecionamento.

A primeira opção é usar as [Classes de instalação de dispositivo definidas pelo sistema disponíveis para fornecedores](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Selecione a classe que mais se aproxima do dispositivo conectado ao computador local. Para câmeras digitais, isso pode ser uma classe de Dispositivo de Imagem ou de Dispositivo de Captura de Vídeo. Você precisará fazer alguns testes com as classes de dispositivo para encontrar o GUID de classe correto que funciona com o dispositivo USB conectado localmente (em nosso caso, a webcam).

Uma maneira melhor, ou a segunda opção, é seguir estas etapas para encontrar o GUID de classe de dispositivo específico:

1. Abra o Gerenciador de Dispositivos, localize o dispositivo que será redirecionado e clique com o botão direito do mouse nele e, em seguida, abra as propriedades. ![Abrir o Gerenciador de Dispositivos](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Na guia **Detalhes**, escolha a propriedade **Guid de Classe**. O valor que aparece é o GUID de Classe para esse tipo de dispositivo. ![Propriedades da câmera](./media/remoteapp-usbredir/ra-classguid.png)
3. Use o valor de GUID de classe para redirecionar dispositivos que correspondem a ele.

Por exemplo:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

É possível combinar vários redirecionamentos de dispositivo no mesmo cmdlet. Por exemplo: para redirecionar o armazenamento local e uma webcam USB, o cmdlet tem esta aparência:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Ao definir o redirecionamento de dispositivo pelo GUID de classe, todos os dispositivos que correspondem ao GUID de classe na coleção especificada são redirecionados. Por exemplo, se houver vários computadores na rede local que tenham as mesmas webcams USB, você poderá executar um único cmdlet para redirecionar todas as webcams.

## Redirecionando um dispositivo USB usando a ID de instância do dispositivo

Se desejar um controle mais refinado e controlar o redirecionamento por dispositivo, você poderá usar o parâmetro de redirecionamento **USB\\InstanceID**.

A parte mais difícil deste método é encontrar a ID de instância do dispositivo USB. Você precisará ter acesso ao computador e ao dispositivo USB específico. Depois, siga estas etapas:

1. Habilite o redirecionamento de dispositivo na Sessão de Área de Trabalho Remota, como descrito em [Como posso usar meus dispositivos e recursos em uma sessão de Área de Trabalho Remota?](http://windows.microsoft.com/pt-BR/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Abra uma Conexão de Área de Trabalho Remota e clique em **Mostrar Opções**.
3. Clique em **Salvar como** para salvar as configurações atuais de conexão em um arquivo RDP. ![Salvar as configurações como um arquivo RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Escolha um nome de arquivo e um local, por exemplo, “MyConnection.rdp” e “This PC\\Documents” e salve o arquivo.
5. Abra o arquivo MyConnection.rdp usando um editor de texto e encontre a ID de instância do dispositivo que você deseja redirecionar.

Agora, use a ID de instância no seguinte cmdlet:

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB<Device InstanceID value>"

<!---HONumber=Oct15_HO4-->
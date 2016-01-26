<properties
   pageTitle="Como integrar o OneDrive for Business e o Azure RemoteApp | Microsoft Azure"
   description="Saiba como usar o OneDrive for Business com o Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# Como integrar o OneDrive for Business e o Azure RemoteApp

Você pode usar o OneDrive for Business como um repositório de arquivos com o Azure RemoteApp. O OneDrive for Business é uma ótima maneira de manter os arquivos sincronizados em todos os seus dispositivos e espaços de trabalho. O [UDP](remoteapp-upd.md) (disco de perfil de usuário) de um usuário é um local em que os usuários podem armazenar arquivos para aplicativos do Azure RemoteApp, mas esses arquivos são acessíveis apenas usando o Azure RemoteApp. Por outro lado, o OneDrive for Business permite ao usuário acessar arquivos onde e quando desejar, sem o pré-requisito de utilizar o Azure RemoteApp. Este artigo abordará as versões do OneDrive for Business com suporte e as diferentes maneiras pelas quais os administradores podem configurar o OneDrive for Business para o Azure RemoteApp.

## Todas as versões do OneDrive têm suporte?

Há duas versões do OneDrive: OneDrive e OneDrive for Business. Apenas o OneDrive for Business tem suporte no Azure RemoteApp. O OneDrive pessoal funciona, mas não tem suporte oficial. Além disso, somente a última versão do OneDrive for Business, também conhecida como Cliente de Sincronização de Próxima Geração, tem suporte no Azure RemoteApp (e em servidores RDSH/Citrix/Terminal).

>[AZURE.NOTE]Não há suporte ao OneDrive (para consumidores/edição pessoal) no Azure RemoteApp. Além disso, nem todas as versões do OneDrive for Business têm suporte, pois ainda não foram certificadas para funcionar no Windows Server. Embora o novo cliente (Cliente de Sincronização de Próxima Geração) e as versões mais antigas do Groove possam parecer funcionar bem no Azure RemoteApp, conforme descrito em [https://support.microsoft.com/pt-BR/kb/2965687](https://support.microsoft.com/kb/2965687), os mecanismos de sincronização antigos não terão funcionalidade completa no Citrix/Servidores de Terminal (Windows Server). Use o novo cliente de sincronização no Azure RemoteApp (e outras implantações do Windows Server).

## Quais são as diferentes opções para a configuração do OneDrive for Business?

- **Instalação tradicional do mecanismo de sincronização do OneDrive for Business:** o cliente de sincronização do OneDrive for Business pode ser instalado em uma SKU de servidor (Área de Trabalho Remota, bem como na sessão do RemoteApp/sessão do Servidor de Terminal) e em pastas selecionadas para sincronização na sessão do RemoteApp, exatamente como você faria em uma SKU de cliente do Windows. O local padrão em que o OneDrive for Business sincroniza arquivos é o mesmo local em que reside o disco de perfil de usuário usado para armazenar dados e configurações de usuário no Azure RemoteApp - em C:\\users<username>. Esse disco seguirá o usuário para qualquer máquina virtual em que ele fizer logon e, assim, os arquivos ODB também seguirão o usuário. O aplicativo do OneDrive for Business precisa ser publicado pelo administrador para todos os usuários, e os usuários precisam iniciá-lo a cada nova sessão (ou a inicialização pode ser automatizada com um script de logon) para garantir que o mecanismo de sincronização seja ativado. O OneDrive for Business baixará o arquivo inteiro na VM em que a sessão está em execução. Durante a sincronização do conteúdo de um usuário, sincronizar tudo representa uma carga de trabalho pesada (CPU/dados transferidos/armazenamento ocupado), que simplesmente não é otimizada para computadores de terminal com um grande número de usuários que entram brevemente em cada computador. Com a sincronização seletiva, a carga de trabalho seria reduzida, mas o problema ainda permaneceria.
- **"Virtualize" o OneDrive for Business/redirecione-o do cliente gordo local para a sessão:** se estiver sincronizando o OneDrive para uma pasta no dispositivo do cliente, em uma unidade, você poderá optar por [redirecionar](remoteapp-redirection.md) essa unidade para o Azure RemoteApp. Essa unidade deve ser a mesma em todos os clientes dos usuários, e eles devem ter o OneDrive sincronizado para uma pasta nessa unidade. Caso eles acessem o RemoteApp por meio de qualquer outro cliente, esses arquivos poderão não estar disponíveis (solução alternativa: eles sempre podem acessar os arquivos usando a versão online do OneDrive). 
- **Apresentar o OneDrive for Business como uma unidade no ambiente do Azure RemoteApp sem armazenar em cache/sincronizar os arquivos:** (mapear a URL http do OneDrive for Business para uma unidade na VM) há suporte ao mapeamento do OneDrive for Business para a unidade de rede dentro do ambiente RDSH. Cenários em que isso pode ser usado: 
	- Quando clientes finos (sem armazenamento local) são usados para acessar o Azure RemoteApp: o aplicativo requer que os arquivos sejam armazenados no OneDrive for Business, mas desejaria que eles "parecessem" locais, e o administrador não deseja sincronizar os arquivos para uma VM.
	- Quando há muitos arquivos grandes no OneDrive for Business que foram selecionados para sincronização. Dada a carga de trabalho de sincronização, talvez nem todos os arquivos sejam sincronizados quando o usuário desejar usá-los. Além disso, se o tamanho total dos arquivos exceder 50 GB, eles não poderão ser armazenados no UPD.

Nos cenários acima, os administradores podem optar por usar as opções de mapeamento de uma unidade na VM para a URL http do OneDrive for Business do usuário. Algumas opções para habilitar isso:

- Ter os binários do Office na imagem e não ativar o mecanismo de sincronização do OneDrive for Business.
- NÃO ter binários do Office na imagem. Esse tem um pré-requisito: o Pacote de Experiência Desktop. Especificamente, o serviço WebClient (também conhecido como WebDAV) precisa ser instalado como parte do pacote do Pacote de Experiência Desktop. 

### Instalar o Pacote de Experiência Desktop no Windows Server 2012 R2
Para instalar o Pacote de Experiência Desktop:

1. No Gerenciador de Servidores, clique em **Gerenciar -> Adicionar Funções e Recursos**.
2. Clique em **Recursos** e em **Interfaces do Usuário e Infraestrutura -> Experiência Desktop**.

### Mapear uma unidade para a URL do OneDrive for Business

Siga as instruções no artigo de suporte: [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
Uma etapa importante na configuração é selecionar **mantenha-me conectado**.

Em um alto nível, aqui estão as instruções:

1.	Localize a URL da unidade. A URL usada para o mapeamento de unidade é aquela você obtém ao navegar até o diretório base no OneDrive for Business online. Por exemplo:
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	Abra a URL usando um navegador na sessão do RemoteApp e selecione **mantenha-me conectado** antes de fazer logon na URL de sua conta.
3.	Abra o Windows Explorer e mapeie uma unidade para a URL acima. Se a URL não foi resolvida, o formato mais curto poderá ser usado:
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com.

	Isso cria imediatamente a unidade mapeada. Ela tem esta aparência:
 
	![OneDrive for Business como uma unidade de rede mapeada](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->
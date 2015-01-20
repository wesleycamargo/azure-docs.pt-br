<properties title="Azure RemoteApp FAQ" pageTitle="Perguntas frequentes sobre o RemoteApp do Azure" description="Saiba sobre o Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Perguntas frequentes sobre o RemoteApp do Azure
Ouvimos as seguintes perguntas sobre o RemoteApp do Azure. Você tem outras? Visite os [fóruns do RemoteApp](https://social.msdn.microsoft.com/Forums/azure/pt-br/home?forum=AzureRemoteApp) e diga-nos o que você precisa saber.

##O que é o RemoteApp do Azure?


- **O que é o Azure RemoteApp?** O RemoteApp é um serviço do Azure que fornece a funcionalidade do RemoteApp dos Microsoft local, feito pelos serviços de área de trabalho remota, do Azure. O RemoteApp o ajudará a fornecer acesso remoto seguro aos aplicativos de vários dispositivos de usuário diferente. Leia mais sobre [Azure RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-whatis/).
- **Quais são os dois tipos de opções de implantação?** Há dois tipos de implantações do RemoteApp (ou coleções): híbrida e nuvem. Descubra qual a [Opção de implantação](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-whatis/) funciona melhor para sua organização.

##Configurações com suporte##


- **Há suporte para aplicativos personalizados de linha de negócios (LOB)?** Sim. Para usar um aplicativo personalizado no Azure RemoteApp, crie uma [imagem do modelo personalizado](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/)e, em seguida, carregue-a para a coleção de RemoteApp.
- **Meu aplicativo de LOB personalizado funcionará no RemoteApp do Azure?** A melhor maneira de descobrir isso é testá-lo. Examine os [requisitos de compatibilidade de aplicativos](http://www.microsoft.com/pt-br/download/details.aspx?id=18704) e verifique [Centro de compatibilidade de área de trabalho remota (RD)](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Qual método de implantação (nuvem ou híbrida) é a melhor para minha organização?** Coleções híbridas fornecem uma experiência mais completa se você quer integração total com o logon único (SSO) e conectividade de rede local segura. Coleções na nuvem fornecem uma maneira fácil e ágil para isolar a implantação usando vários métodos de autenticação. Leia mais sobre as [opções de implantação](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-whatis/).
- **A coleção híbrida requer uma VNET. Podemos usar nosso NET existente?** Não neste momento, mas nós sabemos que você deseja. Enquanto estamos trabalhando nisso, você pode conectar sua VNET existente ao Azure RemoteApp VNET seguindo [estas instruções](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx).
- **Posso usar uma nuvem ou máquina virtual existente como modelo para minha coleção de RemoteApp?** Não neste momento, mas isso é um pedido frequente em nosso [site de comentários](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w).
- **Temos SQL ou outro banco de dados no local ou no Azure. Qual o tipo de implantação que nós usamos?** Isso depende de onde está o seu banco de dados SQL ou back-end. Se o banco de dados estiver em uma rede privada, use a coleção híbrida. Se o banco de dados estiver exposto à Internet e permite ao cliente conexões para se conectar a ele, você pode usar a coleção na nuvem.
- **E quanto ao mapeamento de unidade, porta USB e serial, compartilhamento de área de transferência e redirecionamento de impressora?** Todos esses recursos têm suporte no RemoteApp do Azure. O redirecionamento de impressora e compartilhamento de área de transferência está habilitado por padrão. [Entre em contato conosco](mailto:remoteappforum@microsoft.com? assunto = Azure % 20RemoteApp % 20enable % 20Redirection % 20request) para habilitar o mapeamento de unidade ou redirecionamento de porta seria ou USB. (Estamos trabalhando sobre como adicionar esse suporte no portal, mas ainda não chegamos lá).
- **E quanto a autenticação? Há suporte para quais métodos?** A coleção na nuvem oferece suporte a contas da Microsoft e contas do Active Directory do Azure, que são contas do Office 365 também. A coleção híbrida suporta somente as contas do Active Directory do Azure que foram sincronizadas (usando uma ferramenta como DirSync) de uma implantação do Windows Server Active Directory. Especificamente, seja sincronizado com a opção de sincronização de senha ou sincronizados com federação de serviços de Federação do Active Directory (AD FS) configurada. Você também pode configurar a [Autenticação multifator (MFA)](http://azure.microsoft.com/pt-br/documentation/services/multi-factor-authentication/).

	**Observação:** os usuários do Active Directory do Azure devem ser do locatário associado a sua assinatura. (Você pode exibir e modificar sua assinatura na guia **Configurações** no portal. Consulte [Alterar o locatário do Active Directory do Azure usado pelo RemoteApp](http://msdn.microsoft.com/pt-br/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obter mais informações.)

- **Por que não posso dar meu acesso de conta do Active Directory do Azure?** Os usuários do Active Directory do Azure devem ser do diretório associado a sua assinatura. Você pode exibir ou modificar esse diretório na guia Configurações no portal. Consulte [Alterar o locatário do Active Directory do Azure usado pelo RemoteApp](http://msdn.microsoft.com/pt-br/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obter mais informações.
- **Quais dispositivos e sistemas operacionais dão suporte aos aplicativos cliente?** Aplicativos cliente estão disponíveis para Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, dispositivos com Android e Windows Phone. Também há suporte para a visualização do Windows 10.
 
	[Baixe](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) um cliente RemoteApp agora.
- **O RemoteApp do Azure oferece suporte a clientes finos?** Sim, os clientes finos Windows Embedded a seguir têm suporte:
	- Windows Embedded Standard 7 com Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Qual versão do Windows Server é suportada para Host de sessão de área de trabalho remota (RDSH)?** Windows Server 2012 R2.

##Suporte e comentários

- **Posso testar esse serviço gratuitamente?** Sim. Uma avaliação gratuita está disponível por 30 dias. Após o término da avaliação, você poderá fazer a transição para uma conta paga (que pode ser usada na produção) ou parar de usar o serviço. Inicie sua avaliação gratuita acessando [manage.windowsazure.com](http://manage.windowsazure.com) - criar uma nova instância do RemoteApp. Com a versão de avaliação gratuita, você pode criar duas instâncias do RemoteApp com 10 usuários por instância. Lembre-se de que esta avaliação só é válida por 30 dias.
- **Qual é o plano de suporte para o RemoteApp?** Suporte para gerenciamento de assinaturas e cobrança é fornecido sem custo adicional. Suporte técnico está disponível por meio de [planos de serviço do Azure](http://azure.microsoft.com/support/plans/). Você também pode obter suporte gratuito da comunidade por meio de nosso [Fórum de discussão do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/pt-br/home?forum=AzureRemoteApp).
- **Qual é o custo de RemoteApp?** Verifique os [Detalhes de preços do Azure RemoteApp ](http://azure.microsoft.com/pt-br/pricing/details/remoteapp/).
- **Como posso enviar comentários?** Visite o [Fórum de comentários](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **Com quem posso falar para saber mais sobre o Azure RemoteApp?** Além do nosso [Fórum de discussão](http://social.msdn.microsoft.com/Forums/windowsazure/pt-br/home?forum=AzureRemoteApp), que é um ótimo lugar para publicar perguntas, você pode se associar ao [webinar semanal Pergunte aos especialistas](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), onde podemos falar sobre tudo do RemoteApp.
- **E a documentação do RemoteApp?** Estamos feliz pela pergunta. Além do conteúdo da Ajuda na gaveta de Ajuda do portal (basta clicar no **?** em qualquer página do portal), os artigos a seguir estão disponíveis para ensinar a você tudo sobre o RemoteApp:
	- **Introdução:**
		- [O que é o RemoteApp?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-whatis/)
		- [O que são as imagens de modelo do RemoteApp?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-images/)
		- [Como funciona o licenciamento?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-licensing/)
		- [Como RemoteApp e Office funcionam juntos?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-o365/)
	- **Implantação:**
		- [Criar uma imagem de modelo personalizado](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/)
		- [Criar uma coleção híbrida](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Criar uma coleção na nuvem](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Configurar o Active Directory do Azure para RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-ad/)
		- [Publicar um aplicativo no RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-publish/)
	- **Gerenciamento:**
		- [Adicionar usuários](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-user/)
		- [Práticas recomendadas para configurar e usar o RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-bestpractices/)	

	Vídeos! Também temos inúmeros vídeos sobre o RemoteApp. Alguns fornecem a apresentação ([Introdução ao Azure RemoteApp](http://azure.microsoft.com/pt-br/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) enquanto outros o orientam por meio da implantação ([Implantação na nuvem](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [Implantação híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Experimente-os!


<!--HONumber=35.2-->

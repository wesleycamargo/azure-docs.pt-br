

As imagens são usadas no Azure para fornecer uma nova máquina virtual com um sistema operacional. Uma imagem também pode ter um ou mais discos de dados. As imagens estão disponíveis de várias fontes:

* O Azure oferece imagens no [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Há versões recentes do Windows Server e das distribuições do sistema operacional Linux. Algumas imagens também contêm aplicativos, como o SQL Server. Assinantes de MSDN Benefício e Pré-pago têm acesso a imagens adicionais.
* A comunidade de software livre oferece imagens por meio do [VM Depot](http://vmdepot.msopentech.com/List/Index).
* Você também pode armazenar e usar suas próprias imagens no Azure, seja capturando uma máquina virtual do Azure para usar como uma imagem ou carregando uma imagem.

## Sobre imagens de VM e de SO
Dois tipos de imagens podem ser usadas no Azure: *imagem da VM* e *imagem do SO*. Uma imagem de VM inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Uma imagem de VM é o tipo mais recente da imagem. Antes das imagens de VM serem introduzidas, uma imagem no Azure podia ter apenas um sistema operacional generalizado e nenhum disco adicional. Uma imagem de VM que contém apenas um sistema operacional generalizado é basicamente o mesmo que o tipo original da imagem, ou seja, a imagem do SO.

Você pode criar suas próprias imagens com base em uma máquina virtual no Azure ou uma máquina virtual em execução em outro lugar que você copiar e carregar. Se quiser usar uma imagem para criar mais de uma máquina virtual, você precisará prepará-la para uso como uma imagem, generalizando-a. Para criar uma imagem do Windows Server, execute o comando Sysprep no servidor para generalizá-la antes de carregar o arquivo .vhd. Para obter detalhes sobre o Sysprep, confira [How to Use Sysprep: An Introduction](http://go.microsoft.com/fwlink/p/?LinkId=392030) (Como usar o Sysprep: uma introdução) e [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup da VM antes de executar o Sysprep. A criação de uma imagem do Linux varia de acordo com a distribuição. Normalmente, você precisa executar um conjunto de comandos que são específicos para a distribuição e executar o Agente Linux do Azure.

<!---HONumber=AcomDC_0831_2016-->
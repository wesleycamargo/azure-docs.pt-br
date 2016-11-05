> Não é possível usar uma Rede Virtual v1 (Clássica) do Azure com o HDInsight baseado em Linux. A Rede Virtual deve ser v2 (Gerenciador de Recursos do Azure) para que seja listada como uma opção durante o processo de criação de cluster HDInsight no portal de visualização do Azure ou para poder ser usada durante a criação de um cluster por meio da CLI do Azure ou do Azure PowerShell.
> 
> Se você tiver recursos em uma rede v1 e desejar disponibilizar o HDInsight diretamente a esses recursos por meio de uma rede virtual, veja [Conectando VNets clássicas a novas VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) para obter informações sobre como conectar uma Rede Virtual v2 a uma Rede Virtual v1. Quando essa conexão for estabelecida, você poderá criar o cluster HDInsight na Rede Virtual v2.
> 
> 

<!---HONumber=Oct15_HO3-->
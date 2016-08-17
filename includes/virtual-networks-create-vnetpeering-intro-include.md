O Emparelhamento VNet é um mecanismo para conectar duas Redes Virtuais na mesma região por meio da rede de backbone do Azure. Uma vez emparelhadas, as duas Redes Virtuais aparecerão como uma única Rede Virtual para todas as finalidades de conectividade. Leia a [Visão geral do emparelhamento VNet](../articles/virtual-network/virtual-network-peering-overview.md) se você não estiver familiarizado com o Emparelhamento VNet.

O Emparelhamento VNet está em visualização pública. Para poder usá-lo, você deverá registrar-se usando o comando abaixo:

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!---HONumber=AcomDC_0810_2016-->
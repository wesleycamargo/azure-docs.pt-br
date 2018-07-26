| Recurso | Limite padrão |
| --- | :--- |
| Nós máx por cluster | 100 |
| Pods máx por nó ([rede básica com Kubenet][basic-networking]) | 110 |
| Pods máx por nó ([rede avançada com CNI do Azure][advanced-networking]) | 30<sup>1</sup> |
| Cluster máx por assinatura | 100 |

<sup>1</sup> Esse valor pode ser personalizado por meio de implantação de modelo do ARM. Veja exemplos [aqui][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69

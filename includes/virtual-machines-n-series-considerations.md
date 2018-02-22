## <a name="deployment-considerations"></a>Considerações de implantação

* Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/en-us/regions/services/).

* As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

* As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). As VMs NCv2, ND e NCv3 (versão prévia) somente dão suporte a discos de VM que executam backup em SSD (Armazenamento em Disco Premium).

* Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* Talvez seja necessário aumentar a cota de núcleos (por região) em sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, ND ou NV. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

* Uma imagem VM que você pode implantar nas VMs da série N é a [Máquina Virtual de Ciência de Dados do Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). A Máquina Virtual de Ciência de Dados pré-instala e configura muitas ferramentas conhecidas de ciência de dados e de aprendizado. Ela também pré-instala drivers NVIDIA Tesla GPU.






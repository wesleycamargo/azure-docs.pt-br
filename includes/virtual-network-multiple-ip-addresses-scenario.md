## <a name="scenario"></a>Cenário
Uma VM com uma única NIC é criada e conectada a uma rede virtual. A VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos*. Os endereços IP são atribuídos às seguintes configurações de IP:

* **IPConfig-1:** atribui um endereço IP privado *dinâmico* (padrão) e um endereço IP público *estático*.
* **IPConfig-2:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-3:** atribui um endereço IP privado *dinâmico* e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-scenario/OneNIC-3IP.png)

As configurações de IP são associadas à NIC quando a NIC é criada e a NIC é conectada à máquina Virtual quando a VM é criada. Os tipos de endereços IP usados para o cenário são para fins de ilustração. Você pode atribuir quaisquer tipos de atribuição e de endereço IP necessários.

> [!NOTE]
> Embora as etapas neste artigo atribuam todas as configurações de IP a uma única NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com várias NICs, leia o artigo [Criar uma VM com várias NICs](../articles/virtual-network/virtual-network-deploy-multinic-arm-ps.md).

<!--HONumber=Dec16_HO2-->



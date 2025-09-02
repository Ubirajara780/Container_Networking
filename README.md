## Conexão Externa do Core 5G

Quando trabalhamos com containers Docker, cada aplicação ou serviço costuma ser isolado em sua própria rede interna.  Isso é útil para segurança e organização, mas cria uma limitação: os serviços só conseguem se comunicar dentro dessa rede restrita.

No caso do **Core 5G**, precisamos que **todos os módulos (AMF, SMF, UPF, NRF, etc.) se comuniquem entre si** de forma confiável e, além disso, que alguns deles possam ser acessados externamente — por exemplo, por uma estação base (**gNodeB**) ou por uma **UE (User Equipment)** simulada.

Se cada container ficasse em uma rede diferente ou apenas com endereços privados, essa comunicação seria impossível.  
Por isso, a solução foi:

1. **Criar uma rede Docker dedicada**  
   Essa rede funciona como um “switch virtual” onde todos os containers podem se conectar.

2. **Mover todos os containers para essa rede**  
   Assim, todos passam a compartilhar o mesmo espaço de endereçamento IP.

3. **Atribuir endereços IP fixos (públicos) a cada container**  
   Antes, cada container tinha apenas um IP privado, atribuído automaticamente.  
   Agora, definimos manualmente endereços IP dentro da nova rede, garantindo que cada módulo do Core possa ser identificado de forma única e acessível.

4. **Resultado**  
   - O Core passa a funcionar como se estivesse em uma rede pública real.  
   - As UEs e o gNodeB podem alcançar os serviços do Core diretamente pelos novos IPs.  
   - A comunicação entre os módulos internos também se torna estável, já que os endereços não mudam a cada reinício dos containers.
---

**Files**

- [setup guide] (setup-guide.md)

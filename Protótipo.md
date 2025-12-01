# 1. Padrão Criacional: Prototype
## Objetivo geral:
Os padrões Criacionais fornecem vários mecanismos de criação de objetos, que aumentam a flexibilidade e reutilização de código já existente. [1]

## Observação:
Nem todos os objetos podem ser copiados dessa forma porque alguns campos de objeto podem ser privados e não serão visíveis fora do próprio objeto. [1]

## Prototype:
O Prototype é um padrão de projeto **Criacional** que permite copiar objetos existentes sem fazer seu código ficar dependente de suas classes. [1]

### Qual Problema Resolve?
Cria novos objetos **copiando** uma instância existente (o protótipo), em vez de passar por um processo de criação complexo ou caro. Isso é especialmente útil quando precisa-se criar muitos objetos que são quase idênticos ou que dependem de uma configuração inicial complexa.

### Solução:
O padrão define uma interface para clonagem. O objeto protótipo (a instância original) sabe como se clonar. O código cliente simplesmente solicita a clonagem ao protótipo, sem saber nada sobre a complexidade interna de sua criação.

### UML (Propósito Geral):
O diagrama mostra a interface `Prototype` com o método `clone()` e as classes concretas que a implementam.

```mermaid
classDiagram
    class Prototype {
        <<Interface>>
        + clone()
    }
    class ComponenteConcreto {
        + clone()
    }
    
    Prototype <|-- ComponenteConcreto : implementa
    Note right of ComponenteConcreto: O componente sabe como se clonar
# Objeto Aninhado (Referência Circular)
class Cliente:
    """ Representa o objeto Cliente, que pode ter uma referência de volta ao Pedido. """
    def __init__(self, nome, email):
        self.nome = nome
        self.email = email
        self.pedidos = [] # Referência ao pedido (circular)

    def set_pedido(self, pedido):
        """ Configura a referência circular de volta ao objeto Pedido. """
        self.pedidos.append(pedido)

    def __str__(self):
        return f"Cliente: {self.nome} ({self.email})"

class PedidoModelo:
    """ 
    O objeto Protótipo que implementa __copy__ (rasa) e __deepcopy__ (profunda).
    """

    def __init__(self, id_base, lista_de_itens, ref_cliente):
        self.id_base = id_base
        self.lista_de_itens = lista_de_itens
        self.ref_cliente = ref_cliente
        self.status = "NOVO" # Atributo simples

    def __copy__(self):
        """ Cria uma cópia rasa. O Cliente (ref_cliente) será compartilhado. """
        
        # Copia superficial da lista de itens, mas os itens internos são compartilhados
        lista_de_itens_copia = copy.copy(self.lista_de_itens) 
        # Cópia superficial da referência do Cliente (aponta para o mesmo Cliente)
        ref_cliente_copia = copy.copy(self.ref_cliente) 

        # Cria a nova instância do Pedido (o clone)
        novo_pedido = self.__class__(
            self.id_base, lista_de_itens_copia, ref_cliente_copia
        )
        # Copia atributos simples como 'status'
        novo_pedido.__dict__.update(self.__dict__)

        return novo_pedido

    def __deepcopy__(self, memo=None):
        """ Cria uma cópia profunda, clonando todos os objetos aninhados. """
        if memo is None:
            memo = {}

        # Clonagem profunda dos objetos aninhados (lista de itens e cliente)
        lista_de_itens_copia = copy.deepcopy(self.lista_de_itens, memo)
        ref_cliente_copia = copy.deepcopy(self.ref_cliente, memo)

        # Cria a nova instância do Pedido
        novo_pedido = self.__class__(
            self.id_base, lista_de_itens_copia, ref_cliente_copia
        )
        
        # Clonagem profunda de todo o dicionário de atributos
        novo_pedido.__dict__ = copy.deepcopy(self.__dict__, memo)
        
        # Atualiza a referência circular no objeto Cliente clonado para apontar para o novo pedido.
        # Isto é crucial para manter a integridade após o deepcopy.
        ref_cliente_copia.pedidos = [novo_pedido] 
        
        return novo_pedido

if __name__ == "__main__":
    
    # ----------------------------------------------------
    # 1. SETUP DO PROTÓTIPO ORIGINAL
    # ----------------------------------------------------
    itens_do_pedido = [{"produto": "Notebook", "qtd": 1, "preco": 3000}, {"produto": "Mouse", "qtd": 2, "preco": 50}]
    
    cliente_original = Cliente("Maria Silva", "maria@email.com")
    
    pedido_original = PedidoModelo(1001, itens_do_pedido, cliente_original)
    cliente_original.set_pedido(pedido_original)

    print("--- 1. PROTÓTIPO ORIGINAL ---")
    print(f"Pedido ID Base: {pedido_original.id_base}, Cliente: {pedido_original.ref_cliente.nome}")
    print("-" * 30)

    # ----------------------------------------------------
    # 2. CÓPIA RASA (Shallow Copy)
    # ----------------------------------------------------
    pedido_raso = copy.copy(pedido_original)
    pedido_raso.id_base = 1002 # ID único para o novo pedido
    pedido_raso.status = "PAGO"
    
    # ALTERAÇÃO: Mudando o nome do cliente no pedido raso
    pedido_raso.ref_cliente.nome = "Maria Silveira"

    print("--- 2. CÓPIA RASA (Cliente Compartilhado) ---")
    print(f"Original: Cliente: {pedido_original.ref_cliente.nome}, Status: {pedido_original.status}")
    print(f"Raso:     Cliente: {pedido_raso.ref_cliente.nome}, Status: {pedido_raso.status}")
    
    # Demonstra o efeito da cópia rasa: a alteração no cliente_raso AFETOU o cliente_original
    print(f"Clientes são o mesmo objeto na memória? {pedido_original.ref_cliente is pedido_raso.ref_cliente}")
    print("-" * 30)

    # ----------------------------------------------------
    # 3. CÓPIA PROFUNDA (Deep Copy)
    # ----------------------------------------------------
    # Resetando o nome do cliente original para o teste
    cliente_original.nome = "Maria Silva" 
    
    pedido_profundo = copy.deepcopy(pedido_original)
    pedido_profundo.id_base = 1003
    pedido_profundo.status = "ENVIADO"

    # ALTERAÇÃO: Mudando o nome do cliente no pedido profundo
    pedido_profundo.ref_cliente.nome = "Pedro Novo Cliente"
    
    print("--- 3. CÓPIA PROFUNDA (Cliente Independente) ---")
    print(f"Original: Cliente: {pedido_original.ref_cliente.nome}, Status: {pedido_original.status}")
    print(f"Profundo: Cliente: {pedido_profundo.ref_cliente.nome}, Status: {pedido_profundo.status}")
    
    # Demonstra o efeito da cópia profunda: a alteração no cliente_profundo NÃO AFETOU o cliente_original
    print(f"Clientes são o mesmo objeto na memória? {pedido_original.ref_cliente is pedido_profundo.ref_cliente}")
    print("-" * 30)
    
    # Demonstração de Referência Circular Clonada
    # O cliente_profundo agora aponta para o novo PedidoModelo clonado (1003).
    print(f"Referência circular no Pedido Profundo aponta para o Pedido ID: {pedido_profundo.ref_cliente.pedidos[0].id_base}")
    # Saída esperada: 1003

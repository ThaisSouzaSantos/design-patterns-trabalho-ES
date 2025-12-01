# 3. Padrão Comportamental: Strategy

## Objetivo geral:
Os padrões **Comportamentais** se concentram em como os objetos interagem entre si e como as responsabilidades são distribuídas entre eles. Eles aumentam a flexibilidade na comunicação e delegação de tarefas. [1]

## Observação:
O Padrão Strategy é a melhor maneira de substituir algoritmos condicionais complexos (como grandes blocos `if/else` ou `switch`) por um conjunto limpo de classes intercambiáveis. [1]

## Strategy:
O Strategy é um padrão de projeto **Comportamental** que define uma família de algoritmos, encapsula cada um deles e os torna intercambiáveis. Permite que o algoritmo varie independentemente dos clientes que o utilizam. [1]

### Qual problema resolve?
Quando uma classe tem vários comportamentos ou algoritmos que podem ser usados, e é necessário poder **alternar** entre eles em tempo de execução de forma limpa, sem modificar o código do cliente.

### Solução:
A solução envolve três componentes principais:
1.  **Contexto:** O objeto que mantém uma referência à estratégia atual e delega o trabalho a ela.
2.  **Estratégia (Interface):** Uma interface comum que define o método que todos os algoritmos devem implementar.
3.  **Estratégias Concretas:** As classes que implementam a interface, cada uma contendo uma versão diferente do algoritmo.

### UML (Propósito Geral):
O diagrama mostra que o `Contexto` (o objeto principal) utiliza a interface `Estrategia`, permitindo que ele mude dinamicamente entre as implementações (`EstrategiaA` e `EstrategiaB`).

```mermaid
classDiagram
    class Contexto {
        + set_estrategia(Estrategia)
        + executar_tarefa()
    }
    class Estrategia {
        <<Interface>>
        + realizar_operacao(a, b)
    }
    class EstrategiaA {
        + realizar_operacao(a, b)
    }
    class EstrategiaB {
        + realizar_operacao(a, b)
    }
    
    Contexto o-- Estrategia : contém
    Estrategia <|-- EstrategiaA
    Estrategia <|-- EstrategiaB
````
_____________________
#Exemplo de Aplicação: Múltiplas Formas de Envio

## Descrição do problema:
Um sistema de processamento de pedidos precisa calcular o custo de envio para um cliente. O custo varia dependendo da forma de envio escolhida (Expresso, Normal ou Econômico), e essa escolha pode mudar a qualquer momento.

## O problema da flexibilidade:
Se usássemos if/else no código de cálculo, seria necessário modificar a classe principal de ProcessadorPedidos toda vez que um novo método de envio fosse adicionado ou alterado.

## A solução com Strategy:
Cada forma de cálculo de custo de envio é encapsulada em uma classe separada (Estratégia). O ProcessadorPedidos (Contexto) recebe um objeto Estratégia e delega a ele o cálculo, permitindo a troca do algoritmo em tempo real.

### Implementação em Python

```` Python
from abc import ABC, abstractmethod

# 1. Interface da Estratégia (Strategy)
class EstrategiaEnvio(ABC):
    @abstractmethod
    def calcular_custo(self, peso: float) -> float:
        pass

# 2. Estratégias Concretas (Concrete Strategies)
class EnvioExpresso(EstrategiaEnvio):
    def calcular_custo(self, peso: float) -> float:
        # Fórmula: Taxa Fixa + (Peso * Custo Alto)
        return 15.0 + (peso * 3.50)

class EnvioNormal(EstrategiaEnvio):
    def calcular_custo(self, peso: float) -> float:
        # Fórmula: Taxa Média + (Peso * Custo Médio)
        return 8.0 + (peso * 1.50)

class EnvioEconomico(EstrategiaEnvio):
    def calcular_custo(self, peso: float) -> float:
        # Fórmula: Taxa Baixa + (Peso * Custo Baixo)
        return 3.0 + (peso * 0.75)

# 3. Contexto (Objeto que usa a Estratégia)
class ProcessadorPedidos:
    def __init__(self, estrategia: EstrategiaEnvio):
        self._estrategia = estrategia

    def set_estrategia(self, nova_estrategia: EstrategiaEnvio):
        """Permite que o cliente altere o algoritmo em tempo de execução."""
        self._estrategia = nova_estrategia

    def calcular_total_envio(self, peso: float):
        custo = self._estrategia.calcular_custo(peso)
        nome_estrategia = self._estrategia.__class__.__name__
        print(f"Cálculo de Envio ({nome_estrategia}): R$ {custo:.2f}")

# --- Demonstração ---
if __name__ == "__main__":
    print("--- Strategy: Múltiplas Formas de Envio ---")
    peso_pedido = 7.5  # Peso do pacote em kg

    # 1. Inicia com a estratégia Normal
    print("\n--- Modo Normal (Padrão) ---")
    pedido = ProcessadorPedidos(EnvioNormal())
    pedido.calcular_total_envio(peso_pedido)

    # 2. Cliente exige a estratégia mais rápida (Expresso)
    print("\n--- Alterando para Expresso (Tempo de Execução) ---")
    pedido.set_estrategia(EnvioExpresso())
    pedido.calcular_total_envio(peso_pedido)

    # 3. Cliente exige a estratégia mais barata (Econômico)
    print("\n--- Alterando para Econômico (Alternando o Algoritmo) ---")
    pedido.set_estrategia(EnvioEconomico())
    pedido.calcular_total_envio(peso_pedido)
````


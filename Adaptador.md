# 2. Padrão Estrutural: Adapter
## Objetivo geral:
Os padrões **Estruturais** descrevem como classes e objetos podem ser combinados para formar estruturas maiores e mais complexas. Eles se concentram na composição de classes e objetos, facilitando a organização e o design de sistemas grandes. [1]

## Observação:
O Padrão Adapter é um dos mais úteis em projetos de manutenção ou integração, pois permite a reutilização de código legado ou de bibliotecas externas sem a necessidade de reescrever o código cliente que as utiliza. [1]

## Adapter:
O Adapter é um padrão de projeto **Estrutural** que permite que objetos com interfaces incompatíveis trabalhem juntos. Atua como um **tradutor** entre duas interfaces diferentes. [1] 

### Qual Problema Resolve?
Fazer com que objetos com **interfaces incompatíveis** trabalhem em conjunto. Isso ocorre quando se tem um código cliente que espera métodos com nomes e parâmetros específicos, mas a classe que possui a funcionalidade desejada (o Adaptee) usa uma assinatura de método diferente.

### Solução:
O padrão define uma classe **Adapter** que herda (ou implementa) a interface que o Cliente espera (o `Target`). Dentro dessa classe Adapter, é mantida uma referência ao objeto incompatível (`Adaptee`), e o Adapter é responsável por **traduzir** as chamadas do Target para as chamadas do Adaptee.

### UML (Propósito Geral):
O diagrama mostra como o `Adapter` implementa a interface `Target` e se associa ao objeto `Adaptee` para fazer a tradução.
```mermaid
classDiagram
    class Target {
        <<Interface>>
        + enviar_notificacao()
    }
    class Cliente {
        + disparar_alerta(Target)
    }
    class Adaptee {
        + conectar_e_enviar()
    }
    class Adapter {
        + enviar_notificacao()
    }
    
    Target <|-- Adapter : Implementa Target
    Adapter o-- Adaptee : Associa (Traduz)
    Cliente o-- Target : Usa
````
____________
# Exemplo de Aplicação: Sistema de Notificação Legado

## Descrição do problema:
Um SistemaNotificacao moderno (o Cliente) foi desenvolvido para usar uma interface limpa (Notificador). No entanto, o único serviço de envio disponível é um serviço legado (AlertaLegado) que possui métodos com nomes e parâmetros incompatíveis.

## O problema da interface:
O Cliente espera o método enviar_notificacao(titulo, corpo), mas o serviço legado só oferece o método conectar_e_enviar(mensagem_completa, nivel_prioridade).

## A solução com Adapter:
O AlertaLegadoAdapter implementa a interface Notificador (o Target) e, em seu método enviar_notificacao, ele formata o titulo e o corpo em uma mensagem_completa e chama o método incompatível do serviço legado.

### Implementação em Python

```` Python
from abc import ABC, abstractmethod

# 1. Target (Interface que o Cliente Espera)
class Notificador(ABC):
    @abstractmethod
    def enviar_notificacao(self, titulo: str, corpo: str):
        pass

# 2. Cliente (Usa a Interface Target)
class SistemaNotificacao:
    def __init__(self, notificador: Notificador):
        self._notificador = notificador

    def disparar_alerta(self, titulo: str, mensagem: str):
        print(f"Sistema: Disparando notificação via {self._notificador.__class__.__name__}")
        self._notificador.enviar_notificacao(titulo, mensagem)

# 3. Adaptee (A Classe Incompatível/Legada)
class AlertaLegado:
    def conectar_e_enviar(self, mensagem_completa: str, nivel_prioridade: int):
        print(f"[ALERTA LEGADO] Nível {nivel_prioridade}: {mensagem_completa}")

# 4. Adapter (O Adaptador/Tradutor)
class AlertaLegadoAdapter(Notificador):
    def __init__(self, alerta_legado: AlertaLegado):
        self._alerta_legado = alerta_legado

    def enviar_notificacao(self, titulo: str, corpo: str):
        """
        Método de tradução: converte a chamada Target para a chamada Adaptee.
        """
        # Formatação dos dados para o formato que o Adaptee entende
        mensagem_completa = f"TÍTULO: {titulo} | {corpo}"
        nivel_prioridade = 3  # Nível de prioridade padronizado

        # Delega a chamada para o método incompatível do objeto legado
        self._alerta_legado.conectar_e_enviar(mensagem_completa, nivel_prioridade)

# --- Demonstração ---
if __name__ == "__main__":
    print("--- Adapter: Notificação Legada ---")

    # Criamos o objeto incompatível (Adaptee)
    servico_legado = AlertaLegado()

    # Criamos o Adaptador, passando o serviço legado
    adapter = AlertaLegadoAdapter(servico_legado)

    # O Cliente (SistemaNotificacao) usa o Adapter como se fosse um Notificador moderno
    sistema = SistemaNotificacao(adapter)

    # O sistema chama a interface esperada (enviar_notificacao), e o Adaptador traduz.
    sistema.disparar_alerta("Erro de Conexão", "Falha no banco de dados primário.")
````

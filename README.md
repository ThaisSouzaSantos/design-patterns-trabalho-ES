# design-patterns-trabalho-ES

Este repositório contém a implementação de três padrões de projeto essenciais (Criacional, Estrutural e Comportamental) em Python, conforme solicitado para o trabalho acadêmico.
## Oque são padões de projetos:
Padrões de projeto (design patterns) são soluções típicas para problemas comuns em projetos de software. Cada padrão é como uma planta de construção que você pode customizar para resolver um problema de projeto particular em seu código.[1]

## ⚠️ Nota de Referencia

O conteúdo conceitual e a base teórica para a explicação de cada padrão foram obtidos e referenciados a partir do catálogo online:

* ** [1] REFACTORING.GURU.** **Padrões de Projeto (Design Patterns)**. Disponível em: <https://refactoring.guru/pt-br/design-patterns>. Acesso em: 25 nov. 2025.
* ** [2] GOOGLE. Gemini (Modelo de linguagem grande). Resposta gerada à consulta "padrões de projeto, como implementar" e auxílio na geração de código em 25 de novembro de 2025.

---
## 1. Padrão Criacional: Singleton

### Qual Problema Resolve?
Garantir que uma classe tenha **apenas uma instância** e fornecer um ponto de acesso global e centralizado para essa instância. Isso é útil quando você precisa de um gerenciador único para um recurso, como configurações de sistema ou conexões de banco de dados.

### A solução
O padrão `Singleton` controla o processo de instanciação, impedindo a criação de novas instâncias após a primeira.

###  UML (Propósito Geral)
O diagrama UML de um Singleton é simples:

```mermaid
classDiagram
    class Singleton {
        - instance
        - __init__()
        + get_instance()
    }
    Singleton ..> Singleton : instância




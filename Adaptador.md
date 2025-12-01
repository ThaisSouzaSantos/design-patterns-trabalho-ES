# 2. Padrão Estrutural: Adapter (Adaptador)
## Objetivo geral:
Os padrões estruturais explicam como montar objetos e classes em estruturas maiores mas ainda mantendo essas estruturas flexíveis e eficientes. [1]

## Adapter:
O Adapter é um dos padrões mais intuitivos, pois ele resolve um problema que vemos no dia a dia com frequência: incompatibilidade de interfaces. [1]

## Qual Problema Resolve?
O Adapter permite que classes com interfaces incompatíveis trabalhem juntas. Pense em como você pluga um carregador de celular americano (dois pinos chatos) em uma tomada brasileira (três furos redondos).
O seu código espera uma interface (TomadaBrasileira).

O objeto que você tem (o código legado, de terceiros) oferece uma interface diferente (PlugueAmericano).

2. A Solução (O "Tradutor")
O padrão Adapter atua como um invólucro (wrapper) ou tradutor.

Ele recebe a chamada da interface esperada (TomadaBrasileira.ligar()).

Ele traduz essa chamada para a interface que o objeto incompatível realmente usa (PlugueAmericano.fornecer_energia()).

O seu código cliente interage apenas com o Adaptador, sem precisar saber os detalhes da interface original incompatível.

# Locadora de Veículos

Projeto da **AS de Programação Orientada a Objetos** — um sistema de locação de carros e motos que permite consultar o preço de um aluguel, alugar e devolver veículos.

**Integrantes:** Nicolas Santiago e Lorenzo Rodrigues
**Linguagem:** Java 21+ · sem bibliotecas externas · execução pela `main`

## Vídeo de explicação do código

https://drive.google.com/file/d/1tHNGzeBO9Z_ZP_nyCkVDfzgHw68Dbh9-/view?usp=sharing

---

## Sobre o projeto

A locadora trabalha com dois tipos de veículo, que compartilham os mesmos dados e as mesmas regras de aluguel, mas cobram preços por fórmulas diferentes. O desafio do projeto é justamente esse: escrever as regras comuns **uma única vez** e deixar que cada tipo defina apenas o seu próprio cálculo — sem nenhum `if`, `switch` ou `instanceof` para descobrir com que tipo de veículo o programa está lidando.

## Estrutura

```
src/
├── Tarifavel.java   interface — o contrato "sei calcular um valor"
├── Veiculo.java     classe abstrata — dados, estado e regras comuns
├── Carro.java       classe concreta — fórmula do carro
├── Moto.java        classe concreta — fórmula da moto
└── Main.java        execução e verificação dos cenários
```

### Diagrama

```
         «interface»
          Tarifavel
      + calcularValor(dias)
               ▲
               ┆ implementa
          «abstract»
           Veiculo
   - codigo, modelo, valorDiaria, disponivel
   + alugar(), devolver(), alterarValorDiaria()
   + calcularValor(dias)   ← abstrato
               ▲
        ┌──────┴──────┐  herança
      Carro          Moto
```

## Como executar

**IntelliJ IDEA:** abra a pasta do projeto, marque `src` como *Sources* caso ainda não esteja, e rode `Main.main()`.

**Linha de comando:**

```bash
javac -d out src/*.java
java -cp out Main
```

## Regras de negócio

| Operação | Regra |
|---|---|
| Construção | Todo veículo nasce disponível |
| `alugar()` | Só aluga se estiver disponível. Retorna `true` na primeira vez e `false` se já estiver alugado |
| `devolver()` | Só devolve se estiver alugado. Retorna `true` na primeira vez e `false` se já estiver na garagem |
| `alterarValorDiaria(v)` | Aceita apenas valores maiores que zero. Caso contrário, mantém a diária e retorna `false` |
| Orçamento do carro | `dias × diária + R$ 20` de limpeza, cobrada uma única vez |
| Orçamento da moto | `dias × diária + R$ 5` de limpeza, cobrada uma única vez |
| Dias ≤ 0 | Retorna `0`, sem taxa |

Consultar preço **nunca** altera a disponibilidade do veículo.

| Exemplo | Resultado |
|---|---|
| Carro (diária 100) por 3 dias | R$ 320 |
| Moto (diária 50) por 3 dias | R$ 155 |
| Carro com diária alterada para 120, por 3 dias | R$ 380 |

## Conceitos de POO aplicados

| Conceito | Onde está | Por quê |
|---|---|---|
| **Encapsulamento** | Atributos `private` em `Veiculo`, apenas getters | O objeto controla o próprio estado: sem `setDisponivel`, ninguém aluga duas vezes o mesmo carro por fora |
| **Herança** | `Carro` e `Moto` `extends Veiculo` | Passam no teste do "é um". Dados e regras de aluguel ficam escritos uma vez só |
| **Classe abstrata** | `Veiculo` | "Veículo" genérico não existe no pátio — e sem fórmula de preço o objeto estaria incompleto, então a classe não pode ser instanciada |
| **Interface** | `Tarifavel` | Um contrato de capacidade: qualquer coisa que saiba calcular um valor entra, mesmo sem ser um veículo |
| **Polimorfismo** | `mostrarOrcamento(Tarifavel item, int dias)` | A mesma linha `item.calcularValor(dias)` executa a fórmula do carro ou a da moto, decidida pelo objeto real em tempo de execução |

## Saída

A `main` percorre cinco cenários e imprime **40 verificações**, todas com `PASSOU`:

```
=== 2. ORCAMENTOS E POLIMORFISMO ===
PASSOU: Carro: 3 dias = 320
PASSOU: Moto: 3 dias = 155
Orcamento (3 dias): R$ 320,00
Orcamento (3 dias): R$ 155,00
PASSOU: Contrato Tarifavel: carro
PASSOU: Contrato Tarifavel: moto
PASSOU: Orcamentos preservam estado inicial
```

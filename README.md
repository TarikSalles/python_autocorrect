# Corretor Automático de Python Baseado em Distância de Edição


Utilizando conceitos como **Distância de Damerau-Levenshtein**, tokenização nativa do Python e extração de vocabulário via `keyword` e `builtins`, tokens de um arquivo `.py` são analisados e corrigidos automaticamente. O corretor é capaz de identificar erros em keywords, builtins e até variáveis definidas pelo próprio usuário no código.

---

## Instalação

Clone o repositório:

```bash
git clone "https://github.com/TarikSalles/python_autocorrect"
```

Instale as dependências:

```bash
pip install -r requirements.txt
```

---

## Fundamento Matemático

A correção é baseada na **Distância de Damerau-Levenshtein**, que mede o número mínimo de operações para transformar uma palavra em outra:

- **Insert** — inserir um caractere
- **Delete** — remover um caractere
- **Replace** — substituir um caractere
- **Transpose** — trocar dois caracteres adjacentes de posição


![Distancia "imt" para "int"](images/distance_imt_int.png)

A distância é calculada via programação dinâmica em uma matriz `(m+1) x (n+1)`:

```
dp[i][j] = custo mínimo para transformar s1[:i] em s2[:j]
```

Em caso de empate na distância, a palavra com maior frequência no próprio código é priorizada


---

## Pipeline

O corretor funciona da seguinte forma:

1. O **vocabulário base** é construído com keywords e builtins do Python via `keyword.kwlist` e `dir(builtins)`.

2. O arquivo `.py` é **tokenizado** usando o módulo nativo `tokenize`, registrando também a frequência de cada token.

3. **Variáveis, parâmetros e nomes de funções** definidos pelo usuário são extraídos e adicionados ao vocabulário.

4. Para cada token, são gerados **candidatos a distância n (considerado sempre 2)** aplicando as 4 transformações em todas as posições possíveis.

5. Os candidatos são **filtrados pelo vocabulário** e o de menor distância é escolhido como correção.

6. O resultado é exibido com tokens mantidos em **verde** e corrigidos em **vermelho**.

---

## Testes

Quatro cenários de erro são testados ao final do notebook:

- `teste_falha.py` — Builtin errado (`imt` -> `int`)
- `teste_falha2.py` — Variável errada (`var_aa` -> `var_a`)
- `teste_falha3.py` — Função errada (`somar` -> `soma`)
- `teste_falha4.py` — Múltiplos erros combinados


---

## Observações

Durante os testes foi observado que:

- Tokens de um único caractere (`(`, `)`, `+`, `=`) e números são ignorados pelo corretor intencionalmente.
- O corretor funciona melhor para erros de até 2 transformações, em que erros maiores geram candidatos demais e podem introduzir correções incorretas.
- Variáveis só são reconhecidas se definidas antes do erro no arquivo, ou seja, o corretor depende do próprio código como fonte de verdade.


![teste_falha.py](images/results1.png)

![teste_falha2.py](images/results2.png)

![teste_falha3.py](images/results3.png)

![teste_falha4.py](images/results4.png)

---

## Tecnologias Utilizadas

- Python
- NumPy
- Matplotlib
- Jupyter Notebook

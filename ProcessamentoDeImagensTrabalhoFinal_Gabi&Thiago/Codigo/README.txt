# Processamento de Imagens para Classificação de Frutas: Mamões vs Pimentas

**Nomes:** GABRIELA SAFIRA NEVES DE OLIVEIRA e  THIAGO MARCEL MESSIAS DE ANDRADE

---

## Introdução

Este trabalho apresenta o desenvolvimento de um pipeline de processamento digital de imagens voltado à detecção e classificação automática de sementes, com o objetivo específico de distinguir **mamões maduros**, caracterizados por sua coloração clara, de **pimentas**, que geralmente apresentam dimensões reduzidas e podem exibir tonalidades verdes ou vermelhas.  

O fluxo de processamento estrutura-se em etapas sequenciais que envolvem:

- Segmentação por intensidade  
- Detecção de bordas por meio do operador de Sobel com diferentes níveis de limiarização  
- Rotulação de componentes conectados  
- Análise quantitativa de sobreposição entre regiões  
- Classificação final  

Ao final, cada componente identificado recebe a categoria de **mamão** ou **pimenta**, com base em sua consistência com as bordas extraídas da cena.

---

## Metodologia

### 1. Segmentação de Objetos Claros

A primeira etapa do pipeline consiste na obtenção de uma máscara binária que isole regiões da imagem correspondentes a possíveis frutas.

1. A imagem original em RGB é convertida para tons de cinza.  
2. Os níveis de intensidade são invertidos.  
3. Aplica-se um limiar global definido como um terço do valor máximo da imagem invertida.  
4. Pixels com intensidade superior ao limiar são considerados primeiro plano.  
5. Aplicam-se operações morfológicas de fechamento seguido de abertura.

O resultado é uma máscara binária onde:
- `1` indica possíveis frutas  
- `0` corresponde ao fundo  

---

### 2. Rotulação de Componentes Conectados

Aplica-se um algoritmo de flood fill com conectividade 4, que:

- Percorre a imagem  
- Atribui um identificador único a cada conjunto de pixels conectados  

O algoritmo retorna:
- A imagem rotulada  
- O número total de componentes  
- Um vetor com o tamanho de cada componente  

---

### 3. Detecção de Bordas com Múltiplos Limiares

A imagem em escala de cinza é processada com o operador de Sobel para calcular a magnitude do gradiente.

São definidos dois limiares:

- **Limiar alto:** 1/5 do valor máximo da magnitude do gradiente  
- **Limiar baixo:** 1/15 do valor máximo  

Ambas as máscaras passam por fechamento e abertura para redução de ruídos.

---

### 4. Análise de Sobreposição

Para cada componente, calcula-se:

Taxa de Sobreposição = (Pixels Sobrepostos) / (Tamanho do Componente)

Essa métrica indica o quanto o contorno do objeto coincide com bordas detectadas.

---

### 5. Seleção e Classificação

1. Ordenam-se os componentes por taxa de sobreposição.  
2. Selecionam-se os 7 maiores valores para cada limiar.  
3. A interseção entre esses dois conjuntos corresponde aos mamões.  

Os demais componentes são classificados como pimentas.

---

### 6. Visualização

- Componentes classificados como mamões → azul  
- Componentes classificados como pimentas → vermelho  

A função principal exibe a imagem final e retorna um dicionário com:
- Lista de mamões  
- Lista de pimentas  
- Imagem rotulada  
- Imagem colorida  

---

## Funções Principais

| Função | Propósito |
|--------|-----------|
| segment_light_objects | Máscara binária de regiões claras |
| label_components | Atribui IDs únicos |
| sobel_edges_threshold_1_5 / 1_15 | Máscaras de borda |
| overlap_ratios_per_component | Calcula taxa de sobreposição |
| select_components_by_overlap | Identifica mamões |
| classify_components | Gera imagem colorida |
| count_and_classify_fruits | Executa o pipeline completo |

---

## Obervações

Após o plot das imagens de teste, optamos por deixar todo o código desenvolvido e prototipado algo longo do desenvolvimento desse algoritmo com o intuito do professor poder entender nosso processo de raciocinio até chegar no resultado, para separar a parte funcional do código da de prototipação foi marcado por meio de comentario uma divisão do que é prototipação do que é o código de fato.

## Resultados

O pipeline foi testado em 20 fotografias contendo mamões e pimentas.

A contagem de mamões geralmente fica entre 4 e 7, enquanto a de pimentas é significativamente maior.

Observou-se que, na maioria dos casos, o algoritmo identifica corretamente 4 ou mais dos 6 mamões. Eventualmente, uma pimenta pode ser classificada como mamão devido à similaridade de formato.



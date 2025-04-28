
## OSMNX

É uma biblioteca para trabalhar com dados do mapa OpenStreetMap

```python
import sys
sys.path.insert(0, r'../../../quetzal')
```
Esta linha de comando serve importar arquivos Python (.py) ou pacotes que estão fora do diretório atual.

```python
import geopandas as gpd
from shapely import geometry
import osmnx as ox
import geopandas as gpd
import os
```
Estas linhas de comando importam bibliotecas que serão utlizadas :
- **GeoPandas**: Trabalha com dados geográficos.
- **Shapely**: Cria e manipula objetos geométricos.
- **OSMnx**: Baixar, manipular e analisar dados de mapas do OpenStreetMap (OSM).
- **Os**: Serve para interagir com o sistema operacional.

 ```python
from syspy.spatial.graph.graphbuilder import GraphBuilder, OsmnxCleaner
from syspy.spatial.graph import graphbuilder
from quetzal.model import stepmodel
```
Estas linhas de comando importam:
- Duas classes do módulo *graphbuilder*, que faz parte do pacote *syspy*:
  + **GraphBuilder**: Usada para criar grafos espaciais.
  + **OsmnxCleaner**: Limpa e prepara dados baixados com OSMnx.
-  O módulo inteiro *graphbuilder*.
- O módulo *stepmodel*, que faz parte do pacote *quetzal*, voltado para modelagem de transporte.

 ```python
training_folder = '../../'
input_folder = training_folder + r'inputs/'
```
Esta linha de comando realiza organização dos arquivos e pastas em projetos Python.
- **'../../'**: Este comomando volta duas pastas.
```python
%matplotlib inline
import matplotlib.pyplot as plt
plt.rcParams['figure.figsize'] = [16, 9]
```
Esta linha de comando plota um gráfico.
- **%matplotlib inline**: Mostra os gráficos diretamente dentro do notebook.
- **matplotlib**: É um módulo de plotagem para gráficos no Python.
- **plt.rcParams[]**:Altera um parâmetro global de configuração dos gráficos.
- **'figure.figsize'**:Defini todos os tamanhos dos gráficos.

### OSMNX API
É a interface de programação (API) oferecida pela biblioteca Python chamada *OSMnx*.
```python
zones = gpd.read_file(input_folder+'zones/zones.geojson', driver='GeoJSON')
zones.head(1)
```
Esta linha de comando lê arquivos espaciais.
- **gpd.read_file()**: Função do GeoPandas usada para ler arquivos espaciais
- **driver=**: Especifica o tipo de arquivo que está sendo lido.
```python
hull = geometry.MultiPolygon(list(zones['geometry'])).convex_hull
hull.bounds
```
Esta linha de comando traça um contorno formados pelas zonas
- **list()**: Transforma em uma lista de polígonos.
- **geometry.MultiPolygon(...)**: Combina todos os polígonos da lista em um único objeto MultiPolygon.
- **.convex_hull**: Calcula o envoltório convexo da geometria.
- **hull.bounds**: Retorna as coordenadas extremas.
```python
drive = ox.graph_from_polygon(hull.buffer(1e-6))
```
Nesta linha de comando baixa a rede viária e coloca no drive.
- **hull.buffer()**: Cria uma margem.
- **ox.graph_from_polygon(...)**: Baixa uma rede de ruas da OpenStreetMap, para dentro de uma área definida por um polígono.

```pyhton
ox.__version__
```
Essa linha do código verifica a versão instalada da biblioteca osmnx no seu ambiente Python.
- **__version__**: Verifica a versão que está instalada do pacote em Python.

```python
hull
```
Esta linha mostra o conteúdo do objeto *hull*

```python
plot = ox.plot_graph(drive, figsize=[20, 10], node_size=2)
```
Esta linha do comando desenha o grafo da rede de ruas salvo na variável *drive*, usando o *OSMnx*.
- **ox.plot_graph(...)**: Plota um grafo de ruas.
- **figsize=[...,...]**: Define o tamanho da figura
-  **node_size=**: Define o tamanho dos nós.

```python
road_nodes, road_links = ox.graph_to_gdfs(drive)
```
Esta linha de comando converte o grafo *drive* em dois GeoDataFrames.
- **ox.graph_to_gdfs(...)**: Transforma um grafo de ruas em dois objetos do GeoPandas.
- **road_nodes**: nós dos grafos.
- **road_links**: nós das linhas.

```python
road_links.index = 'osm_link_' + road_links['osmid'].astype(str)
road_nodes.index = 'osm_node_' + road_nodes['osmid'].astype(str)
```
Esta linha de comando renomeam os índices dos GeoDataFrames.Usando uns baseado nos IDs do OpenStreetMap (OSM).
- **.astype(str)**: Converte para um string.
- **.index =**: Redefine o índice do DataFrame para esses novos valores.

```python
road_links.rename(columns={'u': 'from', 'v': 'to'}, inplace=True)
road_nodes = road_nodes[['geometry']]
road_links[['from', 'to']] = 'osm_node_' + road_links[['from', 'to']].astype(str)
```
Esta linha de comando renomeia colunas de origem e destino ( para "from" e "to"), mantém as geometrias dos nós e atualiza os IDs dos nós nos links.
- **road_links.rename(...)**: Renomeia os nomes das colunas.
- **road_nodes[['geometry']]**: Mantém a geometria.
- **'osm_node_'**: Aplica o prefixos as colunas.

 ## Cleaning
```python
oc = OsmnxCleaner(
    road_links, 
    road_nodes, 
    a='from', 
    b='to'
)
```
Esta linha de comando cria um objeto *OsmnxCleaner* que limpa, corrigi e ajusta a rede viária extraída com o OSMnx.
















  

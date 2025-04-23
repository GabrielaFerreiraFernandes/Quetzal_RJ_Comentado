## Teste Quetzal

Esta biblioteca do python é usada para modelagem de transporte, redes urbanas ou análise de acessibilidade 

A primeira etapa é importar as bibliotecas :
- Pandas para dados tabulares,
- GeoPandas para dados espaciais,
- Shapely para operações geométricas avançadas,

```python
import pandas as pd
import geopandas as gpd
from shapely import ops, geometry

``` 
Carrega um arquivo CSV, shapes.txt, na pasta gtfs, que contém dados de shapes um arquivo GTFS:

```python
shapes = pd.read_csv(r'gtfs/shapes.txt')
```
Essa linha do código cria uma nova coluna chamada geometry no DataFrame shapes, para armazenar as geometrias dos pontos representados pelas coordenadas de latitude e longitude. 
- ***apply()*** aplica uma função
- ***lambda x:*** cria uma função anônima que faz algo com a linha x
- ***x['shape_pt_lon']*** e ***x['shape_pt_lat']*** são os valores de latitude e longitude
- ***geometry.Point():*** Cria um objeto geométrico do tipo ponto.

```python
shapes['geometry'] = shapes.apply(lambda x: geometry.Point(x['shape_pt_lon'], x['shape_pt_lat']), 1)
```
Esta linha do comando cria um GeoDataFrame a partir do DataFrame shapes,para trabalhar com dados geoespaciais no GeoPandas.
- ***gpd.GeoDataFrame():*** converte um DataFrame comum (do Pandas) em um GeoDataFrame. 
- ***shapes_geom.head():*** que retorna as primeiras 5 linhas do DataFrame.
  
```python
shapes_geom = gpd.GeoDataFrame(shapes)
shapes_geom.head()
```
A linha transforma os pontos das rotas em linhas, convertendo coordenadas sequenciais em uma rota geográfica contínua.
- ***shapes_geom.groupby()***: Agrupa todos os pontos por uma coluna espefícica.
- ***list(x)***: Transforma uma série de objeto pontos em uma linha.
- ***.apply(lambda x: geometry.LineString(list(x)))***: Cria um objeto *LineString*, que representa uma linha contínua conectando todos os pontos na ordem fornecida.

```python
shapes_lines = shapes_geom.groupby('shape_id')['geometry'].apply(lambda x: geometry.LineString(list(x)))
```
Esta linha de comando transforma essa série em um GeoDataFrame
- ***.reset_index()***:  Transforma a coluna indice em uma coluna normal

```python
to_export = gpd.GeoDataFrame(shapes_lines).reset_index()
to_export.head()
```
 Esta linha carrega o arquivo trips.txt das viagens da pasta GTFS

 ```python
 trips = pd.read_csv(r'gtfs/trips.txt')
trips.head()
```
Esta linha retoma o número de elementos dos DataFrames
```python
len(trips)
len(to_export)
```
Nesta linha junta os dois DataFrame de acordo com a coluna * shape_ID*
```python
to_export = to_export.merge(trips[['route_id', 'shape_id']], on='shape_id')
to_export.head()
```
- ***to_export.length***: Cria uma coluna com o comprimento de cada LineString
- ***to_export.sort_values***: Ordena do lenght do menor para maior
- ***gpd.GeoDataFrame(to_export_longest.groupby('route_id').first()).reset_index()***: Neste comando agrupa o route_id, pega a primeira ocorrência transforma em uma GeoDataFrame e reseta a coluna indice em uma coluna normal

```python
  to_export['length'] = to_export.length
  to_export_longest = to_export.sort_values('length')
  to_export_longest = gpd.GeoDataFrame(to_export_longest.groupby('route_id').first()).reset_index()
  to_export_longest.head()
```
Esta linhas de comando exportam os DataFrames para um shapefile:
- ***to_export.to_file('shp/all_shapes.shp')***: Exporta o arquivo com todas as viagens
- ***to_export_longest.to_file('shp/longest.shp')***: Exporta o arquivo com caminhos mais longos.

```python
to_export.to_file('shp/all_shapes.shp')
to_export_longest.to_file('shp/longest.shp')
```  



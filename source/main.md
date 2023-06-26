**Requirements**


```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
```

**Import data**


```python
centres = pd.read_csv('Centres_docents.csv', low_memory=False)
alumnes = pd.read_csv('Alumnes_matriculats.csv', low_memory=False)
centres_coords = pd.read_excel('centres_coords.xlsx')
```

**Data for Interactive Map**


```python
out = centres_coords.iloc[:,[0,1,3,5,26,27,28,29,30,31,32,24,25]]
out = out.astype({'Codi_centre':'str'})
out = out.apply(lambda x: x.str.replace(',','.'))
out = out.astype({'Coordenades_GEO_X':'float','Coordenades_GEO_Y':'float'})
out = out.rename(columns={'Denominació_completa': 'Nom', 
                          'Nom_naturalesa': 'Naturalesa',
                         'Nom_titularitat':'Titularitat',
                         'E-mail_centre': 'E-mail',
                         'Coordenades_GEO_X':'Longitud',
                         'Coordenades_GEO_Y':'Latitud'})
out = out.loc[out['Estudis'].str.contains("ESO|BATX|EINF2C|EPRI|EINF1C", case=False)]

out.fillna('No', inplace=True)
for i in ['ESO','BATX','EINF2C','EPRI','EINF1C']:
    out[i] = out[i].replace([i], 'Sí')

out = out.rename(columns={'EINF1C':'Infantil 1',
                         'EINF2C':'Infantil 2',
                         'EPRI':'Primària',
                         'BATX':'Batxillerat'})
out

```

Generate csv file 'out'


```python
out.to_csv('out.csv', index=False)
```

**Data for Proportional Chart**


```python
out2 = alumnes[alumnes['Any']==2022].iloc[:,[5,11,-7,-6,-5]]
out2 = out2.groupby(by=['Nom comarca','Nom naturalesa']).sum()
out2["Matrícules. Dones %"] = out2['Matrícules. Dones'] *100/ out2['Matrícules. Total']
out2["Matrícules. Homes %"] = out2['Matrícules. Homes'] *100/ out2['Matrícules. Total']
out2 = out2.iloc[:,1:]
out2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Matrícules. Dones</th>
      <th>Matrícules. Homes</th>
      <th>Matrícules. Dones %</th>
      <th>Matrícules. Homes %</th>
    </tr>
    <tr>
      <th>Nom comarca</th>
      <th>Nom naturalesa</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">Alt Camp</th>
      <th>Privat</th>
      <td>815</td>
      <td>734</td>
      <td>52.614590</td>
      <td>47.385410</td>
    </tr>
    <tr>
      <th>Públic</th>
      <td>2492</td>
      <td>2607</td>
      <td>48.872328</td>
      <td>51.127672</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Alt Empordà</th>
      <th>Privat</th>
      <td>1292</td>
      <td>1368</td>
      <td>48.571429</td>
      <td>51.428571</td>
    </tr>
    <tr>
      <th>Públic</th>
      <td>9135</td>
      <td>9306</td>
      <td>49.536359</td>
      <td>50.463641</td>
    </tr>
    <tr>
      <th>Alt Penedès</th>
      <th>Privat</th>
      <td>2082</td>
      <td>2067</td>
      <td>50.180766</td>
      <td>49.819234</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Val d'Aran</th>
      <th>Públic</th>
      <td>612</td>
      <td>603</td>
      <td>50.370370</td>
      <td>49.629630</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Vallès Occident</th>
      <th>Privat</th>
      <td>24565</td>
      <td>26169</td>
      <td>48.417297</td>
      <td>51.578761</td>
    </tr>
    <tr>
      <th>Públic</th>
      <td>47732</td>
      <td>49310</td>
      <td>49.185429</td>
      <td>50.811479</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Vallès Oriental</th>
      <th>Privat</th>
      <td>6510</td>
      <td>6750</td>
      <td>49.095023</td>
      <td>50.904977</td>
    </tr>
    <tr>
      <th>Públic</th>
      <td>24086</td>
      <td>25317</td>
      <td>48.754124</td>
      <td>51.245876</td>
    </tr>
  </tbody>
</table>
<p>79 rows × 4 columns</p>
</div>



Generate csv file 'out2'


```python
out2.to_csv('out2.csv', index=True)
```

**Data for Donut Chart**


```python
out3 = centres.iloc[:,[7,15]]
out3['count'] = [1]* len(out3)
out3 = out3.groupby(by=['Nom comarca','Nom titularitat']).sum()
out3
```

    C:\Users\Robert\AppData\Local\Temp\ipykernel_11200\107619191.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      out3['count'] = [1]* len(out3)
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>count</th>
    </tr>
    <tr>
      <th>Nom comarca</th>
      <th>Nom titularitat</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">Alt Camp</th>
      <th>Cooperatives</th>
      <td>3</td>
    </tr>
    <tr>
      <th>Corporacions Locals</th>
      <td>58</td>
    </tr>
    <tr>
      <th>Departament d'Educació</th>
      <td>93</td>
    </tr>
    <tr>
      <th>Fundacions</th>
      <td>6</td>
    </tr>
    <tr>
      <th>Ordes i Congregacions Catòlics</th>
      <td>3</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">Vallès Oriental</th>
      <th>Ordes i Congregacions Catòlics</th>
      <td>12</td>
    </tr>
    <tr>
      <th>Persones Físiques</th>
      <td>50</td>
    </tr>
    <tr>
      <th>Persones Jurídiques Estrangeres</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Societats Civils</th>
      <td>14</td>
    </tr>
    <tr>
      <th>Societats Mercantils</th>
      <td>83</td>
    </tr>
  </tbody>
</table>
<p>338 rows × 1 columns</p>
</div>



Generate csv file 'out3'


```python
out3.to_csv('out3.csv', index=True)
```

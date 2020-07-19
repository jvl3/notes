---
title: "Outlier Detection With Isolation Forests"
author: "Chris Albon"
date: 2019-12-20T11:53:49-07:00
description: "Outlier Detection With Isolation Forests Using Python."
type: technical_note
draft: false
---
An isolation forest is comprised of many of a special kind of tree. In each node the tree, a random feature is split in a random point (for example, if values in a feature range between 0 and 100, a random split might be 34). This process continues until every data point is isolated in its own brand. This is repeated until the end result is a forest of trees.

The intuition is that isolating a non-outlier data point will require many splits to be isolated because it is very similar to other data points. On the flip side, an outlier data point will require few splits to be isolated because it is very disimilar to the other data points.

## Preliminaries


```python
from sklearn.datasets import make_blobs
from sklearn.ensemble import IsolationForest
import numpy as np
```

## Create Data

`make_blobs` with a single center will create a single cluster of data.


```python
# Make the features (X) with 200 samples,
X, _ = make_blobs(n_samples = 300,
                  # two feature variables,
                  n_features = 2,
                  # three clusters,
                  centers = 1,
                  # with .5 cluster standard deviation,
                  cluster_std = 0.5,
                  # shuffled,
                  shuffle = True)

# View the two features
X
```




    array([[4.73967471, 7.72630304],
           [3.37828574, 8.01554621],
           [3.51365219, 8.78544516],
           [4.85330559, 9.33483891],
           [3.73981318, 8.52568663],
           [2.73191512, 8.71551403],
           [4.51654455, 8.99794452],
           [3.75681895, 9.79288515],
           [3.72099469, 8.21238069],
           [4.13375696, 8.82387981],
           [4.23132321, 9.13911734],
           [3.63499843, 7.52097065],
           [4.65571557, 8.81447377],
           [2.75505626, 7.89688485],
           [3.48769846, 8.39237723],
           [3.48109108, 7.84481044],
           [3.50481859, 8.96866248],
           [3.26077879, 8.85692956],
           [3.85754571, 8.98532986],
           [3.77492493, 8.78112782],
           [3.91109002, 8.26971091],
           [4.53992567, 8.34021788],
           [3.57704548, 8.60060707],
           [4.44272122, 8.21874564],
           [2.39049763, 8.63694227],
           [2.98074699, 7.56211626],
           [3.61656443, 8.7923086 ],
           [3.20115279, 8.3506277 ],
           [3.49554796, 8.87866493],
           [3.43283   , 8.10576015],
           [2.3669313 , 9.70385859],
           [3.1208632 , 8.08543923],
           [3.9207385 , 8.57329571],
           [2.77555961, 8.87670907],
           [3.87301865, 8.379548  ],
           [3.79469544, 7.5736419 ],
           [3.71648758, 8.45164823],
           [3.65107664, 9.5164288 ],
           [3.37041418, 8.42855027],
           [3.49707756, 8.8147657 ],
           [3.87167998, 8.61696541],
           [2.5929621 , 9.42503664],
           [3.54379395, 8.18618543],
           [3.16871763, 8.72601793],
           [3.6905635 , 8.500749  ],
           [4.08442292, 9.54499746],
           [3.95096895, 9.24822695],
           [3.92955314, 8.82955041],
           [2.68277223, 7.98684686],
           [4.12618488, 9.98475224],
           [2.91833423, 8.39050505],
           [3.07423043, 8.45213132],
           [3.78601021, 8.56475787],
           [2.80479205, 8.48991988],
           [4.01557426, 9.58731487],
           [4.13295821, 8.53335875],
           [3.7832038 , 8.55658321],
           [3.93094817, 8.23873847],
           [3.46490391, 9.11861619],
           [3.39147768, 9.0109381 ],
           [3.63584799, 7.8079236 ],
           [3.38169219, 9.32750492],
           [3.6169133 , 8.77408522],
           [3.3071831 , 9.21268904],
           [4.02423081, 8.30170962],
           [2.8514338 , 8.28903705],
           [3.04584011, 8.31932086],
           [3.84705924, 8.82252937],
           [4.390838  , 9.27950929],
           [4.39512517, 8.70885824],
           [3.95114183, 8.10220133],
           [3.37059719, 7.96060793],
           [2.77363613, 8.96504169],
           [2.56354557, 8.49093704],
           [2.81214439, 8.85197515],
           [3.73341561, 7.71510368],
           [3.61309437, 8.37355217],
           [3.40757906, 8.65397132],
           [2.7960017 , 7.59818744],
           [3.99731145, 7.9104029 ],
           [2.9432936 , 7.57798041],
           [3.15617813, 9.00021939],
           [3.15114496, 8.85184782],
           [5.00197885, 8.66937718],
           [3.5357447 , 7.67227057],
           [3.08841629, 9.25148861],
           [3.80483838, 8.43255201],
           [3.34756397, 8.61543553],
           [3.27047157, 8.87368393],
           [3.02609853, 9.10711819],
           [3.36422351, 8.35061231],
           [3.13390873, 7.78705338],
           [3.63031292, 8.39931159],
           [3.81411664, 8.33862601],
           [2.94032069, 8.48221438],
           [2.97798822, 8.60188831],
           [3.45643819, 8.21534724],
           [3.82696854, 7.98566692],
           [3.48879407, 8.48201188],
           [3.06936894, 8.57231662],
           [3.71257262, 7.91523984],
           [4.68580023, 7.25885383],
           [3.20050891, 8.45801619],
           [2.33836184, 8.50489586],
           [3.99524779, 9.46241105],
           [2.84811782, 9.47464361],
           [3.36905457, 8.3894873 ],
           [2.2495307 , 8.88892387],
           [3.01449492, 7.98411793],
           [3.72884361, 9.43421633],
           [3.00576244, 8.69703799],
           [3.4785553 , 7.70043887],
           [4.05069614, 9.19830162],
           [3.18678461, 9.52358053],
           [3.17129462, 8.95787198],
           [2.5826041 , 9.04653238],
           [3.85329198, 9.41451281],
           [4.115034  , 9.1695505 ],
           [3.36980049, 8.35821239],
           [3.31737026, 9.48336557],
           [4.11175111, 8.98546977],
           [2.64579491, 9.72724548],
           [3.55108935, 8.78199803],
           [2.64660229, 7.97224606],
           [2.51833277, 8.55224054],
           [3.297224  , 8.81521485],
           [3.49864347, 9.04244203],
           [3.52150918, 8.55028814],
           [3.29518723, 8.35507072],
           [4.41894911, 8.4122554 ],
           [3.47629638, 8.88124916],
           [3.2250218 , 8.87466106],
           [3.03859782, 8.10040056],
           [3.81563099, 8.98739426],
           [4.06625424, 8.66563599],
           [3.20341153, 8.51021692],
           [3.71070401, 9.02198456],
           [3.96972575, 7.52204473],
           [3.43337647, 9.3688374 ],
           [3.98037636, 8.84202745],
           [3.43350934, 7.82447012],
           [3.81700142, 8.81186286],
           [3.90284816, 8.85363956],
           [3.09698552, 8.00383768],
           [2.62897781, 9.04277047],
           [3.38209646, 8.35812588],
           [3.08121076, 7.88762085],
           [3.48324907, 9.02217727],
           [3.65683425, 8.12480577],
           [3.01991206, 8.47731646],
           [3.18283075, 8.9565682 ],
           [3.64305124, 8.4916415 ],
           [3.09709641, 8.02144707],
           [3.32853547, 8.11335803],
           [2.84906717, 8.50853598],
           [3.55882448, 8.95777533],
           [3.2925073 , 7.70671204],
           [3.37228946, 7.78214602],
           [4.78243067, 8.79591468],
           [2.69456708, 9.33772483],
           [2.60843642, 8.57306764],
           [2.35318212, 9.18976648],
           [2.7406211 , 8.12844767],
           [3.35907441, 8.05992092],
           [3.05522449, 7.97659422],
           [2.91468274, 9.10489297],
           [3.22248343, 8.15106971],
           [3.83079618, 8.37380105],
           [3.77958307, 8.39489255],
           [3.54078286, 8.8080521 ],
           [3.54015699, 8.76408202],
           [3.2179008 , 7.81493093],
           [2.32988094, 8.85134324],
           [2.77694463, 8.79199604],
           [3.80772646, 8.25511373],
           [3.68490599, 8.3163998 ],
           [3.78424733, 9.07293294],
           [3.20813228, 8.88382875],
           [3.12190871, 8.19024689],
           [3.76215041, 9.28638295],
           [3.03589066, 8.08338236],
           [3.11921461, 8.06560604],
           [3.37146008, 8.03470709],
           [2.70458161, 8.34962646],
           [3.62387302, 9.00300885],
           [3.54332453, 8.48048717],
           [3.07018162, 8.97210901],
           [3.40888736, 8.44964566],
           [3.04315017, 9.19773554],
           [2.888677  , 9.01745402],
           [2.43422834, 9.27504628],
           [3.78864304, 7.85722116],
           [4.06427054, 8.3481058 ],
           [3.25645896, 8.83125055],
           [2.81405704, 8.28574931],
           [3.70455436, 8.03008997],
           [2.65763227, 8.02834923],
           [3.77091013, 9.11772873],
           [2.87402525, 9.03119279],
           [3.91792576, 9.52898003],
           [2.60792705, 8.77854235],
           [3.56561214, 8.31265861],
           [3.11261978, 8.66894344],
           [3.33524968, 8.84192383],
           [2.34267152, 8.82964707],
           [3.27306874, 9.25307666],
           [3.42053018, 9.00409365],
           [3.34571972, 9.16525442],
           [3.01897019, 8.48209741],
           [4.01184332, 8.60822018],
           [3.47463061, 7.91482194],
           [4.02441736, 8.4329444 ],
           [3.62716072, 8.32153851],
           [2.95867089, 8.90806663],
           [3.48220594, 8.83993739],
           [2.30578881, 8.8044265 ],
           [4.21356677, 9.35879846],
           [4.43352362, 8.89969822],
           [3.41767995, 8.10855455],
           [3.04922012, 8.75737561],
           [4.41819542, 8.37667402],
           [3.45851623, 8.99870446],
           [3.66264742, 9.49184672],
           [3.72893654, 8.25037887],
           [3.17285517, 8.03949862],
           [3.67854346, 7.89485289],
           [3.19709836, 9.22383637],
           [5.00214862, 9.23430008],
           [3.35073656, 8.89511303],
           [2.26340968, 8.66990451],
           [3.8958047 , 8.55138479],
           [4.35215279, 8.43395012],
           [3.89616521, 8.4817067 ],
           [3.661724  , 8.89044649],
           [3.01189415, 9.08768175],
           [3.41930709, 9.12771841],
           [3.68230476, 8.75958992],
           [3.39538096, 8.70401094],
           [3.44881989, 8.61121639],
           [3.94825035, 8.83887341],
           [3.22123844, 9.20824031],
           [4.07205514, 8.12572992],
           [3.97902259, 7.92127468],
           [3.28457928, 8.38821358],
           [3.54316542, 8.65709044],
           [3.49075443, 9.18760104],
           [2.6257829 , 8.56658308],
           [3.73650384, 8.62416922],
           [4.2349426 , 8.86540638],
           [2.69318355, 9.35321761],
           [4.18171521, 8.58936147],
           [3.75758994, 8.74698573],
           [3.40861227, 8.6781448 ],
           [3.3308373 , 8.86854095],
           [3.61293963, 8.69961513],
           [3.65105548, 9.45415292],
           [3.14777699, 8.85473507],
           [3.27404481, 8.397613  ],
           [3.22443024, 9.10352939],
           [3.25175296, 9.06079171],
           [3.39666824, 8.92739224],
           [2.53754264, 8.49711159],
           [2.40663692, 9.01886235],
           [4.01108283, 7.61671322],
           [3.16485775, 8.82070618],
           [2.88536985, 8.93758159],
           [3.82992399, 8.32363517],
           [4.0586476 , 8.4660661 ],
           [3.71515529, 8.97801482],
           [3.21899802, 8.70198875],
           [4.88312744, 8.15748134],
           [3.40831635, 8.53576516],
           [3.44051414, 8.77839388],
           [3.81708681, 8.69984549],
           [4.16245265, 8.44276741],
           [3.7826444 , 8.68989164],
           [3.97244855, 8.39615738],
           [2.95338815, 8.22148971],
           [3.44430376, 8.78623906],
           [4.1408932 , 8.42956721],
           [3.62729166, 9.05999017],
           [3.31348462, 9.05986762],
           [4.15875368, 8.80956907],
           [3.19688581, 9.04242507],
           [4.42013444, 8.53555058],
           [3.56160485, 7.64863866],
           [3.76206042, 8.28257993],
           [2.97488449, 8.2078485 ],
           [4.09714847, 7.59261899],
           [3.652716  , 9.01573663],
           [4.20049707, 8.99185553],
           [2.68785583, 8.74278651],
           [2.01261853, 8.17145037],
           [2.96267412, 9.09581448],
           [3.81124499, 8.73981902],
           [3.16475154, 8.93440215],
           [3.86007326, 8.40278987],
           [3.20037869, 8.01631789],
           [3.74852215, 9.4031086 ],
           [3.65649213, 8.3902306 ]])



## Add Outlier To Data


```python
# Create an outlier data point
outlier = [[100,100]]

# Concat the outlier with the 
X_with_outlier = np.concatenate((outlier, X))
```

## Train Isolation Forest


```python
# Setup the isolation forest
clf = IsolationForest(# Randomly sample observations for each tree with replacement
                      bootstrap=True, 
                      # Number of trees
                      n_estimators=100,
                      # Unnecessary but added to avoid a deprecation error
                      contamination='auto', 
                      # Unnecessary but added to avoid a deprecation error
                      behaviour='new')

# Train the isolation forest
clf.fit(X_with_outlier)
```




    IsolationForest(behaviour='new', bootstrap=True, contamination='auto',
            max_features=1.0, max_samples='auto', n_estimators=100,
            n_jobs=None, random_state=None, verbose=0)



## Predict If New Data Point Is Outlier


```python
# Create a new data point that is an outlier
new_data_point = [[200,20]]
```


```python
# Predict if the new data point is an outlier (1 is not an outlier, -1 is an outlier)
clf.predict(new_data_point)
```




    array([-1])




```python
# Display the anomaly score for the new data point (lower is more abnormal)
clf.score_samples(new_data_point)
```




    array([-0.7477598])


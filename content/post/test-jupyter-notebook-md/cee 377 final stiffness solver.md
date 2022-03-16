---
title: Alan's Stiffness Solver
subtitle: A test jupyter notebook -> md

# Summary for listings and search engines
summary: md

# Link this post with a project
projects: []

# Date published
date: "2022-03-15T00:00:00Z"

# Date updated
lastmod: "2022-03-15T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/CpkOjOcXdUY)'
  focal_point: ""
  placement: 2
  preview_only: false

authors:
- admin
- 吳恩達

tags:
- Academic
- 开源

categories:
- Demo
- 教程
---


# Alan's Stiffness Solver!

This is (hopefully) a fully general stiffness solver for a truss or spring system. To test my work, I'm going to first walk through the system using the example from the lecture notes, then use the functions I've written to solve the stiffness problems on the final. 

Document contains:
- General stiffness solver (and the process of testing it using the truss example from our stiffness notes)
- Final problems 23 - end (just scroll all the way down or ctrl + f for "Problem 23" because it's a long way down)

First off, here's the syntax for defining points and connections:


```python
import matplotlib.pyplot as plot
import numpy as np
import pandas as pd
import math

#points
Q = np.array([(0.0,120.0),(180.0,120.0),(360.0,0.0), (180.0,0.0), (0.0,0.0)])

#boundary conditions,
P = [["undefined","undefined"], [0.0,0.0], [0.0,-20.0], [0.0,-20.0], ["undefined","undefined"]]
delta = [[0.0,0.0],["undefined", "undefined"], ["undefined", "undefined"], ["undefined", "undefined"], [0.0,0.0]]


#joint 1, joint 2, area, E, L, n, k = EA/L
A=4
E=29000
connections = [[(0, 1), A, E, 0, 0, 0], [(1, 2), A, E, 0, 0, 0], [(2, 3), A, E, 0, 0, 0],  [(3, 4), A, E, 0, 0, 0], 
               [(0, 3), A, E, 0, 0, 0], [(1, 4), A, E, 0, 0, 0], [(1, 3), A, E, 0, 0, 0]]


def formatBounds(P, delta):
    bounds = [[],[]]
    for p in P:
        for i in p:
            bounds[0].append(i)
    for d in delta:
        for i in d:
            bounds[1].append(i)
    return bounds

def drawTable(data, title=""):
    d = pd.DataFrame(data)
    d = d.style.set_caption(title)
    display(d)


#this draws a pretty picture (assuming that Q consists of points in 2d space, no clue what matplot would do with 3d vectors)
def drawConnections(connections):
    for connection in connections:
        a = connection[0][0]
        b = connection[0][1]
        if len(Q[a]) == 2:
            plot.plot([Q[a][0], Q[b][0]],[Q[a][1], Q[b][1]])
        elif len(Q[a]) == 1:
            plot.plot([Q[a][0],Q[b][0]],[0, 0])

bounds = formatBounds(P, delta)        
drawTable(bounds, "Boundary Conditions")
drawConnections(connections)


```


<style type="text/css">
</style>
<table id="T_c2b83_">
  <caption>Boundary Conditions</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_c2b83_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_c2b83_row0_col0" class="data row0 col0" >undefined</td>
      <td id="T_c2b83_row0_col1" class="data row0 col1" >undefined</td>
      <td id="T_c2b83_row0_col2" class="data row0 col2" >0.000000</td>
      <td id="T_c2b83_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_c2b83_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_c2b83_row0_col5" class="data row0 col5" >-20.000000</td>
      <td id="T_c2b83_row0_col6" class="data row0 col6" >0.000000</td>
      <td id="T_c2b83_row0_col7" class="data row0 col7" >-20.000000</td>
      <td id="T_c2b83_row0_col8" class="data row0 col8" >undefined</td>
      <td id="T_c2b83_row0_col9" class="data row0 col9" >undefined</td>
    </tr>
    <tr>
      <th id="T_c2b83_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_c2b83_row1_col0" class="data row1 col0" >0.000000</td>
      <td id="T_c2b83_row1_col1" class="data row1 col1" >0.000000</td>
      <td id="T_c2b83_row1_col2" class="data row1 col2" >undefined</td>
      <td id="T_c2b83_row1_col3" class="data row1 col3" >undefined</td>
      <td id="T_c2b83_row1_col4" class="data row1 col4" >undefined</td>
      <td id="T_c2b83_row1_col5" class="data row1 col5" >undefined</td>
      <td id="T_c2b83_row1_col6" class="data row1 col6" >undefined</td>
      <td id="T_c2b83_row1_col7" class="data row1 col7" >undefined</td>
      <td id="T_c2b83_row1_col8" class="data row1 col8" >0.000000</td>
      <td id="T_c2b83_row1_col9" class="data row1 col9" >0.000000</td>
    </tr>
  </tbody>
</table>




    
![png](output_1_1.png)
    


## Calculating properties of connections

This function calculates the lengths and normal vectors for each connection. 


```python
#calculate lengths and unit normal vectors
def computeNormals(connections):
    for x in range(0, len(connections)):
        c = connections[x]
        
        vector = np.subtract(Q[c[0][1]],Q[c[0][0]]) #create a vector that represents the connection
        
        #pythagorean theorem for any number of dimensions
        square_length = 0
        for i in vector:
            square_length += i**2
        length = math.sqrt(square_length)
        
        connections[x][3] = length
        
        #divide our connection vector by length to get a normal vector
        for i in range(0,len(vector)):
            vector[i] = vector[i]/length
            connections[x][4] = vector
    return connections
    
connections = computeNormals(connections)
drawTable(connections, "Connections")
```


<style type="text/css">
</style>
<table id="T_b8edd_">
  <caption>Connections</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_b8edd_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_b8edd_row0_col0" class="data row0 col0" >(0, 1)</td>
      <td id="T_b8edd_row0_col1" class="data row0 col1" >4</td>
      <td id="T_b8edd_row0_col2" class="data row0 col2" >29000</td>
      <td id="T_b8edd_row0_col3" class="data row0 col3" >180.000000</td>
      <td id="T_b8edd_row0_col4" class="data row0 col4" >[1. 0.]</td>
      <td id="T_b8edd_row0_col5" class="data row0 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_b8edd_row1_col0" class="data row1 col0" >(1, 2)</td>
      <td id="T_b8edd_row1_col1" class="data row1 col1" >4</td>
      <td id="T_b8edd_row1_col2" class="data row1 col2" >29000</td>
      <td id="T_b8edd_row1_col3" class="data row1 col3" >216.333077</td>
      <td id="T_b8edd_row1_col4" class="data row1 col4" >[ 0.83205029 -0.5547002 ]</td>
      <td id="T_b8edd_row1_col5" class="data row1 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_b8edd_row2_col0" class="data row2 col0" >(2, 3)</td>
      <td id="T_b8edd_row2_col1" class="data row2 col1" >4</td>
      <td id="T_b8edd_row2_col2" class="data row2 col2" >29000</td>
      <td id="T_b8edd_row2_col3" class="data row2 col3" >180.000000</td>
      <td id="T_b8edd_row2_col4" class="data row2 col4" >[-1.  0.]</td>
      <td id="T_b8edd_row2_col5" class="data row2 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_b8edd_row3_col0" class="data row3 col0" >(3, 4)</td>
      <td id="T_b8edd_row3_col1" class="data row3 col1" >4</td>
      <td id="T_b8edd_row3_col2" class="data row3 col2" >29000</td>
      <td id="T_b8edd_row3_col3" class="data row3 col3" >180.000000</td>
      <td id="T_b8edd_row3_col4" class="data row3 col4" >[-1.  0.]</td>
      <td id="T_b8edd_row3_col5" class="data row3 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_b8edd_row4_col0" class="data row4 col0" >(0, 3)</td>
      <td id="T_b8edd_row4_col1" class="data row4 col1" >4</td>
      <td id="T_b8edd_row4_col2" class="data row4 col2" >29000</td>
      <td id="T_b8edd_row4_col3" class="data row4 col3" >216.333077</td>
      <td id="T_b8edd_row4_col4" class="data row4 col4" >[ 0.83205029 -0.5547002 ]</td>
      <td id="T_b8edd_row4_col5" class="data row4 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_b8edd_row5_col0" class="data row5 col0" >(1, 4)</td>
      <td id="T_b8edd_row5_col1" class="data row5 col1" >4</td>
      <td id="T_b8edd_row5_col2" class="data row5 col2" >29000</td>
      <td id="T_b8edd_row5_col3" class="data row5 col3" >216.333077</td>
      <td id="T_b8edd_row5_col4" class="data row5 col4" >[-0.83205029 -0.5547002 ]</td>
      <td id="T_b8edd_row5_col5" class="data row5 col5" >0</td>
    </tr>
    <tr>
      <th id="T_b8edd_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_b8edd_row6_col0" class="data row6 col0" >(1, 3)</td>
      <td id="T_b8edd_row6_col1" class="data row6 col1" >4</td>
      <td id="T_b8edd_row6_col2" class="data row6 col2" >29000</td>
      <td id="T_b8edd_row6_col3" class="data row6 col3" >120.000000</td>
      <td id="T_b8edd_row6_col4" class="data row6 col4" >[ 0. -1.]</td>
      <td id="T_b8edd_row6_col5" class="data row6 col5" >0</td>
    </tr>
  </tbody>
</table>



## Calculating stiffness matrix



```python
#calculate stiffness matrix
def calcStiffness(connections):
    # if no predefined stiffness value, calculate stiffness using EA/L. This is basically a workaround so this works for springs
    for c in connections:
        if not c[5]:
            c[5] = c[1]*c[2]/c[3]
    return connections

def calcStiffnessMatrix(connections, Q):
    #calculate the stiffness matrix using the connections, stiffnesses, nodes of truss
    connections = calcStiffness(connections)
    
    k = np.zeros((len(Q)*len(Q[0]), len(Q)*len(Q[0]))) #initialize empty matrix of correct size for k

    spring_matrix = [[1, -1], [-1, 1]] #this will be multiplied by normal vector to get matrix elements

    for c in connections:
        s = c[5] *np.outer(c[4], np.matrix.transpose(c[4])) #stiffness * n * transpose(n)
        target =  [len(Q[0])*c[0][0],len(Q[0])*c[0][1]] # where each element of the stiffness we just calculated should end up
        
        for i in target: #for location in target, add the relevant stiffness value to the k matrix
            for j in target:
                ss = s
                if i!=j:
                    ss = ss * -1

                for l in range(0,len(s)):
                    for m in range(0,len(s)):
                        k[i+l][j+m] = k[i+l][j+m] + ss[l][m] #yuck
    return k
                        
k = calcStiffnessMatrix(connections, Q)
drawTable(k, "Stiffness Matrix")
```


<style type="text/css">
</style>
<table id="T_74038_">
  <caption>Stiffness Matrix</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_74038_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_74038_row0_col0" class="data row0 col0" >1015.666883</td>
      <td id="T_74038_row0_col1" class="data row0 col1" >-247.481626</td>
      <td id="T_74038_row0_col2" class="data row0 col2" >-644.444444</td>
      <td id="T_74038_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_74038_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_74038_row0_col5" class="data row0 col5" >0.000000</td>
      <td id="T_74038_row0_col6" class="data row0 col6" >-371.222439</td>
      <td id="T_74038_row0_col7" class="data row0 col7" >247.481626</td>
      <td id="T_74038_row0_col8" class="data row0 col8" >0.000000</td>
      <td id="T_74038_row0_col9" class="data row0 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_74038_row1_col0" class="data row1 col0" >-247.481626</td>
      <td id="T_74038_row1_col1" class="data row1 col1" >164.987751</td>
      <td id="T_74038_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_74038_row1_col3" class="data row1 col3" >0.000000</td>
      <td id="T_74038_row1_col4" class="data row1 col4" >0.000000</td>
      <td id="T_74038_row1_col5" class="data row1 col5" >0.000000</td>
      <td id="T_74038_row1_col6" class="data row1 col6" >247.481626</td>
      <td id="T_74038_row1_col7" class="data row1 col7" >-164.987751</td>
      <td id="T_74038_row1_col8" class="data row1 col8" >0.000000</td>
      <td id="T_74038_row1_col9" class="data row1 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_74038_row2_col0" class="data row2 col0" >-644.444444</td>
      <td id="T_74038_row2_col1" class="data row2 col1" >0.000000</td>
      <td id="T_74038_row2_col2" class="data row2 col2" >1386.889322</td>
      <td id="T_74038_row2_col3" class="data row2 col3" >0.000000</td>
      <td id="T_74038_row2_col4" class="data row2 col4" >-371.222439</td>
      <td id="T_74038_row2_col5" class="data row2 col5" >247.481626</td>
      <td id="T_74038_row2_col6" class="data row2 col6" >0.000000</td>
      <td id="T_74038_row2_col7" class="data row2 col7" >0.000000</td>
      <td id="T_74038_row2_col8" class="data row2 col8" >-371.222439</td>
      <td id="T_74038_row2_col9" class="data row2 col9" >-247.481626</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_74038_row3_col0" class="data row3 col0" >0.000000</td>
      <td id="T_74038_row3_col1" class="data row3 col1" >0.000000</td>
      <td id="T_74038_row3_col2" class="data row3 col2" >0.000000</td>
      <td id="T_74038_row3_col3" class="data row3 col3" >1296.642168</td>
      <td id="T_74038_row3_col4" class="data row3 col4" >247.481626</td>
      <td id="T_74038_row3_col5" class="data row3 col5" >-164.987751</td>
      <td id="T_74038_row3_col6" class="data row3 col6" >0.000000</td>
      <td id="T_74038_row3_col7" class="data row3 col7" >-966.666667</td>
      <td id="T_74038_row3_col8" class="data row3 col8" >-247.481626</td>
      <td id="T_74038_row3_col9" class="data row3 col9" >-164.987751</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_74038_row4_col0" class="data row4 col0" >0.000000</td>
      <td id="T_74038_row4_col1" class="data row4 col1" >0.000000</td>
      <td id="T_74038_row4_col2" class="data row4 col2" >-371.222439</td>
      <td id="T_74038_row4_col3" class="data row4 col3" >247.481626</td>
      <td id="T_74038_row4_col4" class="data row4 col4" >1015.666883</td>
      <td id="T_74038_row4_col5" class="data row4 col5" >-247.481626</td>
      <td id="T_74038_row4_col6" class="data row4 col6" >-644.444444</td>
      <td id="T_74038_row4_col7" class="data row4 col7" >0.000000</td>
      <td id="T_74038_row4_col8" class="data row4 col8" >0.000000</td>
      <td id="T_74038_row4_col9" class="data row4 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_74038_row5_col0" class="data row5 col0" >0.000000</td>
      <td id="T_74038_row5_col1" class="data row5 col1" >0.000000</td>
      <td id="T_74038_row5_col2" class="data row5 col2" >247.481626</td>
      <td id="T_74038_row5_col3" class="data row5 col3" >-164.987751</td>
      <td id="T_74038_row5_col4" class="data row5 col4" >-247.481626</td>
      <td id="T_74038_row5_col5" class="data row5 col5" >164.987751</td>
      <td id="T_74038_row5_col6" class="data row5 col6" >0.000000</td>
      <td id="T_74038_row5_col7" class="data row5 col7" >0.000000</td>
      <td id="T_74038_row5_col8" class="data row5 col8" >0.000000</td>
      <td id="T_74038_row5_col9" class="data row5 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_74038_row6_col0" class="data row6 col0" >-371.222439</td>
      <td id="T_74038_row6_col1" class="data row6 col1" >247.481626</td>
      <td id="T_74038_row6_col2" class="data row6 col2" >0.000000</td>
      <td id="T_74038_row6_col3" class="data row6 col3" >0.000000</td>
      <td id="T_74038_row6_col4" class="data row6 col4" >-644.444444</td>
      <td id="T_74038_row6_col5" class="data row6 col5" >0.000000</td>
      <td id="T_74038_row6_col6" class="data row6 col6" >1660.111328</td>
      <td id="T_74038_row6_col7" class="data row6 col7" >-247.481626</td>
      <td id="T_74038_row6_col8" class="data row6 col8" >-644.444444</td>
      <td id="T_74038_row6_col9" class="data row6 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_74038_row7_col0" class="data row7 col0" >247.481626</td>
      <td id="T_74038_row7_col1" class="data row7 col1" >-164.987751</td>
      <td id="T_74038_row7_col2" class="data row7 col2" >0.000000</td>
      <td id="T_74038_row7_col3" class="data row7 col3" >-966.666667</td>
      <td id="T_74038_row7_col4" class="data row7 col4" >0.000000</td>
      <td id="T_74038_row7_col5" class="data row7 col5" >0.000000</td>
      <td id="T_74038_row7_col6" class="data row7 col6" >-247.481626</td>
      <td id="T_74038_row7_col7" class="data row7 col7" >1131.654417</td>
      <td id="T_74038_row7_col8" class="data row7 col8" >0.000000</td>
      <td id="T_74038_row7_col9" class="data row7 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_74038_row8_col0" class="data row8 col0" >0.000000</td>
      <td id="T_74038_row8_col1" class="data row8 col1" >0.000000</td>
      <td id="T_74038_row8_col2" class="data row8 col2" >-371.222439</td>
      <td id="T_74038_row8_col3" class="data row8 col3" >-247.481626</td>
      <td id="T_74038_row8_col4" class="data row8 col4" >0.000000</td>
      <td id="T_74038_row8_col5" class="data row8 col5" >0.000000</td>
      <td id="T_74038_row8_col6" class="data row8 col6" >-644.444444</td>
      <td id="T_74038_row8_col7" class="data row8 col7" >0.000000</td>
      <td id="T_74038_row8_col8" class="data row8 col8" >1015.666883</td>
      <td id="T_74038_row8_col9" class="data row8 col9" >247.481626</td>
    </tr>
    <tr>
      <th id="T_74038_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_74038_row9_col0" class="data row9 col0" >0.000000</td>
      <td id="T_74038_row9_col1" class="data row9 col1" >0.000000</td>
      <td id="T_74038_row9_col2" class="data row9 col2" >-247.481626</td>
      <td id="T_74038_row9_col3" class="data row9 col3" >-164.987751</td>
      <td id="T_74038_row9_col4" class="data row9 col4" >0.000000</td>
      <td id="T_74038_row9_col5" class="data row9 col5" >0.000000</td>
      <td id="T_74038_row9_col6" class="data row9 col6" >0.000000</td>
      <td id="T_74038_row9_col7" class="data row9 col7" >0.000000</td>
      <td id="T_74038_row9_col8" class="data row9 col8" >247.481626</td>
      <td id="T_74038_row9_col9" class="data row9 col9" >164.987751</td>
    </tr>
  </tbody>
</table>



## Divide into submatrices

I'm pretty sure there's a more concise way to do this, but it works


```python
def calcSubmatrices(bounds, k):
    #figure out which loads/displacements are defined and make an array for each so we can refer to them
    defined_ss=[]
    for i in range(0,len(bounds[1])):
        if bounds[1][i]!="undefined":
            defined_ss.append(i)
    defined_ff=[]
    for i in range(0,len(bounds[0])):
        if bounds[0][i]!="undefined":
            defined_ff.append(i)

    # initialize four empyt matrices of the right size
    k_ff=np.zeros((len(defined_ff),len(defined_ff)))
    k_fs=np.zeros((len(defined_ff), len(defined_ss)))
    k_sf=np.zeros((len(defined_ss),len(defined_ff)))
    k_ss=np.zeros((len(defined_ss),len(defined_ss)))

    #divide load and displacement vectors into fixed and supported
    P_f = []
    delta_s = []
    for f in defined_ff:
        P_f.append(bounds[0][f])
    for s in defined_ss:
        delta_s.append(bounds[1][s])

    # for each submatrix in k, assign the correct values
    
    for i in range(0,len(defined_ff)):
        for j in range(0,len(defined_ff)):
            k_ff[i][j]=k[defined_ff[i]][defined_ff[j]]

    for i in range(0,len(defined_ss)):
        for j in range(0,len(defined_ss)):
            k_ss[i][j]=k[defined_ss[i]][defined_ss[j]]

    for i in range(0,len(defined_ss)):
        for j in range(0,len(defined_ff)):
            k_sf[i][j]=k[defined_ss[i]][defined_ff[j]]

    for i in range(0,len(defined_ff)):
        for j in range(0,len(defined_ss)):
            k_fs[i][j]=k[defined_ff[i]][defined_ss[j]]

    return [k_ff, k_fs, k_sf, k_ss, defined_ff, defined_ss, P_f, delta_s]

def drawSubs(k, subs):
    # nicely print out the submatrices
    drawTable(k, "k")
    drawTable(subs[0], "k_ff")
    drawTable(subs[1], "k_fs")
    drawTable(subs[2], "k_sf")
    drawTable(subs[3], "k_ss")

subs = calcSubmatrices(bounds, k)
drawSubs(k, subs)
```


<style type="text/css">
</style>
<table id="T_aab71_">
  <caption>k</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_aab71_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_aab71_row0_col0" class="data row0 col0" >1015.666883</td>
      <td id="T_aab71_row0_col1" class="data row0 col1" >-247.481626</td>
      <td id="T_aab71_row0_col2" class="data row0 col2" >-644.444444</td>
      <td id="T_aab71_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_aab71_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_aab71_row0_col5" class="data row0 col5" >0.000000</td>
      <td id="T_aab71_row0_col6" class="data row0 col6" >-371.222439</td>
      <td id="T_aab71_row0_col7" class="data row0 col7" >247.481626</td>
      <td id="T_aab71_row0_col8" class="data row0 col8" >0.000000</td>
      <td id="T_aab71_row0_col9" class="data row0 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_aab71_row1_col0" class="data row1 col0" >-247.481626</td>
      <td id="T_aab71_row1_col1" class="data row1 col1" >164.987751</td>
      <td id="T_aab71_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_aab71_row1_col3" class="data row1 col3" >0.000000</td>
      <td id="T_aab71_row1_col4" class="data row1 col4" >0.000000</td>
      <td id="T_aab71_row1_col5" class="data row1 col5" >0.000000</td>
      <td id="T_aab71_row1_col6" class="data row1 col6" >247.481626</td>
      <td id="T_aab71_row1_col7" class="data row1 col7" >-164.987751</td>
      <td id="T_aab71_row1_col8" class="data row1 col8" >0.000000</td>
      <td id="T_aab71_row1_col9" class="data row1 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_aab71_row2_col0" class="data row2 col0" >-644.444444</td>
      <td id="T_aab71_row2_col1" class="data row2 col1" >0.000000</td>
      <td id="T_aab71_row2_col2" class="data row2 col2" >1386.889322</td>
      <td id="T_aab71_row2_col3" class="data row2 col3" >0.000000</td>
      <td id="T_aab71_row2_col4" class="data row2 col4" >-371.222439</td>
      <td id="T_aab71_row2_col5" class="data row2 col5" >247.481626</td>
      <td id="T_aab71_row2_col6" class="data row2 col6" >0.000000</td>
      <td id="T_aab71_row2_col7" class="data row2 col7" >0.000000</td>
      <td id="T_aab71_row2_col8" class="data row2 col8" >-371.222439</td>
      <td id="T_aab71_row2_col9" class="data row2 col9" >-247.481626</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_aab71_row3_col0" class="data row3 col0" >0.000000</td>
      <td id="T_aab71_row3_col1" class="data row3 col1" >0.000000</td>
      <td id="T_aab71_row3_col2" class="data row3 col2" >0.000000</td>
      <td id="T_aab71_row3_col3" class="data row3 col3" >1296.642168</td>
      <td id="T_aab71_row3_col4" class="data row3 col4" >247.481626</td>
      <td id="T_aab71_row3_col5" class="data row3 col5" >-164.987751</td>
      <td id="T_aab71_row3_col6" class="data row3 col6" >0.000000</td>
      <td id="T_aab71_row3_col7" class="data row3 col7" >-966.666667</td>
      <td id="T_aab71_row3_col8" class="data row3 col8" >-247.481626</td>
      <td id="T_aab71_row3_col9" class="data row3 col9" >-164.987751</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_aab71_row4_col0" class="data row4 col0" >0.000000</td>
      <td id="T_aab71_row4_col1" class="data row4 col1" >0.000000</td>
      <td id="T_aab71_row4_col2" class="data row4 col2" >-371.222439</td>
      <td id="T_aab71_row4_col3" class="data row4 col3" >247.481626</td>
      <td id="T_aab71_row4_col4" class="data row4 col4" >1015.666883</td>
      <td id="T_aab71_row4_col5" class="data row4 col5" >-247.481626</td>
      <td id="T_aab71_row4_col6" class="data row4 col6" >-644.444444</td>
      <td id="T_aab71_row4_col7" class="data row4 col7" >0.000000</td>
      <td id="T_aab71_row4_col8" class="data row4 col8" >0.000000</td>
      <td id="T_aab71_row4_col9" class="data row4 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_aab71_row5_col0" class="data row5 col0" >0.000000</td>
      <td id="T_aab71_row5_col1" class="data row5 col1" >0.000000</td>
      <td id="T_aab71_row5_col2" class="data row5 col2" >247.481626</td>
      <td id="T_aab71_row5_col3" class="data row5 col3" >-164.987751</td>
      <td id="T_aab71_row5_col4" class="data row5 col4" >-247.481626</td>
      <td id="T_aab71_row5_col5" class="data row5 col5" >164.987751</td>
      <td id="T_aab71_row5_col6" class="data row5 col6" >0.000000</td>
      <td id="T_aab71_row5_col7" class="data row5 col7" >0.000000</td>
      <td id="T_aab71_row5_col8" class="data row5 col8" >0.000000</td>
      <td id="T_aab71_row5_col9" class="data row5 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_aab71_row6_col0" class="data row6 col0" >-371.222439</td>
      <td id="T_aab71_row6_col1" class="data row6 col1" >247.481626</td>
      <td id="T_aab71_row6_col2" class="data row6 col2" >0.000000</td>
      <td id="T_aab71_row6_col3" class="data row6 col3" >0.000000</td>
      <td id="T_aab71_row6_col4" class="data row6 col4" >-644.444444</td>
      <td id="T_aab71_row6_col5" class="data row6 col5" >0.000000</td>
      <td id="T_aab71_row6_col6" class="data row6 col6" >1660.111328</td>
      <td id="T_aab71_row6_col7" class="data row6 col7" >-247.481626</td>
      <td id="T_aab71_row6_col8" class="data row6 col8" >-644.444444</td>
      <td id="T_aab71_row6_col9" class="data row6 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_aab71_row7_col0" class="data row7 col0" >247.481626</td>
      <td id="T_aab71_row7_col1" class="data row7 col1" >-164.987751</td>
      <td id="T_aab71_row7_col2" class="data row7 col2" >0.000000</td>
      <td id="T_aab71_row7_col3" class="data row7 col3" >-966.666667</td>
      <td id="T_aab71_row7_col4" class="data row7 col4" >0.000000</td>
      <td id="T_aab71_row7_col5" class="data row7 col5" >0.000000</td>
      <td id="T_aab71_row7_col6" class="data row7 col6" >-247.481626</td>
      <td id="T_aab71_row7_col7" class="data row7 col7" >1131.654417</td>
      <td id="T_aab71_row7_col8" class="data row7 col8" >0.000000</td>
      <td id="T_aab71_row7_col9" class="data row7 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_aab71_row8_col0" class="data row8 col0" >0.000000</td>
      <td id="T_aab71_row8_col1" class="data row8 col1" >0.000000</td>
      <td id="T_aab71_row8_col2" class="data row8 col2" >-371.222439</td>
      <td id="T_aab71_row8_col3" class="data row8 col3" >-247.481626</td>
      <td id="T_aab71_row8_col4" class="data row8 col4" >0.000000</td>
      <td id="T_aab71_row8_col5" class="data row8 col5" >0.000000</td>
      <td id="T_aab71_row8_col6" class="data row8 col6" >-644.444444</td>
      <td id="T_aab71_row8_col7" class="data row8 col7" >0.000000</td>
      <td id="T_aab71_row8_col8" class="data row8 col8" >1015.666883</td>
      <td id="T_aab71_row8_col9" class="data row8 col9" >247.481626</td>
    </tr>
    <tr>
      <th id="T_aab71_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_aab71_row9_col0" class="data row9 col0" >0.000000</td>
      <td id="T_aab71_row9_col1" class="data row9 col1" >0.000000</td>
      <td id="T_aab71_row9_col2" class="data row9 col2" >-247.481626</td>
      <td id="T_aab71_row9_col3" class="data row9 col3" >-164.987751</td>
      <td id="T_aab71_row9_col4" class="data row9 col4" >0.000000</td>
      <td id="T_aab71_row9_col5" class="data row9 col5" >0.000000</td>
      <td id="T_aab71_row9_col6" class="data row9 col6" >0.000000</td>
      <td id="T_aab71_row9_col7" class="data row9 col7" >0.000000</td>
      <td id="T_aab71_row9_col8" class="data row9 col8" >247.481626</td>
      <td id="T_aab71_row9_col9" class="data row9 col9" >164.987751</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_f3f7f_">
  <caption>k_ff</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_f3f7f_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_f3f7f_row0_col0" class="data row0 col0" >1386.889322</td>
      <td id="T_f3f7f_row0_col1" class="data row0 col1" >0.000000</td>
      <td id="T_f3f7f_row0_col2" class="data row0 col2" >-371.222439</td>
      <td id="T_f3f7f_row0_col3" class="data row0 col3" >247.481626</td>
      <td id="T_f3f7f_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_f3f7f_row0_col5" class="data row0 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_f3f7f_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_f3f7f_row1_col0" class="data row1 col0" >0.000000</td>
      <td id="T_f3f7f_row1_col1" class="data row1 col1" >1296.642168</td>
      <td id="T_f3f7f_row1_col2" class="data row1 col2" >247.481626</td>
      <td id="T_f3f7f_row1_col3" class="data row1 col3" >-164.987751</td>
      <td id="T_f3f7f_row1_col4" class="data row1 col4" >0.000000</td>
      <td id="T_f3f7f_row1_col5" class="data row1 col5" >-966.666667</td>
    </tr>
    <tr>
      <th id="T_f3f7f_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_f3f7f_row2_col0" class="data row2 col0" >-371.222439</td>
      <td id="T_f3f7f_row2_col1" class="data row2 col1" >247.481626</td>
      <td id="T_f3f7f_row2_col2" class="data row2 col2" >1015.666883</td>
      <td id="T_f3f7f_row2_col3" class="data row2 col3" >-247.481626</td>
      <td id="T_f3f7f_row2_col4" class="data row2 col4" >-644.444444</td>
      <td id="T_f3f7f_row2_col5" class="data row2 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_f3f7f_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_f3f7f_row3_col0" class="data row3 col0" >247.481626</td>
      <td id="T_f3f7f_row3_col1" class="data row3 col1" >-164.987751</td>
      <td id="T_f3f7f_row3_col2" class="data row3 col2" >-247.481626</td>
      <td id="T_f3f7f_row3_col3" class="data row3 col3" >164.987751</td>
      <td id="T_f3f7f_row3_col4" class="data row3 col4" >0.000000</td>
      <td id="T_f3f7f_row3_col5" class="data row3 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_f3f7f_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_f3f7f_row4_col0" class="data row4 col0" >0.000000</td>
      <td id="T_f3f7f_row4_col1" class="data row4 col1" >0.000000</td>
      <td id="T_f3f7f_row4_col2" class="data row4 col2" >-644.444444</td>
      <td id="T_f3f7f_row4_col3" class="data row4 col3" >0.000000</td>
      <td id="T_f3f7f_row4_col4" class="data row4 col4" >1660.111328</td>
      <td id="T_f3f7f_row4_col5" class="data row4 col5" >-247.481626</td>
    </tr>
    <tr>
      <th id="T_f3f7f_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_f3f7f_row5_col0" class="data row5 col0" >0.000000</td>
      <td id="T_f3f7f_row5_col1" class="data row5 col1" >-966.666667</td>
      <td id="T_f3f7f_row5_col2" class="data row5 col2" >0.000000</td>
      <td id="T_f3f7f_row5_col3" class="data row5 col3" >0.000000</td>
      <td id="T_f3f7f_row5_col4" class="data row5 col4" >-247.481626</td>
      <td id="T_f3f7f_row5_col5" class="data row5 col5" >1131.654417</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_1128f_">
  <caption>k_fs</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_1128f_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_1128f_row0_col0" class="data row0 col0" >-644.444444</td>
      <td id="T_1128f_row0_col1" class="data row0 col1" >0.000000</td>
      <td id="T_1128f_row0_col2" class="data row0 col2" >-371.222439</td>
      <td id="T_1128f_row0_col3" class="data row0 col3" >-247.481626</td>
    </tr>
    <tr>
      <th id="T_1128f_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_1128f_row1_col0" class="data row1 col0" >0.000000</td>
      <td id="T_1128f_row1_col1" class="data row1 col1" >0.000000</td>
      <td id="T_1128f_row1_col2" class="data row1 col2" >-247.481626</td>
      <td id="T_1128f_row1_col3" class="data row1 col3" >-164.987751</td>
    </tr>
    <tr>
      <th id="T_1128f_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_1128f_row2_col0" class="data row2 col0" >0.000000</td>
      <td id="T_1128f_row2_col1" class="data row2 col1" >0.000000</td>
      <td id="T_1128f_row2_col2" class="data row2 col2" >0.000000</td>
      <td id="T_1128f_row2_col3" class="data row2 col3" >0.000000</td>
    </tr>
    <tr>
      <th id="T_1128f_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_1128f_row3_col0" class="data row3 col0" >0.000000</td>
      <td id="T_1128f_row3_col1" class="data row3 col1" >0.000000</td>
      <td id="T_1128f_row3_col2" class="data row3 col2" >0.000000</td>
      <td id="T_1128f_row3_col3" class="data row3 col3" >0.000000</td>
    </tr>
    <tr>
      <th id="T_1128f_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_1128f_row4_col0" class="data row4 col0" >-371.222439</td>
      <td id="T_1128f_row4_col1" class="data row4 col1" >247.481626</td>
      <td id="T_1128f_row4_col2" class="data row4 col2" >-644.444444</td>
      <td id="T_1128f_row4_col3" class="data row4 col3" >0.000000</td>
    </tr>
    <tr>
      <th id="T_1128f_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_1128f_row5_col0" class="data row5 col0" >247.481626</td>
      <td id="T_1128f_row5_col1" class="data row5 col1" >-164.987751</td>
      <td id="T_1128f_row5_col2" class="data row5 col2" >0.000000</td>
      <td id="T_1128f_row5_col3" class="data row5 col3" >0.000000</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_121bb_">
  <caption>k_sf</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_121bb_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_121bb_row0_col0" class="data row0 col0" >-644.444444</td>
      <td id="T_121bb_row0_col1" class="data row0 col1" >0.000000</td>
      <td id="T_121bb_row0_col2" class="data row0 col2" >0.000000</td>
      <td id="T_121bb_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_121bb_row0_col4" class="data row0 col4" >-371.222439</td>
      <td id="T_121bb_row0_col5" class="data row0 col5" >247.481626</td>
    </tr>
    <tr>
      <th id="T_121bb_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_121bb_row1_col0" class="data row1 col0" >0.000000</td>
      <td id="T_121bb_row1_col1" class="data row1 col1" >0.000000</td>
      <td id="T_121bb_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_121bb_row1_col3" class="data row1 col3" >0.000000</td>
      <td id="T_121bb_row1_col4" class="data row1 col4" >247.481626</td>
      <td id="T_121bb_row1_col5" class="data row1 col5" >-164.987751</td>
    </tr>
    <tr>
      <th id="T_121bb_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_121bb_row2_col0" class="data row2 col0" >-371.222439</td>
      <td id="T_121bb_row2_col1" class="data row2 col1" >-247.481626</td>
      <td id="T_121bb_row2_col2" class="data row2 col2" >0.000000</td>
      <td id="T_121bb_row2_col3" class="data row2 col3" >0.000000</td>
      <td id="T_121bb_row2_col4" class="data row2 col4" >-644.444444</td>
      <td id="T_121bb_row2_col5" class="data row2 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_121bb_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_121bb_row3_col0" class="data row3 col0" >-247.481626</td>
      <td id="T_121bb_row3_col1" class="data row3 col1" >-164.987751</td>
      <td id="T_121bb_row3_col2" class="data row3 col2" >0.000000</td>
      <td id="T_121bb_row3_col3" class="data row3 col3" >0.000000</td>
      <td id="T_121bb_row3_col4" class="data row3 col4" >0.000000</td>
      <td id="T_121bb_row3_col5" class="data row3 col5" >0.000000</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_b9f09_">
  <caption>k_ss</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_b9f09_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_b9f09_row0_col0" class="data row0 col0" >1015.666883</td>
      <td id="T_b9f09_row0_col1" class="data row0 col1" >-247.481626</td>
      <td id="T_b9f09_row0_col2" class="data row0 col2" >0.000000</td>
      <td id="T_b9f09_row0_col3" class="data row0 col3" >0.000000</td>
    </tr>
    <tr>
      <th id="T_b9f09_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_b9f09_row1_col0" class="data row1 col0" >-247.481626</td>
      <td id="T_b9f09_row1_col1" class="data row1 col1" >164.987751</td>
      <td id="T_b9f09_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_b9f09_row1_col3" class="data row1 col3" >0.000000</td>
    </tr>
    <tr>
      <th id="T_b9f09_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_b9f09_row2_col0" class="data row2 col0" >0.000000</td>
      <td id="T_b9f09_row2_col1" class="data row2 col1" >0.000000</td>
      <td id="T_b9f09_row2_col2" class="data row2 col2" >1015.666883</td>
      <td id="T_b9f09_row2_col3" class="data row2 col3" >247.481626</td>
    </tr>
    <tr>
      <th id="T_b9f09_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_b9f09_row3_col0" class="data row3 col0" >0.000000</td>
      <td id="T_b9f09_row3_col1" class="data row3 col1" >0.000000</td>
      <td id="T_b9f09_row3_col2" class="data row3 col2" >247.481626</td>
      <td id="T_b9f09_row3_col3" class="data row3 col3" >164.987751</td>
    </tr>
  </tbody>
</table>



## All the complex matrix algebra bits



```python
def calcDeltaR(subs, bounds):
    # take our big submatrix vector from earlier and separate it back out into components
    k_ff = subs[0]
    k_fs = subs[1]
    k_sf = subs[2]
    k_ss = subs[3]
    defined_ff = subs[4]
    defined_ss = subs[5]
    P_f = subs[6]
    delta_s = subs[7]
    
    
    A = np.asmatrix(k_ff)
    
    
    # inverse(k_ff) * (P_f - k_fs delta_s)
    delta_f = np.array(np.matmul(np.linalg.inv(A), P_f - np.matmul(k_fs, delta_s)))
    
    #shove that result into the bounds array so it can be reported
    for i in range(0, len(delta_f[0])):
        bounds[1][defined_ff[i]] = delta_f[0][i]
    
    # k_sf delta_f + k_ss delta_s
    R_s = np.array(np.transpose(np.add(np.matmul(k_sf, np.transpose(delta_f)), 
                              np.matmul(k_ss, np.matrix.transpose(np.asmatrix(delta_s))))))
    
    #report results
    for i in range(0, len(R_s[0])):
        bounds[0][defined_ss[i]] = R_s[0][i]

    return(bounds)


bounds = calcDeltaR(subs, bounds)
drawTable(bounds, "Bounds")
```


<style type="text/css">
</style>
<table id="T_6cfb8_">
  <caption>Bounds</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_6cfb8_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_6cfb8_row0_col0" class="data row0 col0" >-90.000000</td>
      <td id="T_6cfb8_row0_col1" class="data row0 col1" >20.000000</td>
      <td id="T_6cfb8_row0_col2" class="data row0 col2" >0.000000</td>
      <td id="T_6cfb8_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_6cfb8_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_6cfb8_row0_col5" class="data row0 col5" >-20.000000</td>
      <td id="T_6cfb8_row0_col6" class="data row0 col6" >0.000000</td>
      <td id="T_6cfb8_row0_col7" class="data row0 col7" >-20.000000</td>
      <td id="T_6cfb8_row0_col8" class="data row0 col8" >90.000000</td>
      <td id="T_6cfb8_row0_col9" class="data row0 col9" >20.000000</td>
    </tr>
    <tr>
      <th id="T_6cfb8_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_6cfb8_row1_col0" class="data row1 col0" >0.000000</td>
      <td id="T_6cfb8_row1_col1" class="data row1 col1" >0.000000</td>
      <td id="T_6cfb8_row1_col2" class="data row1 col2" >0.093103</td>
      <td id="T_6cfb8_row1_col3" class="data row1 col3" >-0.260876</td>
      <td id="T_6cfb8_row1_col4" class="data row1 col4" >-0.139655</td>
      <td id="T_6cfb8_row1_col5" class="data row1 col5" >-0.731235</td>
      <td id="T_6cfb8_row1_col6" class="data row1 col6" >-0.093103</td>
      <td id="T_6cfb8_row1_col7" class="data row1 col7" >-0.260876</td>
      <td id="T_6cfb8_row1_col8" class="data row1 col8" >0.000000</td>
      <td id="T_6cfb8_row1_col9" class="data row1 col9" >0.000000</td>
    </tr>
  </tbody>
</table>



## Calculate internal forces given displacements and the number of dimensions the problem exists in


```python
def calcInternalForces(connections, bounds, dimensions):
    f = [] # initialize a list of forces

    # for each connection, find the difference between its beginning and end points. 
    # Then dot that with the normal vector for that connection
    for c in connections:
        d = bounds[1]
        x_1 = d[c[0][0]*dimensions:c[0][0]*dimensions + dimensions]
        x_2 = d[c[0][1]*dimensions:c[0][1]*dimensions + dimensions]
        x_i = np.subtract(x_2, x_1)
        dot = np.dot(x_i, c[4])

        f.append([c[0], round(dot*c[5], 4)])
        
    return(f)
                 
f = calcInternalForces(connections, bounds, len(Q[0]))
drawTable(f, "internal forces")
```


<style type="text/css">
</style>
<table id="T_236c2_">
  <caption>internal forces</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_236c2_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_236c2_row0_col0" class="data row0 col0" >(0, 1)</td>
      <td id="T_236c2_row0_col1" class="data row0 col1" >60.000000</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_236c2_row1_col0" class="data row1 col0" >(1, 2)</td>
      <td id="T_236c2_row1_col1" class="data row1 col1" >36.055500</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_236c2_row2_col0" class="data row2 col0" >(2, 3)</td>
      <td id="T_236c2_row2_col1" class="data row2 col1" >-30.000000</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_236c2_row3_col0" class="data row3 col0" >(3, 4)</td>
      <td id="T_236c2_row3_col1" class="data row3 col1" >-60.000000</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_236c2_row4_col0" class="data row4 col0" >(0, 3)</td>
      <td id="T_236c2_row4_col1" class="data row4 col1" >36.055500</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_236c2_row5_col0" class="data row5 col0" >(1, 4)</td>
      <td id="T_236c2_row5_col1" class="data row5 col1" >-36.055500</td>
    </tr>
    <tr>
      <th id="T_236c2_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_236c2_row6_col0" class="data row6 col0" >(1, 3)</td>
      <td id="T_236c2_row6_col1" class="data row6 col1" >-0.000000</td>
    </tr>
  </tbody>
</table>



## Define a function to do all of that for me at once

Now that I have functions for each step of the process, I can just call them in order to analyze another truss. This function calculates the external reactions, displacements, and internal forces for any truss in any number of dimensions (at least hypothetically--I haven't tested for more than 2)


```python
def main(Q, P, delta, connections, draw_bounds = False, draw_stiffness = False, draw_connections = False, 
         draw_subs = False, draw_displacements=False, draw_forces = False):
    #make bounds easier to read for the computer, draw truss
    bounds = formatBounds(P, delta)
    if draw_bounds:
        drawTable(bounds, "Boundary Conditions")
        
    drawConnections(connections)

    #compute normals for connections
    connections = computeNormals(connections)

    #calculate stiffness matrix
    k = calcStiffnessMatrix(connections, Q)   
    if draw_stiffness:
        drawTable(k, "Stiffness Matrix")
    if draw_connections:
        drawTable(connections, "Connections")

    #calculate submatrices
    subs = calcSubmatrices(bounds, k)
    if draw_subs:
        drawSubs(k, subs)
        
    #drawTable(bounds, "Bounds")

    #calculate displacements
    displacements_reactions = calcDeltaR(subs, bounds)
    if draw_displacements:
        drawTable(displacements_reactions, "External Forces and Displacements")

    #calculate internal forces
    f = calcInternalForces(connections, displacements_reactions, len(Q[0]))
    if draw_forces:
        drawTable(f, "Internal Forces")
```

# Problem 23

Running the stiffness solver but giving each other member AE/L = 0 to isolate k matrix for D (this is a total hack but whatever)

the relevant values here is the copy of d's stiffness matrix in each corner of the k matrix (and also the normal vector in column 4 of the connection properties table)


```python
#nodes
Q = np.array([[0.0, 0.0],[-5.0*12, 5.0*12],[0.0, 5.0*12],[3.0*12, 5.0*12], [9.0*12, 5.0*12]])

#forces/displacements
P = [["undefined", "undefined"], ["undefined", "undefined"], 
     ["undefined", "undefined"], ["undefined", "undefined"], 
     ["undefined", "undefined"]]
delta = [[-.05, 0.0],[0.0, 0.0],[0.0, 0.0],[0.0, 0.0],[0.0, 0.0]] 
#"k_ss is usually zero" - Michael Motley

#connections
E=30000
A=3

connections = [[(0, 1), 0, E, 0, 0, 0], #a
               [(0, 2), 0, E, 0, 0, 0], #b
               [(0, 3), 0, E, 0, 0, 0], #c
               [(0, 4), A, E, 0, 0, 0] #d
              ]
#magic
main(Q, P, delta, connections, draw_connections = True, draw_stiffness = True)
```


<style type="text/css">
</style>
<table id="T_2b5fe_">
  <caption>Stiffness Matrix</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_2b5fe_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_2b5fe_row0_col0" class="data row0 col0" >556.656756</td>
      <td id="T_2b5fe_row0_col1" class="data row0 col1" >309.253753</td>
      <td id="T_2b5fe_row0_col2" class="data row0 col2" >0.000000</td>
      <td id="T_2b5fe_row0_col3" class="data row0 col3" >0.000000</td>
      <td id="T_2b5fe_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_2b5fe_row0_col5" class="data row0 col5" >0.000000</td>
      <td id="T_2b5fe_row0_col6" class="data row0 col6" >0.000000</td>
      <td id="T_2b5fe_row0_col7" class="data row0 col7" >0.000000</td>
      <td id="T_2b5fe_row0_col8" class="data row0 col8" >-556.656756</td>
      <td id="T_2b5fe_row0_col9" class="data row0 col9" >-309.253753</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_2b5fe_row1_col0" class="data row1 col0" >309.253753</td>
      <td id="T_2b5fe_row1_col1" class="data row1 col1" >171.807641</td>
      <td id="T_2b5fe_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_2b5fe_row1_col3" class="data row1 col3" >0.000000</td>
      <td id="T_2b5fe_row1_col4" class="data row1 col4" >0.000000</td>
      <td id="T_2b5fe_row1_col5" class="data row1 col5" >0.000000</td>
      <td id="T_2b5fe_row1_col6" class="data row1 col6" >0.000000</td>
      <td id="T_2b5fe_row1_col7" class="data row1 col7" >0.000000</td>
      <td id="T_2b5fe_row1_col8" class="data row1 col8" >-309.253753</td>
      <td id="T_2b5fe_row1_col9" class="data row1 col9" >-171.807641</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_2b5fe_row2_col0" class="data row2 col0" >0.000000</td>
      <td id="T_2b5fe_row2_col1" class="data row2 col1" >0.000000</td>
      <td id="T_2b5fe_row2_col2" class="data row2 col2" >0.000000</td>
      <td id="T_2b5fe_row2_col3" class="data row2 col3" >0.000000</td>
      <td id="T_2b5fe_row2_col4" class="data row2 col4" >0.000000</td>
      <td id="T_2b5fe_row2_col5" class="data row2 col5" >0.000000</td>
      <td id="T_2b5fe_row2_col6" class="data row2 col6" >0.000000</td>
      <td id="T_2b5fe_row2_col7" class="data row2 col7" >0.000000</td>
      <td id="T_2b5fe_row2_col8" class="data row2 col8" >0.000000</td>
      <td id="T_2b5fe_row2_col9" class="data row2 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_2b5fe_row3_col0" class="data row3 col0" >0.000000</td>
      <td id="T_2b5fe_row3_col1" class="data row3 col1" >0.000000</td>
      <td id="T_2b5fe_row3_col2" class="data row3 col2" >0.000000</td>
      <td id="T_2b5fe_row3_col3" class="data row3 col3" >0.000000</td>
      <td id="T_2b5fe_row3_col4" class="data row3 col4" >0.000000</td>
      <td id="T_2b5fe_row3_col5" class="data row3 col5" >0.000000</td>
      <td id="T_2b5fe_row3_col6" class="data row3 col6" >0.000000</td>
      <td id="T_2b5fe_row3_col7" class="data row3 col7" >0.000000</td>
      <td id="T_2b5fe_row3_col8" class="data row3 col8" >0.000000</td>
      <td id="T_2b5fe_row3_col9" class="data row3 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_2b5fe_row4_col0" class="data row4 col0" >0.000000</td>
      <td id="T_2b5fe_row4_col1" class="data row4 col1" >0.000000</td>
      <td id="T_2b5fe_row4_col2" class="data row4 col2" >0.000000</td>
      <td id="T_2b5fe_row4_col3" class="data row4 col3" >0.000000</td>
      <td id="T_2b5fe_row4_col4" class="data row4 col4" >0.000000</td>
      <td id="T_2b5fe_row4_col5" class="data row4 col5" >0.000000</td>
      <td id="T_2b5fe_row4_col6" class="data row4 col6" >0.000000</td>
      <td id="T_2b5fe_row4_col7" class="data row4 col7" >0.000000</td>
      <td id="T_2b5fe_row4_col8" class="data row4 col8" >0.000000</td>
      <td id="T_2b5fe_row4_col9" class="data row4 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_2b5fe_row5_col0" class="data row5 col0" >0.000000</td>
      <td id="T_2b5fe_row5_col1" class="data row5 col1" >0.000000</td>
      <td id="T_2b5fe_row5_col2" class="data row5 col2" >0.000000</td>
      <td id="T_2b5fe_row5_col3" class="data row5 col3" >0.000000</td>
      <td id="T_2b5fe_row5_col4" class="data row5 col4" >0.000000</td>
      <td id="T_2b5fe_row5_col5" class="data row5 col5" >0.000000</td>
      <td id="T_2b5fe_row5_col6" class="data row5 col6" >0.000000</td>
      <td id="T_2b5fe_row5_col7" class="data row5 col7" >0.000000</td>
      <td id="T_2b5fe_row5_col8" class="data row5 col8" >0.000000</td>
      <td id="T_2b5fe_row5_col9" class="data row5 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_2b5fe_row6_col0" class="data row6 col0" >0.000000</td>
      <td id="T_2b5fe_row6_col1" class="data row6 col1" >0.000000</td>
      <td id="T_2b5fe_row6_col2" class="data row6 col2" >0.000000</td>
      <td id="T_2b5fe_row6_col3" class="data row6 col3" >0.000000</td>
      <td id="T_2b5fe_row6_col4" class="data row6 col4" >0.000000</td>
      <td id="T_2b5fe_row6_col5" class="data row6 col5" >0.000000</td>
      <td id="T_2b5fe_row6_col6" class="data row6 col6" >0.000000</td>
      <td id="T_2b5fe_row6_col7" class="data row6 col7" >0.000000</td>
      <td id="T_2b5fe_row6_col8" class="data row6 col8" >0.000000</td>
      <td id="T_2b5fe_row6_col9" class="data row6 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_2b5fe_row7_col0" class="data row7 col0" >0.000000</td>
      <td id="T_2b5fe_row7_col1" class="data row7 col1" >0.000000</td>
      <td id="T_2b5fe_row7_col2" class="data row7 col2" >0.000000</td>
      <td id="T_2b5fe_row7_col3" class="data row7 col3" >0.000000</td>
      <td id="T_2b5fe_row7_col4" class="data row7 col4" >0.000000</td>
      <td id="T_2b5fe_row7_col5" class="data row7 col5" >0.000000</td>
      <td id="T_2b5fe_row7_col6" class="data row7 col6" >0.000000</td>
      <td id="T_2b5fe_row7_col7" class="data row7 col7" >0.000000</td>
      <td id="T_2b5fe_row7_col8" class="data row7 col8" >0.000000</td>
      <td id="T_2b5fe_row7_col9" class="data row7 col9" >0.000000</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_2b5fe_row8_col0" class="data row8 col0" >-556.656756</td>
      <td id="T_2b5fe_row8_col1" class="data row8 col1" >-309.253753</td>
      <td id="T_2b5fe_row8_col2" class="data row8 col2" >0.000000</td>
      <td id="T_2b5fe_row8_col3" class="data row8 col3" >0.000000</td>
      <td id="T_2b5fe_row8_col4" class="data row8 col4" >0.000000</td>
      <td id="T_2b5fe_row8_col5" class="data row8 col5" >0.000000</td>
      <td id="T_2b5fe_row8_col6" class="data row8 col6" >0.000000</td>
      <td id="T_2b5fe_row8_col7" class="data row8 col7" >0.000000</td>
      <td id="T_2b5fe_row8_col8" class="data row8 col8" >556.656756</td>
      <td id="T_2b5fe_row8_col9" class="data row8 col9" >309.253753</td>
    </tr>
    <tr>
      <th id="T_2b5fe_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_2b5fe_row9_col0" class="data row9 col0" >-309.253753</td>
      <td id="T_2b5fe_row9_col1" class="data row9 col1" >-171.807641</td>
      <td id="T_2b5fe_row9_col2" class="data row9 col2" >0.000000</td>
      <td id="T_2b5fe_row9_col3" class="data row9 col3" >0.000000</td>
      <td id="T_2b5fe_row9_col4" class="data row9 col4" >0.000000</td>
      <td id="T_2b5fe_row9_col5" class="data row9 col5" >0.000000</td>
      <td id="T_2b5fe_row9_col6" class="data row9 col6" >0.000000</td>
      <td id="T_2b5fe_row9_col7" class="data row9 col7" >0.000000</td>
      <td id="T_2b5fe_row9_col8" class="data row9 col8" >309.253753</td>
      <td id="T_2b5fe_row9_col9" class="data row9 col9" >171.807641</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_cc4bc_">
  <caption>Connections</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_cc4bc_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_cc4bc_row0_col0" class="data row0 col0" >(0, 1)</td>
      <td id="T_cc4bc_row0_col1" class="data row0 col1" >0</td>
      <td id="T_cc4bc_row0_col2" class="data row0 col2" >30000</td>
      <td id="T_cc4bc_row0_col3" class="data row0 col3" >84.852814</td>
      <td id="T_cc4bc_row0_col4" class="data row0 col4" >[-0.70710678  0.70710678]</td>
      <td id="T_cc4bc_row0_col5" class="data row0 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_cc4bc_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_cc4bc_row1_col0" class="data row1 col0" >(0, 2)</td>
      <td id="T_cc4bc_row1_col1" class="data row1 col1" >0</td>
      <td id="T_cc4bc_row1_col2" class="data row1 col2" >30000</td>
      <td id="T_cc4bc_row1_col3" class="data row1 col3" >60.000000</td>
      <td id="T_cc4bc_row1_col4" class="data row1 col4" >[0. 1.]</td>
      <td id="T_cc4bc_row1_col5" class="data row1 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_cc4bc_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_cc4bc_row2_col0" class="data row2 col0" >(0, 3)</td>
      <td id="T_cc4bc_row2_col1" class="data row2 col1" >0</td>
      <td id="T_cc4bc_row2_col2" class="data row2 col2" >30000</td>
      <td id="T_cc4bc_row2_col3" class="data row2 col3" >69.971423</td>
      <td id="T_cc4bc_row2_col4" class="data row2 col4" >[0.51449576 0.85749293]</td>
      <td id="T_cc4bc_row2_col5" class="data row2 col5" >0.000000</td>
    </tr>
    <tr>
      <th id="T_cc4bc_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_cc4bc_row3_col0" class="data row3 col0" >(0, 4)</td>
      <td id="T_cc4bc_row3_col1" class="data row3 col1" >3</td>
      <td id="T_cc4bc_row3_col2" class="data row3 col2" >30000</td>
      <td id="T_cc4bc_row3_col3" class="data row3 col3" >123.547562</td>
      <td id="T_cc4bc_row3_col4" class="data row3 col4" >[0.87415728 0.48564293]</td>
      <td id="T_cc4bc_row3_col5" class="data row3 col5" >728.464397</td>
    </tr>
  </tbody>
</table>




    
![png](output_15_2.png)
    


# Problem 24 - end

Rerunning the stiffness solver but this time with stiffnesses for all members so that we can get forces and displacements.

Relevant values here are the first two items of the external forces and displacements table, the internal forces table, and the pythagorean theorem cell that's calculating the magnitude of P)

P = (-71.4, -17.3) kip
|P| = 73.4 kip

f_a = 	(0, 1)	-37.500000 kip
f_b = 	(0, 2)	0.000000 kip (incredibly small elongation)
f_c =	(0, 3)	33.088200 kip
f_d =	(0, 4)	31.839600 kip


```python
#nodes
Q = np.array([[0.0, 0.0],[-5.0*12, 5.0*12],[0.0, 5.0*12],[3.0*12, 5.0*12], [9.0*12, 5.0*12]])

#forces/displacements
P = [["undefined", "undefined"], ["undefined", "undefined"], ["undefined", "undefined"], ["undefined", "undefined"], ["undefined", "undefined"]]
delta = [[-.05, 0.0],[0.0, 0.0],[0.0, 0.0],[0.0, 0.0],[0.0, 0.0]]

#connections
E=30000
A=3

connections = [[(0, 1), A, E, 0, 0, 0], #a
               [(0, 2), A, E, 0, 0, 0], #b
               [(0, 3), A, E, 0, 0, 0], #c
               [(0, 4), A, E, 0, 0, 0] #d
              ]
#magic
main(Q, P, delta, connections, draw_displacements = True, draw_forces = True)
```


<style type="text/css">
</style>
<table id="T_585e8_">
  <caption>External Forces and Displacements</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
      <th class="col_heading level0 col2" >2</th>
      <th class="col_heading level0 col3" >3</th>
      <th class="col_heading level0 col4" >4</th>
      <th class="col_heading level0 col5" >5</th>
      <th class="col_heading level0 col6" >6</th>
      <th class="col_heading level0 col7" >7</th>
      <th class="col_heading level0 col8" >8</th>
      <th class="col_heading level0 col9" >9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_585e8_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_585e8_row0_col0" class="data row0 col0" >-71.373099</td>
      <td id="T_585e8_row0_col1" class="data row0 col1" >-17.319111</td>
      <td id="T_585e8_row0_col2" class="data row0 col2" >26.516504</td>
      <td id="T_585e8_row0_col3" class="data row0 col3" >-26.516504</td>
      <td id="T_585e8_row0_col4" class="data row0 col4" >0.000000</td>
      <td id="T_585e8_row0_col5" class="data row0 col5" >0.000000</td>
      <td id="T_585e8_row0_col6" class="data row0 col6" >17.023757</td>
      <td id="T_585e8_row0_col7" class="data row0 col7" >28.372928</td>
      <td id="T_585e8_row0_col8" class="data row0 col8" >27.832838</td>
      <td id="T_585e8_row0_col9" class="data row0 col9" >15.462688</td>
    </tr>
    <tr>
      <th id="T_585e8_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_585e8_row1_col0" class="data row1 col0" >-0.050000</td>
      <td id="T_585e8_row1_col1" class="data row1 col1" >0.000000</td>
      <td id="T_585e8_row1_col2" class="data row1 col2" >0.000000</td>
      <td id="T_585e8_row1_col3" class="data row1 col3" >0.000000</td>
      <td id="T_585e8_row1_col4" class="data row1 col4" >0.000000</td>
      <td id="T_585e8_row1_col5" class="data row1 col5" >0.000000</td>
      <td id="T_585e8_row1_col6" class="data row1 col6" >0.000000</td>
      <td id="T_585e8_row1_col7" class="data row1 col7" >0.000000</td>
      <td id="T_585e8_row1_col8" class="data row1 col8" >0.000000</td>
      <td id="T_585e8_row1_col9" class="data row1 col9" >0.000000</td>
    </tr>
  </tbody>
</table>




<style type="text/css">
</style>
<table id="T_8efc0_">
  <caption>Internal Forces</caption>
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >0</th>
      <th class="col_heading level0 col1" >1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_8efc0_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_8efc0_row0_col0" class="data row0 col0" >(0, 1)</td>
      <td id="T_8efc0_row0_col1" class="data row0 col1" >-37.500000</td>
    </tr>
    <tr>
      <th id="T_8efc0_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_8efc0_row1_col0" class="data row1 col0" >(0, 2)</td>
      <td id="T_8efc0_row1_col1" class="data row1 col1" >0.000000</td>
    </tr>
    <tr>
      <th id="T_8efc0_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_8efc0_row2_col0" class="data row2 col0" >(0, 3)</td>
      <td id="T_8efc0_row2_col1" class="data row2 col1" >33.088200</td>
    </tr>
    <tr>
      <th id="T_8efc0_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_8efc0_row3_col0" class="data row3 col0" >(0, 4)</td>
      <td id="T_8efc0_row3_col1" class="data row3 col1" >31.839600</td>
    </tr>
  </tbody>
</table>




    
![png](output_17_2.png)
    



```python
#pythagorean theorem
print(math.sqrt((-71.373099)**2 + (-17.319111)**2))
```

    73.44433856121329
    

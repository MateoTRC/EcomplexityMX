# Economic complexity in Mexico

## Author
Mateo Tonatiuh Rodríguez Cervantes

## Abstract

We applied the economic complexity framework pioneered by Hidalgo et al. in [1] to analyze the employment and economic units within Mexico's territorial boundaries, segmented by states and regions. We developed a machine learning approach to predict product development within the product space and gauge the significance of each complexity feature. Our analysis revealed that complexity metrics serve as significant predictors for economic activities development across the majority of states and regions.

## Data

We used the data from INEGI, ENOE. This data contains the number of employees (_POT) and economic units (_UE) of each economic activity (EA) and service (registered at INEGI) in each state for the years: 2004, 2009, 2014, and 2019. The products and services are labeled according to the NAICS-SCIAN classification. The 32 Mexican states are arranged in 4 regions according to their geographical location, as follows:

- **South**: Campeche, Chiapas, Guerrero, Oaxaca, Quintana Roo, Tabasco, Veracruz, Yucatán.
- **Center**: Ciudad de México, Estado de México, Guanajuato, Hidalgo, Morelos, Puebla, Querétaro, Tlaxcala.
- **Center-North**: Aguascalientes, Baja California Sur, Colima, Durango, Jalisco, Michoacán, Nayarit, San Luis Potosí, Sinaloa.
- **North**: Baja California, Chihuahua, Coahuila, Nuevo León, Sonora, Tamaulipas, Zacatecas.

The data corresponding to the states are in the folders that start with 'Edo' and the regional data in those starting with 'Reg'. Thus, the data for the number of employees per state is in the folder 'Edo_POT', and the regional data is in 'Reg_POT'. On the other hand, the economic units data is in 'Edo_UE' and 'Reg_UE' for the state and regional division.

The overall sector distributions of the number of employees and economic units per year in each region are presented in the `Data_Visualization_by_sector.ipynb` notebooks.

## Metrics

Now we describe some of the computed complexity metrics. The metrics are computed in the `Ecomplexity_metrics.ipynb` notebooks.


### Revealed Comparative Advantage (RCA)

The revealed comparative advantage ($RCA_{r,i}$) measures whether a region $r$'s economic indicator (personnel employed or economic units) has a greater value of the $i$-th EA, as a share of its total value, than the “average” state. It is measured as:

$$
RCA_{r,i} = \frac{x_{r,i} / \sum_i x_{r,i}}{\sum_r x_{r,i} / \sum_{r,i} x_{r,i}}
$$

A region $r$ is considered to specialize in the $i$-th EA if the indicator of the EA, as a percentage of the total indicator in the entity, is equal to or greater than its analogous measure at the national level, i.e., 

$$
RCA_{r,i} > 1
$$

We defined the ubiquity $k_i$ of the EA $i$ as the total of regions specialized in this activity.

### Undeveloped and Transition Products

For each EA indicator (number of employees and number of economic units), we computed the revealed comparative advantage ($RCA$) per location each year. We are interested in finding the factors that condition the development of an EA at each location for each time period, focusing on the EA’s diversification.

Following [1], we define:
- **Underdeveloped products**: $RCA < 0.5$
- **Developed products**: $RCA > 1$

We define:
- **Strong transition EA**: $RCA < 0.5$ in the first year and $RCA > 1$ in the second year.
- **Weaker transition**: $0.5 < RCA < 1$ in the first year and $RCA > 1$ in the second. (We then define a **_weak underdevelopement_** if $0.5 < RCA < 1$).

For each pair of consecutive years (2004-2009, 2009-2014, 2014-2019) and the total period (2004-2019), we identified the subset of underdeveloped and transition EA.

### Proximity and Networks

Once the $RCA$ is computed, we can calculate the proximity, $0 < \phi_{ij} < 1$, between EAs and build the associated weighted networks of the EA space for each year. In these networks, each node represents an EA and the weighted edges represent the proximity of these EAs (for further details consult [1]). The networks are created in the `Create_graphs_files.ipynb` notebooks. The proximity measure is based on the conditional probability that a region specialized in the EA $i$ will also be specialized in the EA $j$. Formally:

$$
\phi_{ij} = \frac{\sum_r M_{r,i} \sum_r M_{r,j}}{\max(k_i, k_j)}
$$

where $M_{r,i} = 1$ if the region $r$ is specialized in the EA $i$, and $M_{r,i} = 0$ otherwise.

As a complementary edge metric, we also computed the distance between products as the geometric inverse of its proximity, given by:

$$
d_{ij} = 1 - \phi_{ij}.
$$

I see what you mean. Let’s correct the summation notation to ensure clarity:

### Complexity Measures

The density is defined as the average proximity of an EA to a location’s current productive structure:

$$
\omega_j^k = \frac{\sum_{i \neq j} x_i \phi_{ij}}{\sum_{i \neq j} \phi_{ij}}
$$

where $\omega_j^k$ is the density around product or service $j$ given the indicator (number of employees and number of economic units) of the $k$-th location, and $x_i = 1$ if $RCA_{k,i} > 1$ and $0$ otherwise. A high-density value indicates that the $k$-th location has many developed products surrounding the $j$-th product.

At the single product level, we considered the ratio between the average density of all locations in which the $j$-th product was a transition product and the average density of all locations in which the $j$-th product was not developed. Formally, we define the “discovery factor” $H_j$ as:

$$
H_j = \frac{\frac{1}{T} \sum_{k=1}^T \omega_j^k}{\frac{1}{N-T} \sum_{k=T+1}^N \omega_j^k}
$$

where $T$ is the number of locations in which the $j$-th product was a transition product and $N$ is the total number of locations.

Here's the Markdown code with LaTeX formatted properly for the node metrics:

### Nodes Metrics

Additionally, with the EA space networks, we computed the following node metrics associated with each EA each year. This computation is done in the `Compute_graph_metrics.ipynb`, using the previously made networks files in `Create_graphs_files.ipynb`.

1. **Degree Centrality**:
   - **Formal Definition**: Degree centrality measures the importance of a node in a network based on the number of connections it has.
   - **Equation**:
     $$
     C_d (i) = \frac{\text{Number of connections of node } i}{\text{Total number of nodes in the network} - 1}
     $$

2. **Eigenvector Centrality**:
   - **Formal Definition**: Eigenvector centrality measures the centrality of a node based on the centrality of its neighbors.
   - **Equation**:
     $$
     A x = \lambda x
     $$
     **Where**:
     - $A$ is the adjacency matrix of the network.
     - The centrality of node $i$ is the $i$-th element of an eigenvector associated with the eigenvalue of maximum modulus that is positive.

3. **Closeness Centrality**:
   - **Formal Definition**: Closeness centrality measures how quickly a node can reach all other nodes in the network.
   - **Equation**:
     $$
     C_c (i) = \frac{1}{\sum_{j} d(i, j)}
     $$
     **Where**:
     - $d(i, j)$ is the shortest path distance between node $i$ and node $j$. In our case, the distance between connected nodes is given by $d_{ij} = 1 - \phi_{ij}$, which has to be considered in order to compute the shortest path.

Here's the Markdown code with the relevant content for the logistic regression application:

### Logistic Regression

As previously explained, for each economic indicator (number of employees and number of economic units), the EAs at each location can be classified into two categories based on their RCA values in two different years: underdeveloped and transitional products (absolute and relaxed). Subsequently, we implemented a fixed effects logistic model [5], incorporating the metrics outlined in the preceding section as predictors for these distinct categories.

Additionally, we implemented mixed variable models, where the metrics of one indicator were used to complement the predictors of the other. For example, the number of economic units of an EA, as well as its density or RCA, were used to predict the development of the number of employees indicator in the same EA.

Every model returns the statistical relevance of each predictor. To evaluate the performance of each model, we obtained the ROC curve and used the area under the curve (AUC) as a score metric. The methodology and results are in the `Logistic_regression.ipynb` notebooks.

## References

1. Hidalgo, C. A., Klinger, B., Barabási, A. L., & Hausmann, R. (2007). The product space conditions the development of nations. *Science, 317*(5837), 482-487.
2. G.W. Heiman, “Understanding research methods and statistics: An integrated introduction for psychology”, Houghton, Mifflin and Company, 2001.
3. W. H. Kruskal & W. W. Wallis, “Use of Ranks in One-Criterion Variance Analysis”, *Journal of the American Statistical Association, 47*(260), 583-621, 1952.
4. Fawcett T. An introduction to ROC analysis. *Pattern Recognition Letters, 27*(8), 861-874, 2006.
5. Wilson, J. R., Lorenz, K. A., Wilson, J. R., & Lorenz, K. A. (2015). Fixed effects logistic regression model. *Modeling binary correlated responses using SAS, SPSS and R*, 225-246.

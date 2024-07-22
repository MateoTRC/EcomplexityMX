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

Now we describe some of the ecomputed complexity metrics. The metrics are computed in the `Ecomplexity_metrics.ipynb` notebooks.


### 2.1 Revealed Comparative Advantage (RCA)

The revealed comparative advantage ($RCA_{r,i}$) measures whether a region $r$'s economic indicator (personnel employed or economic units) has a greater value of the $i$-th EA, as a share of its total value, than the “average” state. It is measured as:

$$
RCA_{r,i} = \frac{x_{r,i} / \sum_i x_{r,i}}{\sum_r x_{r,i} / \sum_{r,i} x_{r,i}}
$$

A region $r$ is considered to specialize in the $i$-th EA if the indicator of the EA, as a percentage of the total indicator in the entity, is equal to or greater than its analogous measure at the national level, i.e., 

$$
RCA_{r,i} > 1
$$

We defined the ubiquity $k_i$ of the EA $i$ as the total of regions specialized in this activity.

### 2.1.1 Undeveloped and Transition Products

For each EA indicator (number of employees and number of economic units), we computed the revealed comparative advantage ($RCA$) per location each year. We are interested in finding the factors that condition the development of an EA at each location for each time period, focusing on the EA’s diversification.

Following [1], we define:
- **Underdeveloped products**: $RCA < 0.5$
- **Developed products**: $RCA > 1$

We define:
- **Strong transition EA**: $RCA < 0.5$ in the first year and $RCA > 1$ in the second year.
- **Weaker transition**: $0.5 < RCA < 1$ in the first year and $RCA > 1$ in the second. (We then define a **_weak underdevelopement_** if $0.5 < RCA < 1$).

For each pair of consecutive years (2004-2009, 2009-2014, 2014-2019) and the total period (2004-2019), we identified the subset of underdeveloped and transition EA.

### 2.1.2 Proximity and Networks

Once the $RCA$ is computed, we can calculate the proximity, $0 < \phi_{ij} < 1$, between EAs and build the associated weighted networks of the EA space for each year. In these networks, each node represents an EA and the weighted edges represent the proximity of these EAs (for further details consult [1]). The proximity measure is based on the conditional probability that a region specialized in the EA $i$ will also be specialized in the EA $j$. Formally:

$$
\phi_{ij} = \frac{\sum_r M_{r,i} \sum_r M_{r,j}}{\max(k_i, k_j)}
$$

where $M_{r,i} = 1$ if the region $r$ is specialized in the EA $i$, and $M_{r,i} = 0$ otherwise.

As a complementary edge metric, we also computed the distance between products as the geometric inverse of its proximity, given by:

$$
d_{ij} = 1 - \phi_{ij}
$$



## References

1. Hidalgo, C. A., Klinger, B., Barabási, A. L., & Hausmann, R. (2007). The product space conditions the development of nations. *Science, 317*(5837), 482-487.
2. G.W. Heiman, “Understanding research methods and statistics: An integrated introduction for psychology”, Houghton, Mifflin and Company, 2001.
3. W. H. Kruskal & W. W. Wallis, “Use of Ranks in One-Criterion Variance Analysis”, *Journal of the American Statistical Association, 47*(260), 583-621, 1952.
4. Fawcett T. An introduction to ROC analysis. *Pattern Recognition Letters, 27*(8), 861-874, 2006.
5. Wilson, J. R., Lorenz, K. A., Wilson, J. R., & Lorenz, K. A. (2015). Fixed effects logistic regression model. *Modeling binary correlated responses using SAS, SPSS and R*, 225-246.

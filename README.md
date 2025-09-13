# **Malaria Prevalence Mapping in The Gambia** 

##  **Project Overview**  
This project investigates **malaria prevalence in The Gambia** using spatial statistical modeling. The analysis leverages geostatistical methods, particularly the **Stochastic Partial Differential Equation (SPDE) approach in INLA**, to produce high-resolution prevalence maps. The work is motivated by the need to better understand spatial heterogeneity in malaria burden and to inform targeted interventions.  

### **Why This Project Is Needed**

#### Malaria remains a major health burden in sub-Saharan Africa

The Gambia, like many West African countries, has seen substantial progress in malaria control but transmission persists, often in pockets or hotspots.

National-level statistics hide important local variation in prevalence.

Resource allocation requires high-resolution maps

Ministries of Health and NGOs (e.g., WHO, PMI, Global Fund) need spatially detailed maps to identify areas with higher malaria burden.

Without spatial modeling, interventions (bed nets, indoor residual spraying, drug distribution) may be spread too thinly or miss high-risk communities.

#### Surveys alone are insufficient

Household surveys provide cluster-level estimates, but they are sparse (only 63 clusters in this dataset).

We need statistical models (like INLA-SPDE) to “fill in the gaps” and predict prevalence continuously across the country.

#### Modern Bayesian geostatistics (SPDE in INLA)

The SPDE approach allows computationally efficient Bayesian modeling of large spatial datasets.

It accounts for spatial correlation between nearby survey clusters, producing smoother, more realistic maps than ad hoc methods.

#### Decision-making and equity

By mapping where malaria is highest, policymakers can ensure equitable distribution of interventions.

Helps move from “one-size-fits-all” to targeted strategies that save resources and maximize impact.
---

##  Data  
The dataset contains **63 survey clusters** across The Gambia, with the following variables :  
- **total**: Number of individuals sampled per cluster.  
- **positive**: Number of malaria-positive cases per cluster.  
- **prevalence**: Cluster-level malaria prevalence (`positive / total`).  
- **id**: Cluster identifier.  
- **geometry, long, lat**: Geographic coordinates of clusters.  
- **alt**: Altitude (meters).  

---

##  Methods  

### 1. Exploratory Analysis  
- Cluster-level prevalence ranged from ~0.30 to 0.52.  
- Missingness checks confirmed complete data for key outcome and predictors .  

### 2. Spatial Modeling Framework  
The spatial model was built in **R-INLA** using the **SPDE approach**:  
- **Mesh construction**: A 2D triangulated mesh was generated over The Gambia to approximate the spatial field.  
- **Projection matrix (A)**: Linked cluster coordinates to the mesh nodes.  
- **SPDE model**: Matérn covariance structure with \(\alpha = 2\).  
- **Model formula**:  
  ```r
  y ~ 0 + intercept + altitude + f(spatial, model = spde)
  ```  
- **Likelihood**: Binomial with logit link.  

### 3. Model Estimation  
- Fitted using `inla()` with binomial likelihood and logit link.  
- Posterior summaries generated for fixed effects, random effects, and fitted values.  

---

##  Key Results  

### Fixed Effects  
- **Intercept**: -0.572 (95% CrI: -1.384, 0.443).  
- **Altitude**: -0.002 (95% CrI: -0.026, 0.023).  
  - Suggests weak or negligible altitude effect on prevalence.  

### Random Effects  
- Spatial effect modeled with SPDE (Matérn covariance).  
- Hyperparameters indicated moderate spatial correlation across survey sites.  

### Posterior Predictions  
- Predicted prevalence ranged between **~0.34 and 0.36** across the study area.  
- Example predictions (mean and 95% CrI):  
  - Longitude -15.06, Latitude 13.77 → Mean: 0.36, CrI: (0.23, 0.55)  
  - Longitude -14.98, Latitude 13.77 → Mean: 0.35, CrI: (0.22, 0.51)  

---

##  Outputs  

1. **Cluster-level dataset** (`gambia_sf`): survey points with total, positives, prevalence, and altitude.  
2. **Predicted prevalence raster maps**:  
   - Posterior mean prevalence (`r_prev_mean`).  
   - Lower (2.5%) credible bound (`r_LL`).  
   - Upper (97.5%) credible bound (`r_UL`).  
3. **Prediction points dataset** (`pred_points`): includes predicted mean prevalence and credible intervals at rasterized grid locations.  

---

##  How to Reproduce  

### Requirements  
- **R** (≥ 4.3)  
- Packages: `INLA`, `sf`, `terra`, `tidyverse`, `spdep`, `naniar`, `ggplot2`  

### Workflow  
1. Load and preprocess the malaria survey dataset.  
2. Construct mesh and projection matrices.  
3. Define and fit the SPDE-INLA model.  
4. Generate posterior prevalence maps.  
5. Visualize results (maps, credible intervals).  

---

##  Applications  
- Mapping malaria hotspots for **targeted interventions**.  
- Demonstrating the use of **INLA-SPDE** for disease mapping.  
- Educational tool for spatial epidemiology and Bayesian geostatistics.  

---

##  Author  
Developed by **Kwabena Asabere**  

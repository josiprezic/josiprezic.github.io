---
title: Chapter 2 - Weighted Least square method
date: 2021-01-07 07:51:00 +0545
categories: [Robotics, Estimation]
tags: [robotics]     # TAG names should always be lowercase
---

# Weighted least square 

Suppose we take measurements with multiple multimeters, some of which are better than others.  
For general linear measurement model for *m* measurements and *n* unknowns:  
$$y = Hx + v$$  

In regular least squares, we implicitly assumed that each noise term was of equal variance:
$$ E[v_i^2] = \sigma ^2  (i = 1,....,m)$$  
$$ R = E[vv^T] = \begin{bmatrix}
\sigma^2 & 0 & 0\\ 
 & ... & \\ 
 0& 0 & \sigma^2
\end{bmatrix} $$

If we assume each noise term is independent, but of different variance then,   
$$ E[v_i^2] = \sigma_i^2, (i = 1,...,m) $$  
$$ R = E[vv^T] = \begin{bmatrix}
\sigma_1^2 & 0 & 0\\ 
 & ... & \\ 
 0& 0 & \sigma_m^2
\end{bmatrix} $$

Then we can define a weighted least squares criterion as:

$$ \widehat{x}_{WLS} = e^TR^{-1}e = \frac{e_1^2}{\sigma_1^2} + \frac{e_2^2}{\sigma_2^2} + .... + \frac{e_m^2}{\sigma_m^2}$$

where,   
$$ \begin{bmatrix}
e_1\\ 
e_2\\ 
..\\ 
e_m
\end{bmatrix} = e = \begin{bmatrix}
y_1\\ 
y_2\\ 
..\\ 
y_m
\end{bmatrix} - H \begin{bmatrix}
x_1\\ 
x_2\\ 
..\\ 
x_n
\end{bmatrix} $$

The higher the expected noise, the lower the weight we place on the measurement. 

Expanding our new criterion, we get,

$$ \widehat{x}_{WLS} = e^TR^{-1}e = (y-Hx)^TR^{-1}(y-Hx) $$

We can minimize it as before, but accounting for the new weighting term:

$$ \widehat{x} = argmin_x (\widehat{x}_{WLS})\rightarrow \frac{\partial }{\partial x}|_{x=\widehat{x}} = -y^TR^{-1}H + \widehat{x}^TH^TR^{-1}H $$

$$ H^TR^{-1}\widehat{x}_{WLS} = H^TR^{-1}y $$

Hence the weighted least square equation is:  
$$ \widehat{x} = (H^TR^{-1}H)^{-1}H^TR^{-1}y $$

## Solving for resistance using weighted least square
Assume,  
Multimeter 1 has variance $$ \sigma = 20 ohms $$  
Multimeter 2 has variance $$ \sigma = 2 ohms $$  

| Measurements                      | Multimeter1 (Ohms)          | Multimeter 2 (Ohms) |
|:-----------------------------|:-----------------|--------:|
| 1          | 1068    |   |
| 2               | 988    |      |
| 3 |  | 1002   |
| 4 |       | 996                 |

Here, 

$$ y = \begin{bmatrix}
1068\\ 
988\\ 
1002\\ 
996
\end{bmatrix} $$  

$$ H = \begin{bmatrix}
1\\ 
1\\ 
1\\ 
1
\end{bmatrix} $$  

$$ R = \begin{bmatrix}
\sigma_1^2 &  &  & \\ 
 & \sigma_2^2 &  & \\ 
 &  & \sigma_3^2 & \\ 
 &  &  & \sigma_4^2
\end{bmatrix} = \begin{bmatrix}
400 &  &  & \\ 
 & 400 &  & \\ 
 &  & 4 & \\ 
 &  &  & 4
\end{bmatrix}$$

Therefore, using formula given below  
$$ \widehat{x} = (H^TR^{-1}H)^{-1}H^TR^{-1}y $$  
we get,  
$$ \widehat{x} = \frac{1}{\frac{1}{400}+\frac{1}{400}+\frac{1}{4}+\frac{1}{4}}  (\frac{1068}{400}+\frac{988}{400}+\frac{1002}{4}+\frac{996}{4}) $$
$$ \widehat{x} = 999.3 Ohms $$

## Conclusion
In this tutorial, we used least square method for measurement that have different noisy charactersitics. Weighted least squares lets us weight each measurement according to noise variance. The method we used upto now had a set of batch data (i.e data obtained beforehand). How can we use least squares for continuous obtained data? We use recursive least square method. More on this on next tutorial. 


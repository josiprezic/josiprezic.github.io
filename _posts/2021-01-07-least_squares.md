---
title: Chapter 1 - Least square method
date: 2021-01-07 00:27:00 +0545
categories: [Robotics, Estimation]
tags: [robotics]     # TAG names should always be lowercase
---



# Least squares method of estimation
```
The most probable value of the unknown quantities will be that in which the sum of the squares of the differences between the actually observed and the computed values multiplied by numbers that measure the degree of precision is a minimum.
-Karl Fredrich Gauss
```

## Estimating the resistance by using least square

Let's say we measured resistance of a resistor using a multimeter four times. Each time we get a different resistance value as shown in the table below. 

| Measurement | Resistance(ohms) |
|-------------|------------------|
| 1           | 1068             |
| 2           | 988              |
| 3           | 1002             |
| 4           | 996              |

Now, since we don't know the exact value of the resistance from these four measurements, we can use least square method to estimate the resistance value with less error. 

Let *x* be the resistance. Hence *x* is a constant, but is unknown. 
Let *y* be the measurement of the resistance. We model our measurement as corrupted by noise *v*.  
Hence we can write,  
  $$y = x + v$$

So, we can write each of our four measurement as:  
$$ y_1 = x + v_1 $$  
$$ y_2 = x + v_2 $$  
$$ y_3 = x + v_3 $$  
$$ y_4 = x + v_4 $$  

In the least squared error method, our aim is to decrease the sum of squared of the error of each measurement. The squared error in the measurement is given by,  
$$ e^2 = (y - x)^2 $$  

Therefore, each error in the measurement is given by,   
$$ e_1^2 = (y_1 - x)^2 $$  
$$ e_2^2 = (y_2 - x)^2 $$  
$$ e_3^2 = (y_3 - x)^2 $$  
$$ e_4^2 = (y_4 - x)^2 $$  

Now, the best estimate of resistance is the one that minimizes the sum of squared errors.

$$ \widehat{x}_{LS} = argmin(e_1^2 + e_2^2 + e_3^2 + e_4^2) $$  

Let's write our equations in vector form:

$$ e = \begin{bmatrix}
e_1\\ 
e_2\\ 
e_3\\ 
e_4
\end{bmatrix}  = y - Hx  = y - \begin{bmatrix}
1\\ 
1\\ 
1\\ 
1
\end{bmatrix} x$$ 

where, H is a matrix called **Jacobian**.  
Now, we can express our criterion as,  
$$ \widehat{x}_{LS} = argmin(e_1^2 + e_2^2 + e_3^2 + e_4^2) = e^Te $$  
solving, we get,  
$$ \widehat{x}_{LS} = y^Ty - x^TH^Ty - y^THx + x^TH^THx $$    
To minimize this, we have to calculate partial derivative of $$\widehat{x}_{LS}$$  with respect to our parameter, set it to 0, and solve for an extremum.

$$ \frac{\partial \widehat{x}_{LS}}{\partial x} |_{x = \widehat{x}} = -2y^TH + 2 \widehat{x}^TH^TH = 0 $$  

Arranging, we arrive at,   
$$ \widehat{x}_{LS} = (H^TH)^{-1}H^Ty $$  

Hence, using this formula, we can calculate the resistance using least square error method. 

### Things to remember
- we can only solve for $$\widehat{x}$$ if $$(H^TH)^-1$$ exists.
- If we have *m* measurements, and *n* unknown parameters then, $$H \epsilon \mathbb{R}^{m\times n}$$ and, $$H^TH \epsilon \mathbb{R}^{n\times n}$$  
  This means,  $$(H^TH)^-1$$  exists only if $$m \geq n$$.

## Solving for resistance using least square

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

putting the value in the equation below,  
$$\widehat{x}_{LS} = (H^TH)^{-1}H^Ty$$  
we get,   
$$\widehat{x}_{LS} = \frac{1}{4}(1068 + 988 + 1002 + 996) = 1013.5 Ohms $$

## Conclusion
We assumed that our measurement model is linear, i.e  $$y = x + v$$, and also we assumed our measurement are equally weighted.  
What happens if one you measure resistance of same resistor using two multimeters one which is more reliable and another which is less reliable? This is where weighted least square comes into action. We will get to that on the next tutorial. 



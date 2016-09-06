---
layout: post
title:  "Regularized Hinge Loss and the Bounded Solution"
date:   2016-08-16 17:00:04 -0700
categories: jekyll update
---

As a theoretical advantage, adding regularization makes a convex loss function strongly-convex, allowing one to use fast converging gradient algorithms for optimization. An interesting observation is that regularization also restricts the solution space \(under some constraints\).

***Theorem :*** *For a regularized loss function of the form* 

$$
  L(w) = \frac{\sum_{i=1}^{n} l(w, x_i, y_i)}{n} + \frac{\lambda}{2}\|w\|^2 
$$

*if* $$l(0, x_i, y_i) = 1$$ *for all* $$x_i, y_i$$, *then* $$w^*$$ *or the unconstrained minimizer of* $$L(w)$$ *lies in a ball of radius* $$ \sqrt{\frac{2}{\lambda}} $$ *centered at the origin or* $$\|w^*\| \leq \sqrt{\frac{2}{\lambda}}$$

**Proof :**  

&nbsp;&nbsp;&nbsp;&nbsp; Observer that L(0) = 1 

&nbsp;&nbsp;&nbsp;&nbsp; Since $$l(w,x,y)$$ is a positive function, and $$\frac{\lambda}{2}\|w\|^2 \geq 1 $$,  $$ \forall w $$  s.t.  $$ \|w\|^2 >  \frac{2}{\lambda}$$

&nbsp;&nbsp;&nbsp;&nbsp; It is easy to see that $$ \|w^*\|^2 \leq \frac{2}{\lambda} \blacksquare $$ 



Isn\'t this an interesting observations that for convex positive loss functions (which have the same constant value at w = 0), l-2 regularization restricts the solution in a ball around the origin. For some special loss functions, we can imporve this bound. In this blog post, I would show that for regulaized hinge loss, $$\|w^*\| \leq \sqrt{\frac{1}{\lambda}}$$

Hinge loss is used as a surrogate loss for soft-margin SVM problems, and is defined as  : $$  l(w,x,y) = max(0, 1 - yw^Tx) $$. It is a positive convex function with $$l(0,x_i,y_i) = 1$$. For the soft-margin-SVM, the objective loss function can be defined as : 

$$
	L(w) = \frac{\lambda}{2}\|w\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-y_iw^Tx_i)
$$ 

We want to bound $$ w^* = \arg \min L(w) $$

 **Claim :** 
$$ \forall x_i, y_i \epsilon R $$, the minimizer $$w^*$$ of the hinge loss always lies in a ball of radius $$ \sqrt{\frac{1}{\lambda}} $$, centered at origin or $$ \| {w^*}^2 \| \leq \frac{1}{\lambda} $$ 

This claim was used in the [Pegasos algorithm by Shai Shalev-Shwartz et al](http://www.ee.oulu.fi/research/imag/courses/Vedaldi/ShalevSiSr07.pdf). Their proof follows by applying strong duality theorem for SVM optimization problem, or mathematically, there exists a vector $$\mathbf{\alpha^*} \epsilon [0,1]^n$$ such that 

$$
  
  \frac{\lambda}{2}\|w^*\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-y_iw^{*T}x_i) =   -\frac{\lambda}{2}\|w^*\|^2 + \frac{\|\alpha^*\|_1}{n}
 
$$

Rearraning the terms, we get out desired bound. 

In this post, I give a proof of the above claim using basic geometry and linear algebra. Instead of analyzing the general problem, we make the following simplifications, all of which are consistent with the result : 

1. Since the solution exists for all $$x_i$$ and $$y_i$$, we would be replacing $$x_iy_i$$ by $$\alpha_i$$

2.  We give the analysis for one dimensional problem. Analysis for multiple dimensions is similar where we go to concentric rings and quadratic surfaces instead of parabola's and intervals in our analysis. Our proof's can be easily extended to multiple dimensions by operating on norms. 

3. Without loss of generality, we would be assuming $$w^* \geq 0 $$. In case $$w^* \leq 0$$, we can simply replace all the $$\alpha_i$$ by $$-\alpha_i$$ and then bound $$w^*$$. 

4. Under the assumption 3, we observe that the presence of terms with negative $$\alpha_k$$ pushes the optimal solution closer to the origin, when compared to the case when we replace $$\alpha_k$$ with $$- \alpha_k$$. Hence, we would be replacing all the negative $$\alpha_k$$ with $$-\alpha_k$$.

5. We assume that $$ \alpha_i's $$ are ordered i.e. $$\alpha_1 \geq \alpha_2 \geq \alpha_3 ... \geq \alpha_n$$. Thus, $$ \frac{1}{\alpha_n} \geq \frac{1}{\alpha_{n-1}} .... \geq \frac{1}{\alpha_3} \geq \frac{1}{\alpha_2} \geq \frac{1}{\alpha_1}$$


We observe that if we can bound the solution under the above assumptions, we also provide a bound in general case. Our new loss function now becomes - 

$$
    L(w) = \frac{\lambda}{2}\|w\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-w^T\alpha_i)  ,  \text{where  } \alpha_i \geq 0 \text{ and  } \alpha_1 \geq \alpha_2 \geq ... \geq \alpha_n
$$

Our loss function is composed of a bunch of Hinge losse wedges, each growing towards the left from $$ \frac{1}{\alpha_i} $$ and slope $$ - \alpha_i$$, as shown in the below plot for $$ \alpha_1 = 4, \alpha_2 = 2, \alpha_3 = 1, \alpha_4 = 0.5 $$, for illustration -

![Hinge-Loss](/public/Hinge-Loss/Hinge-Loss.png)   
{: style="width: 60%; margin: 0 auto; display: block;"}
<br>

and $$ L(w) $$ is piecewise quadratic, similar to the below plot for the above example, 

![Total-Loss](/public/Hinge-Loss/Total-Loss.png)   
{: style="width: 60%; margin: 0 auto; display: block;"}
<br>

Now let us divide the number line into the intervals $$ [0, \frac{1}{\alpha_1}] $$  , $$[\frac{1}{\alpha_1} , \frac{1}{\alpha_2}]$$,  ... ,  $$[\frac{1}{\alpha_{n-1}}, \frac{1}{\alpha_{n}}]$$, $$[\frac{1}{\alpha_n} , \infty]$$. 

We observe that the loss function takes the same function form within each interval, but changes across intervals.  We can break down the loss function in these intervals  as follows : 

 $$
    L(w) = \begin{cases}  
        \frac{\lambda}{2}w^2 + \frac{1}{n}\sum_{i=1}^{n} (1-w\alpha_i)   & \text{for }0\leq w\leq \frac{1}{\alpha_1}\\
        \frac{\lambda}{2}w^2 + \frac{1}{n}\sum_{i=j}^{n} (1-w\alpha_i)  & \text{for }\frac{1}{\alpha_{j-1}}\leq w\leq \frac{1}{\alpha_j} \\
          \frac{\lambda}{2}w^2 & \text{for } \frac{1}{\alpha_n} \leq w
    \end{cases}

 $$


All of these are quadratics, each with a different minimizer. Let us look at the optimal solution of each of these quadratics (without worrying about if it lies in the domain of the quadratic for now). These are the points where the derivatives become 0 for the quadratic. 

$$

    w^* = \begin{cases}
         \frac{1}{n\lambda}\sum_{i=1}^{n} (\alpha_i)  & \text{for }0\leq w\leq \frac{1}{\alpha_1}\\
         \frac{1}{n\lambda}\sum_{i=j}^{n} (\alpha_i)  & \text{for }\frac{1}{\alpha_{j-1}}\leq w\leq \frac{1}{\alpha_j} \\
         0  & \text{for } \frac{1}{\alpha_n} \leq w \\
        \end{cases} 
$$


The actual minimizer is either a local-quadratic minimizer or an interval boundaries. 


***CASE 1 :*** $$ w^* $$ ***is a local-quadratic minimizer***

$$w^*$$ must lie in the interval for which the corresponding quadratic is defined. 

***Sub-Case 1 :***  When $$ \frac{1}{\alpha_n} \leq w^* $$ 

$$w^*$$ = 0 $$ {w^*}^2 \leq \frac{1}{\lambda}$$   (if this is the solution, when $$\forall i, \frac{1}{\alpha_i} = 0$$)

***Sub-Case 2 :***  When $$w^* \leq \frac{1}{\alpha_1}$$

Since the solution $w^*$ must exist in the domain of the relevant quadratic, we have :

$$ \frac{1}{n\lambda}\sum_{i=1}^{n} (\alpha_i)  \leq   \frac{1}{\alpha_n}  $$ 

and thus, 

$$\begin{eqnarray}

  \lambda &\geq& \alpha_n \frac{\sum_{i=1}^{n} (\alpha_i)}{n} \geq \left( { \frac{\sum_{i=1}^{n} (\alpha_i)}{n} } \right) ^2  \nonumber \\

  \frac{1}{\lambda} & \geq & \left( \frac{\sum_{i=1}^{n} (\alpha_i)}{n\lambda} \right) ^2 \nonumber  \\

 w^* & \leq & \sqrt{\frac{1}{\lambda}} \nonumber \\

\end{eqnarray}$$


***Sub-Case 3 :*** When  $$\frac{1}{\alpha_{j-1}}\leq w^* \leq \frac{1}{\alpha_j}$$

Since the solution $w∗$ must exist in the domain of the relevant quadratic, we have :

$$\frac{1}{\alpha_{j-1}}\leq   \frac{\sum_{i=j}^{n} \alpha_i}{n\lambda}  \leq \frac{1}{\alpha_j}$$

and thus, 

$$\begin{eqnarray}

 \lambda & \geq &  \alpha_j \frac{\sum_{i=j}^{n} \alpha_i }{n} \nonumber  \\
 \lambda &\geq &  \frac{\sum_{i=j}^{n} \alpha_i}{n - j  +1} \frac{\sum_{i=j}^{n} \alpha_i }{n} \nonumber  \\ 

 & \geq &  \left(\frac{\sum_{i=j}^{n} \alpha_i}{n} \right)^2 \nonumber  \\ 

\frac{1}{\lambda}  & \geq &  \left(\frac{\sum_{i=j}^{n} \alpha_i}{n}\right)^2 \nonumber  \\ 

\Rightarrow w^*  &\leq&  \sqrt{\frac{1}{\lambda}} \nonumber  \\ 

\end{eqnarray}$$

***CASE 2 :*** When the solution occurs at an interval boundary or the solution occours at the intersection of the two quadratics i.e. at some $$\frac{1}{\alpha_j}$$


Since the two quadratics should have their minimizers on the opposite sides of  $$\frac{1}{\alpha_j}$$, we have, 

$$ \frac{\sum_{i=j}^{n} \alpha_i }{n\lambda} \geq \frac{1}{\alpha_j} \geq \frac{\sum_{i=j + 1}^{n} \alpha_i }{n\lambda}$$ 

$$   \frac{(n-i + 1) \alpha_j}{n \lambda} \geq \frac{1}{\alpha_j}$$

$$  \frac{1}{\alpha_j^2} \leq \frac{(n-i + 1)}{n \lambda} \leq \frac{1}{\lambda}$$

$$ w^* = \frac{1}{\alpha_j} \leq \sqrt{\frac{1}{\lambda}}  $$ 



This completes our simple proof. I am sure more elegant proofs exist for this problem, Please comment below if you know of other proofs. 

#### Acknowledgements
I am thankful to Yoram Singer for giving me this problem. I am also thankful to [Alex Irpan](http://www.alexirpan.com/) and [David Bieber](https://www.davidbieber.com/) in helping me set up this blog. 


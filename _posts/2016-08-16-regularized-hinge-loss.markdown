---
layout: post
title:  "Regularized Hinge Loss and the Bounded Solution"
date:   2016-08-16 17:00:04 -0700
categories: jekyll update
---

From a theoretical perspective, adding regularization makes a convex loss function strongly-convex, allowing faster optimization.  Regularization also restricts the solution space (under some constraints). Before diving into analyzing the minimizer, let us first define the problem. Hinge loss is often used as a surrogate loss for soft-margin SVM problems, and is defined as  : $$	l(w,x,y) = max(0, 1 - yw^Tx) $$

Note that Hinge loss is a positive convex function (but not strongly-convex). If we consider the soft margin SVM we have the objective function as follows : 

$$
	L(w) = \frac{\lambda}{2}\|w\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-y_iw^Tx_i)
$$ 

We want to find $$ w^* = argmin ( L(w)) $$

 **Claim :** 
$$ \forall x_i, y_i \epsilon R $$ ,the minimizer of the hinge loss always lies in a ball of radius $$ \sqrt{\frac{1}{\lambda}} $$ or $$ {w^*}^2 \leq \frac{1}{\lambda} $$ 


#### A Trivial Bound 
**It is easy to show that the solution $$w^*$$ lies in a ball of radius  $$\sqrt{\frac{2}{\lambda}}$$**

**Proof** - 

&nbsp;&nbsp;&nbsp;&nbsp; Observer that L(0) = 1 

&nbsp;&nbsp;&nbsp;&nbsp; Since, $$l(w,x,y)$$ is a positive function, and $$\frac{\lambda}{2}\|w\|^2 \geq 1 $$,  $$ \forall w $$  s.t.  $$ \|W\|^2 >  \frac{2}{\lambda}$$

&nbsp;&nbsp;&nbsp;&nbsp; It is easy to see that $$ \|w^*\|^2 \leq \frac{2}{\lambda} $$ 

Isn\'t this an interesting observations that for convex positive loss functions (which have the same constant value at w = 0), l-2 regularization restricts the solution in a ball around the origin.  But we can do better than $$ \frac{2}{\lambda}$$. This fact was used in the PEGASOS algorithm by Shai Shalev-Shwartz et al. Their proof used ideas from Duality to restrict the solution space. 

#### Improving over the Trivial Bound 
We give a proof using Geometric Intuition and Basic Algebra. Instead of analyzing the general problem, we make the following simplifications, all of which are consistent with the result : 

1. Since the solution exists for all $$x_i$$ and $$y_i$$, we would be replacing $$x_iy_i$$ by $$\alpha_i$$

2.  We give the analysis for one dimensional problem. Analysis for multiple dimensions is similar where we go to concentric rings and quadratic surfaces instead of parabola's and intervals in our analysis. Our proof's can be easily extended to multiple dimensions by operating on norms. 

3. Without loss of generality, we would be assuming $$w^* \geq 0 $$. In case $$w^* \leq 0$$, we can simply replace all the $$\alpha_i$$ by $$-\alpha_i$$ and then bound $$w^*$$. 

4. Under the assumption 3, we observe that the presence of terms with negative $$\alpha_k$$ pushes the optimal solution closer to the origin, when compared to the case when we replace $$\alpha_k$$ with $$- \alpha_k$$. Hence, we would be replacing all the negative $$\alpha_k$$ with $$-\alpha_k$$.

5. We assume that $$ \alpha_i's $$ are ordered i.e. $$\alpha_1 \geq \alpha_2 \geq \alpha_3 ... \geq \alpha_n$$. Thus, $$ \frac{1}{\alpha_n} \geq \frac{1}{\alpha_{n-1}} .... \geq \frac{1}{\alpha_3} \geq \frac{1}{\alpha_2} \geq \frac{1}{\alpha_1}$$

We observe that if we can bound the solution under the above assumptions, we also provide a bound in general case. Our new loss function now becomes - 

$$
    L(w) = \frac{\lambda}{2}\|w\|^2 + \frac{1}{n}\sum_{i=1}^{n}max(0,1-w^T\alpha_i)  ,  \text{where  } \alpha_i \geq 0 
$$

Our loss function is composed of a bunch of Hinge losse wedges, each growing towards the left from $$ \frac{1}{\alpha_i} $$ and slope $$ - \alpha_i$$.

The actual minimizer is either a local-quadratic minimizer or an interval boundaries. 


***CASE 1 :*** $$ w^* $$ ***is a local-quadratic minimizer***

$$w^*$$ must lie in the interval for which the quadratic is defined. 

***Sub-Case 1 :***  When $$ \frac{1}{\alpha_n} \leq w* $$ 

$$w^*$$ = 0 $$\Rightarrow {w^*}^2 \leq \frac{1}{\lambda}$$   (if this is the solution, when $$\forall i, \frac{1}{\alpha_i} = 0$$)

***Sub-Case 2 :***  When $$w^* \leq \frac{1}{\alpha_1}$$

$$ \frac{1}{n\lambda}\sum_{i=1}^{n} (\alpha_i) \leq \frac{1}{\alpha_n}  $$  (solution exists in this interval)

$$ \Rightarrow n\lambda \geq \alpha_n \sum_{i=1}^{n} (\alpha_i) \geq \frac{\sum_{i=1}^{n} (\alpha_i)}{n} \sum_{i=1}^{n} (\alpha_i)  $$

$$ \Rightarrow \lambda \geq (\frac{\sum_{i=1}^{n} (\alpha_i)}{n}) ^2 $$ 

$$ \Rightarrow \frac{1}{\lambda} \geq (\frac{\sum_{i=1}^{n} (\alpha_i)}{n\lambda}) ^2 $$ 

$$\Rightarrow w* \leq \sqrt{\frac{1}{\lambda}} $$

***Sub-Case 3 :*** When  $$\frac{1}{\alpha_{j-1}}\leq w^* \leq \frac{1}{\alpha_j}$$

$$\frac{1}{\alpha_{j-1}}\leq   \frac{1}{n\lambda}\sum_{i=j}^{n} (\alpha_i)  \leq \frac{1}{\alpha_j}$$

$$\Rightarrow n\lambda \geq  \alpha_j\sum_{i=j}^{n} (\alpha_i) $$

$$\Rightarrow n\lambda \geq  \frac{\sum_{i=j}^{n} \alpha_i}{n - j  +1} \sum_{i=j}^{n} (\alpha_i) $$

$$\Rightarrow n\lambda \geq  \frac{\sum_{i=j}^{n} \alpha_i}{n} \sum_{i=j}^{n} (\alpha_i) $$

$$\Rightarrow \frac{1}{\lambda} \geq  (\frac{\sum_{i=j}^{n} \alpha_i}{n\lambda})^2$$

$$\Rightarrow \frac{1}{\lambda} \geq  (w^*)^2$$

$$\Rightarrow w^* \leq \sqrt{\frac{1}{\lambda}} $$

***CASE 2 :*** When the solution occurs at an interval boundary or the solution occours at the intersection of the two quadratics i.e. at some $$\frac{1}{\alpha_j}$$


Since the two quadratics should have their minimizers on the opposite sides of  $$\frac{1}{\alpha_j}$$, we have, 

$$ \frac{\sum_{i=j}^{n} \alpha_i }{n\lambda} \geq \frac{1}{\alpha_j} \geq \frac{\sum_{i=j + 1}^{n} \alpha_i }{n\lambda}$$ 

$$ \Rightarrow  \frac{(n-i + 1) \alpha_j}{n \lambda} \geq \frac{1}{\alpha_j}$$

$$ \Rightarrow \frac{1}{\alpha_j^2} \leq \frac{(n-i + 1)}{n \lambda} \leq \frac{1}{\lambda}$$

$$\Rightarrow w* = \frac{1}{\alpha_j} \leq \sqrt{\frac{1}{\lambda}} $$

$$\tag*{$\blacksquare$}$$


This completes our simple proof. I am sure more elegant proofs exist for this problem, and I would be very happy to know about alternate solutions. 

#### Learning Experience 
1. Experiment your theoretical beliefs. At first, I could not believe that this constrain on the solution space exists for all the $$\alpha_i's$$ and then I wrote a code to experiment it and it was indeed true. 


---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.4.2
  kernelspec:
    display_name: Python
    language: python3
    name: python3
---


<a id='re-with-feedback'></a>
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>

<!-- #region -->
# Stability in Linear Rational Expectations Models


<a id='index-0'></a>
<!-- #endregion -->

## Contents

- [Stability in Linear Rational Expectations Models](#Stability-in-Linear-Rational-Expectations-Models)  
  - [Overview](#Overview)  
  - [Linear difference equations](#Linear-difference-equations)  
  - [Illustration: Cagan’s Model](#Illustration:-Cagan’s-Model)  
  - [Some Python code](#Some-Python-code)  
  - [Alternative code](#Alternative-code)  
  - [Another perspective](#Another-perspective)  
  - [Log money supply feeds back on log price level](#Log-money-supply-feeds-back-on-log-price-level)  
  - [Big $ P $, little $ p $ interpretation](#Big-$-P-$,-little-$-p-$-interpretation)  
  - [Fun with Sympy code](#Fun-with-Sympy-code)  


In addition to what’s in Anaconda, this lecture deploys the following libraries:

```python3 hide-output=true
!pip install --upgrade quantecon
```

```python3 hide-output=false
import numpy as np
import quantecon as qe
import matplotlib.pyplot as plt
%matplotlib inline
from sympy import *
init_printing()
```

<!-- #region -->
## Overview

This lecture studies stability in the context of an elementary rational expectations model.

We study a rational expectations version of Philip Cagan’s model [[Cag56]](https://python-programming.quantecon.org/zreferences.html#cagan) linking
the price level to the money supply.

Cagan did not use a rational expectations version of his model, but Sargent [[Sar77]](https://python-programming.quantecon.org/zreferences.html#sargent77hyper) did.

We study this model because it is intrinsically interesting and also because it  has a mathematical structure that
also appears in virtually all  linear rational expectations model, namely, that a key  endogenous variable equals
a mathematical expectation of a geometric sum of future values of another variable.

In a rational expectations version of Cagan’s model, the endogenous variable is the price level or rate of inflation and
the other variable is the money supply or the rate of change in the money supply.

In this lecture, we’ll encounter:

- a convenient formula for the expectation of geometric sum of future values of a variable  
- a way of solving an expectational difference equation by mapping it into a vector first-order difference equation and appropriately manipulating an eigen decomposition of the transition matrix in order to impose stability  
- a way to use a Big $ K $, little $ k $ argument to allow apparent feedback from endogenous to exogenous variables within a rational expectations equilibrium  
- a use of eigenvector decompositions of matrices that allowed Blanchard and Khan (1981) [[BK80]](https://python-programming.quantecon.org/zreferences.html#blanchard-khan) and Whiteman (1983) [[Whi83]](https://python-programming.quantecon.org/zreferences.html#whiteman) to solve a class of linear rational expectations models  


Cagan’s model with rational expectations
is formulated as an **expectational difference equation** whose solution is a rational expectations equilibrium.

We’ll start this lecture with a quick review of deterministic (i.e., non-random)
first-order and second-order linear difference equations.
<!-- #endregion -->

## Linear difference equations

In this quick review of linear difference equations, we’ll use the *backward shift* or *lag* operator $ L $

The lag operator $ L $  maps a sequence $ \{x_t\}_{t=0}^\infty $ into the sequence $ \{x_{t-1}\}_{t=0}^\infty $

We’ll can use  $ L $ in linear difference equations by using the equality
$ L x_t \equiv x_{t-1} $ in algebraic expressions.

Further,  the inverse $ L^{-1} $ of the lag operator is  the *forward shift*
operator.

In linear difference equations, we’ll often use the equaltiy  $ L^{-1} x_t \equiv x_{t+1} $ in the the algebra
below.

The algebra of lag and forward shift operators often simplifies formulas for linear difference equations and their
solutions.

<!-- #region -->
### First order

We want to solve a linear first-order scalar difference equation.

First, let $ |\lambda | < 1 $, and let
$ \{u_t\}_{t=-\infty}^\infty $ be a bounded sequence of scalar real
numbers.

Let $ L $ be the lag operator defined by
$ L x_t \equiv x_{t-1} $ and let $ L^{-1} $ be the forward shift
operator defined by $ L^{-1} x_t \equiv x_{t+1} $.

Then


<a id='equation-equn-1'></a>
$$
(1 - \lambda L) y_t = u_t, \forall t \tag{1}
$$

has solutions


<a id='equation-equn-2'></a>
$$
y_t = (1 -\lambda L)^{-1} u_t +k \lambda^t \tag{2}
$$

or

$$
y_t =  \sum_{j=0}^\infty \lambda^j u_{t-j} +k \lambda^t
$$

for any real number $ k $.

You can verify this fact by applying $ (1-\lambda L) $ to both sides
of equation [(2)](#equation-equn-2) and noting that $ (1 - \lambda L) \lambda^t =0 $.

To pin down $ k $ we need one condition imposed from outside (e.g.,
an initial or terminal condition) on the path of $ y $.

Now let $ | \lambda | > 1 $.

Rewrite equation [(1)](#equation-equn-1) as


<a id='equation-equn-3'></a>
$$
y_{t-1} = \lambda^{-1} y_t - \lambda^{-1} u_t , \forall t \tag{3}
$$

or


<a id='equation-equn-4'></a>
$$
(1 - \lambda^{-1} L^{-1}) y_t = - \lambda^{-1} u_{t+1}. \tag{4}
$$

A solution is


<a id='equation-equn-5'></a>
$$
y_t = - \lambda^{-1}\left({ 1 \over  1 - \lambda^{-1} L^{-1}} \right)
         u_{t+1} + k \lambda^t \tag{5}
$$

for any $ k $.

To verify that this is a solution, check the consequences of operating
on both sides of equation [(5)](#equation-equn-5) by $ (1 -\lambda L) $ and compare to
equation [(1)](#equation-equn-1).

Solution [(2)](#equation-equn-2) exists for $ |\lambda | < 1 $ because
the distributed lag in $ u $ converges.

Solution [(5)](#equation-equn-5) exists when $ |\lambda| > 1 $ because the **distributed
lead** in $ u $ converges.

When $ |\lambda | > 1 $, the distributed lag in $ u $ in [(2)](#equation-equn-2) may
diverge, so that a solution of this form does not exist.

The distributed lead in $ u $ in [(5)](#equation-equn-5) need not
converge when $ |\lambda| < 1 $.
<!-- #endregion -->

<!-- #region -->
### Second order

Now consider the second order difference equation


<a id='equation-equn-6'></a>
$$
(1-\lambda_1 L) (1 - \lambda_2 L) y_{t+1} = u_t \tag{6}
$$

where $ \{u_t\} $ is a bounded sequence, $ y_0 $ is an initial
condition, $ | \lambda_1 | < 1 $ and $ | \lambda_2| >1 $.

We seek a bounded sequence $ \{y_t\}_{t=0}^\infty $ that satisfies
[(6)](#equation-equn-6). Using insights from our analysis of the first-order equation,
operate on both sides of [(6)](#equation-equn-6) by the forward inverse of
$ (1-\lambda_2 L) $ to rewrite equation [(6)](#equation-equn-6) as

$$
(1-\lambda_1 L) y_{t+1} = -{\frac{\lambda_2^{-1}}{1 - \lambda_2^{-1}L^{-1}}} u_{t+1}
$$

or


<a id='equation-equn-7'></a>
$$
y_{t+1} = \lambda_1 y_t - \lambda_2^{-1} \sum_{j=0}^\infty \lambda_2^{-j} u_{t+j+1} . \tag{7}
$$

Thus, we obtained equation [(7)](#equation-equn-7) by
solving stable roots (in this case $ \lambda_1 $) **backward**, and
unstable roots (in this case $ \lambda_2 $) **forward**.

Equation [(7)](#equation-equn-7) has a form that we shall encounter often.

$ \lambda_1 y_t $ is called the **feedback part** and
$ -{\frac{\lambda_2^{-1}}{1 - \lambda_2^{-1}L^{-1}}} u_{t+1} $ is
called the **feedforward part** of the solution.
<!-- #endregion -->

<!-- #region -->
## Illustration: Cagan’s Model

Let

- $ m_t^d $ be the log of the demand for money  
- $ m_t $ be the log of the supply of money  
- $ p_t $ be the log of the price level  


It follows that $ p_{t+1} - p_t $ is the rate of inflation.

The logarithm of the demand for real money balances $ m_t^d - p_t $
is an inverse function of the expected rate of inflation
$ p_{t+1} - p_t $ for $ t \geq 0 $:

$$
m_t^d - p_t = - \beta (p_{t+1} - p_t ), \quad \beta >0
$$

Equate the demand for log money $ m_t^d $ to the supply of log money
$ m_t $ in the above equation and rearrange to deduce that the
logarithm of the price level $ p_t $ is related to the logarithm of
the money supply $ m_t $ by


<a id='equation-equation-1'></a>
$$
p_t = (1 -\lambda) m_t + \lambda p_{t+1} \tag{8}
$$

where $ \lambda \equiv \frac{\beta}{1+\beta} \in (0,1) $.

Solving the first order difference equation [(8)](#equation-equation-1) forward gives


<a id='equation-equation-2'></a>
$$
p_t = (1 - \lambda) \sum_{j=0}^\infty \lambda^j m_{t+j}, \tag{9}
$$

which is the unique **stable** solution of difference equation [(8)](#equation-equation-1) among
a class of more general solutions


<a id='equation-equation-1a'></a>
$$
p_t = (1 - \lambda) \sum_{j=0}^\infty \lambda^j m_{t+j} + c \lambda^{-t} \tag{10}
$$

that is indexed by the real number $ c \in {\bf R} $.

Because we want to focus on stable solutions, we set $ c=0 $.

We begin by assuming that the log of the money supply is **exogenous**
in the sense that it is an autonomous process that does not feed back on
the log of the price level.

In particular, we assume that the log of the money supply is described
by the linear state space system


<a id='equation-equation-3'></a>
$$
\begin{aligned}
  m_t &  = G x_t \\ x_{t+1} & = A x_t
 \end{aligned} \tag{11}
$$

where $ x_t $ is an $ n \times 1 $ vector that does not include
$ p_t $ or lags of $ p_t $, $ A $ is an $ n \times n $
matrix with eigenvalues that are less than $ \lambda^{-1} $ in
absolute values, and $ G $ is a $ 1 \times n $ selector matrix.

Variables appearing in the vector $ x_t $ contain information that
might help predict future values of the money supply.

We’ll take an example in which $ x_t $ includes only $ m_t $,
possibly lagged values of $ m $, and a constant.

An example of such an $ \{m_t\} $ process that fits info state space
system [(11)](#equation-equation-3) is one that satisfies the second order linear difference
equation

$$
m_{t+1} = \alpha + \rho_1 m_t + \rho_2 m_{t-1}
$$

where the zeros of the characteristic polynomial
$ (1 - \rho_1 z - \rho_2 z^2) $ are strictly greater than $ 1 $
in modulus

We seek a stable or non-explosive solution of the difference equation [(8)](#equation-equation-1) that
obeys the system comprised of [(8)](#equation-equation-1)-[(11)](#equation-equation-3).

By stable or non-explosive, we mean that neither $ m_t $ nor $ p_t $
diverges as $ t \rightarrow + \infty $.

This means that we are shutting down the term $ c \lambda^{-t} $ in equation [(10)](#equation-equation-1a) above by setting $ c=0 $

The solution we are after is


<a id='equation-equation-4'></a>
$$
p_t = F x_t \tag{12}
$$

where


<a id='equation-equation-5'></a>
$$
F = (1-\lambda) G (I - \lambda A)^{-1} \tag{13}
$$

**Note:** As mentioned above, an *explosive solution* of difference
equation [(8)](#equation-equation-1) can be constructed by adding to the right hand of [(12)](#equation-equation-4) a
sequence $ c \lambda^{-t} $ where $ c $ is an arbitrary positive
constant.
<!-- #endregion -->

<!-- #region -->
## Some Python code

We’ll construct examples that illustrate [(11)](#equation-equation-3).

Our first example takes as the law of motion for the log money supply
the second order difference equation


<a id='equation-equation-6'></a>
$$
m_{t+1} = \alpha + \rho_1 m_t + \rho_2 m_{t-1} \tag{14}
$$

that is parameterized by $ \rho_1, \rho_2, \alpha $

To capture this parameterization with system [(9)](#equation-equation-2) we set

$$
x_t = \begin{bmatrix} 1 \cr m_t \cr m_{t-1} \end{bmatrix} , \quad
  A= \begin{bmatrix} 1 & 0 & 0 \cr
                     \alpha & \rho_1 & \rho_2 \cr
                      0 & 1 & 0 \end{bmatrix} , \quad
  G = \begin{bmatrix} 0 & 1 & 0 \end{bmatrix}
$$

Here is Python code
<!-- #endregion -->

```python3 hide-output=false
λ = .9

α = 0
ρ1 = .9
ρ2 = .05

A = np.array([[1,  0,  0],
              [α, ρ1, ρ2],
              [0,  1,  0]])
G = np.array([[0, 1, 0]])
```

The matrix $ A $ has one eigenvalue equal to unity that is
associated with the $ A_{11} $ component that captures a
constant component of the state $ x_t $.

We can verify that the two eigenvalues of $ A $ not associated with
the constant in the state $ x_t $ are strictly less than unity in
modulus.

```python3 hide-output=false
eigvals = np.linalg.eigvals(A)
print(eigvals)
```

```python3 hide-output=false
(abs(eigvals) <= 1).all()
```

Now let’s compute $ F $ in formulas [(12)](#equation-equation-4) and [(13)](#equation-equation-5)

```python3 hide-output=false
# compute the solution, i.e. forumula (3)
F = (1 - λ) * G @ np.linalg.inv(np.eye(A.shape[0]) - λ * A)
print("F= ",F)
```

Now let’s simulate paths of $ m_t $ and $ p_t $ starting from an
initial value $ x_0 $.

```python3 hide-output=false
# set the initial state
x0 = np.array([1, 1, 0])

T = 100 # length of simulation

m_seq = np.empty(T+1)
p_seq = np.empty(T+1)

m_seq[0] = G @ x0
p_seq[0] = F @ x0

# simulate for T periods
x_old = x0
for t in range(T):

    x = A @ x_old

    m_seq[t+1] = G @ x
    p_seq[t+1] = F @ x

    x_old = x
```

```python3 hide-output=false
plt.figure()
plt.plot(range(T+1), m_seq, label='$m_t$')
plt.plot(range(T+1), p_seq, label='$p_t$')
plt.xlabel('t')
plt.title(f'λ={λ}, α={α}, $ρ_1$={ρ1}, $ρ_2$={ρ2}')
plt.legend()
plt.show()
```

In the above graph, why is the log of the price level always less than
the log of the money supply?

The answer is because

- according to equation [(9)](#equation-equation-2), $ p_t $ is a geometric weighted
  average of current and future values of $ m_t $, and  
- it happens that in this example future $ m $’s are always less
  than the current $ m $  


## Alternative code

We could also have run the simulation using the quantecon
**LinearStateSpace** code.

The following code block performs the calculation with that code.

```python3 hide-output=false
# construct a LinearStateSpace instance

# stack G and F
G_ext = np.vstack([G, F])

C = np.zeros((A.shape[0], 1))

ss = qe.LinearStateSpace(A, C, G_ext, mu_0=x0)
```

```python3 hide-output=false
T = 100

# simulate using LinearStateSpace
x, y = ss.simulate(ts_length=T)

# plot
plt.figure()
plt.plot(range(T), y[0,:], label='$m_t$')
plt.plot(range(T), y[1,:], label='$p_t$')
plt.xlabel('t')
plt.title(f'λ={λ}, α={α}, $ρ_1$={ρ1}, $ρ_2$={ρ2}')
plt.legend()
plt.show()
```

<!-- #region -->
### Special case

To simplify our presentation in ways that will let focus on an important
idea, in the above second-order difference equation [(14)](#equation-equation-6) that governs
$ m_t $, we now set $ \alpha =0 $,
$ \rho_1 = \rho \in (-1,1) $, and $ \rho_2 =0 $ so that the law
of motion for $ m_t $ becomes


<a id='equation-equation-7'></a>
$$
m_{t+1} =\rho m_t \tag{15}
$$

and the state $ x_t $ becomes

$$
x_t = m_t .
$$

Consequently,  we can set $ G =1, A =\rho $ making our formula [(13)](#equation-equation-5) for $ F $
become

$$
F = (1-\lambda) (1 -\lambda \rho)^{-1} .
$$

and the log the log price level satisfies

$$
p_t = F m_t .
$$

Please keep these formulas in mind as we investigate an alternative
route to and interpretation of the formula for $ F $.
<!-- #endregion -->

<!-- #region -->
## Another perspective

Above, we imposed stability or non-explosiveness on the solution of the key difference equation [(8)](#equation-equation-1)
in Cagan’s model by solving the  unstable root $ \lambda^{-1} $ forward.

To shed light on the mechanics involved in imposing stability on a
solution of a potentially unstable system of linear difference equations
and to prepare the way for generalizations of our model in which the
money supply is allowed to feed back on the price level itself, we stack
equations [(8)](#equation-equation-1) and [(15)](#equation-equation-7) to form the system


<a id='equation-equation-8'></a>
$$
\begin{bmatrix} m_{t+1} \cr p_{t+1} \end{bmatrix} = \begin{bmatrix} \rho & 0 \\ - (1-\lambda)/\lambda & \lambda^{-1}  \end{bmatrix} \begin{bmatrix} m_t \\ p_t \end{bmatrix} \tag{16}
$$

or


<a id='equation-equation-9'></a>
$$
y_{t+1} = H y_t, \quad t \geq 0 \tag{17}
$$

where


<a id='equation-equation-10'></a>
$$
H = \begin{bmatrix} \rho & 0 \\ - (1-\lambda)/\lambda & \lambda^{-1}  \end{bmatrix} . \tag{18}
$$

Transition matrix $ H $ has eigenvalues $ \rho \in (0,1) $ and
$ \lambda^{-1} > 1 $.

Because an eigenvalue of $ H $ exceeds unity, if we iterate on
equation [(17)](#equation-equation-9) starting from an arbitrary initial vector
$ y_0 = \begin{bmatrix} m_0 \\ p_0 \end{bmatrix} $, we discover that
in general absolute values of both components of $ y_t $ diverge
toward $ +\infty $ as $ t \rightarrow + \infty $.

To substantiate this claim, we can use the eigenector matrix
decomposition of $ H $ that is available to us because the
eigenvalues of $ H $ are distinct

$$
H = Q \Lambda Q^{-1} .
$$

Here $ \Lambda $ is a diagonal matrix of eigenvalues of $ H $
and $ Q $ is a matrix whose columns are eigenvectors of the
corresponding eigenvalues.

Note that

$$
H^t = Q \Lambda^t Q^{-1}
$$

so that

$$
y_t = Q \Lambda^t Q^{-1} y_0
$$

For almost all initial vectors $ y_0 $, the presence of the
eigenvalue $ \lambda^{-1} > 1 $ causes both components of
$ y_t $ to diverge in absolute value to $ +\infty $.

To explore this outcome in more detail, we use the following
transformation

$$
y^*_t = Q^{-1} y_t
$$

that allows us to represent the dynamics in a way that isolates the
source of the propensity of paths to diverge:

$$
y^*_{t+1} = \Lambda^t y^*_t
$$

Staring at this equation indicates that unless


<a id='equation-equation-11'></a>
$$
y^*_0 = \begin{bmatrix} y^*_{1,0} \cr 0 \end{bmatrix} , \tag{19}
$$

the path of $ y^*_t $ and therefore the paths of both components of
$ y_t = Q y^*_t $ will diverge in absolute value as
$ t \rightarrow +\infty $. (We say that the paths *explode*)

Equation [(19)](#equation-equation-11) also leads us to conclude that there is a unique setting
for the initial vector $ y_0 $ for which both components of
$ y_t $ do not diverge.

The required setting of $ y_0 $ must evidently have the property
that

$$
Q y_0 =  y^*_0 = \begin{bmatrix} y^*_{1,0} \cr 0 \end{bmatrix} .
$$

But note that since
$ y_0 = \begin{bmatrix} m_0 \cr p_0 \end{bmatrix} $ and $ m_0 $
is given to us an an initial condition, it has to be $ p_0 $ that
does all the adjusting to satisfy this equation.

Sometimes this situation is described by saying that while $ m_0 $
is truly a **state** variable, $ p_0 $ is a **jump** variable that
is free to adjust at $ t=0 $ in order to satisfy the equation.

Thus, in a nutshell the unique value of the vector $ y_0 $ for which
the paths of $ y_t $ do not diverge must have second component
$ p_0 $ that verifies equality [(19)](#equation-equation-11) by setting the second component
of $ y^*_0 $ equal to zero.

The component $ p_0 $ of the initial vector
$ y_0 = \begin{bmatrix} m_0 \cr p_0 \end{bmatrix} $ must evidently
satisfy

$$
Q^{\{2\}} y_0 =0
$$

where $ Q^{\{2\}} $ denotes the second row of $ Q^{-1} $, a
restriction that is equivalent to


<a id='equation-equation-12'></a>
$$
Q^{21} m_0 + Q^{22} p_0 = 0 \tag{20}
$$

where $ Q^{ij} $ denotes the $ (i,j) $ component of
$ Q^{-1} $.

Solving this equation for $ p_0 $ we find


<a id='equation-equation-13'></a>
$$
p_0 = - (Q^{22})^{-1} Q^{21} m_0. \tag{21}
$$

This is the unique **stabilizing value** of $ p_0 $ as a function of
$ m_0 $.
<!-- #endregion -->

<!-- #region -->
### Refining the formula

We can get an even more convenient formula for $ p_0 $ that is cast
in terms of components of $ Q $ instead of components of
$ Q^{-1} $.

To get this formula, first note that because $ (Q^{21}\ Q^{22}) $ is
the second row of the inverse of $ Q $ and because
$ Q^{-1} Q = I $, it follows that

$$
\begin{bmatrix} Q^{21} & Q^{22} \end{bmatrix}  \begin{bmatrix} Q_{11}\cr Q_{21} \end{bmatrix} = 0
$$

which implies that

$$
Q^{21} Q_{11} + Q^{22} Q_{21} = 0.
$$

Therefore,

$$
-(Q^{22})^{-1} Q^{21} = Q_{21} Q^{-1}_{11}.
$$

So we can write


<a id='equation-equation-14'></a>
$$
p_0 = Q_{21} Q_{11}^{-1} m_0 . \tag{22}
$$

It can be verified that this formula replicates itself over time so that


<a id='equation-equation-15'></a>
$$
p_t = Q_{21} Q^{-1}_{11} m_t. \tag{23}
$$

To implement formula [(23)](#equation-equation-15), we want to compute $ Q_1 $ the
eigenvector of $ Q $ associated with the stable eigenvalue
$ \rho $ of $ Q $.

By hand it can be verified that the eigenvector associated with the
stable eigenvalue $ \rho $ is proportional to

$$
Q_1  = \begin{bmatrix} 1-\lambda  \rho \\ 1 - \lambda   \end{bmatrix}.
$$

Notice that if we set $ A=\rho $ and $ G=1 $ in our earlier
formula for $ p_t $ we get

$$
Q = G (I - \lambda A)^{-1} m_t =  (1-\lambda) (1 - \lambda \rho)^{-1} m_t
$$

a formula that is equivalent with

$$
p_t = Q_{21} Q_{11}^{-1}  m_t ,
$$

where

$$
Q_1 = \begin{bmatrix} Q_{11} \\ Q_{21}  \end{bmatrix}.
$$
<!-- #endregion -->

<!-- #region -->
### Some remarks about feedback

We have expressed [(16)](#equation-equation-8) in what superficially appears to be a form in
which $ y_{t+1} $ feeds back on $ y_t $. even though what we
actually want to represent is that the component $ p_t $ feeds
**forward** on $ p_{t+1} $, and through it, on future
$ m_{t+j} $, $ j = 0, 1, 2, \ldots $.

A tell-tale sign that we should look beyond its superficial “feedback”
form is that $ \lambda^{-1} > 1 $ so that the matrix $ H $ in
[(16)](#equation-equation-8) is **unstable**

- it has one eigenvalue $ \rho $ that is less than one in modulus
  that does not imperil stability, but $ \ldots $  
- it has a second eigenvalue $ \lambda^{-1} $ that exceeds one in
  modulus and that makes $ H $ an unstable matrix  


We’ll keep these observations in mind as we turn now to a case in which
the log money supply actually does feed back on the log of the price
level.
<!-- #endregion -->

<!-- #region -->
## Log money supply feeds back on log price level

The same pattern of eigenvalues splitting around unity, with one being
below unity and another greater than unity, sometimes continues to
prevail when there is  *feedback* from the log price level to the log
money supply.

Let the feedback rule be


<a id='equation-equation-16'></a>
$$
m_{t+1} =  \rho m_t + \delta p_t \tag{24}
$$

where $ \rho \in (0,1) $ as before and where we shall now allow
$ \delta \neq 0 $.

However,
$ \delta $ cannot be too large if things are to fit together as we
wish to deliver a stable system for some initial value $ p_0 $ that we want to determine uniquely.
.

The forward-looking equation [(8)](#equation-equation-1) continues to describe equality between
the demand and supply of money.

We assume that equations [(8)](#equation-equation-1) and [(24)](#equation-equation-16) govern
$ y_t \equiv \begin{bmatrix} m_t \cr p_t \end{bmatrix} $ for
$ t \geq 0 $

The transition matrix $ H $ in the law of motion

$$
y_{t+1} = H y_t
$$

now becomes

$$
H = \begin{bmatrix} \rho & \delta \\ - (1-\lambda)/\lambda & \lambda^{-1}  \end{bmatrix} .
$$

We take $ m_0 $ as a given intial condition and as before seek an
initial value $ p_0 $ that stabilizes the system in the sense that
$ y_t $ converges as $ t \rightarrow + \infty $.

Our approach is identical with that followed above and is based on an
eigenvalue decomposition in which, cross our fingers, one eigenvalue
exceeds unity and the other is less than unity in absolute value.

When $ \delta \neq 0 $ as we now assume, the eigenvalues of
$ H $ are no longer $ \rho \in (0,1) $ and
$ \lambda^{-1} > 1 $

We’ll just calculate them and apply the same algorithm that we used
above.

That algorithm remains valid so long as the eigenvalues split around
unity as before.

Again we assume that $ m_0 $ is  an initial condition, but that
$ p_0 $ is not given but to be solved for.

Let’s write and execute some Python code that will let us explore how outcomes depend on
$ \delta $.
<!-- #endregion -->

```python3 hide-output=false
def construct_H(ρ, λ, δ):
    "contruct matrix H given parameters."

    H = np.empty((2, 2))
    H[0, :] = ρ,δ
    H[1, :] = - (1 - λ) / λ, 1 / λ

    return H

def H_eigvals(ρ=.9, λ=.5, δ=0):
    "compute the eigenvalues of matrix H given parameters."

    # construct H matrix
    H = construct_H(ρ, λ, δ)

    # compute eigenvalues
    eigvals = np.linalg.eigvals(H)

    return eigvals
```

```python3 hide-output=false
H_eigvals()
```

Notice that a negative δ will not imperil the stability of the matrix
$ H $, even if it has a big absolute value.

```python3 hide-output=false
# small negative δ
H_eigvals(δ=-0.05)
```

```python3 hide-output=false
# large negative δ
H_eigvals(δ=-1.5)
```

A sufficiently small positive δ also causes no problem.

```python3 hide-output=false
# sufficiently small positive δ
H_eigvals(δ=0.05)
```

But a large enough positive δ makes both eigenvalues of $ H $
strictly greater than unity in modulus.

For example,

```python3 hide-output=false
H_eigvals(δ=0.2)
```

We want to study systems in which one eigenvalue exceeds unity in
modulus while the other is less than unity in modulus, so we avoid
values of $ \delta $ that are too large

```python3 hide-output=false
def magic_p0(m0, ρ=.9, λ=.5, δ=0):
    """
    Use the magic formula (8) to compute the level of p0
    that makes the system stable.
    """

    H = construct_H(ρ, λ, δ)
    eigvals, Q = np.linalg.eig(H)

    # find the index of the smaller eigenvalue
    ind = 0 if eigvals[0] < eigvals[1] else 1

    # verify that the eigenvalue is less than unity
    if eigvals[ind] > 1:

        print("both eigenvalues exceed unity in modulus")

        return None

    p0 = Q[1, ind] / Q[0, ind] * m0

    return p0
```

Let’s plot how the solution $ p_0 $ changes as $ m_0 $
changes for different settings of $ \delta $.

```python3 hide-output=false
m_range = np.arange(0.1, 2., 0.1)

for δ in [-0.05, 0, 0.05]:
    plt.plot(m_range, [magic_p0(m0, δ=δ) for m0 in m_range], label=f"δ={δ}")
plt.legend()

plt.xlabel("$m_0$")
plt.ylabel("$p_0$")
plt.show()
```

To look at things from a different angle, we can fix the initial value $ m_0 $ and
see how $ p_0 $ changes as $ \delta $ changes.

```python3 hide-output=false
m0 = 1

δ_range = np.linspace(-0.05, 0.05, 100)
plt.plot(δ_range, [magic_p0(m0, δ=δ) for δ in δ_range])
plt.xlabel('$\delta$')
plt.ylabel('$p_0$')
plt.title(f'$m_0$={m0}')
plt.show()
```

Notice that when $ \delta $ is large enough, both eigenvalues exceed
unity in modulus, causing a stabilizing value of $ p_0 $ not to
exist.

```python3 hide-output=false
magic_p0(1, δ=0.2)
```

## Big $ P $, little $ p $ interpretation

It is helpful to view our solutions with feedback from the price level or inflation to money or the rate of money
creation in terms of the Big $ K $, little $ k $ idea discussed in [Rational Expectations Models](https://python-programming.quantecon.org/rational_expectations.html)

This will help us sort out what is taken as given by the decision makers who use the
difference equation [(9)](#equation-equation-2) to determine $ p_t $ as a function of their forecasts of future values of
$ m_t $.

Let’s write the stabilizing solution that we have computed using the eigenvector decomposition of $ H $ as
$ P_t = F^* m_t $ where

$$
F^* = Q_{21} Q_{11}^{-1}
$$

Then from $ P_{t+1} = F^* m_{t+1} $ and $ m_{t+1} = \rho m_t + \delta P_t $ we can deduce the recursion $ P_{t+1} = F^* \rho m_t + F^* \delta P_t $ and create the stacked system

$$
\begin{bmatrix} m_{t+1} \cr P_{t+1} \end{bmatrix}  =    \begin{bmatrix} \rho & \delta \cr
                F^* \rho & F^* \delta   \end{bmatrix} \begin{bmatrix} m_t \cr P_t \end{bmatrix}
$$

or

$$
x_{t+1} = A x_t
$$

where $ x_t = \begin{bmatrix} m_t \cr P_t \end{bmatrix} $.

Then apply formula [(13)](#equation-equation-5) for $ F $ to deduce that

$$
p_t = F \begin{bmatrix} m_t \cr P_t \end{bmatrix} = F \begin{bmatrix} m_t \cr F^* m_t \end{bmatrix}
$$

which implies that

$$
p_t = \begin{bmatrix} F_1 & F_2 \end{bmatrix}    \begin{bmatrix} m_t \cr F^* m_t \end{bmatrix} = F_1 m_t + F_2 F^* m_t
$$

so that we expect to have

$$
F^* = F_1 + F_2 F^*
$$

We verify this equality in the next block of Python code that implements the following
computations.

1. For the system with $ \delta\neq 0 $ so that there is feedback,
  we compute the stabilizing solution for $ p_t $ in the form
  $ p_t = F^* m_t $ where $ F^* = Q_{21}Q_{11}^{-1} $ as above.  
1. Recalling the system [(11)](#equation-equation-3), [(12)](#equation-equation-4), and [(13)](#equation-equation-5) above, we define
  $ x_t = \begin{bmatrix} m_t \cr P_t \end{bmatrix} $ and notice
  that it is Big $ P_t $ and not little $ p_t $ here. Then we form $ A $ and $ G $ as
  $ A = \begin{bmatrix}\rho & \delta \cr F^* \rho & F^*\delta \end{bmatrix} $
  and $ G = \begin{bmatrix} 1 & 0 \end{bmatrix} $ and we compute
  $ \begin{bmatrix}  F_1 &  F_2 \end{bmatrix} \equiv F $
  from equation [(13)](#equation-equation-5) above.  
1. We compute $ F_1 +  F_2 F^* $ and compare it
  with $ F^* $ and verify equality.  

```python3 hide-output=false
# set parameters
ρ = .9
λ = .5
δ = .05
```

```python3 hide-output=false
# solve for F_star
H = construct_H(ρ, λ, δ)
eigvals, Q = np.linalg.eig(H)

ind = 0 if eigvals[0] < eigvals[1] else 1
F_star = Q[1, ind] / Q[0, ind]
F_star
```

```python3 hide-output=false
# solve for F_check
A = np.empty((2, 2))
A[0, :] = ρ, δ
A[1, :] = F_star * A[0, :]

G = np.array([1, 0])

F_check= (1 - λ) * G @ np.linalg.inv(np.eye(2) - λ * A)
F_check
```

Compare $ F^* $ with $ F_1 + F_2 F^* $

```python3 hide-output=false
F_check[0] + F_check[1] * F_star, F_star
```

## Fun with Sympy code

This section is a small gift for readers who have made it this far.

It puts Sympy to work on our model.

Thus, we  use Sympy to compute some of the key objects comprising the eigenvector decomposition of $ H $.

$ H $ with nonzero $ \delta $.

```python3 hide-output=false
λ, δ, ρ = symbols('λ, δ, ρ')
```

```python3 hide-output=false
H1 = Matrix([[ρ,δ], [- (1 - λ) / λ, λ ** -1]])
```

```python3 hide-output=false
H1
```

```python3 hide-output=false
H1.eigenvals()
```

```python3 hide-output=false
H1.eigenvects()
```

$ H $ with $ \delta $ being zero.

```python3 hide-output=false
H2 = Matrix([[ρ,0], [- (1 - λ) / λ, λ ** -1]])
```

```python3 hide-output=false
H2
```

```python3 hide-output=false
H2.eigenvals()
```

```python3 hide-output=false
H2.eigenvects()
```

Below we do induce sympy to do the following fun things for us analytically:

1. We compute the matrix $ Q $ whose first column is
  the eigenvector associated with $ \rho $. and whose second column
  is the eigenvector associated with $ \lambda^{-1} $.  
1. We use sympy to compute the inverse $ Q^{-1} $ of $ Q $
  (both in symbols).  
1. We use sympy to compute $ Q_{21} Q_{11}^{-1} $ (in symbols).  
1. Where $ Q^{ij} $ denotes the $ (i,j) $ component of
  $ Q^{-1} $, weighted use sympy to compute
  $ - (Q^{22})^{-1} Q^{21} $ (again in symbols)  

```python3 hide-output=false
# construct Q
vec = []
for i, (eigval, _, eigvec) in enumerate(H2.eigenvects()):

    vec.append(eigvec[0])

    if eigval == ρ:
        ind = i

Q = vec[ind].col_insert(1, vec[1-ind])
```

```python3 hide-output=false
Q
```

$ Q^{-1} $

```python3 hide-output=false
Q_inv = Q ** (-1)
Q_inv
```

$ Q_{21}Q_{11}^{-1} $

```python3 hide-output=false
Q[1, 0] / Q[0, 0]
```

$ −(Q^{22})^{−1}Q^{21} $

```python3 hide-output=false
- Q_inv[1, 0] / Q_inv[1, 1]
```

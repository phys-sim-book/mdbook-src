## Computing $\partial\mathbf{P} /\partial\mathbf{F}$ or $\delta\mathbf{P}$

To compute the derivative of $\mathbf{P}$ with respect to $\mathbf{F}$, we leverage the rotational invariance property discussed earlier for $\mathbf{P}$. Consider two arbitrary rotation matrices $\mathbf{R}$ and $\mathbf{Q}$. From the rotational properties of $\mathbf{P}$, we have:

$$
\mathbf{P}(\mathbf{F}) = \mathbf{P}(\mathbf{R}\mathbf{R}^T \mathbf{F} \mathbf{Q} \mathbf{Q}^T) = \mathbf{R} \mathbf{P}(\mathbf{R}^T \mathbf{F} \mathbf{Q}) \mathbf{Q}^T.
$$

Define $\mathbf{K} = \mathbf{R}^T \mathbf{F} \mathbf{Q}$, then:

$$
\mathbf{P}(\mathbf{F}) = \mathbf{R} \mathbf{P}(\mathbf{K}) \mathbf{Q}^T.
$$

Taking the differential of $\mathbf{P}$, while treating $\mathbf{R}$ and $\mathbf{Q}$ as constants, gives:

$$
\delta\mathbf{P} = \mathbf{R} \left[\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\mathbf{K}) : \delta(\mathbf{K})\right] \mathbf{Q}^T = \mathbf{R} \left[\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\mathbf{K}) : (\mathbf{R}^T \delta\mathbf{F} \mathbf{Q})\right] \mathbf{Q}^T.
$$

By setting $\mathbf{R} = \mathbf{U}$ and $\mathbf{Q} = \mathbf{V}$, where $\mathbf{K} = \bm{\Sigma}$, the differential expression simplifies to:

$$
\delta\mathbf{P} = \mathbf{U} \left[\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma}) : (\mathbf{U}^T \delta\mathbf{F} \mathbf{V})\right] \mathbf{V}^T.
$$

The tensorial derivative $\partial\mathbf{P}/\partial\mathbf{F}$ is then expressed in index notation as:

$$
(\delta\mathbf{P})_{ij} = U_{ik} \left(\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})\right)_{klmn} U_{rm} \delta F_{rs} V_{sn} V_{jl}, \quad \text{and} \quad
(\delta\mathbf{P})_{ij} =\left(\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\mathbf{F})\right)_{ijrs} \delta F_{rs}.
$$

These expressions must hold for any $\delta\mathbf{F}$, leading to the relationship:

$$
\left(\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\mathbf{F})\right)_{ijrs} = \left(\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})\right)_{klmn} U_{ik} U_{rm} V_{sn} V_{jl}.
$$

So the remaining task is computing $\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})$. We show how to do it in 3D.

First, let's introduce Rodrigues' rotation formula, which provides a method for expressing any rotation matrix in terms of a unit vector $\mathbf{k}$ and a rotation angle $\theta$. The formula is given by:
$$
\mathbf{R}=\mathbf{I}+\sin(\theta)\mathbf{K}+(1-\cos(\theta))\mathbf{K}^2, {{numeq}}{eqn:rodrigues}
$$
where $\mathbf{K}$ is the skew-symmetric cross-product matrix associated with $\mathbf{k}$. This formula shows that any rotation matrix is characterized by just three degrees of freedom, denoted as $r_1, r_2, r_3$. These components are used to define the rotation vector $\mathbf{r}$, from which $\mathbf{k}$ and $\theta$ are derived as follows:

$$
\mathbf{k} = \frac{\mathbf{r}}{|\mathbf{r}|}, \quad \theta = |\mathbf{r}|.
$$

Using this parameterization, rotation matrices $\mathbf{U}$ and $\mathbf{V}$ can each be described by three parameters.

Now we have the following code for defining $\mathbf{F}$ in terms of
$s1$, $s2$, $s3$, $u1$, $u2$, $u3$, $v1$, $v2$, $v3$, where $\mathbf{U}$ and $\mathbf{V}$ are defined by $u_i$ and $v_i$ with
Rodrigues' rotation formula, $s_i$ are the singular values from $\bm{\Sigma}$.

```
id=IdentityMatrix[3];
var={s1,s2,s3,u1,u2,u3,v1,v2,v3};
Sigma=DiagonalMatrix[{s1,s2,s3}];
cp[k1_,k2_,k3_]={{0,-k3,k2},{k3,0,-k1},{-k2,k1,0}};
vV={v1,v2,v3};
vU={u1,u2,u3};
nv=Sqrt[Dot[vV,vV]];
nu=Sqrt[Dot[vU,vU]];
UU=cp[u1,u2,u3]/nu;
VV=cp[v1,v2,v3]/nv;
U=id+Sin[nu]*UU+(1-Cos[nu])*UU.UU;
V=id+Sin[nv]*VV+(1-Cos[nv])*VV.VV;
F=U.Sigma.Transpose[V];
```
where cp is a function for generating the cross-product matrix (corresponding to computing
$\mathbf{K}$ in Equation {{eqref: eqn:rodrigues}}).

From now on, we write the $3\times 3\times 3\times 3$ tensor
$\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})$ and any other such tensors to $9 \times 9$ matrices.
That means each $3 \times 3$ matrix is now a size-$9$ vector. It is easy to see the old
$\frac{\partial P_{ij}}{\partial F_{kl}}$ is now
$\frac{\partial P_{3(i-1)+j}}{\partial F_{3(k-1)+l}}$. We further call vector $\mathbf{S}=\{s1,s2,s3,u1,u2,u3,v1,v2,v3\}$ being the parametrization of
$\mathbf{F}$. Then we can apply the chain rule
$$
\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})=\frac{\partial\mathbf{P}}{\partial\mathbf{S}}(\bm{\Sigma})\frac{\partial\mathbf{S}}{\partial\mathbf{F}}(\bm{\Sigma})
$$

Here are the Mathematica code for computing them. Note that we achieve $\mathbf{F}=\bm{\Sigma}$ by taking the limit $\{u1,u2,u3,v1,v2,v3\}=+\epsilon$, which
correspond to nearly zero rotations.
```language=Mathematica
dFdS=D[Flatten[F],{var}];
dFdS0=dFdS/.{u1->e,u2->e,u3->e,v1->e,v2->e,v3->e};
dFdS1=Limit[dFdS0,e->0,Direction->-1];
dSdF0=Inverse[dFdS1];
Phat=DiagonalMatrix[{t1[s1,s2,s3],t2[s1,s2,s3],t3[s1,s2,s3]}];
P=U.Phat.Transpose[V];
dPdS=D[Flatten[P],{var}];
dPdS0=dPdS/.{u1->e,u2->e,u3->e,v1->e,v2->e,v3->e};
dPdS1=Limit[dPdS0,e->0,Direction->-1];
dPdF=Simplify[dPdS1.dSdF0];
```
Note 'Direction->-1' in Mathematica means taking the limit from large values to the small
limit value. The Mathematica computation result will be given in terms of the singular
values and $\hat{\mathbf{P}}$. One can then take the formula for implementing them in the code.
{{#cite stomakhin2012energetically}} gives the result where $\frac{\partial\mathbf{P}}{\partial\mathbf{F}}(\bm{\Sigma})$ (size $9
\times 9$ matrix) is permuted to be a block diagonal matrix with diagonal blocks $\mathbf{A}^{3\times 3},\mathbf{B}_{12}^{2\times 2},\mathbf{B}_{13}^{2\times 2},\mathbf{B}_{23}^{2\times 2}$, where
$$
\mathbf{A} = \left(
\begin{array}{ccc}
\hat{\Psi}_{,\sigma_1 \sigma_1} & \hat{\Psi}_{,\sigma_1 \sigma_2} & \hat{\Psi}_{,\sigma_1 \sigma_3} \\
\hat{\Psi}_{,\sigma_2 \sigma_1} & \hat{\Psi}_{,\sigma_2 \sigma_2} & \hat{\Psi}_{,\sigma_2 \sigma_3} \\
\hat{\Psi}_{,\sigma_3 \sigma_1} & \hat{\Psi}_{,\sigma_3 \sigma_2} & \hat{\Psi}_{,\sigma_3 \sigma_3}
\end{array}
\right)
$$
and
$$
\mathbf{B}_{ij} = \frac{1}{\sigma_i^2-\sigma_j^2}\left(
\begin{array}{cc}
\sigma_i \hat{\Psi}_{,\sigma_i}-\sigma_j \hat{\Psi}_{,\sigma_j} & \sigma_j \hat{\Psi}_{,\sigma_i}-\sigma_i \hat{\Psi}_{,\sigma_j} \\
\sigma_j \hat{\Psi}_{,\sigma_i}-\sigma_i \hat{\Psi}_{,\sigma_j} & \sigma_i \hat{\Psi}_{,\sigma_i}-\sigma_j \hat{\Psi}_{,\sigma_j} 
\end{array}
\right).
$$
Denominator clamping is needed for terms in $\mathbf{B}$ that may introduce division-by-zero (after fully simplifying them).
Here we denote $\frac{\partial \hat{\Psi}}{\partial \sigma_i}$ and $\frac{\partial^2 \hat{\Psi}}{\partial \sigma_i\partial \sigma_j}$ as $\hat{\Psi}_{,\sigma_i}$ and $\hat{\Psi}_{,\sigma_i\sigma_j}$ respectively.
The division by $\sigma_i^2-\sigma_j^2$ is problematic when two singular values are nearly equal or when two singular
values nearly sum to zero. The latter is possible with a convention for permitting negative singular values (as in invertible elasticity {{#cite irving2004invertible}} {{#cite stomakhin2012energetically}}).

Expanding $\mathbf{B}_{ij}$ in terms of partial fractions yields the useful decomposition
$$
\mathbf{B}_{ij} = \frac{1}{2}\frac{\hat{\Psi}_{,\sigma_i}-\hat{\Psi}_{,\sigma_j}}{\sigma_i-\sigma_j}\left(
\begin{array}{cc}
1 & 1\\
1 & 1
\end{array}
\right) + \frac{1}{2}\frac{\hat{\Psi}_{,\sigma_i}+\hat{\Psi}_{,\sigma_j}}{\sigma_i+\sigma_j}\left(
\begin{array}{cc}
1 & -1\\
-1 & 1
\end{array}
\right).
$$
Note that if $\hat{\Psi}$ is invariant under permutation of the singular values, then $\hat{\Psi}_{,\sigma_i} \rightarrow \hat{\Psi}_{,\sigma_j}$ as 
$\sigma_i \rightarrow \sigma_j$. Thus,
the first term can normally be computed robustly for an isotropic model if implemented carefully. The other
fraction can be computed robustly if $\hat{\Psi}_{,\sigma_i} + \hat{\Psi}_{,\sigma_j} \rightarrow 0$ as 
$\sigma_i +\sigma_j \rightarrow 0$.
But this usually does not hold as it means the constitutive model will have difficulty recovering from degenerate or
inverted configurations. Thus, this term will be unbounded under some circumstances.
We address this by clamping the magnitude of the denominator to not be smaller than $10^{-6}$ before division to bound the derivatives.

For 2D, a rotation matrix is now simply
paremetrized with a single $\theta$ where the reconstruction is

$$
\mathbf{R}=
\left(
\begin{array}{cc}
\cos\theta & -\sin\theta\\
\sin\theta & \cos\theta
\end{array}
\right).
$$
The 2D version of the whole Mathematica code is
```language=Mathematica
id=IdentityMatrix[2];
var={s1,s2,u1,v1};
S=DiagonalMatrix[{s1,s2}];
U={{Cos[u1],-Sin[u1]

},{Sin[u1],Cos[u1]}};
V={{Cos[v1],-Sin[v1]},{Sin[v1],Cos[v1]}};
F=U.S.Transpose[V];
dFdS=D[Flatten[F],{var}];
dFdS0=dFdS/.{u1->e,v1->e};
dFdS1=Limit[dFdS0,e->0,Direction->-1};
dSdF0=Inverse[dFdS1];
Phat=DiagonalMatrix[{t1[s1,s2],t2[s1,s2]}];
P=U.Phat.Transpose[V];
dPdS=D[Flatten[P],{var}];
dPdS0=dPdS/.{u1->e,v1->e};
dPdS1=Limit[dPdS0,e->0,Direction->-1];
dPdF=Simplify[dPdS1.dSdF0];
```
where $\mathbf{A}$ is now also $2\times 2$ and there is only one $\mathbf{B}$.
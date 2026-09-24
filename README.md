# lab3
Deliverable- 1 - 3D Perspective Projection


Camera model used throughout: principal point $(0,0)$, focal length $f=1$, $s_x=s_y=1$, skew $s_\theta=0$, so the intrinsic matrix is $K=I$ and a 3D point $(X,Y,Z)$ projects to

$$
u = \frac{X}{Z}, \qquad v = \frac{Y}{Z}.
$$

---

Deliverable 1: 3D Perspective Projection 

a) Sphere centered on the optical axis, $C=(0,0,d)$, radius $r$

**Expected shape.** The rays from the optical center that touch the sphere form a circular cone whose axis is the $Z$-axis. Slicing this cone with the plane $Z=1$ gives a **circle** centered at $(0,0)$.

Derivation (tangent-ray condition).** A viewing ray through pixel $(u,v)$ has direction $n=(u,v,1)$. The ray is tangent to the sphere when the distance from the center $C$ to the line $\{t\,n\}$ equals $r$:


\|C\|^2 - \frac{(C\cdot n)^2}{\|n\|^2} = r^2 .


With $\|C\|^2=d^2$, $C\cdot n = d$ and $\|n\|^2=u^2+v^2+1$:


d^2 - \frac{d^2}{u^2+v^2+1} = r^2
\;\Longrightarrow\;
d^2(u^2+v^2) = r^2 (u^2+v^2+1)



\boxed{\,u^2+v^2 = \frac{r^2}{d^2-r^2}\,}


This is a circle of radius $\rho = \dfrac{r}{\sqrt{d^2-r^2}}$ (well defined since $d>r+1>r$). The image of the (solid) sphere is the disk $u^2+v^2 \le \dfrac{r^2}{d^2-r^2}$.

Sanity check The tangent points lie on the circle $Z = d - r^2/d$ with radius $R=\dfrac{r\sqrt{d^2-r^2}}{d}$. Projecting, $\rho = R/Z = \dfrac{r}{\sqrt{d^2-r^2}}$. ✓

Equivalently, the cone half-angle $\alpha$ satisfies $\sin\alpha = r/d$, so $\rho=\tan\alpha$.

b) Sphere centered at an arbitrary location $C=(X_0,Y_0,Z_0)$

The same tangent-ray condition holds, with $D^2=\|C\|^2 = X_0^2+Y_0^2+Z_0^2$ and $k = D^2-r^2$:


(X_0u+Y_0v+Z_0)^2 = (D^2-r^2)(u^2+v^2+1).


Expanding gives a general **conic**


A u^2 + B uv + C' v^2 + Du + Ev + F = 0,


with $A = X_0^2-k$, $B = 2X_0Y_0$, $C' = Y_0^2-k$, $D=2X_0Z_0$, $E=2Y_0Z_0$, $F=Z_0^2-k$.

**Discriminant:**


B^2-4AC' = 4k\,(X_0^2+Y_0^2-k) = 4k\,(r^2 - Z_0^2).


Since the sphere is entirely in front of the camera ($Z_0>r$) and does not contain the optical center ($k>0$), the discriminant is negative, so the projection is an **ellipse**.

- If $X_0=Y_0=0$ (center on the optical axis), the ellipse becomes a **circle** (part a).
- Otherwise the cone is oblique, so the image is a genuine ellipse, stretched radially away from the image center (perspective distortion).



Deliverable 2: Vanishing Points [7 pts]

a) Vanishing point of two parallel 3D lines

Let the lines be $p_i(\lambda)=p_{0,i}+\lambda u$ with a common direction $u=(u_x,u_y,u_z)$, $i=1,2$. Projecting:


x_i(\lambda)=\frac{p_{0,i,x}+\lambda u_x}{p_{0,i,z}+\lambda u_z},
\qquad
y_i(\lambda)=\frac{p_{0,i,y}+\lambda u_y}{p_{0,i,z}+\lambda u_z}.


Taking $\lambda\to\pm\infty$ and assuming $u_z\neq 0$:


\boxed{\,v_\infty = \left(\frac{u_x}{u_z},\ \frac{u_y}{u_z}\right)\,}

This does not depend on $p_{0,i}$, so every line with direction $u$ converges to the same point. In homogeneous coordinates, $v_\infty \sim K u = u$.

Geometric view.- The projection of line $i$ is the intersection of the image plane $Z=1$ with the plane $\Pi_i$ through the optical center and the line. Both planes contain the direction $u$, so their intersection is the line through the origin with direction $u$. That line pierces $Z=1$ at $u/u_z$, which is the vanishing point.

If $u_z=0$ (lines parallel to the image plane) there is no finite vanishing point; it lies at infinity in the direction $(u_x,u_y)$.

b)  Condition for parallel lines to stay parallel

**Claim.** Distinct parallel 3D lines project to parallel image lines **if and only if** $u_z = 0$, i.e. the lines are parallel to the image plane.

Proof (⇐). If $u_z=0$, then $x_i=(p_{0,i,x}+\lambda u_x)/p_{0,i,z}$ and $y_i=(p_{0,i,y}+\lambda u_y)/p_{0,i,z}$, where $p_{0,i,z}$ is constant along the line. The image direction is

\frac{d}{d\lambda}(x_i,y_i)=\frac{1}{p_{0,i,z}}(u_x,u_y)\ \parallel\ (u_x,u_y),


the same for every line, so the image lines are parallel. ∎

**Proof (⇒).** If $u_z\neq0$, all projected lines pass through the finite point $v_\infty=(u_x/u_z,\,u_y/u_z)$ (part a). Two distinct lines through a common point intersect, so they are not parallel. ∎



Deliverable 3: Harris Corner Matching with SSD [10 pts]

Reference patch (Image 1, top-left 2×2):

```
100 102
 98 101
```

**Candidate A** (position (0,1)):

```
100 102
 98 101
```

Differences (ref − A): 0, 0, 0, 0


\text{SSD}_A = 0^2+0^2+0^2+0^2 = \mathbf{0}


**Candidate B** (position (0,2)):

```
102 103
101 104
```

Differences (ref − B): −2, −1, −3, −3


\text{SSD}_B = (-2)^2+(-1)^2+(-3)^2+(-3)^2 = 4+1+9+9 = \mathbf{23}


| Candidate | SSD |
|---|---|
| A (0,1) | **0** |
| B (0,2) | 23 |

**Candidate A is the better match** (perfect match, SSD = 0).

The reference patch is at (row 0, col 0) in Image 1 and its best match is at (row 0, col 1) in Image 2, so the shift vector is


\boxed{(dx,\,dy) = (1,\,0)}

That is 1 pixel to the right and 0 pixels vertically, consistent with the image having been shifted right by one pixel.


That is 1 pixel to the right and 0 pixels vertically, consistent with the image having been shifted right by one pixel.

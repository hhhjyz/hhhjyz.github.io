**1.2**
_a._ Performance via Pipelining
_b._ Dependability via Redundancy
_c._ Performance via Prediction
_d._ Make common case fast
_e._ Hierarchy of Memories
_f._ Design for Moore's Law
_g._ Use Abstraction to Simplify Design
_h._ Performance via Parallelism

**1.5**
_a._ P2
_b._

| processor | number of cycles | number of instructions |
| --------- | ---------------- | ---------------------- |
| p1        | 30G              | 20G                    |
| p2        | 25G              | 25G                    |
| p3        | 40G              | $\frac {200}{11}G$     |
_c._ 
$C(p1)=1.5\times 1.2=1.8$
$C(p2)=1\times 1.2=1.2$
$C(p3)= 2.2*1.2=2.64$
$f(p1)= \frac {20G\times 1.8}{7}=5.14Ghz$
$f(p2)=\frac {25G\times 1.2}{7}=4.29GHz$
$f(p3)=\frac {\frac {200}{11}G\times 2.64}{7}=6.75GHz$

**1.6**
_a._ 
- P1:$global CPI=1\times 0.1+2\times 0.2+3\times 0.5+3\times 0.2=2.6$
- P2:$global CPI = 2.0$
_b._
p1:$cycles=2.6*1.0E6=2.6E6$
p2:$cycles=2.0*1.0E6=2.0E6$
**1.7**
_a._$CPI(A)=1.1 ~~CPI(B)=1.25$
_b._$$\frac {f_B}{f_A}=\frac {N(B)\times CPI(B)}{N(A)\times CPI(A)}=1.37$$
_c._$\frac {T_A} {T_{new}}=1.67$
$\frac {T_B}{T_new}=2.27$
**1.13**
_1._
$T_{fp}=56s$
$T_{new}=236s,Reduction=5.6\%$
_2._ 
$$reduction = 20\%$$
_3._ 
$$ 
\begin{align}
&T_{reduction}=0.2*250s=50s>
T_{branch} \\ &So~it ~is ~impossible \end{align}$$


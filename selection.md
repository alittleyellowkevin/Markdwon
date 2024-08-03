|                            Symbol                            |                          Definition                          |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                             $m$                              |                     The number of assets                     |
|                             $n$                              |                     The trading periods                      |
|                             $d$                              |             Each asset has $ d $ kinds of prices             |
|                             $t$                              |             Period  $ t$ (where $ t$ = 1,..., n)             |
|                     $p_{t,i} \in R_+^d$                      |            The price of asset  $i$  at period $t$            |
|                   $P_t \in R^{m\times d}$                    |           The prices for all assets at this period           |
| $\Rho_t = \{P_{t-k},...,P_{t-1}\} \in R^{k \times m \times d}$ | prcie series of previous $k$-moment prices regarding period $t$ |



|                 Symbol                 |                          Definition                          |
| :------------------------------------: | :----------------------------------------------------------: |
|                  $S$                   |                     A finite state space                     |
|                  $A$                   |                   A finite set of actions                    |
|                  $T$                   | $T(s' | s, a)$ Is a state transition function that defines the next state $s'$ given the current state $s$ and action $a$ |
|                  $R$                   |                $R(s,a)$ is a reward function.                |
|               $\pi(a|s)$               |             An action given the current state s.             |
| $a_t = [a_{t,1}, a_{t,2},...,a_{t,m}]$ | $a_{t,i}$ indicates the wealth proportion regarding asset $i$ and $\sum_{i=1}^{m}a_{t,i}=1$ |
|                  $S$                   | prcie series of previous $k$-moment prices regarding period $t$ |


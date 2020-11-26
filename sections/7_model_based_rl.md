# `Model-Based` `Reinforcement Learning`

---

**`"Deep Latent Competition : Learning to Race Using Visual Control Policies in Latent Space"`**

- **[** `2020` **]**
**[[:memo:](https://www.gilitschenski.org/igor/publications/202011-corl-deep_latent_competition/corl20-deep_latent_competition.pdf)]**
**[[:octocat:](https://github.com/igilitschenski/multi_car_racing)]**
**[[🎞️](https://sites.google.com/view/deep-latent-competition)]**
**[** :mortar_board: `MIT` **]**
**[** :car: `Toyota` **]**

- **[** _`MARL`, `world models`, `self-play`, `imagination`, `latent space`_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://sites.google.com/view/deep-latent-competition).](../media/2020_schwarting_1.gif "[Source](https://sites.google.com/view/deep-latent-competition).")  |
|:--:|
| *Contrary to original **[`world model`](https://arxiv.org/abs/1803.10122)** where a **single agent** races alone, here **two cars compete against each other**. The authors show that **considering interactions** and trying to **infer the opponent's `actions`** are essential. Ignoring the competitor may be **easier to train**, but shows **limited performances**. One good strategy consists in driving as fast as possible, while trying to **hinder the other**, i.e. **adversarial** behaviours. [Source](https://sites.google.com/view/deep-latent-competition).* |

| ![[Source](https://www.gilitschenski.org/igor/publications/202011-corl-deep_latent_competition/corl20-deep_latent_competition.pdf).](../media/2020_schwarting_1.png "[Source](https://www.gilitschenski.org/igor/publications/202011-corl-deep_latent_competition/corl20-deep_latent_competition.pdf).")  |
|:--:|
| *The idea is to **learn a `world model`** from ground truth data, based on which behaviour is refined through **imagined `self-play`**. Note that `self-play` is performed **in the learnt LATENT space**. Therefore the name **'_Deep Latent Competition_' (`DLC`)**. [Source](https://www.gilitschenski.org/igor/publications/202011-corl-deep_latent_competition/corl20-deep_latent_competition.pdf).* |

Authors: Schwarting, W., Seyde, T., Gilitschenski, I., Liebenwein, L., Sander, R., Karaman, S., & Rus, D.

- Motivations:
  - `0-` Extend the **model-based** work **[`world model`](https://arxiv.org/abs/1803.10122)** where a **single agent** races alone, to a **`2`-player competition**.
  - `1-` Solve the following challenges:
    - **High-dimensional `observations`** such as images.
    - Multi-Agent `RL` (`MARL`), i.e. **interactions** must be considered.
    - **No prior knowledge** about the **environment** is assumed.
    - **Partial observability** of other agents: their **`actions` are unknown**.
    - Competition, with potentially **adversarial** interactions.
  - `2-` "Racing".
    - Agents **share the same goal**: cross the finish line first. They are **opponents**: if `A` looses, `B` wins.
    - On the contrary, normal driving drivers have **different intentions** and must **avoid collisions** while **abiding to traffic rules**.

- About the `Env`:
  - [`MultiCarRacing-v0`](https://github.com/igilitschenski/multi_car_racing/): a novel **multi-agent** racing environment for learning competitive **visual control policies**.
  - It extends the **`Gym` task [`CarRacing-v0`](https://gym.openai.com/envs/CarRacing-v0/)**.
  - > "**Collision dynamics** allow for **elaborate interaction strategies** during the race: **pushing other agents off the track**, blocking overtaking attempts, or turning an opponent sideways via a `PIT` maneuver."

- Main idea of **_Deep Latent Competition_ (`DLC`)**:
  - Why _"Latent Competition"_?
    - The idea is to gain _competitiveness_ from **imagined `self-play`** in a **learned** multi-agent **_latent_ `world model`**.
    - > "Our approach learns a **`world model`** for **imagining competitive behavior** in **_latent_-space**."
    - > "The `DLC` agent can **imagine interaction sequences** in the **compact _latent_ space** based on a multi-agent `world model` that combines a **joint `transition` function** with **opponent viewpoint prediction**."
  - > [Benefits] "**Imagined `self-play`** reduces **costly sample generation** in the real world, while the **_latent_ representation** enables planning to **scale** gracefully with `observation` dimensionality."

- **Model-based `RL`** consists of `3` tasks, iteratively repeated:
  - `1-` **Model learning**: base on previous experience of **all agents**, two functions are learnt:
    - `(i)` The **joint `dynamics`**:
      - It includes **predictions** of other **agents' behaviour**.
      - How the world will evolve, conditioned on **own** and **expected opponent `actions`**.
      - It enables each agent to **imagine the outcome** of games without requiring additional real-world experience. Hence **_`self-play`_**.
      - This `transition` model is represented by a **recurrent state space model (`RSSM`)**.
    - `(ii)` The `reward` function.
      - With a **dense** net.
      - > "We **incentivize competition** by discounting `rewards` based on **visitation order**: the first agent to visit a track tile is rewarded with `+1000/N`, while the second agent receives `+500/N` (on an `N`-tile track)."
    - Note that reasoning and competition are not performed in **image space**. Rather in the **learnt _latent_ space**.
      - > "In order to **discover latent spaces** that not only offer **compact representations** of environment states but further **facilitate prediction** of associated trajectory performance."
      - Encoder: `CNN`
      - Observation model: **transposed `CNN`**

  - `2-` Behaviour optimization = `policy improvement`.
    - The agents interact with their adversaries **through imagined `self-play`**: no necessity for execution in the real world.
    - `policy` and `value` functions are learnt models through **policy iteration** on **imagined model rollouts**.
 
  - `3-` Environment **interaction** = `policy evaluation` + `experience collection`.
    - > "Each agent only has access to **their own `observation`-`action` history** and performance indirectly depends on **how well the states of opponents are being estimated**."
    - Note: what `observation-action` histories can been seen by the ego-agent?
      - Training: those of **all agents**.
      - Deployment **only their individual**.

- Ablation study - it highlights the **importance** of:
  - `1-` The **learned observer**, i.e. **opponent viewpoint prediction**.
    - > "The first [_baseline_] is **`joint transition`**, which **propagates ground truth _latent_ `states` of both agents** and allows for assessing performance of the `observer`."
    - It is able to **acquire _general racing_ skills** faster. But, after `200` races **`joint transition`** methods have learned to _compete_ more effectively through **imagined `self-play`**.
      - > "The `individual transition` baseline can not leverage this effect, as agent `actions` do not affect opponents `states` during **imagined rollouts** and `self-play` may only occur in the real world."
  - `2-` The **joint `transition` model**.
    - > "The second [_baseline_] is **`individual transition`**, which **propagates ground truth _latent_ `states` _individually_** and highlights the added value of a **_joint_** `transition` model."
    - > "Optimizing competitive behaviors through imagined `self-play` based on **these _joint_ predictions** yields an agent that performs superior to an agent that **propagates ground truth `observations` separately**."

- About **consistency** in **imagined `self-play`**:
  - The learned `transition` model **propagates all agents’ _latent_ `states` jointly**.
  - > "To facilitate efficient `self-play` in a learned `world model`, we require all agent states to **be jointly propagated** in a consistent manner."
  - > "We observe the benefit of **recursively estimating `states`** in **`agent 1`’s reconstruction** of **`agent 2`’s viewpoint**."
  - > "The **joint `transition` model** helps keeping **predictions consistent**, as it allows for **information exchange** between both agents’ **latent `states`** during forward propagation."

- Still open questions for me:
  - Who controls the other agent? Is the **same model used for both cars**? Probably yes.
  - How to deal with **multi-modal predictions** and **multi-model believes** about the other `actions`'?

</details>

---


**`"Guided Policy Search Model-based Reinforcement Learning for Urban Autonomous Driving"`**

- **[** `2020` **]**
**[[:memo:](https://arxiv.org/abs/2005.03076)]**
**[** :mortar_board: `Berkeley` **]**

- **[** _`guided policy search`, `dual gradient descent`, [`carla`](http://carla.org/)_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://arxiv.org/abs/2005.03076).](../media/2020_xu_1.PNG "[Source](https://arxiv.org/abs/2005.03076).")  |
|:--:|
| *Model-based `RL` with guided policy search (`GPS`). The `dynamics` model uses a Gaussian mixture model (`GMM`) with `20` mixtures as global prior. The `policy` model is updated via **dual gradient descent (`DGD`)**: it is a constrained optimization problem, where `KL divergence` constrains the magnitude of **policy updates** (since the **`dynamics` model is valid only `locally`**). I am confused by the inconsistency in terminology between **`LQR` / `LQG`**. The **augmented `cost` (`return` and `KL` term) of the Lagrangian** and its **derivatives** being computable, the authors claim that the trajectory optimization can be solved using **`LQG`**. But `LQR` in the figure. Since the optimization is done on an **expectation of `cumulative costs`**, I would say it is `LQG`. [Source](https://arxiv.org/abs/2005.03076).* |

Authors: Xu, Z., Chen, J., & Tomizuka

- Motivations.
  - `1-` **Model-free** `RL` methods suffer from:
    - Very poor **sampling efficiency**.
      - > "Showing **`100x` better sample efficiency** of the `GPS`-based `RL` method [over model-free ones]."
    - Lack of **interpretability**.
    - `reality gap`: It learns in a **non-perfect simulator**, so **transferring** to the real-world vehicle is difficult.
  - `2-` **Behavioural cloning** methods suffer from:
    - It requires collecting a **large amount of driving data**, which is costly and time consuming.
    - `Distributional shift`: the **training dataset is biased** compared to real world driving, since the expert drivers generally do not provide data for dangerous situations.
    - It essentially clones the **human driver demonstrations**, and cannot exceed the **human performance**.

- `MDP` formulation (_very simplified traffic scene - solvable with `PD`/`PID`_):
  - `state`
    - ego `lateral` deviation.
    - ego `yaw` error.
    - ego `speed`. _no info to the max allowed `speed`?_
    - `gap` to leader.
    - relative `speed` to leader.
  - `action`: throttle, brake, and steering angle.
  - `reward`
    - Penalizing `lateral`, `yaw`, `speed` deviations to references as well as changes in `control commands`.
    - Relative `position` and `speed` compared to the **leader** are also considered, but **only if the `gap` is smaller than `20m`**.

- Idea of the proposed model-based `RL`: **Guided Policy Search** (`GPS`). Iterate:
  - `1-` Collect samples by running the `policy`.
  - `2-` Learn a **parameterized `local` `dynamic model`** to approximate the complex and interactive driving task.
  - `3-` Optimize the driving `policy` under the **(non-linear approximate) `dynamic model`**, subject to a `constraint` on the magnitude of the  **`trajectory` change**.
    - > "We can view the `policy` as **imitating a supervised learning teacher**."
    - > "But the **teacher is adapting** to produce `actions` that the learner can also execute."

- `Global` / `local` models. From this [post](https://michaelrzhang.github.io/model-based-rl).
  - `1-` **Local models** are easier to fit, but need to be **thrown away** whenever the policy updates because they are **only accurate for trajectories collected under the old policy**.
    - > "It can take a number of episodes for the training of such parameterized models [`policy` and `dynamics` model]. In order to get **high sample efficiency**, we adopt the idea of **`local` models**, and apply the **time-varying linear Gaussian models** (_why "time varying"?_) to approximate the **local behavior** of the **system dynamics** and the **control policy**."
    - Trajectories are collected to fit **local models** rather than using **linearization’s of a global model** of the `dynamics`.
  - `2-` **Global models** are beneficial in that they generally maintain some sort of **consistency in `state` space** i.e. `states` close to each other generally have **similar dynamic**.
    - We can approximately get this same desirable behaviour by using a **`global` model as a prior** in training **`local` models**.
    - This is called **`Bayesian linear regression`** and can help reduce **sample complexity**.

- _Why is the policy search "guided"?_
  - Probably as opposed to **random search** for the two models? Or because of the `prior` that guides the local fit of the `dynamics` model?

- Learning the `dynamics model`.
  - > "We adopt a **`global` model** as the **prior**, which evolves throughout the whole model based `RL` lifetime, and **fit the `local` linear dynamics** to it at **each iteration**."
  - Nonlinear **prior** model: Gaussian mixture model (`GMM`). Here **`20` mixtures**.
  - Each **mixture element** serving as prior for **one driving pattern**.
  - Idea of **Expectation Maximization (`EM`)** process to train the `GMM`:
    - `1-` Each tuple sample (`st`, `at`, `st+1`) is first **assigned to a pattern**.
    - `2-` Then it is used to **update the mixture element**.
  - > "Finally, at each iteration, we fit the **current episode** of data (`st`, `at`, `st+1`)'s to the `GMM`, incorporating a **normal-inverse-`Wishart` prior**. The **`local` lineal** dynamics **`p`(`st+1`|`st`, `at`)** is derived by **conditioning the Gaussian** on (`st`, `at`)." [_I don't fully understand_]

- Learning the `policy`.
  - `1-` Using `KL divergence` to **constrain policy updates**.
  - `2-` Using **dual gradient descent (`DGD`)** to solve **constrained optimization** problems.
    - > "The main idea of the `DGD` is to **first minimize the Lagrangian function** under **fixed Lagrangian multiplier `λ`**, and then increase the **`λ` penalty** if the **constrained is violated**, so that more emphasis is placed on the **constraint term** in the Lagrangian function in the next iteration."
    - The **Lagrangian objective** can be re-written as an **augmented cost function `c`(`st`, `at`)**. This cost function and its derivatives can be directly computed, hence the trajectory optimization problem can be **solved using `LQG`**.
    - > "After the Lagrangian is optimized under a **fixed `λ`**, in the second step of `DGD`, `λ` is updated using the function below with **step size `α`**, and the `DGD` loop is closed."

- Baseline `1`: Black Box (**derivative-free**) optimization.
  - **Cross Entropy Method** (`CEM`).
  - **Simple** but **not sample efficient**.
  - "In order to optimize the **parameterized policy `πθ`**, the `CEM` adopts the assumption of **Gaussian distribution of `θ` = `N`(`µ`,`σ2`)**. It **iteratively samples `θ`** from the distribution, using which to **collect sample trajectories**, and then updates `µ` and `σ` using the `θ`’s that produces the best trajectories."

- Baseline `2`: model-free `SAC`.
  - It maximizes both the **expected return** and the **entropy** of the policy.

- Initialization (_is it fair?_):
  - `GPS` and `CEM`: **`PD` controller** with **large variance**, since the policies are **linear Gaussians**.
  - `SAC`: pure **random initialization** of the weights.
  - > "Therefore, the initial performances of the `GPS` and `CEM` are slightly better compared to the model free `RL` methods."

</details>

---

**`"Model-based Reinforcement Learning for Time-optimal Velocity Control"`**

- **[** `2020` **]**
**[[:memo:](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control)]**
**[[🎞️](https://www.youtube.com/watch?v=Ffo3SYonwPk)]**
**[** :mortar_board: `Ariel University, Israel` **]**

- **[** _`speed response`, `dynamic stability`, `action masking`_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).](../media/2020_hartmann_2.PNG "[Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).")  |
|:--:|
| *Since the **underlying dynamic model of the vehicle** is complex, it is **learnt** with supervised learning. The learnt **transition** function is then used for `planning`. This improves the sampling efficiency compared to model-free `RL`. Note that instead of **directly predicting the next `state`**, the **neural network** predicts the **difference between the current state `s[t]` and the next state `s[t+1]`**. But no previous `action`s are considered (What about **physical latencies** and **"delayed action effect"**?). [Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).* |

| ![[Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).](../media/2020_hartmann_1.PNG "[Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).")  |
|:--:|
| *The **Failure Prediction and Intervention Module (`FIM`)** uses an **analytical** model to determine the **potential instability** of a given `action`. Actions proposed by the `model-based RL` agent is overwritten if one of the **future predicted states** is prone to `roll-over`. This **maintains safety** also during the **beginning of the training process**, where the **learned model** may be inaccurate. [Source](https://www.researchgate.net/publication/343242054_Model-based_Reinforcement_Learning_for_Time-optimal_Velocity_Control).* |

| ![[Source](https://www.youtube.com/watch?v=Ffo3SYonwPk).](../media/2020_hartmann_1.gif "[Source](https://www.youtube.com/watch?v=Ffo3SYonwPk).")  |
|:--:|
| *[Source](https://www.youtube.com/watch?v=Ffo3SYonwPk).* |

Authors: Hartmann, G., Shiller, Z., & Azaria, A.

- Task: decide **binary acceleration** (`max-throttle` / `hard-brake`) to drive **as fast as possible** along a path (`steering` is controlled by an external module) without compromising **dynamic stability**.
- Motivations. Improve training wrt:
  - `1-` **Sampling efficiency**.
    - The **training time** should be short enough to enable training on **real vehicles** (less than **`1` minute** of real-time learning).
    - Since the **underlying dynamic model of the vehicle** is very complex, it is **learnt** (supervised learning) and then used for `planning`.
  - `2-` Safety. More precisely the **"dynamic stability"**.
    - > "By **“dynamic stability”** we refer to constraints on the vehicle that are functions of its speed, such as not **rolling-over** and not **sliding**."
    - > "An important advantage of `LMVO+FIM` over the other methods is that **it maintains safety** also during the **beginning of the training process**, where the **learned model** may be inaccurate."
  - As opposed to **model-free `RL`** approaches:
    - > "The **millions of training steps** are required to converge, which is impractical for real applications, and the **safety** of the learned driving policy is **not guaranteed**."
    - > "`LMVO+FIM` achieves higher velocity, in approximately **`1%` of the time that is required by `DDPG`**, while **completely preventing failure**."
- Main idea. Combine:
  - `1-` A **model-based `RL`** for **sampling efficiency**.
    - A **prediction transition function** is learnt (and maybe inaccurate at start).
    - _How the `model-based policy` is learnt is not explained. `MCTS`?_
  - `2-` An **analytical planner** to protect the vehicle from **reaching dynamically unstable `states`**.
    - Another prediction transition function (`bicycle` model) is used which _should_ ensure safety **before the other learned model converges**.

- Learning a **dynamic model**.
  - > "Instead of **directly predicting the next `state`**, we use a **neural network** to predict the **_difference_ between the current state `s[t]` and the next state `s[t+1]`**."
  - To make a **multi-step roll-out**, this **single-step prediction** is repeated.
    - > "Since the **multi-step predictions** are computed **iteratively** based on the previous step, the **error** between the predicted and actual values is **expected to grow** with the number of steps. For simplicity, we take a **`safety factor`** that is **linear with the number of future steps**."

- About the **analytical model**: _How to determine the_ **_potential instability_** _of a given `action`?_
  - Based on the **bicycle model**.
  - > "The **Lateral load Transfer Rate** (`LTR`), to estimate **how close the vehicle is to a `roll-over`**. The `LTR` describes the different between the **load** on the `left` and the load on the `right` wheels."
  - > "For **all rolled-out future `states`**, it is checked if the **predicted `LTR`** is lower than `1`, which indicates that the vehicle is **expected to remain safe**."
  - If an _"unsafe"_ manoeuvre is attempted, an alternative _"safe"_ local manoeuvre is executed (`max-brake`).
    - > "`πs` tries to **brake** while using the **regular controller for steering**, but if it predicts that the vehicle will **still result in an unstable state**, it also **straightens the steering wheel** which will prevent the expected **roll-over** by reducing the radius of curvature of the future state and following that, reducing `LTR`."

- A personal concern: **physical latencies** and **"delayed action effect"**.
  - In real cars, the **dynamic reaction** does depend on a sequence of **past actions**.
  - E.g. applying `max-throttle` at `5m/s` will result in totally **different speeds** depending if the car's **previous `action`** was `max-brake` or `max-throttle`.
  - Here:
    - **Predictions** and **decisions** are made at **`5Hz`**.
    - The learnt **transition** function takes as input the `speed` and desired `action`. No information about the past.
  - One solution from [TEXPLORE: ROS-based RL](https://link.springer.com/content/pdf/10.1007%2F978-3-662-44468-9_47.pdf):
    - > "To handle robots, which commonly have **sensor and actuator delays**, we **provide the model with the past `k` actions**."

- Another concern: computationally **expensive inference**.
  - The **forward path** in the net should be light. But `planning` steps seem costly, despite the minimal **`action` space** size (only `2` choices).
  - > "At every time step `t`, the action at must be applied immediately. However, since the **computing time is not negligible**, the **command is applied with some delay**. To solve this problem, instead of computing action `a[t]` at time `t`, action `a[t+1]` is computed based on the predicted next state `s[t+1]` and `a[t+1]` is applied immediately when obtaining the actual state `s[t+1]` at time `t+1` (which may be **slightly different** than the model’s prediction for that state)."

</details>

---

**`"Assurance of Self-Driving Cars: A Reinforcement Learning Approach"`**

- **[** `2020` **]**
**[[:memo:](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf)]**
**[** :mortar_board: `Australian National University` **]**

- **[** _[`PILCO`](https://mlg.eng.cam.ac.uk/pub/pdf/DeiRas11.pdf), [`ρUCT`](https://arxiv.org/pdf/0909.0801.pdf)_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).](../media/2020_quan_2.PNG "[Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).")  |
|:--:|
| *Top-left: The task is not to drive a car! But rather to find the **weather conditions** causing accidents (not clear to me). Right: `Model learning` is done **offline**. Once a good confidence in **prediction accuracy** of `PILCO GPs` is obtained, **online `planning`** is conducted by starting a new episode and building up a `ρUCT` at each `state`. Bottom-left: [`ρUCT`](https://arxiv.org/pdf/0909.0801.pdf) is a **best-first `MCTS`** technique that **iteratively constructs a search tree in memory**. The tree is composed of **two interleaved types of nodes**: **`decision` nodes** and **`chance` nodes**. These correspond to the **alternating `max` and `sum` operations** in the `expectimax` operation. Each `node` in the tree corresponds to a **history `h`**. If `h` ends with an `action`, it is a **chance node**; if `h` ends with an (`observation`-`reward`) pair, it is a **decision node**. Each `node` contains a statistical estimate of the future `reward`. [Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).* |

| ![[Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).](../media/2020_quan_1.PNG "[Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).")  |
|:--:|
| *Inference in the **transition model**: given **`x`**=(`s`, `a`), the `mean` and `variance` of the posterior distribution `p`(`s'`, **`x`**) are computed and used in a **normal distribution** to **sample state transitions**. [Source](https://cs.anu.edu.au/courses/CSPROJECTS/20S1/reports/u6646917_report.pdf).* |

Author: Quan, K.

- About:
  - A **student project**. Results are unfortunately not very good. But the report gives a good example of some **concrete `model-based` implementation** (and its **difficulties**).

- Motivation:
  - Perform `planning` to solve a `MDP` where the **environment dynamics** is **unknown**.
  - A **generative model** that **approximates the transition function** is therefore needed.

- About model-based `RL`.
  - > "In `model-based` `RL`, the agent learns an **approximated model of the environment** and performs **`planning`** utilising this model."
  - > "**Gaussian Process (`GP`)** is widely considered as the state-of-the-art method in **learning stochastic transition models**."
    - [`PILCO`](https://mlg.eng.cam.ac.uk/pub/pdf/DeiRas11.pdf) = *Probabilistic Inference for Learning Control*. It is a **model-based policy search** algorithm.
    - The **transition model** in the `PILCO` algorithm is implemented as a **`GP`**.
  - In short, the author proposes a combination of:
    - `1-` **Offline model learning** via `PILCO` Gaussian Processes.
    - `2-` **Online planning** with [`ρUCT`](https://arxiv.org/pdf/0909.0801.pdf).
  - Miscellaneous:
    - A **fixed frame rate** is necessary to reduce the difficulty of **learning the transition model**.
    - Here, a [python implementation]((https://github.com/nrontsis/PILCO)) of `PILCO` in `TensorFlow v2` is used, leveraging [`GPflow`](https://github.com/GPflow/GPflow) for the `GP` regression.

- Model learning is **OFFLINE**, for **efficiency** reasons.
  - Contrary to [`Dyna`](https://people.cs.umass.edu/~barto/courses/cs687/Chapter%209.pdf) model, **new experience** obtained from interactions with the environment during the **planning phase** is **not fed back** to the **model learning process** to further improve the **approximate transition model**.
    - > "The major reason is that **optimisation of `PILCO` `GPs`** is significantly time-consuming with a large set of experience. Thus, **interleaving online planning** in decision time with **model learning** would make the solution time intractable under the computation resource we have."
    - **Error correction** is therefore impossible.

- **Long `training` time** and **`sampling` time** with the `python` package:
  - A model learnt with `200` episodes has an **`8s` sampling time**, making `planning` intractable.
  - The author raises two reasons:
    - `1-` Optimisation of the `GP’s` hyper parameters are not done in **incremental-basis**.
    - `2-` Intermediate results of sampling from the **posterior distribution** are not **cached**, thus **every sampling** requires a `Cholesky` decomposition of the covariance matrix.

- About `planning`.
  - `Policy iteration` is not applicable.
    - It becomes **intractable** in large problems, since it operates in sweeps of the **entire `state`-`action` space**.
  - Instead Monte Carlo Tree Search (`MCTS`).
    - _No detail about the `state` discretization._
  - About [`ρUCT`](https://arxiv.org/pdf/0909.0801.pdf):
    - > "A generalisation of the popular `MCTS` algorithm `UCT`, that can be used to **approximate a finite horizon `expectimax` operation** given an environment model `ρ`.
    - > "The `ρUCT` algorithm can be realised by replacing the notion of `state` in `UCT` by an **agent history `h`** (which is always a sufficient statistic) and using an environment model `ρ` to **predict the next percept**."
    - `UCB1` is used by `ρUCT` as the **selection** strategy to balance `exploration` and `exploitation`.

- `MDP` formulation (_not clear to me_).
  - > "We set up the `CARLA` environment with a single vehicle moving towards a destination in a **straight lane** and a **pedestrian** that is placed in the path of the vehicle and stands still, which simulates the dynamics between a **moving car** and a pedestrian who is **crossing the street**."
  - > "The results indicate that **sun altitude** is an important influence factor for the stability and consistency of the test autonomous controller in that certain configurations of **sun altitude produce lighting conditions** that cause more frequent **failure** of the controller."
  - `gamma`
    - > "Since the length of episodes is **finite**, we **omit the discounting factor**."
  - `state` (normalisation is performed):
    - Pedestrian's (_or car's?_) `position`, `velocity` and `acceleration`. Plus a weather configuration: `SunAltitude`.
    - > "Out of the **`6` weather parameters** configurable in `CARLA`, in this baseline experiment we will only include `Sun Altitude` in our state-action space given its biggest impact on the **lighting condition**."
  - `reward`:
    - `+10` if the vehicle **crash** into pedestrian. _Should not it be negative? Or maybe the goal is to find the weather conditions that cause crashes?_
    - `-1` as **step penalty**.
  - `action`:
    - The task is **not to drive a car!**
    - Instead to **change the weather parameters**, i.e. `SunAltitude`.
    - > [During **data collection**] "An arbitrary policy produces `actions` that **modify the `SunAltitude`** weather parameter by sampling from a **Bernoulli Process** that gives the probability of **two actions**: increase `SunAltitude` by `2`, or decrease it by `2`: `P(A = 2) = p = 0.8, P(A = −2) = 1 − p`."

- Results:
  - Learning the **dynamics** is not successful.
    - For `velocity` prediction, the **error percentage** remains **higher than `40%`**!
  - > [solving the `MDP`] "The best result of **`1.2` average final reward** [_Rather `return`?_] that we have achieved so far is still having a significant gap from the **theoretical optimal reward of `10`** [_What about the `-1` step penalties??_]. Model error still seems to be a limiting factor to our planning capability."

</details>

---

**`"Safe Policy search using Gaussian process models"`**

- **[** `2019` **]**
**[[:memo:](https://arxiv.org/abs/1712.05556)]**
**[** :mortar_board: `Oxford` **]**

- **[** _`PILCO`, `probabilistic safety guarantees`, `adaptive tuning`, `chance constrained`_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://arxiv.org/abs/1712.05556).](../media/2019_polymenakos_1.png "[Source](https://arxiv.org/abs/1712.05556).")  |
|:--:|
| *A **set of `safe states`** is defined. An **analytically-computed** quantity **`Q`** estimates the **probability** of the system to **stay in these `safe states`** during its `trajectory`. **Two usages** are made of `Q`. First it is made part of the **`objective` function**, together with the `expected return`. Second, a **constraint** is defined: `Q` must be be **higher than some threshold `ϵ`**. If not, the `policy` is prevented from being implemented into the **physical system**. If the `policy` is deemed `unsafe`, the importance of `safety` over `performance` is increased in the `objective` function and the optimization is repeated. This **automated procedure** is called **adaptive tuning**. [Source](https://arxiv.org/abs/1712.05556).* |

Authors: Polymenakos, K., Abate, A., & Roberts, S.

- Motivations:
  - `1-` Target applications on **physical** systems.
    - **Data-efficiency** and **safety** are essential.
    - **Model-free `RL`** is therefore not an option, despite its **flexibility**.
  - `2-` **No predefined `dynamics` model**, which would inhibit learning:
    - ... either by **lack of flexibility**,
    - ... or by introducing **model bias**.
    - Therefore `planning` (with fixed models) is not an option. Model-based `RL` is preferred.
    - > "We want to construct a **model from scratch**, from the **data collected** during training, allowing us to efficiently tune a controller."
    - > "We address the **lack of flexibility** by using a **non-parametric model** which can in principle be as flexible as needed. **Model bias** is also addressed, by using a model that explicitly accounts for **uncertainty** in its outputs. That way, even when the model’s predictions are wrong, the model should provide them along with a suitably **high uncertainty estimation**."
  - `3-` Automate the **tuning of parameters** for **trade-off between `efficiency` and `safety`** and enforce the learnt `policy` to be `safe` before using it (_`safety check` step_).

- About the **`safety` constraint**:
  - A **set of `safe states`** is defined.
    - The **probability** of the system to **stay in `safe states`** during its `trajectory` is noted **`Qπ`(`θ`)**, for a policy parametrized by `θ`.
    - It estimates the **`risk`**.
  - The **constraint** is defined as:
    - "`Qπ`(`θ`) must be **higher than some threshold `ϵ`**."
  - > "These constraints are **defined a priori** and we require them to be respected, **even during training**."

- Model-based `RL` with **Gaussian processes (`GP`)**.
  - A **Gaussian process model** is trained to capture the **system `dynamics`**, i.e. **`transition` model**, based on the [`PILCO`](https://mlg.eng.cam.ac.uk/pub/pdf/DeiRas11.pdf) framework.
  - Compared to other **_policy gradient methods_**, gradients in `PILCO` are **not _numerically approximated_** from the sampled trajectories, but **_analytically_ calculated** given the `GP` model.
  - Output of the model:
    - Not the `next_state` itself. Rather its difference to the input `state`.
    - > "Modelling the **difference in consecutive `states`** is preferable to modelling the `states` themselves for practical reasons, such as the fact that the common **zero-mean prior** of the `GP` is more intuitively natural."
  - **Kernels** for the `covariance` of the `GP`:
    - > "The **`squared exponential` kernel** choice reflects our expectation that the function we are modelling (the system `dynamics`) is **smooth**, with similar parts of the `state` space, along with similar inputs, to lead to similar `next states`."
  - > "The kernel function’s hyper-parameters, namely the `signal variance`, `length scales`, and `noise variance`, are chosen through **_[evidence maximization](http://mlg.eng.cam.ac.uk/pub/pdf/RasWil06.pdf)_**."

- Once the `dynamics` model is learnt, the **`policy`'s performance** and its **`risk`** can be **evaluated**.
  - > "For any parameter value `θ`, we can produce a **sequence** of **`mean` and `variance` predictions** for the `states` the system is going to be in the **next `T` time steps**."
  - This prediction is used to estimate:
    - `1-` The `reward` that would be **accumulated** by implementing the `policy`, starting from an initial `state`.
    - `2-` The **probability of violating the `safe state space` constraints**. _See figure._

- _Once the `risk` associated to a `policy` is estimated, what to do with it?_
  - The probability for the system to **respect/violate the constraints** during an episode has a **dual role**:
  - `1-` In the (`policy evaluation` + `policy improvement`) iterations:
    - `Qπ(θ)` is a component of the **`objective` function**, along with the **expected `return`**.
    - > "The `objective` function, capturing both **safety** and **performance** is (a **risk-sensitive** criterion according to [`16`]) is defined as: `Jπ(θ)` = `Rπ(θ)` + `ξ`*`Qπ(θ)`."

  - `2-` **Safety check step** for **deployment**:
    - High `risk` policies are **preventing** from being applied to the **physical system**.
    - > "Even during **training**, only policies that are **deemed `safe`** are implemented on the real system, minimizing the risk of **catastrophic failure**."

- _What if the `policy` is estimated as `unsafe`?_
  - It can **neither be deployed**, nor be used to **collect further samples for training**.
  - In this case, the **objective is changed**, **increasing the importance of `safety` over `performance`**, and a new optimization and performed.
    - > "This **adaptive tuning** of the hyperparameter `ξ` guarantees that **only safe policies** (according to the current `GP` model of the system dynamics) are implemented, while mitigating the need for a **good initial value of `ξ`**. Indeed, using this scheme, we have observed that a good strategy is to start with a relatively **high initial value, focusing on `safety`**, which leads to safer policies that are allowed to interact with the system, gathering more data and a more accurate model, and steadily discovering high performing policies as `ξ` decreases."

- _`Policy improvement`: how to perform the optimization?_
  - > "The gradients are often estimated **stochastically** in the `policy gradient` literature. However we do not have to resort to **_stochastic_ estimation**, which is a major advantage of using **(differentiable) models** in general and `GP` models in particular."
  - The **probability of collision `Qπ`(`θ`)**, on the other hand, is a product of the **probabilities of collision** at every time step **`q`(`xt`)**.

- Benchmark:
  - Comparisons are made with a `policy` trained on a `MDP` where **`penalties`** (negative `reward`) discourage **visiting unsafe `states`**.
  - > "In that case, instead of calculating a **probability of being in an unsafe `state`**, the system receives an (additive) `penalty` for getting to unsafe `states`."

</details>

---

**`"Model-predictive policy learning with uncertainty regularization for driving in dense traffic"`**

- **[** `2019` **]**
**[[:memo:](https://openreview.net/forum?id=HygQBn0cYm)]**
**[[:memo:](https://postersession.ai/poster/model-predictive-policy-learning-with-un/)]**
**[[🎞️](https://www.youtube.com/watch?v=X2s7gy3wIYw)]**
**[** :mortar_board: `New York University` **]**

- **[** _`uncertainty regularization`, `multi-modal prediction`, `CVAE`, `covariate shift`, `latent dropout`_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://openreview.net/forum?id=HygQBn0cYm).](../media/2019_canziani_2.PNG "[Source](https://openreview.net/forum?id=HygQBn0cYm).")  |
|:--:|
| *Small-top-right: example when averaging the outcomes of dropping a pen, to show that **deterministic prediction** that **averages** over possible futures is not an option. Top: how the **`action`-conditional dynamics model** has its **latent variable sampled** during `training` and `inference`. Bottom: latent **dropout** to solve the **action insensitivity issue** if only sampling from the **learnt posterior distribution** (function of the true `next state`) during training. [Source](https://openreview.net/forum?id=HygQBn0cYm).* |

| ![[Source](https://openreview.net/forum?id=HygQBn0cYm).](../media/2019_canziani_1.PNG "[Source](https://openreview.net/forum?id=HygQBn0cYm).")  |
|:--:|
| *The **world model** produces various possible futures, i.e. the `next-state`. From them, the `reward` is computed. This estimation is good on the training distribution. But depending on the net initialization, moving **out of the training distribution** leads to different results and **arbitrary predictions**. How to reduce this **disagreement** between the models? By **uncertainty regularization**: **multiple forward passes** are performed with **dropout**, and the **variance of the prediction** is computed. The uncertainty is **summarized into this scalar** and used as **regularization term when training the `policy`**. [Source](https://openreview.net/forum?id=HygQBn0cYm).* |

| ![[Source](https://www.youtube.com/watch?v=X2s7gy3wIYw).](../media/2019_canziani_1.gif "[Source](https://www.youtube.com/watch?v=X2s7gy3wIYw).")  |
|:--:|
| *The `latent variable` of the **predictive model** (`CVAE`) enables to **predict a multiple modal future**. Here `4` sequences of **`200` latent variables** were sampled. None of them repeats the actual future but show `4` different variants of the future. The **deterministic predictor** does not work: it **averages** over possible futures, producing **blurred predictions**. [Source](https://www.youtube.com/watch?v=X2s7gy3wIYw).* |

Authors: Henaff, M., LeCun, Y., & Canziani, A.

- Motivations:
  - `1-` **Model-free `RL`** has **poor data efficiency**.
    - **Interactions** with the world can be **slow, expensive, or dangerous**.
    - > "Learning through interaction with the **real environment** is not a viable solution."
    - The idea is to learn a **model of the `environment dynamics`**, and use it to **train a `RL` agent**, i.e. **model-based `RL`**.
  - `2-` Observational data is often plentiful, _how can it be used?_
    - > "Trajectories of human drivers can be **easily collected using traffic cameras** resulting in an **abundance of observational data**."
  - `3-` **Dense** moving traffic, where **`interaction` is key**.
    - > "The **driver behavior is complex** and includes sudden accelerations, lane changes and merges which are **difficult to predict**; as such the dataset has high enviroment (or **aleatoric**) **uncertainty**."

- _Is `behavioural cloning` a good option?_
  - > "Learning policies from **purely observational data** is challenging because the data may **only cover a small region** of the space over which it is defined."
  - > "Another option [_here_] is to **learn a `dynamics model`** from observational data, and then use it to **train a `policy`**."

- `state`:
  - `1-` A vector: `ego-position` and `ego-speed`.
  - `2-` A `3`-channel image (**high-dimensional**):
    - `red` encodes the **lane markings**.
    - `green` encodes the locations of **neighbouring cars**.
    - `blue` represents the **ego car**.
- `action`:
  - Longitudinal `acceleration/braking`.
  - **Change in** `steering` angle.

- Main steps:
  - `1-` Learn an **action-conditional `dynamics model`** using the collected observational data.
    - From `NGSIM`. **`2` million transitions** are extracted.
  - `2-` Use this model to **train** a fast, feedforward **`policy` network**.
    - It minimizes an objective function containing two terms:
    - `1-` A **policy cost** which represents the **objective** the policy seeks to optimize, e.g. **avoid driving too close** to other cars.
    - `2-` An **uncertainty cost** which represents its **divergence from the `states` it is trained on**.
      - > "We measure this second cost by using the uncertainty of the `dynamics model` about its own predictions, calculated using **dropout**."

- Problem `1`: **predictions** cannot be **unimodal**. In particular, **averaging over the possible futures is bad**.
  - Solution: **conditional `VAE`**.
  - The **low dimensional `latent variable`** is sampled from **prior** distribution (`inference`) or from the **posterior** distribution (`training`).
  - **Different samples lead to different outcomes.**
  - Repeating the process enables to **unroll a potential future**, and generate a **`trajectory`**.

- Problem `2`: **`action` sensitivity**: _how to keep the_ **_stochastic dynamics model responsive_** _to `input actions`?_
  - The **predictive model** can **encode `action` information** in the **latent variables**, making the output **insensitive to the input `actions`**.
  - > "It is important for the prediction model to accurately **respond to input `actions`**, and not use the **latent variables** to encode factors of variation in the outputs which are due to the `actions`."
  - Solution: **Latent dropout**.
    - During training: sometimes sample `z` from the **prior** instead of the **latent variable encoder**.
    - > "This **forces** the prediction model to **extract as much information** as possible from the **input `states` and `actions`** by making the **latent variable independent of the output** with some probability."
  - > "Our **modified posterior distribution** discourages the model from **encoding `action` information in the latent variables**."

- _How to generate the_ **`latent variable`** at **`inference`/`testing` time** _if the_ **_`true target` is not available_**_?_
  - By sampling from the **(fixed) prior distribution**, here an **isotropic Gaussian**.

- Problem `3`: how to addresses **`covariate shift` without querying an expert** (`DAgger`)?
  - Solution: **Uncertainty regularization**.
  - A term **penalizing the uncertainty** of the **prediction forward model** is incorporated when training the `policy` network.
  - > "Intuitively, if the `dynamics model` is given a state-action pair from the **same distribution as `D`** (which it was **trained on**), it will have **low uncertainty about its prediction**. If it is given a `state`-`action` pair which is **outside this distribution**, it will have **high uncertainty**."
  - > "Minimizing this quantity with respect to actions encourages the **`policy` network** to produce `actions` which, when plugged into the forward model, will **produce predictions which the forward model is confident about**."
  - This could be seen as a form of **imitation learning**. But what if the **expert was not optimal**?
    - > "This leads to a set of `states` which the model is **presumably confident about**, but may not be a trajectory which also satisfies the policy cost `C` unless the dataset `D` consists of **expert trajectories**."
    - Solution: the second **cost term**, i.e. `RL`.

- Hence **`MPUR`: `M`odel-predictive `P`olicy-learning with `U`ncertainty `R`egularization.**
  - Note that **imitation learning** is performed at the **level of `trajectories`** rather than **individual `actions`**.
  - > "A key feature of is that we **optimize the objective over `T` time steps**, which is made possible by our **learned dynamics model**. This means that the actions will receive gradients from **multiple time steps ahead**, which will penalize actions which lead to large divergences from the training manifold **further into the future**, even if they only cause a **small divergence at the next time step.**"
  - > "We see that the **single-step imitation learner** produces **divergent trajectories** which turn into other lanes, whereas the `MPUR` and `MPER` methods show **trajectories** which primarily stay within their lanes."

</details>

---

**`"Automatic learning of cyclist’s compliance for speed advice at intersections - a reinforcement learning-based approach"`**

- **[** `2019` **]**
**[[:memo:](https://ieeexplore.ieee.org/document/8916847)]**
**[** :mortar_board: `Delft University` **]**

- **[** _`Dyna-2`, `Dyna-Q`_ **]**

<details>
  <summary>Click to expand</summary>

| ![ The proposed algorithm **learns the cyclist’s behaviour** in **reaction** to the **advised `speed`**. It is used to make **prediction about the next state**, allowing for a **search** that help to plan the best next move of the cyclist **on-the-fly**. A **`look-up table`** is used to model `F`. [Source](https://ieeexplore.ieee.org/document/8916847).](../media/2019_dabiri_1.PNG "The proposed algorithm **learns the cyclist’s behaviour** in **reaction** to the **advised `speed`**. It is used to make **prediction about the next state**, allowing for a **search** that help to plan the best next move of the cyclist **on-the-fly**. A **`look-up table`** is used to model `F`. [Source](https://ieeexplore.ieee.org/document/8916847).")  |
|:--:|
| *The proposed algorithm **learns the cyclist’s behaviour** in **reaction** to the **advised `speed`**. It is used to make **prediction about the next state**, allowing for a **search** that help to plan the best next move of the cyclist **on-the-fly**. A **`look-up table`** is used to model `F`. [Source](https://ieeexplore.ieee.org/document/8916847).* |

Authors: Dabiri, A., Hegyi, A., & Hoogendoorn, S.

- Motivation:
  - `1-` Advise a cyclist what speed to adopt when **approaching traffic lights** with uncertainty in the timing.
    - _To me, it looks like the opposite of numerous works that_ **_control traffic lights_**_, assuming behaviours of vehicles, in order to_ **_optimize the traffic flow_**_. Here, it may be worth for cyclists to speed up to catch a green light and avoid stopping._
    - Note that this is not a _global_ optimization for a _group_ of cyclists (e.g. on crossing lanes). Only one **single cyclist** is considered.
    - Note that the so-called "`agent`" is not the cyclist, but rather the module that **provides** the cyclist a **speed advice**.
  - `2-` Do not assume **full compliance** of the cyclist to the given advice, i.e. take into account the effect of **disregarding the advice**.
- Challenges:
  - `1-` There is no advanced knowledge on **how the cyclist may react** to the advice he/she receives.
    - The other **dynamics** (or _transition_) models (**deterministic** kinematics of the bike and **stochastic** evolution of the traffic light state) are assumed to be known.
  - `2-` The **computation time** available at each decision step is **limited**: we **cannot afford** to **wait for `next-state` to be known before starting to "search"**.
- Main ideas:
  - **Learn a model of the reaction** of cyclist to the advice (using a **`look-up table`**), on real-time (_it seems `continuous learning` to me_).
  - Use a **second search procedure** to obtain a **local approximation** of the action-value function, i.e. to help the agent to **select its next action**.
  - Hence:
    - > "Combine **learning** and **planning** to decide of the `speed` of a cyclist at an intersection".
- One strong inspiration: [**`Dyna-2`**](http://www0.cs.ucl.ac.uk/staff/D.Silver/web/Applications_files/dyna2.pdf) (Silver & Sutton, 2007).
  - > "The **value function** is a linear combination of the **transient** and **permanent** memories, such that the transient memory **tracks a local correction** to the permanent memory".
  - Without _transient_ memory, it reduces to `linear Sarsa`.
  - Without _permanent_ memory, it reduces to a **sample-based search** algorithm.
- One idea: use **`2` search** procedures:
  - > "Similar to [`Dyna-2`](http://www0.cs.ucl.ac.uk/staff/D.Silver/web/Applications_files/dyna2.pdf), `Dyna-c` [_`c` for `cyclist`_], **learns from the past and the future**:"
  - `1-` **`Search I`**: The **_long-term_** action-value is updated from what **has happened** in real world.
    - `Q`(`s`,`a`), which is updated from **real experience**.
    - This _long-term_ memory is used to represent **general knowledge** about the domain.
    - `Search I` can benefit from a _local_ approximation provided by `Search II`. _How? is I a real search or just argmax()?_
  - `2-` **`Search II`**: The **_short-term_** action-value is updated from what **could happen** in the future.
    - `Q¯`(`s`,`a`), which uses **simulated experience** for its update and focuses on **generating a local approximation** of the action-value function.
    - Based on the **learnt model** and the selected action, the agent **predicts the state in the next time step**.
    - It can **simulate experiences** (search procedure) that start from this "imagined" state and update `Q¯` accordingly.
- Difference with `dyna-q`. Time constrain: we can neither afford to wait for the next observation nor to take too long to think after observing it (as opposed to e.g. GO).
  - **`Search II`** has **exactly one timestep** to perform its searches:
    - > "Just after the action is taken and before reaching to the next time step, the agent has **`Ts` = `∆t` seconds** to perform `Search II`."
- One take-away:
  - > "Proper **initialisation of `Q`** can significantly **improve the performance** of the algorithm [_I note the logically equivalent contrapositive_]; the closer the algorithm starts to the real optimal action-value, the better."
  - > "Here, `Q` is **initialised** with its optimal value in case of **full compliance of the cyclist** [`next-observed speed` `=` `advised speed`]. Stochastic Dynamic Programming (`SDP`) is used for such initialisation."

</details>

---

**`"ReQueST: Learning Human Objectives by Evaluating Hypothetical Behavior"`**

- **[** `2019` **]**
**[[:memo:](https://arxiv.org/abs/1912.05652)]**
**[[🎞️](https://deepmind.com/blog/article/learning-human-objectives-by-evaluating-hypothetical-behaviours)]**
**[[:octocat:](https://github.com/rddy/ReQueST)]**
**[** :mortar_board: `UC Berkeley` **]**
**[** :car: `DeepMind` **]**

- **[** _`safe exploration`, `reward learning`_ **]**

<details>
  <summary>Click to expand</summary>

| ![ Right: Procedure to learn the **hidden reward function**: Using an **offline-learnt `generative model`**, query trajectories are produced for each **acquisition function** (`AF`).  **Transitions** of these trajectories are **labelled by the user**. The reward model ensemble is retrained on the **updated training data** using maximum-likelihood estimation. [Source](https://arxiv.org/abs/1912.05652).](../media/2019_reddy_1.PNG "Right: Procedure to learn the **hidden reward function**: Using an **offline-learnt `generative model`**, query trajectories are produced for each **acquisition function** (`AF`).  **Transitions** of these trajectories are **labelled by the user**. The reward model ensemble is retrained on the **updated training data** using maximum-likelihood estimation. [Source](https://arxiv.org/abs/1912.05652).")  |
|:--:|
| *Right: Procedure to learn the **hidden reward function**: Using an **offline-learnt `generative model`**, query trajectories are produced for each **acquisition function** (`AF`).  **Transitions** of these trajectories are **labelled by the user**. The reward model ensemble is retrained on the **updated training data** using maximum-likelihood estimation. [Source](https://arxiv.org/abs/1912.05652).* |

| ![ Four acquisition functions: `Maximize predicted rewards` makes the car drive **fast** and **far**. `Maximize reward model uncertainty` makes the car drive **close to the border**. `Minimize predicted rewards` makes the car drives **off-road**. `Maximize the novelty of training data` makes the car **stay still** _(since most training examples show cars in motion)_. Animated figure [here](https://sites.google.com/berkeley.edu/request).](../media/2019_reddy_2.PNG "Four acquisition functions: `Maximize predicted rewards` makes the car drive **fast** and **far**. `Maximize reward model uncertainty` makes the car drive **close to the border**. `Minimize predicted rewards` makes the car drives **off-road**. `Maximize the novelty of training data` makes the car **stay still** _(since most training examples show cars in motion)_. Animated figure [here](https://sites.google.com/berkeley.edu/request).")  |
|:--:|
| *Four acquisition functions: `Maximize predicted rewards` makes the car drive **fast** and **far**. `Maximize reward model uncertainty` makes the car drive **close to the border**. `Minimize predicted rewards` makes the car drives **off-road**. `Maximize the novelty of training data` makes the car **stay still** _(since most training examples show cars in motion)_. Animated figure [here](https://sites.google.com/berkeley.edu/request).* |

Authors: Reddy, S., Dragan, A. D., Levine, S., Legg, S., & Leike, J.

- One quote:
  - > "We **align** agent behavior with a **user’s objectives** by learning a model of the **user’s reward function** and training the agent via (`model-based`) `RL`."
- One term: **_"reward query synthesis via trajectory optimization"_** (`ReQueST`)
  - `synthesis`:
    - The model first learns a **generative model**, i.e. a **transition** or **forward dynamics** function.
    - It is trained using **off-policy** data and **maximum-likelihood** estimation, i.e. **unsupervised learning**.
    - It is used to **produce synthetic trajectories** (instead of using the default training environment).
    - Note: _building a forward dynamics model for cars in_ **interactive environments**_ _looks very challenging_.
  - `reward query`:
    - The user labels each **transition** in the synthetic trajectories based on some reward function (unknown to the agent).
    - Based on these signals, the agent learns a reward model `r`(`s`, `a`, `s'`), i.e. **unsupervised learning**.
    - The task can be regression or classification, for instance:
      - `good` - the car drives onto a new patch of road.
      - `unsafe` - off-road.
      - `neutral` - in a previously-visited road patch.
    - > "We use an ensemble method to model uncertainty."
  - `trajectory optimization`:
    - Once the reward model has converged, a **model-based `RL`** agent that optimizes the learned rewards is deployed.
    - It combines **planning** with **model-predictive control** (`MPC`).
- One concept: **_"acquisition function"_** (`AF`).
  - It answers the question: _how to generate_ **_"useful" query_** _trajectories?_
    - One option is to **sample random trajectories** from the learnt generative model.
    - > "The user knows the rewards and unsafe states, but **querying the user is expensive**." So it has to be done **efficiently**.
    - To generate useful queries, trajectories are **synthesized** so as to **maximize** so-called **_"acquisition functions"_** (`AF`).
  - The authors explain (_I did not understand everything_) that these `FA` serve (but not all) as **proxy** for the **_"value of information"_** (`VOI`):
    - > "The `AF` evaluates how **useful** it would be to **elicit reward labels** for trajectory `τ`".
  - The maximization of each of the `4` `FA` is intended to produce **different types of hypothetical behaviours**, and get **more diverse** training data and a **more accurate** reward model:
    - `1-` **Maximize** reward model **uncertainty**.
      - It is based on `ensemble disagreement`, i.e. generation of trajectories that **maximize the disagreement** between ensemble members.
      - The car is found to drive to **the edge** of the road and **slowing down**.
    - `2-` **Maximize** predicted **rewards**.
      - The agent tries to **act optimally** when trying to maximize this term.
      - It should detect when the reward model **incorrectly** outputs **high rewards** (**`reward hacking`**).
    - `3-` **Minimizes** predicted **rewards**.
      - > "Reward-minimizing queries elicit labels for **unsafe states**, which are **rare** in the training environment unless you explicitly seek them out."
      - The car is going **off-road** as quickly as possible.
    - `4-` Maximize the **novelty** of training data.
      - It produces novel trajectories that **differ** from those already in the training data, **regardless of their predicted reward**.
      - > "The car is **staying still**, which makes sense since the training data tends to contain **mostly trajectories of the car in motion**."
  - More precisely, the trajectory generation targets **two objectives** (balanced with some regularization constant):
    - `1-` Produce **informative** queries, i.e. maximize the `AFs`.
    - `2-` Produce **realistic** queries, i.e. maximize the probability of the **generative model** (staying **on the distribution** of states in the training environment).
- About **safe exploration**.
  - Via `AF-3`, the reward model learns to **detect unsafe states**.
  - > "One of the benefits of our method is that, since it learns from **synthetic trajectories instead of real trajectories**, it only has to **imagine visiting unsafe states**, instead of actually visiting them."
  - In addition (_to decide when the model has learnt enough_), the user observes **query trajectories**, which reveals what the reward model has learned.

</details>

---

**`"Semantic predictive control for explainable and efficient policy learning"`**

- **[** `2019` **]**
**[[:memo:](https://ieeexplore.ieee.org/document/8794437)]**
**[[🎞️](https://www.youtube.com/watch?v=FSrzyR8UhxM)]**
**[[:octocat:](https://github.com/ucbdrive/spc)]**
**[** :mortar_board: `UC Berkeley (DeepDrive Center), Shanghai Jiao Tong University, Nanjing University` **]**

- **[** _`MPC`, `interpretability`, [`CARLA`](http://carla.org)_ **]**

<details>
  <summary>Click to expand</summary>

| ![ `SPC`, inspired from `MPC`, is decomposed into one **feature extractor**, one **semantic and event predictor**, and a **guide for action selection**. [Source](https://github.com/ucbdrive/spc).](../media/2019_pan_1.PNG "`SPC`, inspired from `MPC`, is decomposed into one **feature extractor**, one **semantic and event predictor**, and a **guide for action selection**. [Source](https://github.com/ucbdrive/spc).")  |
|:--:|
| *__`SPC`__, inspired from **`MPC`**, is composed of one **semantic feature extractor**, one **semantic and event predictor**, and one **guide for action selection**. [Source](https://github.com/ucbdrive/spc).* |

Authors: Pan, X., Chen, X., Cai, Q., Canny, J., & Yu, F.

- Motivations:
  - `1-` **Sample efficiency**.
  - `2-` **Interpretability**.
  - Limitation of `behavioural cloning` methods:
    - > "Direct imitative behaviors do not consider **future consequences of actions explicitly**. [...] These models are **reactive** and the methods do not incorporate reinforcement or prediction signals."
  - Limitations of `model-free RL` methods:
    - > "To **train** a reliable policy, an `RL` agent requires **orders of magnitude more training data** than a human does for the same task."
    - > "An **unexplainable `RL` policy** is **undesirable** as a single bad decision can lead to a severe consequence without forewarning."
- One term: **"Semantic Predictive Control"** (**`SPC`**).
  - It is inspired by **_Model Predictive Control_** (`MPC`) in that it seeks an **optimal action sequence** over a finite horizon and **only executes the first action**.
  - **_"Semantic"_** because the idea it to try to **predict future semantic maps**, conditionned on action sequences and current observation.
  - `SPN` is trained on **rollout data sampled online** in the environment.
- Structure:
  - `1-` **Semantic estimation**.
    - Multi-scale intermediate features are extracted from `RGB` observations, using **"Deep Layer Aggregation"** (`DLA`), a special type of **skip connections**.
    - As noted:
      - > "Using **semantic segmentation** as a **latent state representation** helps to improve data efficiency."
    - This **multi-scale feature representation** is passed together with the **planned action** into the **prediction module** to iteratively produce **future feature maps**.
  - `2-` **Representation prediction**.
    - _What is predicted?_
      - `2.1-` The **future scene segmentation**
      - `2.2-` Some **task-dependent variables** (seen as _"future events"_) **conditioned** on _current observation_ and _action sequence_. This can include:
        - `Collision` signal (binary).
        - `Off-road` signal (binary).
        - `Single-step travel distance` (scalar).
        - `Speed` (scalar).
        - `Driving angle` (scalar).
        - Note: in their POC with [flappy bird](https://flappybird.io/), authors also predicted the discounted sum of rewards.
  - `3-` **Action sampling guidance**.
    - _How to select actions?_
      - `3.1-` One possible solution is to perform **gradient descent** to optimize an action sequence.
      - `3.2-` Another solution is to perform a **grid search** on the action space, and select the one with the **smallest cost**.
      - `3.3-` Instead, the authors propose to use the **result of the `SMP`**:
        - > "`SPN` outputs an **action guidance distribution** given a state input, indicating a **coarse action probability distribution**".
        - Then, they **sample multiple action sequences** according to this **action guidance distribution**, then **evaluates their costs**, and finally **pick the best one**.

</details>

---

**`"Vision-Based Autonomous Driving: A Model Learning Approach"`**

- **[** `2019` **]**
**[[:memo:](https://www.researchgate.net/publication/332912542_Vision-Based_Autonomous_Driving_A_Model_Learning_Approach)]**
**[** :mortar_board: `University of Michigan` **]**
**[** :car: `Ford` **]**

- **[** _`VAE`, `stochastic policy search`, [`CARLA`](http://carla.org)_ **]**

<details>
  <summary>Click to expand</summary>

One figure:

| ![ The `perception` module, the `memory` or `prediction` module, and the `control` module. [Source](https://www.researchgate.net/publication/332912542_Vision-Based_Autonomous_Driving_A_Model_Learning_Approach).](../media/2019_baheri_1.PNG "The `perception` module, the `memory` or `prediction` module, and the `control` module. [Source](https://www.researchgate.net/publication/332912542_Vision-Based_Autonomous_Driving_A_Model_Learning_Approach).")  |
|:--:|
| *The `perception` module, the `memory` or `prediction` module, and the `control` module. [Source](https://www.researchgate.net/publication/332912542_Vision-Based_Autonomous_Driving_A_Model_Learning_Approach).* |

Authors: Baheri, A., Kolmanovsky, I., Girard, A., Tseng, E., & Filev, D.

- The idea is to first **learn a model** of the environment (the `transition function` of the `MDP`) and **subsequently derive a policy** based on it.
- Three modules are used:
  - 1- A `VAE` is trained to **encode** front camera views into an **abstract latent representation**.
  - 2- A `LSTM` is trained to **predict** the latent representation of the **one time-step ahead frame**, given the **action** taken and the current state representation. Based on this prediction (`mean` and `std`), a next state representation is sampled using the VAE.
  - 3- A `CMA-ES` is trained to **take actions** (`steering`, `acceleration`, and `brake`) based on the `LSTM` hidden state (capturing history information) and the current state representation (predicted). The problem is formulated as an `MDP`.
- One idea about the **continuous** action space:
  - > "We combine the acceleration and brake commands into a **single value** between `−1` to `+1`, where the values between `−1` and `0` correspond to the brake command and the values between `0` and `1` correspond to the acceleration command".
  - The authors use the term _"acceleration command"_ for one of the actions. CARLA works with `throttle`, as human use the gas-pedal.
  - I have realized that the mapping `acceleration` `->` `throttle` is very complex. Therefore I think the agent is learning the `throttle` and considering the **single NN layer** used for the controller, this may be quite challenging.
- About the [`CMA-ES`](https://en.wikipedia.org/wiki/CMA-ES):
  - `ES` means "Evolution Strategy", i.e. an optimization technique based on ideas of evolution, iterating between of `variation` (via `recombination` and `mutation`) and `selection`.
    - `ES` is easy to **implement**, easy to **scale**, very fast if **parallelized** and extremely **simple**.
  - `CMA` means "Covariance Matrix Adaptation".
    - This means that in the `variation` phase, not only the `mean` but also the `covariance matrix` of the population is updated to increase the probability of previously successful steps.
    - Therefore, it can be seen as _Cross-Entropy Methods_ (`CEM`) with momentum.
- About **sampling efficiency**:
  - The authors note that `IL` and `model-free RL` baselines were taking resp. `14` hours and `12` days of driving for training and were both outperformed by the presented `model-based RL` approach which required `5` hours of human driving.
    - This only considers the **time to interact** with the environment, i.e. to record images.
    - It would be interesting to consider the time needed to **learn the policy** afterward.
  - `CMA-ES`, as a **derivative-free method**, is one of the least sample efficient approach.
    - I find interesting that an _evolutionary algorithm_ was chosen given the motivation of _increasing sampling efficiency_.
- About `model-based` RL:
  - The performance really depends on the **ability to learn a reliable model** of the environment.
    - The **low-level** representation of the `VAE` (size `128`) may not capture the most difficult situations.
    - The authors suggest looking at **mid-level** representations such as the [**affordance** representation](http://deepdriving.cs.princeton.edu/paper.pdf) of [DeepDriving](http://deepdriving.cs.princeton.edu/) instead.
  - Here, the authors **strictly split** the two tasks: First learn a model. Then do planning.
  - Why not **keeping interacting from time to time with the `env`**, in order to vary the **sources of experience**?
    - This should still be more **sample efficient** than model-free approaches while making sure the agent keep seeing **"correct" transitions**.

</details>

---

**`"Vision‑based control in the open racing car simulator with deep and reinforcement learning"`**

- **[** `2019` **]**
**[[:memo:](https://link.springer.com/article/10.1007/s12652-019-01503-y)]**
**[** :mortar_board: `Chinese Academy of Sciences` **]**

- **[** _`PILCO`, [`TORCS`](http://torcs.sourceforge.net/)_ **]**

<details>
  <summary>Click to expand</summary>

Some figures:

| ![ First __extract__ some variables - e.g. `curvature`, `desired speed`, `lateral offset`, `offset in heading` - from images using __supervised learning__ and then apply control learnt with __model-based `RL`__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).](../media/2019_zhu_2.PNG "First __extract__ some variables - e.g. `curvature`, `desired speed`, `lateral offset`, `offset in heading` - from images using __supervised learning__ and then apply control learnt with __model-based `RL`__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).")  |
|:--:|
| *First __extract__ some variables - e.g. `curvature`, `desired speed`, `lateral offset`, `offset in heading` - from images using __supervised learning__ and then apply control learnt with __model-based `RL`__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).* |

| ![ The __model-based__ `PILCO` algorithm is used to quickly learn to predict the __desired speed__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).](../media/2019_zhu_1.PNG "The __model-based__ `PILCO` algorithm is used to quickly learn to predict the __desired speed__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).")  |
|:--:|
| *The __model-based__ `PILCO` algorithm is used to quickly learn to predict the __desired speed__. [Source](https://link.springer.com/article/10.1007/s12652-019-01503-y).* |

Authors: Zhu, Y., & Zhao, D.

- Definitions:
  - **State** variables: `x` = [`lateral deviation`, `angle deviation`, `desired speed`].
  - **Dynamical** variables: `y` = [`x`, `curvature`].
  - **Cost** variables `z` = [`y`, `current speed`].
  - **Control** variables: `u` = [`steering`, `throttle or brake`].
  - The variable `current speed` is always known: either given by `TORCS` or read from `CAN bus`.
- One idea: contrary to `E2E`, the authors want to separate `perception` and `control`. Hence the training is divided into **two steps**:
  - 1- Extract _dynamical variables_ `y` from the simulator (assume _full observation_) and **learn a driving controller**. -> Using **model-based RL**.
  - 2- Try to extract `y` from images. -> Using **supervised learning**.
  - This step-by-step method brings advantages such as the possibility for **intermediate checks** and **uncertainty propagation**.
    - But both learning processes are **isolated**. And one defective block can cause the whole chain to fail.
    - In particular, the authors note that the `CNN` fails at predicting _`0`-lateral-offset_, i.e. when the car is **close to the centre**, causing the full system to **_"vibrate"_**.
    - This could be addressed on the **controller side** (_damping factor_ or adding _action consistency_ in the cost function), but it would be better to back-propagate these errors directly to the perception, as in _pixel-to-control_ approaches.
- _What is learnt by the controller?_
  - One option would be to learn the **transition function** leading to the new state: **`x[t+1]`** = `f`(`y`, `u`, `x`). This is what the simulator applies internally.
  - Instead, here, the _distribution_ of the **_change_** in **state** is learnt: **`delta`(`x`)** = `x[t+1]` - `x[t]` = `f`(`y`, `u`, `x`).
  - **Data is collected through interactions** and used to optimize the parameters of the controller:
    - Training **inputs** are formed by some recorded `Y` = [`y`, `u`].
    - Training **targets** are built with some recorded `ΔX` = [`delta`(`x`)].
- Another idea: the car is expected to **run at different velocities**.
  - Hence vary the **desired speed** depending on the **curvature**, the current velocity and the deviation in `heading`.
  - This is what the agent must learn to predict.
  - In the reward function of `PILCO`, the term about desired velocity play the **largest role** (_if you do not learn to decelerate before a turn, your experiences will always be limited since you will get off-road at each sharp turn_).
- One algorithm: `PILCO` = **`P`robabilistic `I`nference for `L`earning `CO`ntrol**.
  - In short, this is a **model-based** `RL` algorithm where the _system dynamics_ is modelled using a **Gaussian process** (`GP`).
  - The `GP` **predicts outcome distribution** of `delta`(`x`) with probabilities. _Hence first letter `P`_.
    - In particular, the job is to predict the **mean** and the **standard deviation** of this distribution which is **assumed to be Gaussian**.
    - This _probabilistic_ nature is important since **model-based** `RL` usually suffers from **_model bias_**.
  - The _cost variables_ are also predicted and based on this `z` distribution, the optimal control `u` is derived using **_policy gradient search_** (`PGS`).
    - More precisely, the _control variables_ `u` is assumed to be function of the expected cost `z` via an affine transformation followed by some saturation: `u` = `sat`(`w`*`z` + `b`).
    - Hence `PGS` aims at finding {`w`, `b`}: the predicted return and its derivatives are used to **optimize the controller parameters**.
    - The new controller is again used in `TORCS` to **generate data** and the learning process is repeated.
- _Why and how is the vanilla `PILCO` modified?_
  - The computational complexity of `PILCO` is linear in the **size of training set**.
    - Instead of using _sparse_ GP method (e.g. `FITC`), the authors decide to **prune the dataset** instead.
    - In particular, observation data are **sparsely collected** and **renewed at each iteration**.
  - Other modifications relate to the difference between input output/variable types. And the use of **different scenarios** to calculate the expected returns.
- One quote about the difference between `PILCO` and `MPC`:
  - > "The concept of PILCO is quite similar to explicit MPC algorithms, but MPC controllers are usually defined **piecewise affine**. For PILCO, control law can be represented in **any differentiable form**."

</details>

---

**`"World Models"`**

- **[** `2018` **]**
**[[:memo:](https://arxiv.org/abs/1803.10122)]**
**[[:memo:](https://worldmodels.github.io/)]**
**[[:octocat:](https://github.com/hardmaru/WorldModelsExperiments)]**
**[[🎞️](https://worldmodels.github.io/)]**
**[** :mortar_board: `Swiss AI Lab, IDSIA` **]**
**[** :car: `NNAISENSE`, `Google Brain` **]**

- **[** _`VAE`, `world model`, `mixture density nets`, `evolutionary algorithm`, `game exploit`_ **]**

<details>
  <summary>Click to expand</summary>

| ![[Source](https://arxiv.org/abs/1803.10122).](../media/2018_ha_1.PNG "[Source](https://arxiv.org/abs/1803.10122).")  |
|:--:|
| *The **`world model` (`V`+`M`)** is trained separately from the **`policy` (`C`)** to predict the **next latent variable** of the `VAE` (!not the `image` itself!) given the current `action`. The `policy` is then trained by **interacting with the 'real' environment**: each image is **encoded**, and the agent received the **corresponding latent variable `z`** together with the hidden variable of a `RNN` that captures the **temporal evolution** and **makes predictions**. The authors show that it is even possible to train this `policy` in the **learnt model** only. With the advantage that **it can easily be made more challenging** to **increase robustness**, by **adding noise** to the `transition` model. [Source](https://arxiv.org/abs/1803.10122).* |

| ![[Source](https://worldmodels.github.io/).](../media/2018_ha_1.gif "[Source](https://worldmodels.github.io/).")  |
|:--:|
| *__Car Racing__ environment. The `steering` action has a range from `-1` to `1`, the `acceleration` from `0` to `1`, and the `brake` from `0` to `1`. [Source](https://worldmodels.github.io/).* |

| ![[Source](https://worldmodels.github.io/).](../media/2018_ha_2.gif "[Source](https://worldmodels.github.io/).")  |
|:--:|
| *The image is **compressed in the encoder of the `VAE`**. A **latent vector `z`** is **sampled** from the estimated `mean` and `variance` parameters of the **prior Gaussian**. Here `z` contains **`32` features**. One can test see the **influence** of some of them, by observing how vectors are **decoded** by the `VAE`. And also see how **random vectors** are decoded. [Source](https://worldmodels.github.io/).* |

Authors: Ha, D., & Schmidhuber, J.

- Motivations.
  - `1-` Address issues faced by model-free `RL`: **`sampling efficiency`** and **`credit assignment`**.
    - In complex tasks, it would be important to have **large networks**, e.g. `RNN`.
    - But this is limited by the **`credit assignment` problem**.
    - Here the solution is to keep the **`policy` very small**, and give it **features of larger models** that were **trained separately**.
    - > "A small `controller` lets the training algorithm focus on the `credit assignment` problem on a **small search space**, while not sacrificing **capacity and expressiveness** via the larger `world model`."
  - `2-` Getting rid of the **real environment** and `sim-to-real` transfer.
    - > "Can we train our agent to **learn inside of its own dream**, and **transfer this `policy`** back to the actual environment?"
    - > "Running **computationally intensive game engines** require using **heavy compute resources** for rendering the game states into image frames, or **calculating physics** not immediately relevant to the game. We may not want to **waste cycles** training an agent in the actual environment, but instead train the agent as many times as we want **inside its simulated environment**."

  - `3-` Model **long term** dynamics observed from **high dimensional visual data**: `64x64` images.

- _How to deal with these sequences of_ **_raw pixel frames_**_?_
  - > "Learning a model of the dynamics from a **compressed latent space** enable `RL` algorithms to be much more data efficient."
  - The authors invite readers to watched **[Finn’s lecture](https://www.youtube.com/watch?v=iC2a7M9voYU)** on **Model-Based `RL`**.

- Three modules to learn, "inspired by our own **cognitive system**".
  - `1-` **Vision (`V`)**.
    - **Spatial** representation: **_compress what it sees_**.
    - **Variational Auto Encoder (`VAE`)**.
      - Each image frame is compressed into a **small latent vector `z`**, of **size `32`**.
      - The latent vector `z` is sampled from the **Gaussian prior `N`(`µ`,`σI`)** where `µ` and `σI` are learnt.
  - `2-` **Memory (`M`)**.
    - Temporal representation: **make predictions** about future latent variable, `p`(`z`), based on **historical information**.
      - > The `M` model serves as a predictive model of the **future `z` vectors** that **`V` is expected to produce**.
      - One alternative could be to **stack frames**.
    - A **`RNN`** is used, combined with a **`Mixture Density Network`** to model **non-deterministic outcomes**.
  - `3-` **Controller (`C`)**: the `policy`.
    - The decision-making component that **decides what `actions`** to take based only on the **representations created** by its `vision` and `memory` components.
    - `C` is a **simple single layer linear** model that maps the **latent variable `z`(`t`)** and **the hidden state of the `RNN` `h`(`t`)** directly to **action `a`(`t`)** at each time step.
  - This structure is based on [`Learning to Think`](https://arxiv.org/abs/1511.09249) (Schmidhuber, 2015).
    - > "A unifying framework for building a `RNN`-based general problem solver that can learn a `world model` of its environment and also learn to **reason about the future** using this model."

- For `M`: _How to deal with the stochasticity of the `transition` model?_
  - > "Because many complex environments are **stochastic** in nature, we train our `RNN` to **output a probability density function `p`(`z`)** instead of a **deterministic prediction of `z`**."
  - `p`(`z`) is approximated as a **mixture of Gaussian distribution**.
  - Therefore a **Mixture Density Network combined with a `RNN` (`MDN-RNN`)**.

- For `C`: _What input for the `policy`?_
  - Features extracted from the `world model` (`V` + `M`):
    - Only `z` (latent variable of the `VAE`): **wobbly and unstable**.
      - > "The representation `z`(`t`) provided by our `V` model only captures a representation at a moment in time and **does not have much predictive power**."
    - `z` and `h` (hidden state of the `RNN`): more stable.
      - > "Combining `z`(`t`) with `h`(`t`) gives our controller `C` a good representation of both the **current observation**, and **what to expect in the future**."
    - No rollout is needed (as opposed to `MCTS` for instance).
      - > "The agent **does not need to plan ahead** and **roll out hypothetical scenarios** of the future. Since `h`(`t`) contain information about the **probability distribution of the future**, the agent can just **query the `RNN` instinctively** to guide its action decisions. Like the baseball player discussed earlier, the agent can **instinctively predict** when and where to navigate in the heat of the moment."

- Dimensions:
  - `1-` `world model` (`V`+`M`): **large**.
    - The goal is to learn a **compact `spatial` and `temporal` representation**.
    - > "While it is the role of the `Vision` model to **compress what the agent sees** at each time frame, we also want to **compress what happens over time.**"
  - `2-` `policy`: **small**.
    - > "We deliberately make **`C` as simple and small as possible**, and **trained separately** from `V` and `M`, so that most of our agent’s complexity resides in the **world model** (`V` and `M`)."
    - Also because of the **`credit assignment` problem**.
    - Optimization algorithm: **Covariance-Matrix Adaptation Evolution Strategy (`CMA-ES`)** (evolutionary algorithm) since there are **a mere `867` parameters** inside the **linear controller model**.
    - Trained on single machine with multiple `CPU` cores, since `ES` is also **easy to parallelize**.
    - The `fitness` value for the agent is the **average `cumulative reward`** of `16` random rollouts (`16` seeds).

- Training `V`:
  - `1-` `10,000` rollouts are collected (with a random agent) from the real environment.
    - (`action`, `observation`) pairs are recorded.
  - `2-` The `VAE` is trained to encode each frame into **low dimensional latent vector `z`**.
    - By minimizing the difference between a given frame and the **reconstructed version of the frame** produced by the decoder from the **latent** representation `z`.

- Training `M`:
  - Only once `V` has been trained.
    - > "In principle, we can train **both models together** in an end-to-end manner, although we found that training **each separately** is more practical, and also achieves satisfactory results."
  - The `MDN-RNN` is trained to model `P`(`zt+1` | `at`,`zt`,`ht`) as a **mixture of Gaussians**.

- _How to deal with more complex tasks?_
  - For **simple environments**, using a **random policy** to collect demonstrations may be enough to **capture the dynamic**.
  - One could use the **learnt `policy`** to **collect new samples** and **iterate**.

- _How to deal with the_ **_imperfections_** _of the_ **_generated environments_**_?_
  - Risk of **cheating the `world model`**.
    - > "Because our world model is only an **approximate probabilistic model** of the environment, it will occasionally generate trajectories that **do not follow the laws governing** the actual environment. [...] Our world model will be **exploitable by the controller**, even if in the actual environment such exploits do not exist."
  - By **adding noise** to the **predictions** of the learnt model, i.e. **favouring robustness**.
    - > "We train an agent’s controller inside of a **`noisier` and more `uncertain` version** of its generated environment, and demonstrate that this approach helps prevent our agent from **taking advantage of the imperfections** of its internal `world model`."
  - This **temperature parameter `τ`** can be adjusted when **sampling** `z` to control **model uncertainty**.
    - It controls the **tradeoff** between `realism` and `exploitability`.
  - Another solution: using **`Bayesian` models** to **estimate the uncertainty**, as in [`PILCO`](https://mlg.eng.cam.ac.uk/pub/pdf/DeiRas11.pdf).
    - > "Using data collected from the environment, `PILCO` uses a **Gaussian process (`GP`)** model to learn the **system dynamics**, and then uses this model to **sample many trajectories** in order to train a controller to perform a desired task."

- Benefits of **substituting** the actual environment with the **learnt `world model`**.
  - > "In this simulation, **we do not need the `V` model** to **encode any real pixel frames** during the **hallucination process**, so our agent will therefore only train **entirely in a latent space environment**."
  - By **increasing the `uncertainty`**, the dream environment **becomes more difficult** compared to the actual environment.
    - > "Unlike the actual game environment, however, we note that it is possible to **add extra `uncertainty`** into the virtual environment, thus making the **game more challenging** in the dream environment."

- Limitations:
  - `1-` Limited **memory capacity** of the `NN` model.
    - > "While the human brain can hold **decades and even centuries of memories** to some resolution, our neural networks trained with back-propagation have more limited capacity and suffer from issues such as **catastrophic forgetting**."
  - `2-` `V` is trained **independently of the `reward` signals**. Hence it may encode parts of the observations that **are not relevant to a task**.
    - > "After all, **unsupervised learning** cannot, by definition, know what will be useful for the task at hand."
    - > "By training together with an `M` that **predicts rewards**, the **`VAE` may learn to focus on task-relevant areas of the image**, but the tradeoff here is that we may not be able to reuse the `VAE` effectively **for new tasks without retraining**."

</details>

---

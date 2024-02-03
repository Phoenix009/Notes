Accident: 
	Accident is an undesired and unplanned (not necessarily unexpected) event that results in a specified level of loss

Incident:
	Incident or near-miss is an event that involves not loss or minor loss but with potential for loss under different circumstances

Hazard:
	Hazard is a state or set of conditions that, together with other conditions in the environment of the system will lead inevitably to an accident.

Risk:
	Risk is the hazard level combined with 
	1. the likelihood of the hazard leading to an accident
	2. hazard exposure or duration

Safety concept:
	It is a measure taken to remove, contain or reduce a hazard

Safety:
	Safety is freedom from accident or losses

Reliability:
	Reliability is the probability that a piece of equipment or component will perform its intended function satisfactorily for a prescribed time and under stipulated environmental conditions

Availability:
	Availability is the readiness for correct service.
	It is also the fraction of time the system is operational

## Dependability Dimensions
- **Availability**: readiness for correct service
- **Reliability**: continuity of correct service
- **Safety**: absence of catastrophic consequences on the user(s) and the environment
- **Integrity**: absence of improper system alterations
- **Maintainability**: ability to undergo modifications and repairs
- **Confidentiality**: the absence of unauthorized disclosure of information
- **Security**: concurrent existence of confidentiality and integrity and availability
- **Survivability**: chance of surviving a catastrophic failure

- The dimensions are orthogonal and we should not assume and relationship between them.
- With respect to different dimensions, one cannot maximise all of them simultaneously - hence tradeoffs are required



## Eternal Chain of Events

$$\text{Fault} \rightarrow \text{Error} \rightarrow \text{Failure} \rightarrow  \text{Activate next fault} \cdots $$

Fault:
	Fault is the adjudged or hypothesised cause of an error.

Faults can be further divided, depending on when they occur
1. **Development faults** may occur while a system is envisioned and created
2. **Physical faults** includes everything that involves hardware
3. **Interaction faults** are everything where the external environment is the cause

Error:
	Error is a design flaw or deviation from a desired or intended state

Failure:
	Failure is the non-performance or inability of the system or component to perform its intended function for a specified time under specified environmental conditions

## Dependability Means
Achieving dependability is about dealing with faults. this can be achieved using the following classes of approaches

1. **Fault Prevention**: Avoid the occurrence or introduction of faults in the first place
2. **Fault Tolerance**: Keeping the service operational, even if fault happens
3. **Fault Removal**: Reducing the number and decreasing severity of faults
4. **Fault Forecasting**: Estimating the current number of faults, to predict future faults

## Dependability Process

>[!todo] 
>paste the V-model


Verification:
- Verification is the check, using objective means that specified properties re fulfilled
- In our diagram, verification activities deal with the horizontal, left-to-right double ended arrows.
- It deals with a single layer in the V-model

Validation:
- Validation is the check, using objective means that the specified users can in a specified context, reach specified usage goals
- This is a high level activity where one iteration of the V model has been executed.


## Hardware Dependability
The two most common issues for hardware or embedded systems are reliability and availability. Typical faults that happen in hardware are that gates do not properly compute their output or bits get corrupted in memory.

Every system fails, we need to decide what our acceptable failure rate is. Dependability target must exists so that systems can be designed for this target.

### Reliability
Reliability is a measure of systems ability to work completely failure-free for a certain length of time or mission.

Under natural assumptions, we can consider the probability of hardware failures as being determined by the failure rate $\lambda$ and time $t$ in a negative exponential probability distribution

$$R(t) = e^{-\lambda \cdot t}$$

A typical measure is **Mean Time To Failure (MTTF)** is the average length of failure free operation after initialisation.

From above considerations,
$$\text{MTTF} = \frac{1}{\lambda}$$

Depending on how we components are connected, reliability can change.
1. Serial Connection
$$R(t)_{\text{serial}} = \prod_iR(t)_i$$
2. Parallel Connection - ==This assumes that failures are independent==
$$R(t)_{\text{parallel}} = 1 - \prod_i(1- R(t)_i)$$

### Availability
Availability is the fraction of time the system is operational

The time depends on the MTTF as well as the mean time needed to repair the system upon failure:
$$\text{Availability} = \frac{\text{MTTF}}{\text{MTTF} + \text{Mean repair Time}}$$
Note: Availability is independent of the mission time


## Markov Fundamentals
- We often want to analyse how the state of individual components impacts the overall state.
- Hardware often have probability distributions that tell us how likely components are to fail.
- Inorder to execute full-system analysis, we can leverage the formal tool of Markov models.


Markov models are stochastic processes describing system behaviour stochastically using concepts similar to state machines.

**Markov Assumption**:
	the probability of the next state depends on the current state only, independent of the earlier states.

**Discrete Time Markov Chains (DTMC)**: state changes are described by probabilistic experiments over successor states.

WE formally define a Markov chain as $(\square, P, s_0)$
- $\square$ - Set of states
- $P \in [0, 1]^{\mid \square \mid * \mid \square \mid}$ - $P_{i, j}$ is the probability of transitioning from state $i$ to $j$
- $s_0$ - initial state distribution $s_0 \in [0, 1]^{\mid \square \mid}$

**Update Rule**:
$$P(s_{t+1} = j) = \sum_iP(s_t = i) \cdot P(s_{t+1}=j \mid s_t = i)$$
$$P_{i, j} = P(s_{t+1}=j \mid s_t = i)$$
$$P(s_{t+1} = j) = \sum_iP(s_t = i) \cdot P_{i, j}$$

**Stationary Distribution**:
We are often interested in the distribution that we get if we run the chain infinitely often.


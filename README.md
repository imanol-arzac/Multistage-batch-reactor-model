# Multistage-batch-reactor-model
Python model of multistage semibatch reactor with multiple reactions and temperature PID controller. The same structure can be easily replicated for other systems.

The model is composed of 3 polysubstitution reactions which are simultaneously in parallel and in series,

$$ \ce{A + B ->[k_{1}] R} \ , $$

$$ \ce{R + B ->[k_{2}] S} \ , $$

$$ \ce{S + B ->[k_{3}] T} \ . $$

All three reactions are assumed to be exothermic, i.e., $\Delta H_{reaction,i} < 0$, for $\forall i \in \{ 1,2,3 \}$, meaning that the reactions release heat. In order to prevent the temperature from rising, a temperature control mechanism has to be implemented.

A schematic representation of the semibatch reactor is shown below. The reactor is assumed to be a perfectly stirred tank, meaning that there are no concentration and temperature gradients spatially ($\frac{\partial C_{i}}{\partial x} = 0$, where x can be any of the 3 dimensional Cartesian coordinates). Instead, these state variables vary over time according to the system dynamics of the model. Chill water is circulated through a cooling jacket to control the reactor temperature. Hence, the control variable is the cooling water flow-rate $q_{c} (t)$, while the controlled variable is the reactor temperature $T(t)$. In order to avoid a thermal runaway, reactant B is added semi-continuously, so as to keep reaction rates limited.

<p align="center">
    <img src = Semibatch_reactor.png width = "500" height = "450" >
</p>

The model is composed of a system of Ordinary Differential Equations consisting of the material and energy balances of the process. The generic material balances for all the reactants, intermediates and products are 

$$ \frac{d C_{A}}{d t}=-k_{0,1} \exp \left(\frac{-E_{A,1}}{R T}\right) C_{A} C_{B} -\frac{q_{in}}{V} C_{A} \ , $$

$$ \frac{d C_{B}}{d t}=-k_{0,1} \exp \left(\frac{-E_{A,1}}{R T}\right) C_{A} C_{B} -k_{0,2} \exp \left(\frac{-E_{A,2}}{R T}\right)C_{R}C_{B} - ... $$

$$ ... -k_{0,3} \exp \left(\frac{-E_{A,3}}{R T}\right) C_{S} C_{B} -\frac{q_{in}}{V} \left(C_{B}-C_{B,in}\right) \ , $$

$$ \frac{d C_{R}}{d t}= k_{0,1} \exp \left(\frac{-E_{A,1}}{R T}\right) C_{A} C_{B} -k_{0,2} \exp \left(\frac{-E_{A,2}}{R T}\right)C_{R}C_{B} -\frac{q_{in}}{V} C_{R} \ , $$

$$ \frac{d C_{S}}{d t}= k_{0,2} \exp \left(\frac{-E_{A,2}}{R T}\right)C_{R}C_{B} -k_{0,3} \exp \left(\frac{-E_{A,3}}{R T}\right) C_{S} C_{B} -\frac{q_{in}}{V} C_{S} \ , $$

$$ \frac{d C_{T}}{d t}= k_{0,3} \exp \left(\frac{-E_{A,3}}{R T}\right) C_{S} C_{B} -\frac{q_{in}}{V} C_{T} \ . $$

The heat balances over the reactor ($T(t)$) and over the cooling jacket ($T_c (t)$), which provide the temperature dynamics of each component.

$$ \frac{d T}{d t} = \frac{q_{in}}{V} \left(T_{in}-T\right) + \left(-\Delta H_{R,1}\right) k_{0,1} \exp \left(\frac{-E_{A,1}}{R T}\right) C_{A} C_{B} + ... $$

$$ ... + \left(-\Delta H_{R,2}\right) k_{0,2} \exp \left(\frac{-E_{A,2}}{R T}\right) C_{R} C_{B} + \left(-\Delta H_{R,3}\right) k_{0,3} \exp \left(\frac{-E_{A,3}}{R T}\right) C_{S} C_{B} + ... $$

$$ ... + \frac{q_{c} \rho_{c} C_{p,c}}{\rho C_{p} V}\left(T_{c,feed }-T_{c}\right)\left[1-\exp \left(-\frac{UA}{q_{c} \rho_{c} C_{p,c}}\right)\right] + \frac{W_{shaft}}{\rho C_{p} V} \ , $$

$$ \frac{d T_{c}}{d t} = \frac{q_{c}}{V_{c}}\left(T_{c,feed}-T_{c}\right)-\frac{q_{c} \rho_{c} C_{p,c}}{\rho C_{p} V}\left(T_{c,feed}-T \right) \left[1-\exp \left(-\frac{UA}{q_{c} \rho_{c} C_{p,c}}\right)\right] \ . $$

Multiple stages were defined based on modification of the heat and material balances. The following stages were considered:

1. *Solvent loading*: the reactor is filled with solvent up to a volume $V_{0} = 2 \ m^{3}$.

2. *Reactant loading*: reactants are are added up to a concentration of $C_{A,0} = 1500\ mol\ m^{-3}, C_{B,0} = 500\ mol\ m^{-3}$.

3. *Heating*: the reactor temperature is raised up to 300 $^{o}C$ with an electrical wire according to the Joule-Lenz law.

4. *Reaction*: semibatch reaction with feed $q_{in} = 0.0002 \ m^{3}s^{-1}$ until a 90 \% conversion of A is achieved, i.e., $C_{A} = 0.1 \cdot C_{A,0}$. The PID controller is active throughout the duration of the reaction stage.

5. *Cooling*: the cooling water flow rate is raised to the maximum allowed by the pump ($q_{max}$) until the temperature is reduced to $T = 50 ^{o}C$.

6. *Unloading*: a valve is opened to extract the reactor contents until there is no volume left inside of the tank.

It was assumed that the reaction requires no initiator, and only depends on the temperature according to the Arrhenius equation. Hence, the reaction will start to occur in the latter part of the heating stage, mainly on the reaction stage, and to some extent on the cooling stage.

The simulation results are:

<p align="center">
    <img src = Simulation_results.png width = "600" height = "1200" >
</p>

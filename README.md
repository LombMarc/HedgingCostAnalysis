# HegingCostAnalysis
Analysis of the hedging cost and the mismatching error of a claim priced through Heston Model. The hedging rule is the delta hedging from implied volatility.


Algorithm:
    produce trajectory of the security S  using Heston_evolution script;
    for every time step and every particles (sample element) compute the theoretical (analytical) price of the option through Heston_opt
    for every time step and every particles of the option trajectory (or cuncurrently with its computation) find implied volatility though euro_opt
    given the implied volatility compute the new weight for the trading period and from this the hedging cost.

    At terminal time, sum along time axis the costs in order to obtain an array of particles of total cost, which will be subject of distribution analysis
    At terminal time, compute the value of the hedging portfolio (computed as sum of weight time value of the assets) and compute the mismatch between portfolio and claim,  analyse distribution of mismatch

assumpiton:
    costant interest rate along time
    stochastic volatillity follow CIR model, so we will model the volatility using CIR class

report and test:
    martingality test on underlying
    feller condition value for cir volatility

parameter to use:
    T <- 1
    N <- 12 #monthly ==> Dt = 1/12
    J <- 2^10=1024
    theta <- .024
    kappa <- .1153
    sigma <- .01
    initial_volatility <- .0635
    initial_price <- 10
    rho <- .2125
    interest_rate <- .015
    strike <- .97

# Code structure

import utilities

general_input <- T:terminal time, N: number of increments, J: sample in every time interval, Dt: size of time increments(generally T/N)

func Volatility_trajectory_cir (theta, kappa, sigma, initial_volatility, general_input) -> Vol_trj: ndarray(T,J)  , Integral_vol: ndarray(T,J)

func Underlying_trajectory_heston (Volatility_trajectory_cir, Volatility_trajectory_cir.parmas , rho:(correlation between volatility and asset innovation), general_input) -> Stock_trajectory: ndarray(T,J)

func Option_trajectory_heston(Stock_trajectory,strike, interest_rate,general_input) -> Opt_trj:ndarray(T,J)

func Implied_Volatility_trajectory (Option_trajectory_heston,general_input,interest_rate ,strike) -> s_imp_trj: ndarray(T,J)

func Hedging_rule (Implied_volatility_trajectory, Underlying_trajectory_heston, interest_rate, strike,general_input) -> Wheight_underlying_trj: ndarray(T,J),  Weight_banck_trj: ndarray(T,J)

func Hedging_cost (Hedging_rule, Underlying_trajectory_heston,interest_rate,general_input) -> Cost_trj: ndarray(T,J), Total_cost: ndarray(1,J) #distribution analysis

func Portfolio_value (Hedging_rule,Underlying_trajectory_heston,general_input) -> Portfolio_value_trj : ndarray(T,J)

func Mismatch (Portfolio_value, Option_trajectory_heston, general input) -> Final_mismatch:ndarray(1,J)   #distribution analysis

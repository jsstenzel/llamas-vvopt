# llamas-vvopt
Repository of the models, data, and analysis used for a retrospective case study of verification planning. This analysis considers uncertainty quantification and optimal verification planning for LLAMAS, the Large Lenslet Array Magellan Spectrograph.

Note that this repository may not include the latest LLAMAS models and calibration data and is not intended for use in observation planning.


## SETUP

Unfortunately requires Python 3.8.8 specifically -- some of the libraries start to fight each other in later Python versions, last time I checked.

Set up a suitable environment by following these steps:

```
1. python -m venv .venv
2. source .venv/Scripts/Activate (each time)
3. python -m pip install -U pip
4. python -m pip install -r requirements.txt
```

Installing `numpy==1.25.2` is unfortunately necessary to get pytensor working outside of an Anaconda environment, but thats the source of all of those `pytensor.configdefults` warning messages.


## DIRECTORY OVERVIEW

```
 |-approx
 | |-gaussian_process.py		(Define/load/save Gaussian process priors and samples)
 | |-learn_gp.py				(Learn a Gaussian process prior from data)
 | |-regression_models.py		(Support functions for defining Gaussian processes)
 |-inference
 | |-bn_evaluation.py			(Analyzing the training and convergence of BN models)
 | |-bn_modeling.py				(Train Bayesian network models)
 | |-goal_based_inference.py	(Defining and conditioning Bayesian network models) 
 |-obed
 | |-obed_gbi.py				(OBED Monte Carlo for calculating utility)
 |-opt
 | |-nsga.py					(NSGA-II for verification problem)
 |-problems
 | |-llamas_snr_full
 | | |-historical data			(Directory for verification data)
 | | |-llamas-etc				(Effective Transmission Calculator)
 | | |-priors					(Directory for pre-generated prior GP's)
 | | |-snr_cost_model.py		(LLAMAS cost model)
 | | |-snr_exp_models.py		(LLAMAS experiment models)
 | | |-snr_problem.py			(LLAMAS verification problem definition)
 | | |-snr_system_model.py		(LLAMAS system model -- calls the ETC)
 | | |-snr_vv_opt.py			(Analysis scripts)
 | | |-snr_vv_opt_slim.py		(Analysis scripts)
 | | |-snr_vv_val.py			(Analysis scripts)
 | | |-vendor data				(Directory for data used to develop priors)
 | |-problem_definition.py		(Template class for verification problem)
 |-uq
 | |-gsa_convergence.py			(Convergence tests for GSA)
 | |-gsa_plot.py				(Plotting for GSA)
 | |-plotmatrix.py				(Plotting)
 | |-saltelli_gsa.py			(Sampling and evaluation for GSA)
 | |-uncertainty_propagation.py	(Plotting, analysis for UP)
```


## ANALYSIS

Directory: `llamas-vvopt/problems/llamas_snr_full`

`python snr_vv_opt.py --run nominal`\
Test evaluation of the system model.

`python snr_vv_opt.py --run UP_QoI`\
Perform uncertainty propagation on system model **(Figure 6)**.

`python snr_vv_opt.py --run SA_QoI_sample`\
Generate samples for performing GSA on the system model. Save samples to logfiles.

`python snr_vv_opt.py --run SA_QoI_evaluate`\
Perform GSA on system model samples **(Figure 7)**. Performs convergence tests.

`python snr_vv_opt.py --run BN_sample`\
Generate samples for training the Bayesian network. Save samples to a logfile.

`python snr_vv_opt.py --run BN_train`\
Train Bayesian network on generated samples. Pickle and save.

`python snr_vv_opt.py --run BN_examine`\
Generate a covariance heatmap of the generated samples **(Figure 8)**.

`python snr_vv_opt.py --run BN_find_ncomp`\
Calculate the BIC of GMM's over range of ncomp, and plot **(Figure 9)**.

`python snr_vv_opt.py --run OBED`\
Run OBED for a few candidate designs using a trained BN.

`python snr_vv_opt.py --run OBED_convergence`\
Generate and save samples for conducting a convergence study of the OBED Monte Carlo.

`python snr_vv_opt.py --run OBED_convergence_eval`\
Bootstraped OBED samples to measure confidence intervals of convergence **(Figure 10)**.

`python snr_vv_opt_slim.py --run OPT_nmc_p5`\
Run a NSGA-II optimization to develop a Pareto front of cost and utility **(Figure 11)**.

`python snr_vv_opt_slim.py --run plot`\
Pareto front using pre-processed results **(Figure 11)**.

`python snr_vv_val.py --run yhist`\
Calculate and print the "y" measurements from historical LLAMAS verification.

`python snr_vv_val.py --run compare_yhist_to_dstar`\
Compare range of possible posterior variances from optimized plan to actual posterior variance of historical plan and data **(Figure 12)**.

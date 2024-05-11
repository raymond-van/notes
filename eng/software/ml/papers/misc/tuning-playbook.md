https://github.com/google-research/tuning_playbook

## Summary
- First set up training pipeline + prediction 
- Model 
	- use a model that already works / closest to your problem
- Optimizer
	- start with the most popular optimizer for problem at hand
	- be prepared to tune **all** hyperparameters of the optimizer
	- Good well-established optimizers include:
		- SGD w/ momentum (nesterov variant)
		- Adam, NAdam
- Batch size
	- use the biggest one possible such that it reduces the overall training time + can fit in memory
	- optimal values of most hyperparameters are sensitive to the batch size, thus changing the batch size usually means you have to re-tune all other hyperparameters
		 - because of this, batch size should be one of the first things you set after the initial configuration (below)
	 - optimizer hyperparameters (e.g. learning rate, momentum) and the regularization hyperparameters are the most sensitive hyperparameters to changes in batch size

  ### Initial Configuration
  - Need 3 things:
	1. Model Architecture (e.g. # of layers)
	2. Optimizer hyperparams (e.g. learning rate)
	3. # of training steps
- Find based on trial and error
- Guiding principle is to find a simple, relatively fast, relatively low-resource-consumption configuration that obtains a "reasonable" result

## Improving model performance
- ideally requires enough computational resources available to run several training jobs in parallel
#### Incremental tuning
- Start with a simple configuration and incrementally make improvements while building up insight into the problem
- Make sure that any improvement is based on **strong evidence** to avoid adding unnecessary complexity
- use automated search algorithms (?) in each round of tuning
	- these automated search algorithms rely on a hand-designed _search space_ that defines the set of configurations to search in
	- no algo currently good enough that can find the best params out of the entire hyperparam search space
- Although one might think we would spend most of our time trying to maximize performance on the validation set, in practice we spend the majority of our time trying to gain insight into the problem, and comparatively little time greedily focused on the validation error.
	- In other words, we spend most of our time on "exploration" and only a small amount on "exploitation"
	 - Insight enables us to identify which hyperparameters the validation error is most sensitive to, which hyperparameters interact the most and therefore need to be re-tuned together, and which hyperparameters are relatively insensitive to other changes and can therefore be fixed in future experiments.
- Each round of experiments should have a clear goal and be sufficiently narrow in scope that the experiments can actually make progress towards the goal
#### Designing experiments
- Define scientic, nuisance and fixed hyperparameters which can change from experiment to experiment
	- Scientific hyperparameters are those whose effect on the model's performance we're trying to measure.
	- Nuisance hyperparameters are those that need to be optimized over in order to fairly compare different values of the scientific hyperparameters. This is similar to the statistical concept of [nuisance parameters](https://en.wikipedia.org/wiki/Nuisance_parameter).
	- Fixed hyperparameters will have their values fixed in the current round of experiments. These are hyperparameters whose values do not need to (or we do not want them to) change when comparing different values of the scientific hyperparameters.
- Then: _Create a sequence of studies to compare different values of the scientific hyperparameters while optimizing over the nuisance hyperparameters_
- Rules of thumb:
	- Optimizer h. params (e.g. the learning rate, momentum, learning rate schedule parameters, Adam betas etc.) are usually nuisance h.p. because they tend to interact w/ other changes
		- rarely scientific h.p. because a goal like "what is the best learning rate for the current pipeline?" doesn't give much insight
	 - _choice_ of optimizer is typically a scientific hyperparameter or fixed hyperparameter
	 - Hyperparameters introduced by a regularization technique are typically nuisance hyperparameters (e.g. dropout rate), but whether or not we include the regularization technique at all is a scientific or fixed hyperparameter
	 - Architectural hyperparameters are often scientific or fixed hyperparameters
#### Creating a Study
- A study specifies a set of hyperparameter configurations to be run for subsequent analysis. Each configuration is called a "trial".
- Creating a study typically involves choosing the hyperparameters that will vary across trials, choosing what values those hyperparameters can take on (the "search space"), choosing the number of trials, and choosing an automated search algorithm to sample that many trials from the search space.
- practically speaking we optimize over the scientific and nuisance parameters jointly: 
	- "can include the scientific parameters in the same search space as the nuisance hyperparameters and use a search algorithm to sample values of _both_ the scientific and nuisance hyperparameters in a single study"
- Generally prefer a quasi-random search over fancier black-box search algos as quasi-random ensures uniform sampling
	- can further use gradient-free optimization techniques e.g. Bayesian optimization after quasi-random search
- A search space is suspicious if the best point sampled from it is close to its boundary -> refine search space
- need to automatically generate basic hyperparameter axis plots for all hyperparameters that we vary in an experiment.
- Additionally, we automatically produce training curves for all trials
- Example hyperparameter axis plot:
![](../../images/Pasted%20image%2020230122113630.png)
#### Examining training curves (w/ validation curve)
- always compare training curves w/ validation curves for overfitting
- Reducing overfitting is often straightforward using common regularization techniques that add minimal code complexity or extra computation (e.g. dropout, label smoothing, weight decay)
- High step-to-step variance in the training or validation error late in training?
	- most likely causes of step-to-step variance are batch variance (from randomly sampling examples from the training set for each batch), small validation sets, and using a learning rate that’s too high late in training
- If model doesn't seem to have converged:
	- either add more training steps or change learning rate schedule
- training loss going up = bug in training pipeline
#### Compute-bound Training
- when not enough time/resources to continue training despite model not finished learning
- Do multiple rounds of training, with each subsequent round increasing the number of training steps
	- There are probably many questions we can answer while only training for ~10% of the production length, but there is always a risk that our conclusions at this time limit will not apply to experiments at 20% of the production length, let alone 100%
	 - trading off trial turnaround time with relevance to the final, long runs
	- e.g.: As a starting point, we recommend two rounds of tuning:
	    - Round 1: Shorter runs to find good model and optimizer hyperparameters.
	    - Round 2: Very few long runs on good hyperparameter points to get the final model.
	- The biggest question going from `Round i` → `Round i+1` is how to adjust learning rate decay schedules
#### Round 1 tuning:
- What hyperparameter values found in shorter runs do we expect to transfer to longer training runs?
	- Very likely to transfer
	    - Early training instability can be resolved in the first round of tuning using a smaller number of training steps. Perhaps these hyperparameters are the closest thing to a sure bet for transfer that we have.
	        - Warmup length
	        - Initialization
	- Likely to transfer
	    - Model architecture - A dramatic win in the model architecture will usually transfer, but there are probably many counterexamples.
	- Might transfer
	    - Optimization algorithm/optimizer hyperparameters - We think this would "loosely" transfer. It’s definitely weaker than the things above it.
	    - Data augmentation
	    - Regularization
	        - If it isn't possible to perfectly fit the training set, the model might be in a regime where regularization is unlikely to help very much.
	- Unlikely to transfer
	    - Learning rate schedule: unlikely to transfer perfectly.
	        - [This paper](https://arxiv.org/abs/2203.15556) suggests that even decay schedule transfers, but we don't believe this is true in general. Example: Tuning sqrt decay on small # of training steps then extending to large # will result in the majority of training occurring at overly small steps.
	            - One can likely do "good enough" with most schedules in the limit of extreme training budget, but noticeable performance improvements can likely be seen if it is tuned.
	        - [Understanding Short-Horizon Bias in Stochastic Meta-Optimization](https://arxiv.org/abs/1803.02021) describes the dangers of trying to pick learning rates myopically
#### Checkpoints
- Set up the pipeline to keep track of the N best checkpoints seen so far during training. At the end of training, model selection is then a matter of choosing the best checkpoint seen during training. We call this **retrospective optimal checkpoint selection**
#### Experiment tracking
- Find a tracking system that captures at least the following:
	- Study name
	- A link to wherever the config for the study is stored.
	- Notes or a short description of the study.
	- Number of trials run
	- Performance on the validation set of the best checkpoint in the study.
	- Specific reproduction commands or notes on what unsubmitted changes were necessary to launch training.

### FAQ
#### Which learning rate schedule?
- default to either linear decay or cosine decay, and a bunch of other schedule families are probably good too but should be non-constant
#### Tuning Adam
- not all the hyperparameters in Adam are equally important
- If < 10 trials in a study, only tune the (base) learning rate.
- If 10-25 trials, tune learning rate and β1.
Proximal Policy Optimization Algorithms (Schulman et al., 2017)

https://arxiv.org/pdf/1707.06347.pdf

#### Prereq: Policy Gradients
- Abbeel PG lecture: https://www.youtube.com/watch?v=AKbX1Zvo7r8
- Lilian blog: https://lilianweng.github.io/posts/2018-04-08-policy-gradient/
- Seita blog https://danieltakeshi.github.io/2017/03/28/going-deeper-into-reinforcement-learning-fundamentals-of-policy-gradients/
 - Karpathy blog http://karpathy.github.io/2016/05/31/rl/
#### Helpful Links
- Abbeel TRPO/PPO lecture: https://www.youtube.com/watch?v=KjWF8VIMGiY
- Spinning up: https://spinningup.openai.com/en/latest/algorithms/ppo.html
- Code: https://github.com/openai/baselines/blob/master/baselines/ppo2/ppo2.py
- PPO Remastered (CleanRL): https://www.youtube.com/watch?v=MGAMbxMlHdE&list=WL&index=7&t=1138s
- TRPO paper: https://arxiv.org/pdf/1502.05477.pdf

## Summary
- improvement over TRPO
	- replaces explicit KL constraint by subtracting a penalty on the surrogate loss (PPOv1)
	 ![](../../images/Pasted%20image%2020221229113922.png)
		 - penalty coeff B is adaptive (Abbeel PPO lecture explains it well)
		 - not PPOv2 (below), but this penalty can be used alongside PPOv2
	- compatible with architectures that include noise (such as dropout) or parameter sharing (between the policy/actor and value/critic function)
	- doesn't require second order optimizations
- when making policy updates, we need to make sure the steps are not too large (or too small) since RL methods depend on the policy to collect more data, too large of a step can degrade the policy, which will degrade the data, resulting in a loop of poor performance
	- PPO uses clipped probability ratios, which forms a pessimistic estimate (i.e., lower bound) of the performance of the policy
 
### Surrogate Objective

![](../../images/Pasted%20image%2020221229115050.png)
![](../../images/Pasted%20image%2020221229114948.png)
### Clipped Surrogate Objective (PPOv2)
![](../../images/Pasted%20image%2020221229115005.png)
- by clipping the probability ratio, this removes the incentive for moving r_t outside of the interval [1 +/- eps]
	- acts as a KL constraint by keeping theta close to theta_old
 - For architectures that involve a critic (advantage/value function) that share parameters with the policy, we can define another loss function that combines the policy loss with the critic loss
 ![](Pasted%20image%2020221229121448.png)
	- S is an entropy bonus loss to ensure sufficient exploration
### Algorithm 
- From PPO Remastered video

![](../../images/Pasted%20image%2020221229121548.png)
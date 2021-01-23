[//]: # (Image References)

[image1]: https://user-images.githubusercontent.com/10624937/42135619-d90f2f28-7d12-11e8-8823-82b970a54d7e.gif "Trained Agent"

# Project 1: Navigation

### Introduction to Environment

The goal of the Project's Agent is to navigate and collect yellow bananas in a large, square world. 

#### The Environment

![Trained Agent][image1]

A reward of +1 is provided for collecting a yellow banana, and a reward of -1 is provided for collecting a blue banana.  Thus, the goal of an agent is to collect as many yellow bananas as possible while avoiding blue bananas.  

The state space has 37 dimensions and contains the agent's velocity, along with ray-based perception of objects around agent's forward direction.  Given this information, the agent has to learn how to best select actions.  Four discrete actions are available, corresponding to:
- **`0`** - move forward.
- **`1`** - move backward.
- **`2`** - turn left.
- **`3`** - turn right.

The task is episodic, and in order to solve the environment, the agent must get an average score of +13 over 100 consecutive episodes.

### The Implementation

#### The Deep Q-Learning Algorithm

The Agent was trained with Deep Q-Learning Algorithm. The details of the algorithm can be found in [Research Paper](https://storage.googleapis.com/deepmind-media/dqn/DQNNaturePaper.pdf)
The major outlines from the Algorithm are:

* **Experience replay** - The act of random sampling a small batch of tuples from the *replay buffer* prevents action values from oscillating or diverging catastrophically because of unwanted consecutive experience correlations.
  * *keeping track* - The *replay buffer* contains a collection of experience tuples (S, A, R, S'). The tuples are gradually added to the buffer as we are interacting with the environment. This helps efficiently use Agent's experiences by possibly using same experiences multiple times.
  * *random sampling* - random experience (S, A, R, S') sampling from the *replay buffer*. For example contrast *Experience replay* vs. the *naive* Q-learning algorithm Agent - which learns from each of its experience tuples in *sequential* order and runs the risk of getting swayed by the effects of this correlation.
    
* **Fixed Q-Targets** - The different way of updating *QNetwork* parameters prevents action values from oscillating or diverging catastrophically because of unwanted consecutive parameters update correlations. 
    * Instead of updating at every learning step, the *QNetwork* parameters are updated every k-th learning step.
    
There are two main processes in the Algorithm:
1. Sampling experience (S, A, R, S') from the Environment with Epsilon-greedy policy and storing it in memory (*Replay buffer*).
2. Learning from sampled experiences - uniformly randomly selecting 256 experiences (from max 1 000 000) and making Adam optimization step according to the Loss. 

#### The Loss
The Agent's performance is optimized by minimizing Mean Square Loss - in each learning step (every 8-th step) the *local* QNetwork is compared to *target* QNetwork by squaring their predicted action-value differences. 
* Consequently, Adam optimizer updates local QNetwork parameters with 0.001 learning rate.
* Also soft update (0.001 * local_param + 0.999 * target_param) is made to target QNetwork.


#### The QNetwork

The Deep Q-Learning Algorithm Agent uses Neural Network for action-value estimation. The current `model_Bananai.py` network consists of three fully connected hidden Linear layers:
- 256 units (with ReLU activation)
- 32 units (with ReLU activation)
- 4 units (without activation because it directly estimates action-value)

### Achieved performance
The current implementation achieves desired performance (average score of last 100 episodes >= 15) in around 1000 episodes of training. 

![Performance plot](performance_plot.png)

### Future improvements

The Agent could be improved in many ways:
* *faster training* - by tweaking Algorithm's hyper parameters Agent could achieve similar performance with less training Episodes
* *more efficient training*: 
    * by incrementally reducing UPDATE_EVERY step hyper parameter (requires fewer steps in Environment)
    * making use of smaller Neural Network (performance does not decrease drastically)
    * smaller Replay Buffer is sufficient (performance does not decrease drastically)
    
* *better performance* - by training longer, increasing Replay Buffer, using more sophisticated QNetwork could improve Agent Policy by developing more interesting strategies (currently the Agent avoids blue bananas too much)
* by implementing *extensions* to the algorithm - [Double DQN](https://arxiv.org/abs/1509.06461), [Dueling DQN](https://arxiv.org/abs/1511.06581), [Prioritized experience replay](https://arxiv.org/abs/1511.059528) and others. 
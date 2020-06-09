# Navigation in a Unity Banana World

## Introduction

This repository contains code to solve the Udacity Deep Reinforcement Learning Nanodegree Navigation Project.

The target environment is a Unity based environment, available at: https://github.com/udacity/deep-reinforcement-learning/tree/master/p1_navigation

## Description

The target environment is a 3D environment, that provides a ray based vision of the surrounding. 

![banana_environment_gif](https://user-images.githubusercontent.com/10624937/42135619-d90f2f28-7d12-11e8-8823-82b970a54d7e.gif)

The state consists of these state values and velocity value(s). The state vector is not detailed any further. Rewards are +1 for collectiong a yellow (ripe) banana and -1 for collecting a blue (stale) banana. 

Four discrete actions are available: 
 - 0 - move forward.
 - 1 - move backward.
 - 2 - turn left.
 - 3 - turn right.

The termination of the episode is not discussed in the original repo, but empirical testing reveals that an episode is concluded after 300 steps.

## Criteria for solving

The criteria for solving the environment is to have an average of at least +13 episode reward at any point for the last 100 episodes.

## Description of the applied model

A DQL model with 2 fully connected 1D layers with relu transfer functions, applying Experience Replay and local-target network structure, with full update of the target network, implemented in pytorch in an object oriented fashion. 

## Solution & Hyperparameters

The model succesfully converged at epoch 241. Showing an average of 13.08 for the last 100 episodes/epochs with the following parameters.

- training parameters
  - tau: 1.0 (tau) /irrelevant, because full update is used/
  - batch size: 32 (batch_size) 
  - gamma: 0.95 (gamma)
  - learning rate: 0.0002 (lr) 
  - target network update frequency: 5 learning steps (update_freq)
  - number of episodes: 500 (episodes)
  - limitation of steps for episode: 300 (max_steps)
  - epsilon start: 0.1 (epsilon_start)
  - epsilon minimum: 0.01 (epsilon_min)
  - epsilon decay: 0.99 (epsilon_decay)
  - target network update type: 'full' (update_type: 'full' | 'tau')
- environment parameters
  - state size: 37 (state_size)
  - action size: 4 action_size        
- network parameters
  - layer-1 neuron count: 128 (fcn1)
  - layer-2 neuron count: 64 (fcn2)
- experience buffer parameters
  - buffer length: 20000 (buffer_length)
- communication parameters
  - print info every x episode: 10 (print_every)
  - score mean window size: 100 (mean_window_size)    
        
## Replicating

- Download the appropriate Unity environment for your system from above github repository.
- Create a python 3.6* environment, containing the following packages 
  - pytorch 0.4*
  - unityagents 0.4
- Clone repo
- Update environment path in you copy (Navigate.ipynb)
- Run the Navigate.ipynb notebook

\*(higher might work, but not guaranteed)

## Saved model parameter-sets
- navigation_state_dict_local.tsf
- navigation_state_dict_target.tsf

## Changing the model & Using the objects

The Agent Object instanciates a BananaNetQLearner (network) object and an ExperienceBuffer object. 
In order to change hyperparameters change values in a ProjectParameters instance. 
After defining environments create an Agent instance with a Unity Environment instance and a ProjectParameters instance, and run the session with the agent:
```python
env = UnityEnvironment(file_name="Banana_Windows_x86_64/Banana.exe")
brain_name = env.brain_names[0]
brain = env.brains[brain_name]

pps = ProjectParameters(
    #training parameters
        seed=0,
        tau=0.01, 
        batch_size=64, 
        gamma=0.975, 
        lr=0.0002, 
        update_freq=5,
        episodes=1000,
        max_steps=200,
        epsilon_start=0.1,
        epsilon_min=0.01,
        epsilon_decay=0.995,
        update_type='full',
    #environment parameters
        state_size = 37,
        action_size = 4,        
    #network parameters
        fcn1 = 128,
        fcn2 = 64,
    #experience buffer parameters
        buffer_length = 10000,
    #communication parameters
        print_every=10,
        mean_window_size=20    
)

agent = Agent(env, pps)
agent.run_session()
```

# Description of Implementation

## Implementation
The complete code for this project is included in the file Tennis.ipynb. The Solution comprises of a Fully connected neural network which is organized into accomplishing Q-function (with SARSA-max aka. Q-learning). The network utilizes Experience replay. The network that implements Q-function consists of two fully connected hidden layers with 128 and 64 neurons respectively. The network is fed with states from the environment, and its inputs correspond to the size of the state vector (37), the outputs returned at the output layer of the network correspond to the Q values for the discrete actions possible (4). For the learning another topologically equivalent “target” network is used, its role will be discussed in the training section.

## Inference
Q-learning is an off-policy algorithm. During training, the network is used to suggest new actions based on the state of the network and a random element. The action chosen is determined by an epsilon-greedy policy. With epsilon probability, a random action is chosen and with a (1 - epsilon) probability the action with the highest inferred q-value is chosen (at a certain state) In the current implementation epsilon responds to the training and epsilon is decayed (at a rate of 0.99 per step) from its initial value until it reaches its minimal value (0.01). At the start of the training epsilon is relatively high (0.1), to sample states randomly. The final policy is still an epsilon greedy policy with the minimum epsilon. Post-training inference is based on the final policy, which includes the final network and epsilon based random action selection.

## Experience Buffer
Each new action selected (either by epsilon-random or greedy way) is stored in the experience buffer that stores current state, current action, reward, next state, done-indicator values (an observation). The buffer has a maximum capacity of 20,000 in the solution. When the buffer is filled with data oldest entries are overwritten. Note that done-indicators are converted to floats.

## Training
The learning of the Q-function is implemented by a dual neural-network approach. The networks are initialized with zeroes. At each step of an episode after storing the aforementioned values in the buffer, a batch (32) of observations are retrieved from the ExperienceBuffer (if it has at least 32 observations in it). The primary "local" network is the network that is optimized. First, a forward pass yields the inferred q-value for the loss function. Another topologically equivalent "target" network's output is used to calculate the next action q-values that through the modified sarsa-max equation yields present action's expected q-value. Q-expected (for the current state) is calculated by reward + gamma * (max(Qs-from-network-for-next-state)) * (1. - done-indicators). A value of 0.95 is chosen for gamma in the solution. 
Finally, the loss is backpropagated with an ADAM optimizer through the local network, using a learning rate of 0.02. After each 5th learning step (and before the training starts), the parameter values from the local network are copied into the target network’s parameters.

## Results
With the above structure and parameters the local network yielded a moving average of 13.08 above the target value of 13. This value was obtained after 241 episodes.

![score history](https://github.com/petsol/Navigation_UnityAgent_DQL_Udacity/blob/master/score_history.png?raw=true)

## Further improvements
There are many improvement possibilities for this vanilla DQN network structure. A simpler but possibly high gain improvement would be to implement Prioritized Experience Replay, since especially at the beginning the agent collects very similar experiences, and it takes a long time for it in some cases to find its second, third, etc. banana. With PER the initial slow orientation probably would be much faster. 
Since positions are very highly correlated with future rewards in this environment another high impact improvement would be a Duelling DQN, where also a state-value function is calculated to improve action choices.
Another possibility to improve on Q value overestimation would be to implement a Dual DQN, where the minimum of the two independent networks’ Q value output helps stabilize the policy against overestimated Q values.
Eventually, other methods that are applicable to discrete action spaces can also be considered for this environment.


# Distribution Based Neural Network (DBNN)

![DBNN Visualization](dbnn-visualization.gif)

## The Principle
Training a neural network (NN) involves training weights such that the error
function at the final layer is optimized. The size of the weight matrix is dependent
on the architecture of the network used. If we can decrease the size of the weight
matrix by approximating the weights by a distribution, we can reduce the number
of trainable parameters and the memory required for the training.

### Assumption: 
We assume that for every weight matrix W<sub>ijk</sub> connecting the
j<sup>th</sup> node of layer i (n<sub>ij</sub>) and the k<sup>th</sup> node of layer i + 1 (n<sub>i+1,k</sub>, ∀k), there
exists a statistical distribution D(p<sub>1</sub>, p<sub>2</sub>, ..p<sub>h</sub>) with parameters p<sub>1</sub>, p<sub>2</sub>, ..p<sub>h</sub> that
can generate these W<sub>ijk</sub> or an equivalent set of weights W′<sub>ijk</sub>, with similar error
value on the output layer.

### Goal of the training: 
Based on the above assumption, our model determines
the parameters p<sub>1</sub>, p<sub>2</sub>, ..p<sub>h</sub> of the distribution instead of the original weight
matrix. The h values of D are generated by the use of two parameters viz. mean
**mu** and standard deviation **sigma** of a Normal distribution, from which individual
values pi are selected by a hard-coded equal probability breakpoint scheme. In other words, we train our neural network to learn the hyperparameters
of the distribution.
For a network with m features and n nodes in the first hidden layer, we can
see that the number of parameters for this layer combination is:

**NN: m ∗ n**

**DBNN: m ∗ 2 (for a distribution with 2 parameters)**

Thus, the memory saving is in the order of __m*(n-2)__ for each pair of layers
in the network.

![](dbnn_parameters.png)

We have used Normal distribution for the purpose of our study. However, the
model can easily be scaled to new distributions and breakpoint search schemes.

## Proposed Architecture of DBNN

The schematic representation of Distribution Based Neural Network (DBNN) can be seen
in Figure 1. Outgoing weights from each node of layer i connect the same number of
nodes of the i+1<sup>th</sup> layer. We propose to generate equidistant quantiles spaced at
a distance of 1/n from each other, where n is the number of nodes in the i+1<sup>th</sup>
layer. The value of z corresponding to P(Z ≤ k/n) ∀ k ≤ n, can be transformed
back to X domain to get the value of each weight.

W<sub>ijk</sub> = z<sub>ik</sub> ∗ σ<sub>ij</sub> + μ<sub>ij</sub> ...(1)

In a neural network, during forward propagation we take the dot product of
the input feature values Fi with the matrix Wi to get the features F′<sub>i</sub> for the next layer. These are then passed through the activation function to get F<sub>i+1</sub>.
For each layer i, F′<sub>i</sub> can be calculated as the dot product (•)

F′<sub>i</sub> = F<sub>i</sub> • (z<sub>ik</sub> ∗ σ<sub>ij</sub> + μ<sub>ij</sub>) ...(2)

On performing batch operation for each layer i,

F′<sub>i</sub> = F<sub>i</sub> • (Z ∗ σ + μ) ...(3)

Z = [z<sub>ik</sub>] ∀ k = 1..n; σ = [σ<sub>ij</sub>] ∀ j = 1..m; μ = [<sub>μij</sub>] ∀ j = 1..m


## How to use DBNN in your Neural Network

This version of DBNN has been implemented in pytorch.
While implementing your neural network, the Linear class from dbnn-linear.py can be called instead of the default Linear class of pytorch library.

## Example:

### Build the PyTorch model using the functional API of DBNN.
input_layer = Linear(input_size, hidden_size)

act_layer = torch.nn.Tanh()

dense_layer = Linear(hidden_size, num_classes)

output_layer = torch.nn.Softmax(1)


Here Linear(..) will automatically call DBNN Linear class instead of pytorch Linear class.

### Cite our work:
Vinayak, N., Ahmad, S. (2023). A Reduced-Memory Multi-layer Perceptron with Systematic Network Weights Generated and Trained Through Distribution Hyper-parameters. In: Sharma, H., Shrivastava, V., Bharti, K.K., Wang, L. (eds) Communication and Intelligent Systems. ICCIS 2022. Lecture Notes in Networks and Systems, vol 689. Springer, Singapore. https://doi.org/10.1007/978-981-99-2322-9_41

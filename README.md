This is the repository for the MATLAB and Python codes for generating the results in the manuscript: Exploring the rules of chimeric antigen receptor phenotypic output using combinatorial signaling motif libraries and machine learning, K.G. Daniels, S. Wang, M.S. Simic, H.K. Bhargava, S. Capponi, Y. Tonai, W. Yu, S. Bianco, W.A. Lim. https://www.science.org/doi/10.1126/science.abq0225

1. **Data Preparation**

- For the arrayed data, in addition to the positional information of the combinational motifs, the initial CAR T cell number is a variable which affects the experimental output. Both are inputs of the machine learning algorithms. 
- We randomly split D2 and use 90% for training and 10% for test, (we repeated this splitting process until duplicate motif combinations were found either exclusively in the training sets or exclusively in the test sets) ensuring all the motif combinations in the test data are different from those in the training.
- We used a one-hot encoding to input motif combinations into the machine learning algorithms. Each motif position was described by a vector of fifteen 0s, and one 0 in each vector was replaced with a 1 corresponding to the absence of a motif (replace the first 0 with 1), the presence of a motif (replace the 0 equal to the part number + 1 with 1), or the presence of CD3z (replace the 15th 0 with 1). We allowed up to 5 motif positions, as well as CD3z, for a total of 6 vectors. This allows for inclusion of a small number of CARs that contained more than 3 motifs, and allows flexible inclusion of additional data for CARs with more than 3 motifs.

2. **Machine learning Framework**

- In this work, we used a Convolutional Neural Networks (CNN), followed by a Long Short-Term Memory (LSTM) network together with fully connected layers. The code is implemented in both Mathematica (Wolfram) and Python 3.7.8 with TensorFlow v2.4.1, both of which produce nearly identical results. 
- The neural network uses the matrices as inputs and outputs one value corresponding to one of the phenotypes (cytotoxicity and stemness). Between the input and output layers, we have 2 convolutional layers, 1 LSTM layer, 1 dropout layer, and several fully connected layers. The convolutional layers detect spatial correlations in input data and the LSTM layer learns the long-term dependencies of the sequence data. We used dropout regularization to prevent over-fitting. The dropout layer connects to fully connected layers which are then flattened and catenated with the cell number input and connect to a dense layer. We used linear activation function to connect this dense layer and the final output layer. For training in Mathematica, we used mean squared error loss and ADAM optimization algorithm with automatic learning rate, and training over 200 iterations.
- We also compared our methods with other widely used machine learning regression methods, such as k-nearest neighbor regression, linear regression, nearest neighbors, random forest regression, and gradient boosted regression. The CNN + LSTM neural network has the best performance and predictive power of the methods compared.
- The neural network architecture is as below:
<img width="1452" alt="NN_structure_CNN_LSTM_positional_encoding" src="https://user-images.githubusercontent.com/15852893/176556088-407be43a-2f5e-4781-aaad-a93634524a58.png">


3. **Selection of neural network Hyperparameters**
- We tuned the hyperparameters for layers in the neural networks to find optimal hyperparameters for the cytotoxicity and stemness datasets. The tuned hyperparameters include convolutional layers filters (10, 20, 50), kernel size (2, 3, 4, 5); LSTM layer units (2, 4, 8), dropout layer dropout rate (0, 0.1, 0.2), and fully connected layer units (6, 14, 64). 
- Hyperparameters were tuned as follows: We performed a grid search of hyperparameters and scored each parameter set by 10-fold cross validation of the training set. The best-performing 10 hyperparameter sets for each dataset (cytotoxicity or stemness) were selected using the K-fold averaging cross validation (ACV) method and used to train 10 neural networks whose outputs were then averaged. The trained neural networks were used to simulate the cytotoxicity and stemness for the 2379 combinations of 1, 2, or 3 variable motifs at a fixed initial cell count of 2000 cells (corresponding to 2000 CAR T cells in 40 uL of flow cytometry sample).
- Hyperparameters for final neural networks are available in supplementary tables.

4. **Ensemble Method**
- Due to the stochastic nature of network initialization and dropout, as well as the availability of a limited training set, every neural network is unique in terms of the parameterization of the network connections. 
- To mitigate the potential impact of this issue, we implemented an ensemble decision method to obtain consensus prediction from ten identical neural networks.

5. **Acknowledgments**
This work is funded by the National Science Foundation (NSF) grant No. DBI-1548297, Center for Cellular Construction. Disclaimer: Any opinions, findings and conclusions or recommendations expressed in this material are those of the authors and do not necessarily reflect the views of the National Science Foundation.

If you find this code useful in your research, please consider citing.

Daniels, Kyle G., et al. "Decoding CAR T cell phenotype using combinatorial signaling motif libraries and machine learning." Science 378.6625 (2022): 1194-1200.
https://www.science.org/doi/10.1126/science.abq0225


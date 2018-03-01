# Step 7: Examine Input Data<a name="emr-troubleshoot-slow-7"></a>

 Look at your input data\. Is it distributed evenly among your key values? If your data is heavily skewed towards one or few key values, the processing load may be mapped to a small number of nodes, while other nodes idle\. This imbalanced distribution of work can result in slower processing times\. 

 An example of an imbalanced data set would be running a cluster to alphabetize words, but having a data set that contained only words beginning with the letter "a"\. When the work was mapped out, the node processing values beginning with "a" would be overwhelmed, while nodes processing words beginning with other letters would go idle\. 
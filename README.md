# Mixtures of Local Experts

This project is my own implementation of Jacobs et al.'s 1990 paper "Adaptive Mixtures of Local Experts". The goal of the original research paper was to define a supervised learning procedure where multiple separate neural networks (known as “expert networks”) learned to deal with different subsets of the training data.

My implementation has a couple of changes that I thought might improve the performance of the model. Most importantly, I realized that multiplying the errors of the local expert networks by a probability was analagous to having a variable learning rate. In my implementation, this learning rate changes based upon the performance of the model on the current example presented to the expert network. The learning rate increases if the difference between the squared error of that specific local network on the current example is small and decreases if the difference is large. This ensures that certain local models will begin to have increasing weight changes for types of examples that it was already good at predicting, and will have decreasing weight changes for types of examples it was already poor at predicting. This guarantees the competitive nature of the experts, and allows each local expert to specialize in one class of the data.

The function I used to update the learning rates was:

d_p = p*((E_avg**2)-(target-y_i)**2

where d_p is the change in probability (learning rate), E was the average error amongst the 4 local experts for that example, target was the target value for the current example, and y_i is the predicted value for the current example. The problem with this function is that it is not bounded, and if the difference between the squared average error and the local network error are very great, the changes in the learning rates can get exceedingly high or low, and the local networks may fail to converge. A function bounded between two reasonable rates such as 0.001 and 0.2, that still takes into account the previous learning rate and errors might have good results.

I used two loops, with the inner loop iterating over all of the samples in the dataset, providing them to the 4 local expert networks one-by-one, and the outer loop representing epochs, or the amount of times the entire dataset will be passed over. This does take some time to run, so a very large batch size might help alleviate the wait, especially with a high number of epochs on a local PC.

The data used is my recreation of the data used by Jacobs et al. It had to be recreated as the data comes from a 1952 paper by Peterson and Barney, so it was not publically available anywhere online. I viewed the graph on Figure 2 of the "Adaptive Mixtures of Local Experts" paper, as well as the graph on page 6 of Hillenbrand et al., 1994 and estimated the distribution of the data. The data represents the first and second formants of the vowels "[a], [A], [i], and [I]. The formants are the frequency components of an acoustic resonance, and more about them can be read here: https://en.wikipedia.org/wiki/Formant . I created 150 utterances of each vowel, and estimated the distribution of each formant for each value based on the aforementioned graphs. The goal of this example was to train the model to recognize the vowel based on the values of the first and second formant values, as this is generally all humans need to recognize a vowel.

I first tested on two standard backpropagation networks with one hidden layer, one with 4 hidden units, and another with 8, as was done in Jacobs et al.'s original paper, and then created my model, which consisted of 4 local experts, each with one hidden layer with 8 units. There were 4 networks so that hopefully one network could become an expert on each of the 4 aforementioned vowel classes. The categorical cross entropy loss function was used for all of the standard backpropagation networks and the 4 local experts due to the multi-class nature of the target values. I also believe that these loss functions represent an improvement over Jacobs et al's original implementation.

I decided to use Tensorflow Keras in Python because implementing this kind of model in R was causing tons of problems with importing the proper packages, and crafting the specific model I wanted in R, with all of its proclivities, was taking much more time than it did to start over with Keras.

Please feel free to reach out with any questions.

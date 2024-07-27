# Week5-GANs-project

**Introduction**

The goal of a GAN is to "learn" the probability density function of a particular data distribution. The data distribution should be specific and diverse; for instance "trees" is an excellent choice, because they are distinct from other plants, and there are many subcategories for breadth of distribution. That is to say, the distribution is wide, not narrow. The data we use today comes from a Kaggle competition, and contains many photos of different breeds of dogs. In this project, I will follow along with a few successful notebooks from that competition (now over) to give myself an introduction to the construction of GAN's. I'll try to properly cite everything I'm doing - if I miss something please let me know. There will be a bibliography at the end of this project. Let's import the data, clean it, then do some EDA.

**Data Importation and Cleaning**

The first step in importing the data will be to pull it from kaggle, then unzip the files.

**Exploratory Data Analysis**

The following EDA was performed by examining the "properties" page of my directories manually. The data supplied by the Kaggle contain 775.22 MB constisting of 20,582 zipped dog images, with 17.62 MB of zipped breed labels. There is exactly one label for one dog image. There were a total of 120 distinct breeds in the data. After unzipping, my all-dogs folder within ./data/ contained 781 MB on disk, and the labels took up 5.3 MB on disk. After cleaning:

While our assignment instructions dictate that we use a histogram or other plots to visualize the data, such tools are inappropriate in this context. The dog photos I have printed out during importation are sufficient to understand what the GAN discriminator will be comparing the "noise" images made by the GAN generator. Here is a nice visualization supplied by one of the Kaggle user (https://www.kaggle.com/code/roydatascience/introduction-to-generative-adversarial-networks):

![GAN_Diagram](https://github.com/user-attachments/assets/c5ace531-67f7-4beb-864d-8eaf612536b1)

Model Construction
A GAN really consists of two different bots. A discriminator is a supervised nueral network that is trained on random alterations of real data, whose job is to determine whether a supplied datum is "real" or "fake". Our discriminator will be fed images of dogs with intentionally noised up pixels so that it does not simply memorize the ~20,000 images of dogs. A generator will be a nueral network that generates fake samples to feed into the discriminator. Initially the generator will have random weights, resulting in random Normal noise images. They will look like static, and nothing like dogs. As the discriminator learns to determine static from dogs, so will the generator learn to "fool" the discriminator into accepting fake images. There is some super interesting evolutionary game theory that goes into this, I would highly recommend reading material on this! Eventually, we will reach an equilibrium where the discriminator and generator reach a Nash Equilibrium, and the entire system cannot get better at generating new data without additional input images / data.

First, I will build a discriminator.

**Commentary**

That's a really big model! There are over 120 million trainable parameters, so we expect the model to run for a very long time before converging. That doesn't even include competition against the generator, which hasn't been construted yet. Before building the generator we first need to train the discriminator on the real dog photos. Let's do that. I did a grid search as hyper-parameter optimization over the learning rate eta. My best result was eta = 0.5. I'll leave in the grid search commented out, but due to resource intensity I don't want to use too much GPU RAM. Let's visualize the training for the discriminator. We'll graph loss vs. epoch.

<img width="347" alt="image" src="https://github.com/user-attachments/assets/da196487-1624-4263-8dec-391c5592ea48">


We have built a memorizer Discriminator. The discriminator "knows" within its weights what a dog looks like. Now all that's left is to build a generator, and to make the discriminator fight it.

Let's build a generator. In order to prevent the generator from just learning to feed pixel values that the discriminator expects to see, we will throttle the latent space of the generator. This will force the generator to learn the distribution the discriminator expects rather that specific digits. That is, it will only be able to "remember" a finite amount of information - instead of memorizing pixels, it will memorize a function to generate from.

**Results and Analysis**

We have successfully trained our generator to make dog images! I ended up running 12 iterations consisting of a varying number of epochs. That's why there's a big red error message that says "Keyboard Interrupt". As the number of epochs changed, so did the learning rate to help prevent overfitting.

It's important to note that the generator did not know about the different dog breeds, but the discriminator did. As such the generator learned the common features shared by all breeds, as well as the unique features that pertain only to individual breeds. It's really fascinating to watch the generator's understanding of the true "dog" pdf evolve. The final model is still imperfect, as can be seen in the pixellation and fuzziness in the output images. What I think is most fascinating about the GAN is it's ability to not only model what you expect it to. The GAN also predicts common backgrounds for dogs! In epoch 11, there is a very convincing image of what appears to be a German Shepherd standing near a street by a park. That's not a German Shepherd, that's not a street, and that's not a park. They are a random collection of pixels that an AI learned would trick another AI into believing the image was "real" with respect to a metric (MSE).

One important note: it is possible to overfit the data, because we are just trying to make a generator that passes an MSE checker. As the training time increases, the MSE will asymptotically converge to the "real" loss of 0, which would indicate we are just replicating the images stored in our Memorizer Discriminator. This is not an issue in a traditional GAN, because the discriminator doesn't "know" a priori what a "dog" is - it learns in competition with the generator.

I'd like to include some visualizations. I think the intermittant outputted dog photos are sufficient to understand what the generator is doing during the GAN training.

**Conclusion**

In this project I walked through a finished Kaggle competition to learn how to create, train, and make predictions with a memorizer GAN. The general process was as follows:

1) Import and clean data.

2) Do an EDA to understand the data.

3) Create a discriminator model to tell photos of dogs apart from random noise. The discriminator memorized 10,000 dogs, and so our GAN is a memorizer GAN and not a general GAN.

4) Create a generator model that can do reverse convolution to transform noise into sensible images of dogs.

5) Connect the genererator to the discriminator through a throttle so that the generator cannot simply mimic each individual pixel expected by the discriminator.

6) Train that GAN. I ended up using slightly over 12 epochs before I got tired of waiting for marginal improvement. By 12 epochs the generator had made many very convincing dog images still with notable imperfections.

7) Submit to Kaggle for a score.

I learned a lot through this project. In the future I would like to build a general GAN, but for this project I was worried about making the shapes work.

Thanks you for reviewing my project, and have a wonderful day!





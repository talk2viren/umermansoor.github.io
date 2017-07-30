---
layout: post
title: AI Is Not Magic. How Neural Networks Learn
comments: True
excerpt_separator: <!--more-->
---

In my previous [blog post](https://codeahoy.com/2017/07/27/ai-winter-is-coming/), I claimed that "*AI is not magic.*" In this blog post, my goal is to discuss how neural networks learn, and show that AI isn't a crystal ball or magic, just science and some very slick mathematics. This is a *high level intro* and assumes that you are familiar with neural networks.

Let's start with an example. Suppose we are building an app to [identify hot dogs](https://www.theverge.com/2017/6/26/15876006/hot-dog-app-android-silicon-valley). Take a picture of a hot dog and the app will tell you if it's a "hotdog". Give it a picture of something else like a shoe and it will tell you that it's "not hotdog"

![hotdog]({{ site.url }}/img/blogs/nothodogapp.jpg)

<!--more-->

To identify hot dogs, we choose to build a neural network and train it over 1,000 images of hot dogs of different varities and various settings, New York vs Chicago, with and without relish, half eaten and so on. We also train the neural network over 5,000 images of various non hotdog objects like shoes, hamburgers, burrito, human legs to name a few. Our goal is to train the neural network tell "hotdog" apart from "not hotdog" when it's given an image it has never seen before.

Imagine our neural network consists of few layers of neurons. The actual number doesn't matter.

![neural_network]({{ site.url }}/img/blogs/neural_network.svg.png)

The neurons have weights and biases, which allow a neuron to change it's output for the same input values. The **goal is to find the best set of weights and biases** over our training data. What constitutes the best set of weight and biases? The one which gives the **minimum error**. To make sense of it all, let's define a an error function of weights and biases. Let's give it a fancy name: the cost function.

> Cost Function (weights,biases) = # of images incorrectly identified / Total # of images

Great. Now we can measure the performance of our neural network and have a clear objective: **find weights and biases which minimizes the cost function**. So how to find weights and biases? One way is to just **randomly** pick them, run neural network over entire training data (6,000 images) and calculate the cost function. Keep repeating until we've found a low enough value of the cost function that we like. Let's say we want to stop when the neural network has a success rate of 99%. When we reach this goal, the combination of weights and biases have *detected some similarity between hotdogs* and could identify new hotdog images. Let's see how this would work. To keep it simple, suppose we are only doing this for 2 weights and biases.

**Iteration 1**: Weight1 = 1, Weight2 = 3, Bias1 = 1, Bias2 = 4. Let's say it correctly classifies 300 out of 500 images of hot dog correctly. We'll say the error rate is 200/500 = 40%

**Iteration 2**: Weight1 = 14, Weight2 = 6, Bias1 = 3, Bias2 = 0.2. This time is classifies 250 out of 500 images of hot dog correctly. The error rate is 200/500 = 50%, worst than last time.

**Iteration 3**: Choose weights and biases randomly again, roll the dice and error rate is 35%. Hmmm...

...

I hope you can see why this approach won't work. There is no science, even heuristics to it. If the luck isn't on our side, we could keep iterating until the end of time. And this is with just 2 weights and biases. In reality, it's not uncommon for neural networks to have hundreds or even thousands of weights and biases. The shot in the dark approach just isn't practical, it's impossible.

![deep_neural_network]({{ site.url }}/img/blogs/deep_neural_network.png)

But thanks to mathematics and **calculus**, we have a way of finding optimum weights and biases quicker, or in other words, minimizing the cost function in a scientific manner. This is done using an algorithm called the 'Gradient Descent.' The first time it runs, the algorithm picks up random weights and biases, but in **subsequent iterations, it doesn't chooses randomly but in a calculated manner to minimize the cost function even further**. It keeps iterating until it finds the minimum value of the cost function. At this point, our neural network is said to have been 'trained' and it could start classifying pictures that it hasn't seen before.

Of course, there is much, much more happening under the hood. If you are interested, Andrew Ng has an [excellent lecture](https://www.coursera.org/learn/machine-learning/lecture/8SpIM/gradient-descent) on gradient descent and I encourage you to watch the video so understand it in more detail.

Let's revisit our cost function:

> Cost Function (weights,biases) = # of images incorrectly identified / Total # of images

This cost function is too simple and isn't practical for gradient descent. Gradient descent cannot make incremental updates to weights and biases to minimize it. We need a **smooth** cost function. In practice, people usually use a quadratic cost function also known as the *mean squared error* (MSE).  

![mse]({{ site.url }}/img/blogs/mse.svg)

Here **Ŷ** is the prediction of the learning algorithm, **Y** is the actual value and **n** is the size of the training data. It's a measure of how close predictions are to actual values. Data scientists often multiply the cost function by 1/2 because that way the squared term is easier to cancel when taking derivative of the function. Speaking of derivative, gradient descent minimizes the cost function by taking its [derivative](https://en.wikipedia.org/wiki/Derivative) (slope at that point) and moving in the direction where it is decreasing.

![gradient_descent]({{ site.url }}/img/blogs/gradient_descent.png)

Unlike the first simplified cost function we created, MSE is a **smooth** cost function. In each iteration, gradient descent updates weights and biases to move downhill to the lowest point where the cost function is minimum. As a side note, there are [many types of cost functions](https://stats.stackexchange.com/questions/154879/a-list-of-cost-functions-used-in-neural-networks-alongside-applications) but MSE works well in many cases.

There is one more concept that you should know: the **learning rate**. The learning rate is the amount by which gradient descent updates weights and biases in each step. If the learning rate is too low, the algorithm may take a very long time to find the minimum. If the learning rate is too high, the algorithm may overshoot and miss the minimum (i.e. jump to the other side of the hill.) In practice, several parameters like learning rate and others that we'll see in later posts needs to be adjusted to get the best results and performance.

Gradient descent, or rather its variations and several optimizations (as we'll see in later posts), remains in wide use in machine learning algorithms like linear regression and neural networks. In linear regression, it gives us the line that best fits the data we are modeling; in neural networks, it gives us the best weights and biases.

Neural networks and machine learning algorithms are not magic or crystal ball. The reason I say this is because many CEO's and executives overestimate the power of AI because of the unrealistic picture painted by the media. AI is extremely powerful and is proving itself with positive ROI in many domains, but it also has its *limitations*. You should understand what the AI can and cannot do and then incorporate into your overall strategy. Andrew Ng gave an easy [rule of thumb](https://hbr.org/2016/11/what-artificial-intelligence-can-and-cant-do-right-now):

> **If a typical person can do a mental task with less than one second of thought, we can probably automate it using AI either now or in the near future**.

![machinelearningcansandcannots]({{ site.url }}/img/blogs/machinelearningcansandcannots.png)

See you next time.

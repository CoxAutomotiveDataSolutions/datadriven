---
title: "To accuracy... and beyond"
date: 2018-05-08T08:08:50-04:00
author: David Asboth
image:
  thumbnail: /images/bullseye.jpeg
categories: [Data Science]
---

In Data Science, when you learn about how to predict the future with machine learning, you are presented with an optimisation problem. The central focus of machine learning, at least the subset called "supervised learning", is to ask "what is the best way to predict Y using X?" where "best" has a specific meaning. When you have past examples to learn from, "best" is some sort of measure of accuracy. How well did this particular model predict Y using X, when we can check it against past examples? Whether this is a classification task, where you're trying to put things into the right category, or regression, where you are trying to get as close to a continuous number as you can, the higher the accuracy the better your model. There are ways to guard against complacency to ensure you're not just learning the particulars of your dataset, but ultimately you are trying to get as high an accuracy score as possible.

The question we often encounter in business is: is it realistic to boil predictive tasks down to choosing the most accurate model?

## When does it make sense to stop chasing accuracy?

There are a number of reasons why model accuracy is not the only consideration a business has when trying to predict something. One of our missions at Cox Automotive Data Solutions is to *"help our clients make smarter, and more informed, decisions"*. Making better predictions is only a small part of that - it's the final decision that matters. If your most accurate trained model is hard to interpret, and it doesn't teach you anything new about the world, how can you use it to make better decisions? Let's look at a simple example.

Suppose you want to predict the number of clicks you'll receive on an advert for your car on an advertising platform, because you want to understand what makes a car competitive in order to plan your sales strategy. You know the price, manufacturer, size, top speed etc. of all your cars, and you train two algorithms on previously advertised cars: a linear regression and a neural network. The predictions of the linear regression get you within 10% of the number of true clicks, whereas the neural network is eerily accurate, at just 2% off on average. Which one do you use?

If this was a Kaggle competition, where accuracy is key, you'd obviously prefer the neural network. In a business setting, however, how does your neural network inform your car advertising strategy? It is a black box and its accurate predictions *do not teach you anything about cars*. However, a linear regression whose coefficients are more meaningful, can tell you something you perhaps didn't know. If it turns out the size of the car is strongly predictive and positively correlated with clicks, you might decide to buy bigger cars. This sort of decision is not easily extracted from a neural network.

## Should we always just do linear regression?

Data Science in the business world is focused on helping to make decisions, and these are often not served by the most accurate predictive models. However, there are cases where the ability to make better predictions is the catalyst for a decision. If our above model predicted the final sale price instead of clicks, we could use our car price predictor to forecast budgets. An accurate prediction of how much our current stock would sell for may be enough to decide our future revenue targets and the reason behind the prices may not be relevant in that context; we just want to be as close to the real figure as possible. In these cases it is entirely appropriate to optimise for accuracy, so we don't want to throw out neural networks from our repertoire altogether.

Ultimately, Data Science in a business environment mostly consists of working on answering vague questions from stakeholders, which are not neatly packaged predictive problems. Any model we build must be communicated to, and understood by, our stakeholders, which gives our work a constraint that isn't one you run into when studying machine learning. Once you move beyond the textbooks into the real world, you also move beyond just looking at accuracy.


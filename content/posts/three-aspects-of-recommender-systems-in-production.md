---
title: "Three Aspects of Recommender Systems in Production"
date: 2019-08-02T10:38:50+02:00
draft: false
---

Recommender systems are everywhere. They are helpful but sometimes biased. But building a recommender system which is for sure an application of machine learning in production requires some special treatments and aspects to cover in comparison to other machine learning application like let's say classification.

In this article I will go through three of these aspects:

![alt text](/assets/headers/photo-1533073526757-2c8ca1df9f1c.avif)

_Photo by [Javier Allegue Barros](https://unsplash.com/@soymeraki)_

## 1. Model Selection

How do you choose the model? What constitutes a good model?
Let's assume you need a classification model. Pretty easy to find a baseline, right? Any classification problem statement that you might have, the random forest is probably going to work reasonably well. Unless you have images, in which case you might go with a convolutional neural network, and wallah there you have it.
But things are different in recommender systems!
If you do a survey, you will find several standards approaches, such as the class of models that won the Netflix prize called collaborative filtering models, in which it uses information about how people buy or use your items to recommend other items.
But these could be useless for some applications, and the fact that Amazon and Netflix are using these sort of models doesn't mean they work perfectly well for you as well.
This requires a careful and detailed look at your problem statement. If novelty is an important component of your problem like if you need to recommend news articles, a collaborative filtering approach might be completely useless to you.
In collaborative filtering approach when you want to recommend a news item based on another news item, you look what sort of users looks at these two news items. If a given set of users looked at item A and the same set of users looks at item B that means these two items are similar and if a user looks at item A you should probably recommend them item B.
The problem here is that news is an area where novelty is important, and if you use collaborative filtering and the article is new, nobody looked at it, so no data to rate the recommendation and you end up not having any good result at the end.
Another areas to be considered is the popularity and continuation. For instance, if you watched a TV series and you come back to the site again, the recommendation should take into account that you might continue watching the series, or if you went to an e-commerce site and viewed and bookmarked few items, chances are that you come back to purchase them are high.
Another factor could be metadata. Let's recall that news items recommendation problem; You know a little bit about the user, like what sort of interest they have. So if somebody is interested in sports, and you know the given article is about the sport with its metadata, even though it's new you have now something to go on.
If you don't know what is the right model or indeed if there is a right one, ensemble!
Mix different models to cater to different user interactions.
But with all the above, how do you know a given model is better than another? This question takes us to the second main difference between RS and other ML applications.

## 2. Ensemble, ensemble, ensemble

How do you know one model is better than another?
In recommendation systems, there is a technique called ranking metrics which defines the goal of your system is to compute a ranked list of items to recommend to a given user and a good model is the one that put users interest at the top of this list.
The usual way is offline testing is to take the interaction data, split it to a training and testing set, feed the training to model and if the result shows users interests at top of the list in comparison to test set, then you are all good.
Even though you have all these metrics and you have to use them, but the evaluation is still difficult.
What do you even measure? Browse? Click? Purchase? What is the expression of interest?
Like if you are a fashion eCommerce, people might save a lot of items (worth of thousands of dollars) but this is more like an aspiration to them than the expression of interest to purchase.
Even if you take purchases data, that's also sparse, since people didn't buy a lot of items and that's not enough data to train a model.
So, any of these on their own aren't good evaluation metrics, and you need to think about combining those factors that matter to you.
Is the future like the past?
It's a simply difficult challenge for recommender systems. You get some data for training, you train your model and from tomorrow onwards you will have totally different data to feed to your model!
Recalling that fashion eCommerce, imagine you have a collection of dresses and they are going to be immensely popular soon after they're released. RS starts to recommend those items but this is a new collection, after a few weeks people taste are going to change and recommendations won't be relevant anymore.
Presentation bias?
Let's say you put everything in the hand of the recommender system, and all the items being shown are come from the recommender system.
Now if you take the interactions from this site and feed it into another model, it's going to produce the same result as your previous model was predicting! Yes, because the only thing the new system is influenced by, is what your current system is presenting!
One way to overcome this issue is to give your users a set of random data and use that piece of random data as an unbiased evaluation set.
Another way could be to log what you've shown to the user and map what they've seen and what they haven't bought.
These sort of unbiased data could help to boost your system out of the local minimum it might be in.

## 3. Engineering Architecture

Usually, in ML you train and evaluate the model offline, and once you have the model you spin up a service that accepts the features and returns a prediction. So at anytime your service asks for a recommendation item, it goes out to the database search some features and put it in a JSON, send it back to your service.
But the story is different in RS where you are going to scoring hundreds of thousands of data.
Also, Ranking is computationally intensive, imagine a website like Pinterest where it has hundreds of millions of items and you want to recommend the most relevant pins. This means you need to score lots of things, you need to generate candidates from millions of possibilities and score tens o thousands of candidates. The way items are going to be represented is in multi-dimensional vectors and if a single item is 256 floats that's kilobyte on its own, thousands of it would be a megabyte but you need to score hundreds of thousands and serialize them and send it over the wire! That'd be a nightmare for your users.
One way to overcome this is that your model has to live in the same box with your data due to serialization overhead.
Another point is you don't need to compute everything! Put some factor like some items are a year old so we don't need to score them.
In another hand, you can use some sort of clever space partitioning technique where you have these latent representations and then you can carefully slice this space into subsets where you know that similar items live and then when you want to retrieve related products just go to that subspace and not have to scan the whole thing.

### _Precompute can't help!_

Most of the prediction has to happen online, so you can't define a system that goes to your database, compute the recommendation and when a user browses on data it just takes the recommendation from some sort of cache or database.
For example, the way YouTube does it is as a new user comes in and click on video A and the video B and then video C, it folds those interactions and updates the model to serve the users with recommendations.
That means not only your predictions have to be online but also your fitting system. So often what you have is prediction system is online and the fitting system always there in the background churning and ingesting new information and update the presentation.
So you don't just train a model and put it into production, the data is always changing and you need to have tools to monitor these changes.

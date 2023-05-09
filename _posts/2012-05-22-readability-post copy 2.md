---
layout: post
title: "Price elasticity and optimization"
excerpt: "..."
tags: [sample post, readability, test]
comments: true
---

Customer Level Price Optimization

Standing on the sidewalk outside the terminal, 3 of us are waiting to get a ride back home. I checked Uber, they checked Lyft, and we all compare prices. 

“$35.89”,  “$38.70”, “Mine’s $36.01”. We shut our apps, but while we waiting for the cheapest ride, we joked about why our prices were different...


Aside from this ride share example, most of shopping today is an individual experience. When you see a product’s price, it’s a price that only you see. This works the other way around: you cannot see the price anyone else gets unless you ask.

This pricing opacity opens up the opportunity for businesses to charge more curated prices. Although there are many constraints and repercussions to this, many businesses will offer dynamic customer pricing to optimize their revenue. 

Here’s how it’s typically done at credit card companies, banks, airlines, ride shares, hotels, movie theaters, sports tickets, amazon, and more e-commerce industries:

1. Identify price constraints
2. Determine willingness to pay as a function of price
3. Find the price point where expected revenue is maximized
4. AB test, learn, iterate.

There’s a lot that goes into both of these so I’ll dig deeper…


1. Constraints 
If you’ve studied microeconomics, you’ll know P = MC.

The marginal cost (MC) of a product is the lower bound price (P) constraint. Uber will not offer a ride price that is lower than the cost of gasoline + driver earnings + dispute losses + any other cost Uber factors into a single ride.

Competition creates the upper bound. Uber will lose business if its prices are consistently $10 above Lyft. Uber will set its price so that you’re willing to pay for it instead of going to Lyft.

The upper and lower bound determined respectively by competition and marginal cost. 


2. Willingness to Pay (WTP)
WTP is how likely a customer will purchase a product at a certain price point. How a customer’s willingness to pay changes given a price change is the customer’s price elasticity. 

For a single customer, WTP is binary: they are willing to pay or they aren’t. For a segment of customers, or in a probabilistic model, WTP is a demand curve.

￼￼


To maximize profits, businesses will try to understand the demand curve for each customer. In particular, businesses with highly elastic demand curves will build sophisticated models to determine a customer’s price elasticity											
WTP = function(Customer Attributes, Circumstantial Attributes, P)

A function to determine willingness to pay may include attributes about a customer: Their income, debts, past purchasing history, and loyalty to product. The function will also include circumstantial attributes: Time of day, current location, current and forecasted demand. A data scientist’s job is to figure out which function best transforms the customer and circumstantial attributes to best predict WTP at given price points.

Using XG Boost to Predict Willingness to Pay
So which probabilistic model should we use? XGBoost comes to mind first because it has notoriously won many Kaggle competitions. You can find numerous explanations of XGBoost online, but I think it can be summarized to the following (in order of simplicity)
	•	XG Boost is at core a regression tree: It builds a decision tree where each node has an output of a prediction and (usually) mean squared error. Like with regression trees, you can select the maximum depth (max_depth) of the tree. Keeping it at just 3 nodes deep will reduce the likelihood of overfitting. You can also set the minimum gain (gamma) for a new tree node to be established.
	•	XG Boost uses an ensemble method to build trees: It creates a regression tree, then uses the residuals of that regression tree as the independent variable into the next regression tree it builds. This makes a lot of practical sense. If you have one model that has some error, but you are able to predict that error exactly, then you have a model that can predict perfectly. This method is also called “Boosting” and the downside is that it makes the model more nearly impossible to explain in a classical sense. SHAP values and some of XG Boost’s built-in functions like XGB importance can help figure out relative importance of features.
	•	XG Boost computes really, really fast: Parallelization means splitting up a large task into smaller tasks across processing cores. XG Boost uses parallelization per tree, so that each tree can be built off the residuals of the previous tree. 

This is a really simple explanation of XG Boost. The important part is that XG Boost will return a value of 1 and 0 for each customer given a price. This value is the probability that the customer is willing to pay at the given price point.

price = [1,2,3,4,5,6,7,8]
wtp = [0.74,0.68,0.62,0.56,0.5,0.44,0.38,0.31]

The result of the XG Boost model may reveal that some customer segments have really high price elasticity while others may have low price elasticity. In finance, customers with lower credit and therefore less options will be price inelastic whereas customers with good credit and many options will be price elastic. In the rideshare example, customers getting a ride from a remote area at 5AM will be more price inelastic than customer’s in the center of a city during rush hour. 

￼


3. Revenue Calculation

Revenue will vary greatly by industry. For simplicity we’ll assume that if we hold demand constant, there’s a linear relationship between price and revenue. Let’s say if the price is $1, revenue is $1.

To calculate expected revenue, multiply revenue by willingness to pay to get expected revenue. The optimal price is the price at which expected revenue is maximized. 

price = [1,2,3,4,5,6,7,8]
revenue = [1,2,3,4,5,6,7,8]
wtp = [0.89,0.8,0.71,0.62,0.53,0.45,0.36,0.27]
expected_revenue = [0.89,1.6,2.13,2.49,2.67,2.68,2.51,2.17]

￼




---
title: "Comparing Apples to Oranges: The Dangers of Estimating Effects"
date: 2018-08-29T09:00:00-04:00
author: Daniel Timar
image:
  thumbnail: /images/time_of_day.jpg
categories: [Data Science]
excerpt: "Working with [Manheim](https://www.manheim.co.uk), a Cox Automotive brand that provides [vehicle auctions](https://www.manheim.co.uk/our-services/auction-services), [inspections](https://www.manheim.co.uk/our-services/vehicle-solutions/inspection-services), [transport](https://www.manheim.co.uk/our-services/vehicle-solutions/transport) and [reconditioning](https://www.manheim.co.uk/our-services/vehicle-solutions/reconditioning), we are often asked about the potential effect of certain attributes of vehicles on the final sale price."
---
Working with [Manheim](https://www.manheim.co.uk), a Cox Automotive brand that provides [vehicle auctions](https://www.manheim.co.uk/our-services/auction-services), [inspections](https://www.manheim.co.uk/our-services/vehicle-solutions/inspection-services), [transport](https://www.manheim.co.uk/our-services/vehicle-solutions/transport) and [reconditioning](https://www.manheim.co.uk/our-services/vehicle-solutions/reconditioning), we are often asked about the potential effect of certain attributes of vehicles on the final sale price. For example:

* How much more money do cars with MOT or Service Book sell for than those without? 
* For how much more money do vehicles with [SureCheck Gold]( https://www.manheim.co.uk/our-services/more-solutions/manheim-surecheck), a top-level vehicle inspection service, sell for compared to cars without SureCheck Gold?
* How about the benefits of location or time where/when the auction takes place?

Estimating the added value of these attributes on sales performance is essential, especially when our customers consider spending money on improving a vehicle pre-sale, e.g. by obtaining an MOT or relocating to an alternative auction site, with the goal of additional return in the auction.

The way to answer questions of this kind seems rather straightforward at first; why not just take all the vehicles we sold over a fixed timespan, split them into two groups based on the attribute we are estimating the effect of, and compare the two groups based on some kind of sales performance metric, like the final auction price as a percentage of a standard valuation estimate?

It is highly improbable that the metrics we would like to compare to one another will exactly match, but a considerable difference between the two, in theory, should indicate an effect of the attribute in question, *assuming that that the two groups are otherwise identical*.

The problem with this is that in reality, in complex systems where countless moving elements potentially affect each other, the assumption above does not hold.

## From broad question to analysis

Take the following example: wholesale auctions at Manheim are held throughout the day, with morning slots the most popular, but many vehicles are presented and sold in the second part of the day too. A question that arose in the business recently was *how does the time of day affect the sales performance of the vehicles that are entered into auction?*

Is it possible, for instance, that towards the end of the day buyers in the auction site will simply run out of money and lose interest in vehicles that are entered later? Or do they go after a certain type of stock which they will aggressively bid for regardless of when the vehicle comes up? Or is the difference between distant time slots negligible? 

To make this rather vague question easier to tackle, we had to make some simplifications and narrow the scope.

- First, from the questions above we defined a null hypothesis that we would like to collect evidence against. Such null hypothesis could be something like: *The time of day of the auction does not affect the sales performance of the vehicles on sale*. Instead of a continuous timespan, it is easier to split the auctions into two categories, "morning" and "afternoon", to fit into the same two-group structure that was mentioned above. Naturally, auction sites and days where/when only one auction was held are excluded from the dataset, to keep only the scenarios when the vendors and buyers could decide when to sell/buy. That left us with two groups of vehicles; those that were sold in the morning auctions instead of the afternoon one on the same day and site, and vice versa.

- Second, we narrowed the scope to the most frequently sold vehicle types, which left us with a sufficient sample size even when controlling for confounding variables (see below).

- Third, we compared the morning and afternoon vehicle groups, looking for attributes *other than the time of auction* that could explain the difference in the sales performance and hence obfuscate the potential timing effect we would like to shed light on. Such confounding vehicle attributes are mileage, vehicle age and external condition, all of which likely affect the performance in one way or another, provided that the metric does not already contain them implicitly. Minor difference between the composition of the two groups in terms of these attributes can happen by chance, after all, so it is highly unlikely that the average age of the “morning vehicles" sold is exactly same as the afternoon one. A significant difference, however, could possibly indicate entirely different types of auctions. If the latter is the case (and it is), we will have to somehow separate the effects that stem from the difference in vehicle attributes, from those that are the result of when the auction took place. If we hypothetically swap around morning and afternoon auctions, i.e. the vehicles on sale, then the effect of the attributes would also swap around, whereas the effect from the relative timing would stay the same. It is the latter that we are after now.

- Finally, we compared the sales performance of the morning and afternoon groups by controlling for the confounding attributes above. We simply split the two groups by those variable(s) into more uniform subgroups, and then performed pairwise comparison between subgroups. For instance, when controlling for external conditions, we formed one subgroup for each of the 6 possible grades a vehicle can obtain in the [NAMA system](http://www.nama-uk.com/grading/what-is-auction-grading), so we could compare vehicles of similar condition together and therefore make sure we eliminate the confounding effects of the vehicle condition.

By performing this more controlled comparison, the performance differences between the morning and afternoon auctions were much less significant and consistent, even after controlling for the vehicle condition only. That gave us a good amount of evidence *for* our initial hypothesis, which was that *most of the advantage of morning/afternoon auctions is a result of different profiles of the vehicles on sale, rather than of the fact that one auction was held before lunch break, while another after.* 

That is not to say that there is no effect of the time of auction whatsoever. Many variables can affect the final sale price, e.g. the mix of vendors and buyers present, time of the year, type of auction (open or closed), order of vehicles, and so on. These are not necessarily distributed evenly across the two groups and hence can obscure the effect we want to estimate, if it indeed exists.

## Next steps

The obvious constraint of this method of controlling for confounders by forming subgroups is that by splitting the dataset into smaller and smaller pieces we quickly reach the point where we compare just one or two vehicles to each other. Such small groups are clearly not enough for robust inferences! Two of the potential solutions for the problem could be:

1. conduct a formal experiment, designed in a way that makes the groups as homogeneous as possible, with sufficient sample size to make robust inference on the effects, or

2. test our theories *indirectly* by asking *"if our assumption was true, what else would be true?"*, and testing auxiliary hypotheses formed this way. For instance, if our theory is that morning auctions should result in higher sale prices, because buyers arrive with a fixed budget and bid more aggressively earlier in the auction, then we can test this by analysing their bidding strategy *within* auctions, regardless of the time it was held. If we find no difference here, it may serve as evidence against our original theory too.

It is an iterative process, but both ways can generate a compelling answer in the end.

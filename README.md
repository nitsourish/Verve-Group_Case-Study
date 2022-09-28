# Verve Group Data Science Case Study

There is a NB verve_challenge.ipynb with supporting analysis of the two Problems with following assumptions

### **Assumptions**

#### a) All the events are associated with one specific Advertiser(or at least same ad subcategory) and Publisher. Else there will be biased comparison amongst different bid price and the associated win rate(some ads are comparative less demanding, so the average highest bid price may lower and vice-versa)
#### b) Based on the input the upper bound amount of advertiser is willing to pay for a win is 0.50
#### c) There is no other experimented bid_price points other than the mentioned in the data
#### d) There might be domain(real time bidding) specific optimization algorithm, however with time constraint a simple analytical solution is proposed

## *Problem 1*

### **Bayes’ Theorem to arrive expected win rate**

<img width="265" alt="image" src="https://user-images.githubusercontent.com/11828049/192799902-035a2b92-4641-441a-a6a1-957128ea8b86.png">
- **Inputs**
###### a) prior- The win rate for each bid price based on the seen experiments
e.g. P(WINbid price = 0.1)
###### b) Likelihood- Probability of observing the bid price conditional on Win events
e.g. P(bid price = 0.1 | WIN)
###### c) Evidence/Marginal - Probability of observing the bid price
e.g. P(bid price = 0.1) = P(WINbid price = 0.1) *P(bid price = 0.1 | WIN) + P(WINbid price = 0.1) *P(bid price = 0.1 | WIN)


- **Output**
###### Posterior Probability - Expected posterior probability/ win rate
e.g. P(WIN | bid price = 0.1 )


**Step by step calculation** 

<img width="578" alt="image" src="https://user-images.githubusercontent.com/11828049/192799690-fa20916b-b387-4879-9e12-1f0a54a89c3b.png">

## *Problem 2*

###### 1st to analyse relationship between price and expected winrate and respective distribution of the events at different price point

<img width="244" alt="image" src="https://user-images.githubusercontent.com/11828049/192800659-1cd32957-32d2-482c-a7aa-67a4e8d9f406.png">

#### A) There is no monotonic relationship between bid price and win rate(amongst the Bid Price points with significant support or no of events)
#### B) Bid Price 0.20 and 0.40 constitutes of > 98% of total bid events. Also there is no experimentation in in other values between 0.20 and 0.40.
#### C) Although for bid price 0.1 there is higher expected win rate than 0.20, there is insignificant support/data points compared to 0.20 or 0.40 bid price.So more experimentation in this price region can provide more confidence in the decisioning process.

#### Analytical Approach to decide optimal bid price

- As there is no clear trend on between bid price and win rate(mostly because of heterogeneity of the no of events at different  bid price/lack of support as discussed above) we can't regress win rate w.r.t. price.Even with only 10 data points the regression fitting won't be a appropriate.

- So can't predict win rate based on a fitted model for other granular bid_price points(e.g. 0.25 or 0.30) other than the points mentioned in the data

- I assume the upper bound amount advertiser is willing to pay is between 0.5-0.75 as beyond 0.75, we have negligible data points(< 0.01% data). Based on the input the upper bound amount advertiser is willing to pay for a win is 0.50. So naturally we can ignore the price points beyond 0.50, i.e. 0.75, 1.00,2.00,5.00 and 9.00, as winning at these price points would incur loss 

- Let's calculate the per bid expected net revenue(Win Rate * (amount advertiser is willing to pay - bid price)) at each of the remaining price point to come up a optimal bid price and rank them.

<img width="683" alt="image" src="https://user-images.githubusercontent.com/11828049/192800939-bd19a3b8-112b-41ed-94f6-1acbf7327760.png">

#### Based on the calculation we may come up with the optimal bid price

- Scenario I: From the calculation 0.10 gives the maximum expected net revenue/per bid and which is disproportionately higher(almost 3 times) than the closest one(here 0.20), which is a stark contrast as lower price has higher prior and expected win rate(may be difference in ad category).However we have insignificant support(Although 10000 in general is a big trial nos for Bernoulli trials, w.r.t. this data the support for this experiment is < 0.1%). So I am sceptical about this value as a concrete price and would require more experiments to have a conclusive answer.May be with more experiments we may have a updated belief(change in distribution between win and loss) about this point. 

- Next to 0.10 We can see the comparatively closer nos between 0.20 and 0.40 and we have maximum support for 0.2(approx 89%) and 0.40 together with 98% data points.So according to law of large numbers(theorem that describes the result of performing the same experiment a large number of times) and the normal distribution assumption (by taking care of granular prices in between) the optimal bid price value should be expectation of the distribution, consisting values between 0.20(with some tolerance) and 0.40(with some tolerance).

- Scenario II: So assuming only this price points 0.20 would be the optimal bid.

- More experiments with granular prices in between 0.10 to 0.40 would be appropriate.With this few price points assumption of monotonic trend between price and expected win rate is inappropriate. However inclusion of more granular price points experiments(with significant no) we may establish some relationship and a predictive methodology. This would help to find out the exact price point by playing between increase in price(decrease in margin) and increase in expected win rate.

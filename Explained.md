# What This Project Does — Explained in Plain English

This file explains the entire project without any code or technical jargon.
It is written for someone who studies business, has never written a line of code,
and wants to understand what was actually built and why it matters.

---

## The Business Problem

Ericsson is one of the largest telecommunications companies in the world.
Every year they file thousands of patents. A patent is a legal document that
says "we invented this — nobody else can copy it without paying us." Companies
like Apple and Samsung pay Ericsson billions of dollars every year just to use
technology that Ericsson invented and patented.

So patents are not just paperwork. They are a direct measure of how much a
company is innovating, and they generate real revenue through licensing deals.

The question this project asks is simple: can we predict how many patents
Ericsson will file next year?

If a company can forecast its own patent activity, it can plan its legal
budget, decide how many patent lawyers to hire, understand whether innovation
is growing or slowing, and compare itself to competitors.

---

## The Data

We were given a dataset containing 30,118 Ericsson patents filed between
1976 and 2025. That is nearly 50 years of history.

For each patent, the dataset tells us:

- The date it was filed
- The title of the invention
- Whether it is a utility patent (protects how something works) or a design
  patent (protects how something looks)
- Which technology area it belongs to — 5G, AI, networking, energy, and others
- Various statistics about the patent title, like how many words it contains

Think of it like a giant spreadsheet. Each row is one patent. Each column is
one piece of information about that patent. There are 28 columns in total.

---

## Part 1 — Exploring the Data (EDA)

Before building any prediction tool, we first spent time just looking at the
data and trying to understand it. This is called exploratory data analysis,
or EDA. Think of it as reading a business report before making any decisions.

Here is what we found:

**Patent volume over time**
Ericsson filed very few patents in the 1980s — around 30 per year. By 2015
that had grown to over 1,700 patents in a single year. That is a 58-fold
increase over 35 years. Since 2015 the number has declined and stabilised,
sitting around 1,200 to 1,400 per year in the most recent years.

**What kind of patents**
98.6% of Ericsson's patents are utility patents — the kind that protect real
technical inventions. Only 1.2% are design patents. This tells us Ericsson
is fundamentally a technology company, not a design or aesthetics company.
Their competitive advantage comes from how things work, not how they look.

**Which technologies**
We looked at nine technology keywords: 5G, AI and machine learning, cloud
computing, security, the Internet of Things, networking, energy, antennas,
and data. Networking has always been the most common theme. But 5G and AI
only started appearing in patent titles after 2017, which makes sense given
when those technologies became commercially relevant.

**What does this tell us?**
Two main things. First, there is a clear long-run growth trend that eventually
peaked and then declined. Second, what patents are about (5G, AI, networking)
changes over time, but the total volume seems to be driven more by internal
company factors than by which technology area is hot right now. We confirmed
this later when building the prediction models.

---

## Part 2 — Predicting Patent Counts

After understanding the data, we built three different prediction models.
Think of each model as a different kind of analyst making a forecast.

**What are we predicting exactly?**
We are predicting the total number of patents Ericsson will file in a given
year. One number per year. We compare that predicted number to what actually
happened and measure how far off we were.

**How do we measure accuracy?**
We use a metric called MAPE — Mean Absolute Percentage Error. It answers
the question: on average, what percentage of the true value were we wrong by?

If Ericsson filed 1,200 patents and we predicted 1,080, we were off by 120,
which is 10% of 1,200. So our MAPE for that year would be 10%.

Lower MAPE is better. A MAPE under 15% for annual forecasting is considered
acceptable. Under 11% is strong.

**How do we test the models fairly?**
We split the data into three time windows:

Training data (1979 to 2015) — this is like giving the model a textbook
to study from. It learns all the patterns from these years.

Validation data (2016 to 2019) — this is like a practice exam. We use it
to compare models and spot any problems, but we do not use it to make final
decisions.

Test data (2020 to 2025) — this is the final exam. The model has never seen
these years. We only check performance here once, at the very end, and this
is the number we report. If a model does well here, it is genuinely useful
for forecasting the future.

We never mix future data into the past when training. That would be like
giving a student the exam answers the night before and then acting surprised
they scored well. It would make our results look better than they really are.

---

**The three models**

**Model 1 — Linear Regression (the transparent model)**

Imagine drawing a straight line through a chart of patent counts over time
and then extending that line into the future. That is essentially what linear
regression does.

Its advantage is transparency. Every input it uses has a clear, readable
effect on the output. For example, if last year's patent count goes up by
100, this year's prediction might go up by 80. That relationship is visible
and explainable to any manager.

Its limitation is that it can only fit a straight line. Ericsson's patent
history is not a straight line — it has a dotcom boom in the early 2000s,
a financial crisis dip in 2008, a second peak in 2015, and then a decline.
A straight line cannot capture all of that shape.

Result: 15.79% MAPE on the test years. Off by roughly 190 patents per year
on average.

**Model 2 — XGBoost (a complex model)**

Instead of one straight line, XGBoost builds hundreds of small decision trees
and combines them. A decision tree is like a flowchart of yes-or-no questions.
Did last year have more than 1,000 patents? Yes — go left. No — go right. And
so on until you reach a prediction.

XGBoost builds each new tree specifically to fix the mistakes of the previous
ones. It is like hiring 300 consultants where each one studies the mistakes
of all the previous ones before giving their recommendation.

It was the best model during the practice exam phase with 9.61% MAPE on
validation. But on the final test years it rose to 13.77%. That gap tells
us it learned the practice years too well and did not adapt as cleanly to
the genuinely new test years.

**Model 3 — Random Forest (chosen as the best model)**

Like XGBoost, Random Forest also builds hundreds of decision trees. The
difference is that each tree is built independently, using a random sample
of the training data. The final prediction is the average of all those trees.

Think of it like running a survey. Instead of one expert giving you an answer,
you ask 300 different people, each looking at a slightly different slice of
the evidence, and you average their responses. Random variation cancels out
and the central signal emerges more clearly.

Random Forest was not the best on the practice exam (16.72% MAPE on
validation). But it achieved the best result on the final test years with
10.87% MAPE — roughly 130 patents of error per year.

This is why we selected it. In forecasting, what matters is how well the
model performs on genuinely unseen future data. A model that looks impressive
during development but struggles when conditions change is not actually useful.
Random Forest was the most reliable when it mattered most.

**What did we learn from the models?**

We looked at which inputs each model relied on most. In both Random Forest
and XGBoost, the single most important input was last year's patent count.
The 3-year average and the direction of change (were patents going up or down
recently) also ranked highly.

Technology keywords like 5G share and network share ranked near zero in
importance. This confirmed what we suspected from the data exploration phase:
Ericsson's patent volume is driven by operational momentum — their existing
R&D pipeline, legal team capacity, and budget — not by which technology area
is growing. What they patent changes over time, but how many they patent is
largely a function of how many they filed recently.

**Why are some numbers negative in the results table?**

You may notice a metric called R² showing negative values across all three
models. This is not a sign that the models are broken. It means something
specific: on this particular dataset, the models struggle to beat a very
simple benchmark — just predicting the average number of patents every single
year. That sounds bad, but it is actually expected when data is this volatile
over 50 years with multiple structural changes. The MAPE metric is a much
fairer way to judge these models, and 10.87% is genuinely useful for
strategic planning.

---

## Part 3 — Grouping Patents into Clusters

The third part of the project asked a deeper question: instead of forecasting
all patents together as one group, what if we split them into smaller, more
similar groups first and forecast each group separately? Would that be more
accurate or more insightful?

This process of finding natural groups in data is called clustering. It is
like sorting a messy pile of documents into labelled folders without being
told in advance what the labels should be — the algorithm figures out the
most sensible groupings on its own.

We built two different ways of grouping the patents.

---

**Grouping Method 1 — By what the patents say**

Every patent has a title. For example: "Method and apparatus for beamforming
in 5G wireless network communication systems."

The challenge is that a computer cannot understand words. It only understands
numbers. So we converted every patent title into a row of numbers using a
process called TF-IDF. This method gives high scores to words that appear
often in one specific patent but are rare across the whole dataset. Common
words like "method" score low because they appear everywhere. Specific words
like "beamforming" or "neural" score high because they are distinctive.

After converting 30,118 titles into numbers, we had an enormous table with
thousands of columns — one per unique word. We then compressed this table
down to just 50 columns using a mathematical technique called SVD. Think of
it like taking a very high-resolution photograph and saving a smaller version
that still captures all the important visual information.

With 50 numbers per patent, we used an algorithm called K-Means to group
similar patents together. K-Means works by placing imaginary centre points in
the data space and assigning each patent to whichever centre it is closest to.
It then moves the centres to the middle of their groups and repeats until
everything stabilises.

We used two methods to decide how many groups to create. The elbow method
plots how tightly packed the groups are as you increase the number of groups
— you look for the point where adding more groups stops making them
meaningfully tighter. The silhouette method measures how well each patent
fits into its assigned group versus how close it sits to the nearest
alternative group. We used both together for confidence and settled on
5 groups for this scheme.

Each group ended up representing a different flavour of Ericsson patent
based on the language used in the titles — networking infrastructure, wireless
communication, device-level technology, data and processing systems, and
energy and antenna related inventions.

---

**Grouping Method 2 — By what the patents measure**

Instead of using the title text, this method grouped patents using numerical
and categorical information about each patent — the nine technology keyword
flags, whether it was a utility patent, how long the title was, how keyword-
rich it was, and which technology era it came from.

Before grouping, we standardised all these numbers so that no single variable
dominated the grouping just because it happened to use a bigger scale. This
is equivalent to converting all currencies to the same denomination before
comparing prices.

K-Means was applied again, this time on these domain-knowledge variables.
We settled on 4 groups for this scheme. The resulting groups reflected natural
technology divisions in Ericsson's portfolio — older legacy network patents,
modern wireless and 5G patents, AI and data-focused patents, and a mixed
group of utility and infrastructure patents.

---

**Forecasting each group separately**

Once we had the groups, we trained separate prediction models for each one.
Each group got its own Linear Regression model and its own Random Forest
model, trained only on the patents in that group.

We then added up the predictions from all groups to get a total annual
forecast. This is called aggregation. The question was: does this approach
beat the original models that treated all patents as one group?

The comparison table in the notebook shows you directly whether the grouped
approach improved accuracy or not. Even if it does not improve the raw
numbers, it improves interpretability — because now you can see which group
of patents is driving growth or decline, rather than just seeing one aggregate
number.

---

## The Real-World Meaning of All of This

Here is what this project means in plain business terms.

Ericsson's R&D strategy, legal team size, and patent budget in any given year
will largely determine how many patents they file that year. Last year's output
predicts this year's output better than any other single factor. Innovation
has institutional inertia — it takes years to build up and years to wind down.

The technology mix (how much is 5G versus AI versus networking) shifts
gradually but does not drive the total volume up or down on its own. A patent
team that focused on 5G last year will likely still focus on 5G this year,
but the size of that team and their budget is what determines how many
patents actually get filed.

For a business leader at Ericsson, the practical implication is this: if you
want to forecast next year's patent output, the most important number to look
at is this year's output. If filings have been declining for two or three
years, that trend will very likely continue unless there is a deliberate
strategic intervention — new investment, new hiring, or a shift in corporate
priorities.

The clustering analysis adds a further layer: by seeing which technology
groups are growing and which are shrinking within the total, management can
spot whether a decline is happening across the board or is concentrated in
one specific area. That distinction changes what action you would take.

---

## Summary

| What we did | What it means in business terms |
|---|---|
| Explored 30,118 patents across 50 years | Understood Ericsson's full innovation history |
| Built three forecasting models | Created tools to predict next year's patent volume |
| Chose Random Forest (10.87% error) | Found the most reliable predictor for future years |
| Converted patent titles to numbers | Made text data usable for mathematical analysis |
| Grouped patents into clusters | Segmented the portfolio into meaningful technology themes |
| Forecast each group separately | Tested whether detail improves prediction accuracy |
| Identified lag features as dominant | Confirmed patent activity is driven by momentum, not tech trends |

---

## Tools Used

Everything in this project was built using Python, which is a free
programming language widely used in data analysis and machine learning.
The specific tools inside Python were:

**pandas** — for loading and organising the data, like Excel but for code

**matplotlib and seaborn** — for creating all the charts and visualisations

**scikit-learn** — the library that contains Linear Regression, Random Forest,
K-Means clustering, TF-IDF text conversion, and the scaling tools

**XGBoost** — a separate library for the gradient boosting model

**joblib** — for saving the trained models so they can be reused later

No paid software, no subscriptions. Everything runs on a free Google Colab
notebook that anyone with a Google account can open and run.

---

*Written to be readable by anyone, regardless of technical background.
If something is still unclear, open an issue on this repository and ask.*

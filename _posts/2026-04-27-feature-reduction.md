---
layout: distill
title: The effect of feature resolution on embedding dimension

description: High-dimensional data can be compressed into lower-dimensional embeddings while retaining a relatively large amount of relevant information, a phenomenon which, despite its widespread use, we struggle to fully explain. In this post, we use a common property of datasets - a limit on the number of features per data point - to show how a slight uniform dependence between features can be exploited to reduce the required dimensions by at least a third, while sacrificing no information about the features. To do so, we introduce the concepts of dataset resolution and feature composition of a dataset, and analyse how a set of orderings of the dataset affect the types of partitions we can create of the dataset.

date: 2026-04-27
future: true
htmlwidgets: true
hidden: true

mermaid:
  enabled: true
  zoomable: true

authors:
  - name: Anonymous

bibliography: 2026-04-27-feature-reduction.bib

toc:
  - name: Introduction
  - name: What is the feature composition of a dataset?
  - name: Dataset resolution and embedding dimension
    subsections:
      - name: Intuition
      - name: Intuition for dimensions four and above
  - name: Conclusion

_styles: >
  .proof-block {
    border: 3px solid rgba(114, 114, 114, 0.76);
    box-shadow: 4 4px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
    margin-top: 12px;
  }
---

## Introduction

An interesting paper was published in August 2025: “On the Theoretical Limitations of Embedding-Based Retrieval” <d-cite key="weller2025"></d-cite>. In the paper, the authors identify a use case for which embeddings seem to be limited by their dimensionality: identifying individual words in a corpus of documents.

A student of algebra might at this point note that you need $n$ independent dimensions to express $n$ independent variables - so if the words in a document are independent, you would need to embed every document with a dimensionality the size of the vocabulary. But the authors of “On the Theoretical Limitations” identified something interesting: space can be used far more efficiently if each of your documents have a certain number of features, or words, which is smaller than the size of the vocabulary. As it turns out, this limit introduces a uniform dependence between words in a document.

Which inspired the question: how does that actually affect the number of dimensions you need to represent a set of documents, while preserving the ability to identify individual words?

The following post uses that question as a loose cloak to explore the effects of the feature composition of a dataset on how it can be embedded.

## What is the feature composition of a dataset?

A feature of a data point is a property of the data point which can be used to describe it. When the data points in a dataset are embedded, usually their embeddings share a set of features, specifically so that the data point embeddings can be compared. However, in the original dataset, data points might have been represented using vastly different features.

For example, a hypotenuse is a property of a right-angled triangle. But in a dataset of shapes, what is the hypotenuse of a circle? The question doesn’t make sense, because the feature does not exist in the "circle" data point.

So if we want to talk about features which are common to all data points in a dataset, we need to be able to handle the case where the feature does not exist. Luckily, we can do this by defining how we handle nonexistence of a feature in a data point:

<div class="proof-block l-body-outset">
<p style="margin-left: 15px; margin-right: 15px">
<em>Definition</em>:  <b>Feature of a dataset</b>
<br>
Given a dataset $D = {d_0, \ldots , d_n}$, with $d_i$ one data point in $D$, suppose there exists a function $g$ from $D_g \subseteq D$ to a set of values $v_g$. Then we can define the feature associated with $D$ and $g$, $f$, as a function:

$$
f(d_i) =
\begin{cases}
g(d_i) & \text{ if } d_i \in D_g \\
x & \text{ if } d_i \not \in D_g
\end{cases}
$$

where $ x \not \in v_g$.

</p>
</div>

In other words, a feature of a dataset is a function on the dataset which maps it to a column with exactly one entry for each row of the dataset, with one entry value specially reserved to indicate nonexistence.

Since $f$ is a function, it can only map a data point to one output. This makes sense - for example, if a dataset of creatures has one creature per data point, then "number of eyes of a creature" will always give you one number (even if that number is zero). However, as soon as the dataset has more than one creature for a data point, the "number of eyes of a creature" for a data point is unclear; does a picture of a spider and a horse have eight "eyes of a creature", or two, or both?

But this definition of a feature does not only _limit_, it also _allows_ - if you want to apply your feature to a new data point, or rather to a new case, you can add to your set of feature values, with the caveat that the value you add cannot be the same as any one used to represent feature nonexistence.

Now that we have a definition for a feature with respect to a dataset, we can talk about the feature composition of a dataset.

Let’s go back to our question of “how many words can you represent in x dimensions given a corpus of documents?”. We can treat word existence as a feature of the corpus, or dataset, for each word in the dataset. Then each document can be represented by indicating existence or nonexistence for each word in the vocabulary, and we would care about the different combinations of words which occur.

**Feature composition** is concerned with the unique combinations of feature existences in a dataset. One possible feature composition of a dataset with features `dog`, `cat` `horse`, and `fish` looks as follows:

```markdown
{}
{dog}, {cat}, {horse}, {fish}
{dog, cat}, {cat, horse}, {dog, horse}, {dog, fish}, {cat, fish}, {horse, fish}
{dog, cat, horse}, {cat, horse, fish}, {dog, horse, fish}, {dog, cat, fish}
{dog, horse, cat, fish}
```

We can abstract this to general features:

$$\{ \}$$

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations-compact-free.svg" class="img-fluid" %}

If every possible combination of features exists in such a dataset, we shall say that the dataset is **full** with respect to its features. If a dataset is full, and balanced with respect to the combinations, then the existence of any one feature in a document tells us nothing about whether another feature exists in the document. The feature existences are then fully independent - and you would always need $n$ features to embed the data points in your dataset if your dataset contains $n$ unique words.

However, in most cases these combinations do not have an even spread in the dataset. To simplify the problem, we differentiate only between combinations which do and do not appear in the dataset. For example, the combination of “green, orange and pink” might never appear in the document, and so the feature composition of the dataset would look as follows:

$$\{ \}$$

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0 text-center">
        {% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations-minus-one.svg" class="img-fluid" style="max-width: 10%; height: auto;" %}
    </div>
</div>

In general, $n$ features give rise to $\sum_{i=0}^{n} \binom{n}{i}$ combinations. The binomial theorem says $\sum_{i=0}^{n} \binom{n}{i} = 2^n$, and indeed, there is a natural way to map the existence of features to binary representations:

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations-table-boolean-final.svg" class="img-fluid" %}

With the above binary values, we can order the dataset in at least four unique ways, once for each feature:

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations-ordered-by-features-ld.svg" class="img-fluid" %}

We can also create four partitions of the dataset, each based on whether a certain feature exists in a combination:

[do four separate partitions first, then all four at the same time]

$$\{ \}$$

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations-partitioned-ld.svg" class="img-fluid" %}

Partitions quickly become messy to visualise, though. Expressing all possible combinations by partitions requires drawing a venn diagram, which becomes difficult when we want to consider more than three features.

We can also view the structural information of feature composition through the lens of set inclusion, which can be naturally expressed through a _partial_ order. We define a partial order on the combinations: for combinations $x$ and $y$, let $x \leq y$ ifand only if $x \subseteq y$. So if combination $y$ contains every feature in combination $x$, then $x \leq y$. A set $S$ with a partial order $\leq_P$ on it is called a poset (which we will often call $P$). We can represent our poset as a directed graph where edges connect related combinations, pointing from the lesser to the greater element. The graph below shows this structure without indicating direction:

<div class="l-page-outset">
  <iframe src="{{ 'assets/html/2026-04-27-feature-reduction/kinetic-graph-hierarchical (6).html' | relative_url }}" frameborder='0' scrolling='no' height="750px" width="100%"></iframe>
</div>

This graph describes the specific poset which is a Boolean lattice, or Boolean algebra - specifically $\mathcal{B}_4$. <d-footnote>It is also one of the most-used examples of a lattice since it can be used to relate the elements of the power set of a set, which is a fundamental concept in set theory. Not all of the graphs we will draw are lattices, but this one is.</d-footnote>

Our original question only requires identifying individual features within data points that contain a specific number of features. We therefore only need to preserve the parts of the partial order which relate the first level to one other level of the combinations. For a poset $P = (S,\leq_P)$ if we select a subset $U$ of $S$ and maintain the relation $\leq_P$ between the elements of the subset, then we call it a **suborder**. Our special poset where we select two layers of the Boolean lattice and preserve the partial order between their elements has a name: for $0 \leq s < t \leq n$, the poset which is a suborder of the Boolean lattice $\mathcal{B}_n$ and selects the layers $s$ and $t$ of $\mathcal{B}_n$ is denoted by $\mathcal{B}_n(s,t)$. So if you toggle off the third and fourth layers of the diagram above, you get $\mathcal{B}_4(1,2)$.

We now consider one last formulation of our problem. To do so, we map from our poset to a set of linear orders in the following way:

Given a poset $P$ with a partial order $\leq_P$ on a set of combinations $S$, we create a set of linear orders on $S$, $\mathcal{L} = \{L_1, \ldots , L_n\}$ - in simpler terms, we create a set of rankings of the combinations where no two combinations get the same rank in any one ranking. The set of rankings has the property that for every two combinations $x$ and $y$, $x < y$ in $P$ if and only if in every ranking, $x < y$. The set $\mathcal{L}$ is called a realizer of $P$<d-cite key="dushnik1941"></d-cite>. This property automatically means that if $x$ is not comparable to $y$ in $P$ then $x>y$ in at least one of the rankings.

For example, since `{horse,cat}` and `{dog,fish}` share no features, they cannot be compared by our order, and so we have neither `{horse,cat}` $\leq$ `{dog,fish}`, nor `{horse,cat}` $\geq$ `{dog,fish}`. We therefore need to have at least one ranking where `{horse,cat}` $>$ `{dog,fish}` and at least one ranking where `{horse,cat}` $<$ `{dog,fish}`. Since you cannot rank an item as both greater and lesser than another item in one linear order, you need at least two rankings to realise this.

A realizer for this poset might look like:

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations_two.svg" class="img-fluid" %}

Since $a$ is not comparable to $b$, we use the two linear orders $L_1$ and $L_2$ to fulfil the required property that $a<b$ in one order and $a>b$ in one order.

This may seem like an unnecessarily complicated way to view our problem, but it links us back to dimensionality: encoding all points from a dataset in $n$ dimensions yields precisely $n$ independent linear orders of the dataset. So if we can express $P$ through $n$ linear orders, then we can encode $P$ in $n$ dimensions. In fact, the minimal size of the set of rankings required to preserve the information in $P$ is called the classical or Dushnik-Miller **dimension** of the poset <d-footnote>There are other types of dimensions of posets, see <d-cite key="barreracruz2020"></d-cite> for a nice comparison.</d-footnote>. We shall simply call it the dimension of the poset, and denote it by $\text{dim}(s,t;n)$.

To summarize, the feature composition of a dataset is the set of feature combinations appearing in that dataset, which encodes both the number of unique points and their structural relationships. If we want to identify unique words in a dataset, we will need to represent all the combinations of words that the corpus contains, and we will need to preserve some of the structure of those combinations.

## Dataset resolution and embedding dimension

Remember our question from the start: when each document in a corpus contains a certain number of features which is smaller than the total number of features in the dataset, how does that affect the number of dimensions we need to express every feature in each document?

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/feature_subsets_full.svg" class="img-fluid" %}

Let’s call the number of features any data point in the dataset may contain **dataset resolution**, and denote it by $k$. Then if a set of $n$ features describes a dataset and the dataset has resolution $k$ with respect to those features, exactly $k$ of them exist in any given data point.

We rarely have a perfect set of $n$ features for which any data point contains exactly $k$ of them. However, it is reasonable to assume that for many datasets, the data points contain similar amounts of the features we might care about, since data points are often captured in a similar way and standardised in terms of vector size.

Real-world datasets often have an upper limit to their resolution (in other words, $k$ is often smaller than $n$). For example, a dataset of images of animals almost never has every animal type in one image. There are practical reasons for why it would be difficult to get all the animals in one image, especially without eating each other, but more importantly you would need an enormously detailed image to actually capture each animal so that its distinguishing properties are recognisable.

For text, a similar principle applies: you would have to write an enormously long piece of text to use every word in existence in the appropriate context, and almost certainly no piece of text that we might care about has every word in it.

Dataset resolution is defined with respect to a set of features, by necessity. For example, the pixel resolution of a dataset of images of animals could be 28x28, but the RGB resolution would be 28x28x3, and the animal resolution could be two (if we always have two animals appear in each image).

A full four-feature dataset with resolution two has data points which contain two of four features. Suppose we want to be able to identify existence of each feature in a (full) dataset with resolution $k$. Then preserving the following combinations and partial order will ensure that the preserve enough information to identify features:

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations_with_arrows.svg" class="img-fluid" %}

This is $\mathcal{B}_4(1,2)$, which we have previously seen. The feature structure represented in such a dataset will need to use at most $n$ partitions of the dataset, and at least $k$ partitions.

We can now begin to answer the question of how many dimensions we would need to encode such a dataset.

### Intuition

Perhaps you would expect to be able to reduce features the most when a dataset's resolution is small. Suppose you only have two features in any data point in your dataset. Let's consider what happens in the smallest case, where there are three features in your dataset, but every data point has two of the three features.

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/combinations_three_middle.svg" class="img-fluid" %}
We can build intuition by examining this problem in a vector space. Suppose you need to embed these three features in two dimensions, such that you can retrieve existence of any single feature with cosine similarity. What this really means is that when you apply cosine similarity between each data point embedding and a feature embedding, then every data point which contains the feature should have a higher similarity than any data point which does not. This means that the order that the cosine similarity imposes on the data points should allow you to define a threshold such that everything above the threshold certainly contains the feature.

<div class="l-page-outset">
  <iframe src="{{ 'assets/html/2026-04-27-feature-reduction/vector_diagram.html' | relative_url }}" frameborder='0' scrolling='no' height="750px" width="100%"></iframe>
</div>

Three features can be embedded this way without too much complication. Take a moment to convince yourself that when green and pink are embedded on the axes, and the combinations are defined to be halfway between the base features, that orange needs to be embedded within the grey area.

But what is the grey area? It is precisely the area that indicates _not_ green and _not_ pink. We have taken advantage of the fact that orange will never appear at the same time as green and pink, which means we never need to distinguish all the features of a data point which contains green _and_ pink _and_ orange.

<div class="l-page-outset">
  <iframe src="{{ 'assets/html/2026-04-27-feature-reduction/vector_diagram_all_interactive.html' | relative_url }}" frameborder='0' scrolling='no' height="750px" width="100%"></iframe>
</div>

There is one more thing to note here: recall when we said that the dependence that dataset resolution creates is uniform? We would then expect that an efficient embedding would treat each feature the same. Indeed, if you space the base features evenly in the figure, that admits a valid embedding (and one could convince onesself that it is the configuration furthest from being an invalid embedding).

Let us move to three dimensions. Trivially, you could add one more feature, since an extra dimension is available.

<div class="l-page-outset">
  <iframe src="{{ 'assets/html/2026-04-27-feature-reduction/vector_diagram_3d_interactive_sphere.html' | relative_url }}" frameborder='0' scrolling='no' height="750px" width="100%"></iframe>
</div>

In dimensions higher than four, it becomes tricky to approach this problem visually. We already have some intuition, though, around the geometry of the problem: it is likely that if the individual features are embedded evenly with respect to the dimensions in which they are allowed freedom, and the number of features is maximal, that will admit a valid (and optimal with respect to the features) embedding.

The intuitions we have gained around uniformity and embedding in "not"-space have interesting connections to contrastive learning:

- First, contrastive learning optimises for alignment and uniformity <d-cite key="wang2020"></d-cite>, consistent with our intuition that uniformly arranging the base features of a dataset in a space will admit an optimal solution, if the number of features of a dataset is larger than its resolution.
- Second, we have seen that it can be efficient to utilise the "not"-space of more than one embedding at a time. Many popular implementations of contrastive learning use InfoNCE-style loss <d-cite key="oord2018"></d-cite> with cosine similarity. This loss pushes positive samples away from multiple negative samples at once. "Away" in terms of cosine similarity means "on the opposite side of the hypersphere", so the loss pushes the positive into the combined "not"-space of the negative samples in question. Put slightly differently, the loss correlates a positive with the negative of a combination of multiple points which do not relate to it. This is exactly how we embedded extra features into the space above.

### Intuition for dimensions four and above

In order to express our problem in higher dimensions, we turn to the constructions of order theory - in particular, the dimension of a suborder of a Boolean lattice, as addressed previously.

First, let us see what happens when we try to find a two-dimensional realiser of $B_3(1,2)$.

<div class="proof-block l-body-outset">
<p style="margin-left: 15px; margin-right: 15px">
Let us call our features $a$, $b$ and $c$. Then our combinations are $a$, $b$, $c$, $ab$, $cb$ and $ac$. We are allowed two linear orders on the combinations, and since every combination needs to be incomparable to exactly three other combinations, no single combination may be in the bottom three in both orders. Features $a$, $b$ and $c$ need by definition to be below at least two combinations in both orders, so their highest possible ranking is third. However, since there are only two linear orders, there are only two spots that are below third but above the bottom three. Therefore, one of $a$, $b$ and $c$ will need to be in the bottom three for both orders.

{% include figure.liquid path="assets/img/2026-04-27-feature-reduction/linear_order_elements.svg" class="img-fluid" %}

We have reached a contradiction. It is therefore not possible to have a two-dimensional realiser for layers 1 and 2 of the Boolean lattice $\mathcal{B}_3$.

</p>
</div>

Now that is disappointing - we found an embedding which used only two dimensions to represent two-combinations of three features, so we might have expected that the dimension of $\mathcal{B}_3(1,2)$ would have been three.

The poset $\mathcal{B}_n(s,t)$ contains more information than our other problem formulations, and more information than we care to preserve. For example, the order information in $\mathcal{B}_n(s,t)$ is directional, whereas a feature existence does not need a direction. This means that the dimension of $\mathcal{B}_n(s,t)$ provides an upper bound for our problem, but it is not tight even in the smallest case.

So what now? Well, since our nicely defined link to dimension did not work, let us look at how our sucessful embedding used the two axes available to order the 2-combinations of three features:

<div class="l-inline-block">
  <iframe src="{{ 'assets/html/2026-04-27-feature-reduction/vector_diagram_labeled.html' | relative_url }}" frameborder='0' scrolling='no' height="750px" width="100%"></iframe>
</div>

$$
\text{Ordered by x: }
\begin{bmatrix}
a \\
ab \\
ac \\
b \\
c \\
bc
\end{bmatrix}
\qquad
\text{Ordered by y: }
\begin{bmatrix}
b\\
ab \\
bc\\
a \\
c \\
ca
\end{bmatrix}
$$

So the features on the axes, $a$ and $b$, were ordered such that every combination which contains them appeared first when ordering according to the axis on which they are embedded. This is not surprising.

The orders naturally rank the combinations, which can give the following embedding into $x$ and $y$:

$$
\left[
\begin{array}{c | cc}
   & x & y \\
  \hline
  a & 6 & 3 \\
  ab & 5 & 5\\
  ac & 4 & 1 \\
  b & 3 & 6\\
  c & 2 & 2 \\
  bc & 1 & 4
\end{array}
\right]
$$

Note the actual values of the rankings do not matter so much as the order which they preserve.

Let us retrieve each of the features using a similarity function $s$. If we do suppose the values are what is indicated above, we could retrieve "$a$ is in this item" by applying to each embedding the condition $x>3$. Alternatively, we could multiply the rows are follows:

$s = \frac{1}{4}x+0y$

and say that $a$ is in the combination if $s\geq 1$. We could do the same for $b$, with $x$ and $y$ swapped in $s$.

Observe carefully how $c$ was ordered. We cannot isolate $c$ in any one order as we could with $a$ and $b$. However, we can use our earlier retrieval method to recover the items containing $c$:

$$
s = -x-y: \qquad
\left[
\begin{array}{c | cc | c}
   & x & y & s\\
  \hline
  a & 6 & 3 & -9 \\
  a,b & 5 & 5 & -10\\
  a,c & 4 & 1 & -5 \\
  b & 3 & 6 & -9\\
  c & 2 & 2 & -4 \\
  b,c & 1 & 4 & -5
\end{array}
\right]
$$

Then we can say that a combination contains $c$ if $s>-9$. Although our threshold is slightly different, we are still able to recover all the items in $c$. It is important to note that we had to use negative coefficients to do so if we wanted a high similarity to mean the combination contains $c$, as opposed to a low value; remember we embedded $c$ in the "not"-space of the other two features, so it makes sense that we had to flip the orders.

Let us now consider again the values for $x$ and $y$ which our embedding gave.

$$
\left[
\begin{array}{c|cc}
\text{} & x & y \\
\hline
a & 1 & 0 \\
b & 0 & 1 \\
c & -\frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \\
a,b & \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
a,c & 0.38 & -0.92 \\
b,c & -0.92 & 0.38 \\
\end{array}
\right]
$$

Recall we used cosine similarity as our similarity function in the previous section. Where one example ago we _chose_ coefficients for each dimension, now with cosine similarity our coefficients are chosen to be _the values in the order themselves_. With cosine similarity, we also normalise the result. However, since we embed on the unit circle, this scales the similarity by the same amount for each vector and does not affect the final order.

So the coefficients of the linear operations which allow us to retrieve $a$, $b$ and $c$ are the $x$ and $y$ values for $a$, $b$ and $c$ themselves! Note that while these values do allow us to retrieve $a$, $b$ and $c$, there are many other coefficients which could have done the job.

However, another nice property that we get from using cosine similarity in this case, is that a similarity of more than zero indicates that the feature exists. It is convenient to have a common threshold for all features, and for the threshold to relate to sign.

Let us go back to considering how $c$ is ranked. What allowed us to isolate it?

Notice that the combinations of $c$ are grouped as closely as possible to the lower end of the rankings, without making $a$ and $b$ irretrievable. In order to make up for the high position of $a,c$ in $x$, we have to ensure that the value of $a,c$ in $x$ can be cancelled out by the value of $a,c$ in $y$. In fact, $a,c$ in $y$ ranks the lowest, and so we are able on average to bring it below $b$, which ranks third and sixth.

<div class="l-inline">
  {% include figure.liquid path="assets/img/2026-04-27-feature-reduction/ordered_elements_highlight_cb.svg" class="img-fluid" %}

</div>

We could not have done this if there was any combination containing $c$ which was always above $b$. And so if we want to (retrievably-via-cosine-similarity) embed a third feature in the lower ranks of a set of linear orders, then there cannot be a combination not containing that feature which always ranks below a specific combination containing that feature. No amount of scaling can change that order.

This view of our problem is far more similar to the partitions we showed in the second section, but it still requires that we consider order. Specifically, we are finding a set of orders on the dataset for which we can recover each of the feature partitions by an order-respecting partition of the values given by a linear operation on the orders.

That's quite a mouthful. For now, we restrict ourselves to asking: can we embed three features in every two dimensions of an embedding, in the way we just did? In other words, if I have six dimensions, can I embed one feature in the "not"-space of each pair of independent dimensions, allowing me to embed nine features into six?

Maybe you are convinced we can, since the added features are orthogonal to all features in the pre-existing dimensions. We cna prove this by induction:

<div class="proof-block l-body-outset">
<p style="margin-left: 15px; margin-right: 15px">
  We already know that in the case that we have two dimensions, we can embed three features in the following way:

$$
\left[
\begin{array}{c|cc}
\text{} & x & y \\
\hline
a & 1 & 0 \\
b & 0 & 1 \\
c & -\frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \\
a,b & \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
a,c & 0.38 & -0.92 \\
b,c & -0.92 & 0.38 \\
\end{array}
\right]
$$

Then we query for a vector using cosine similarity, which is just the normalised dot product of the query vector and embedding vector. Note that the query for $a$ and $b$ each select one column only. Without loss of generality, since all the strictly positive values in the first column contain $a$, we can identify $a$ as anything which has a positive cosine similarity with $a$. To select $c$, since the two columns selected by $c$'s query vector are evenly weighted, we can compare directly the values in each column. The cosine similarity between $c$ and any document containing $c$ will be positive, and every other document will have a zero or negative cosine similarity to the embedding of $c$ (you can check that - we only require that the sum of the two columns selected by $c$ is negative). We can therefore isolate $c$.
<br>
<br>
Now suppose that you have a set of $2m$ dimensions, consisting of triples of features embedded in pairs of dimensions in this way. We add two more dimensions, $d_1$ and $d_2$ containing three features $a$, $b$, and $c$, where the features and the combinations they produce are embedded as before. Firstly, note that the combination between $a$, $b$ or $c$ and any feature outside of those three will only result in a scaling of the values, since dimensions $d_1$ and $d_2$ are orthogonal in features $a$, $b$ and $c$ to all other dimensions.
<br>
<br>
Now we can use the fact that any query for one feature will select at most two dimensions, and the rows in those dimensions will be scaled copies of the rows in the matrix above.
<br>
<br>
Without loss of generality, let us consider a query for $a$. Then one column is selected. We want to ensure that every combination which contains $a$ and $d$ such that $d$ is not the $b$ and $c$ associated with $a$, will result in a positive value in the dimension selected by query $a$. Since any such feature will have a zero value in column $a$, the value in column $a$ will only be scaled, and will therefore be positive. The only other feature which could affect a value in column $a$ is $c$, but $c$ is negative, and will only be scaled in $a$ by any feature other than $a$, and will therefore never produce a negative combination. So we can still isolate $a$.
<br>
<br>
Without loss of generality, let us consider a query for $c$. Then two columns are selected, with an associated $a$ and $b$ column. Suppose this $c$ is combined with another feature $d \not = a$ or $b$. Then, again, the values in $c$ will only be scaled, and as before they will be scaled proportionally. So the sum of the two columns containing $c$ will be negative if and only if $c$ is in the combination. We can therefore say that a combination contains $c$ if and only if it has a positive cosine similarity to $c$, and so we can isolate $c$.
<br>
<br>
So all of the combinations added when we add features embedded in the target way preserve the fact that a combination has a positive cosine similarity with a feature if and only if it contains that feature. We have therefore shown that it is possible to embed $3m$ features in $2m$ dimensions such that existence of each feature can be identified in any 2-combination of features.

</p>
</div>

Now that we know this, what does it mean? Well, it means we can upper bound the number of dimensions needed to express the existence of two features in a data point given that there are $n$ features in the dataset, and we have a recipe for constructing such an embedding.

In particular, if there are $n$ words and each document contains exactly $2$ of them, we never need more than $\frac{2n}{3}$ dimensions to embed every document losslessly.

Two questions arise from this:

1. What can we say when the resolution is higher than two?
2. Can we improve on this upper bound?

For the first question, we conjecture that for our method of construction, you need to embed a dependent feature in the "not"-space of at least $k$ independent dimensions if your dataset resolution is $k$. We leave it to future work to confirm or deny the conjecture.

The answer to the second question, to the best of our knowledge, is maybe.

## Conclusion

We have picked, from a continuous, roiling sea of data points, a discrete model which provides limits for the dimensionality needed to express a set of data points. While the cases we address using this model occur often in real-world datasets and theoretically limit dimensionality, we often expect exploitable dependencies in the continuous aspects of data that allow further dimensionality reduction (such as through dataset balance and continuous feature value correlations). We may therefore never really hit the bounds applied by the discrete properties of the dataset. It just so happens that identifying words in text is such a discrete and low-resolution goal that the limits the requirements pose actually reach the range of embedding dimensions that are used in practice.
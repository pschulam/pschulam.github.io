---
layout: post
published: false
---

I recently read [Measuring Data](https://arxiv.org/abs/2212.05129), a paper by Margaret Mitchell and her colleagues at Hugging Face. They're motivated by the recent trend of using massive amounts of unvetted data to train models. To train large language models, for instance, we are pulling text from all over the web and have very little understanding of what's in the corpus we build. This makes it difficult to understand whether training data is related to a particular model behavior. Without such an understanding, we can't say much about how to gather data in the future that will make good behavior more likely and defects less likely.

As a solution, they propose "data measurement", which boils down to designing functions that map datasets to one or more numbers. I loved the paper, but it didn't say much about how we should go about building useful data measures.

I think we can learn a lot about what the practice of data measurement might look like in machine learning by looking at how measurement is handled in the social sciences. Measurement theory addresses the challenges faced when we want to measure things like a person's abilities, attitudes, personality traits, and so on. These properties are not directly observable, just as we can't directly observe properties of datasets (we can observe individual items in the dataset, but it's very difficult to spot general trends/patterns in datasets without tools).

In measurement theory, we start by designing a set of *items*, which is a task or question that's intended to illicit behavior related to the thing we want to measure. Researchers design a set of items and compile them into a *test*. Once we have a test, there are important questions about it that we need to answer. First, is the test reliable? Second, is the test valid? These two questions are closely related to the concepts of variance and bias in statistics. A reliable test is robust to error and provides a "clean" measurement. A valid test measures the "right thing".

Norm versus criterion referencing

Hand, Statistics and the theory of measurement, 1996

<!-- This paper was an exciting read, and I'm still chewing on it. It left me wondering what measurement research looks like in other fields, and I want to use this post to explore that question. Mitchell et al. spend a lot of time drawing analogies to measures in the "physical sciences", but I didn't these were helpful to understand how we would develop a theory of measurement for data in machine learning. My hunch is that we may find more insight studying measurement in the social sciences, where (I would imagine) it's much more difficult to validate tests that connect the real world to theories. In some sense, developing tests that "probe" a person for certain characteristics might face the same challenges that we would encounter if we wanted to "probe" a dataset.

As a starting point, I'm reading Bandalos' "Measurement Theory and Applications for the Social Sciences".  -->
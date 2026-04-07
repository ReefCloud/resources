# ReefCloud feature vector model update analysis
Nader Boutros

- [Introduction](#introduction)
- [Dataset](#dataset)
- [Tests](#tests)
- [Results](#results)
  - [Test 1 - Random 80/20 split of all
    data](#test-1---random-8020-split-of-all-data)
  - [Test 2 - Train on year 1, test on year
    2](#test-2---train-on-year-1-test-on-year-2)
  - [Test 3 - Train on year 1 and 30% of year 2, test on remaining 70%
    of year
    2](#test-3---train-on-year-1-and-30-of-year-2-test-on-remaining-70-of-year-2)
- [Summary and recommendations](#summary-and-recommendations)
- [What this means for ReefCloud
  users](#what-this-means-for-reefcloud-users)

## Introduction

In ReefCloud, the region around the image points (we call this a patch)
is characterised by a backend model as a feature vector. These feature
vectors are then used in combination with the users’ manual annotations
to train each project’s classifier model. This is an integral part of
the ReefCloud machinery that users do not interact with, except by
choosing the number of points on each image, which determines which
image patches are used.

At AIMS, the Data and Digital Engineering team have identified that the
packages/environments used in the feature vector extraction process have
reached - or are about to reach - end of life. As such they have updated
the pipeline resulting in the necessary security updates as well as some
performance improvements.

However, the changes have resulted in a change in the outputs of the
feature vector extractor model. In this report we will train models
using combinations of the outputs of the existing and updated pipelines
and compare the results to help us plan the rollout of the new pipeline
and required communications with users.

------------------------------------------------------------------------

## Dataset

The data used in this analysis has been taken from the AIMS LTMP
ReefCloud project. DDE team have extracted the human classified points,
and regenerated the feature vectors using the updated pipeline. We will
use data from 2023 and 2025 in order to analyse the performance and to
simulate the rollout of the new pipeline.

------------------------------------------------------------------------

## Tests

We will perform three tests to compare the performance of the models
trained on the different feature vector outputs. Each test will include
a baseline performance estimate using vectors from the existing pipeline
to both train and test the models.

- **Test 1** will use all the data (2023 and 2025) to train and test the
  models, using a random 80/20 split.
- **Test 2** will use the 2023 data to train and 2025 data to test
- **Test 3** will demonstrate the effects of combining the data from
  both years to train the models, as what may occur existing multi-year
  projects after the transition to the new pipeline. To do so we will
  train models on all the 2023 data and 30% of the 2025 data

<div id="tbl-model">

Table 1: Table 1: Summary of the models trained in each test. In each of
the three tests, model a is the baseline which the new models (b and c)
will be compared against.

| **Model** | **Train data** | **Test Data** |
|----|----|----|
| 1a | Random 80% of all data (old vectors) | Remaining 20% of all data (old vectors) |
| 1b | Random 80% of all data (new vectors) | Remaining 20% of all data (new vectors) |
| 1c | Random 80% of all data (old vectors) | Remaining 20% of all data (new vectors) |
| 2a | Year 1 data (old vectors) | Year 2 data (old vectors) |
| 2b | Year 1 data (new vectors) | Year 2 data (new vectors) |
| 2c | Year 1 data (old vectors) | Year 2 data (new vectors) |
| 3a | Year 1 data (old vectors) + 30% Year 2 (old vectors) | Remaining 70% Year 2 (old vectors) |
| 3b | Year 1 data (old vectors) + 30% Year 2 (old vectors) | Remaining 70% Year 2 (new vectors) |
| 3c | Year 1 data (old vectors) + 30% Year 2 (new vectors) | Remaining 70% Year 2 (new vectors) |

</div>

## Results

### Test 1 - Random 80/20 split of all data

To demonstrate the baseline performance, we’ll first train a model on
80% of the data using the existing feature vectors, and test on the
remaining 20% again using the existing feature vectors. This is model 1a
in <a href="#tbl-model" class="quarto-xref">Table 1</a>. *Note, in all
plots classes containing fewer than 50 examples in the test set are not
shown*

<div id="fig-1-baseline">

<div class="cell-output cell-output-display" execution_count="14">

<div id="fig-1-baseline-1">

<img src="report_files/figure-commonmark/fig-1-baseline-output-1.png"
data-ref-parent="fig-1-baseline" />

(a) Overall and classwise F1 scores for model 1a trained and tested on
old vectors

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-1-baseline-2">

<img src="report_files/figure-commonmark/fig-1-baseline-output-2.png"
id="fig-1-baseline-2" data-ref-parent="fig-1-baseline" />

(b)

</div>

</div>

Figure 1

</div>

------------------------------------------------------------------------

Next, we’ll train a model on the same 80% of the data but using the new
feature vectors, and test on the same 20% but also using the new feature
vectors. This is model 1b in
<a href="#tbl-model" class="quarto-xref">Table 1</a>. We’ll then compare
the F1 scores against those shown in the baseline model
(<a href="#fig-1-baseline" class="quarto-xref">Figure 1</a>). The plot
below shows the difference between the F1 scores of this model and the
baseline model, where a positive value indicates a higher F1 score and a
negative value a lower one.

<div id="fig-1-new-new">

<div class="cell-output cell-output-display" execution_count="15">

<div id="fig-1-new-new-1">

<img src="report_files/figure-commonmark/fig-1-new-new-output-1.png"
data-ref-parent="fig-1-new-new" />

(a) Difference in overall and classwise F1 scores between model 1b
(trained and tested on new vectors) and the baseline model 1a (trained
and tested on old vectors).

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-1-new-new-2">

<img src="report_files/figure-commonmark/fig-1-new-new-output-2.png"
id="fig-1-new-new-2" data-ref-parent="fig-1-new-new" />

(b)

</div>

</div>

Figure 2

</div>

------------------------------------------------------------------------

Finally, we’ll check the performance when train on the old vectors and
test on the new vectors (model 1c in
<a href="#tbl-model" class="quarto-xref">Table 1</a>). Again, we’ll
compare the F1 scores against those shown in the baseline model
(<a href="#fig-1-baseline" class="quarto-xref">Figure 1</a>).

<div id="fig-1-old-new">

<div class="cell-output cell-output-display" execution_count="16">

<div id="fig-1-old-new-1">

<img src="report_files/figure-commonmark/fig-1-old-new-output-1.png"
data-ref-parent="fig-1-old-new" />

(a) Difference in overall and classwise F1 scores between model 1c
(trained on old vectors and tested on new vectors) and the baseline
model 1a (trained and tested on old vectors).

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-1-old-new-2">

<img src="report_files/figure-commonmark/fig-1-old-new-output-2.png"
id="fig-1-old-new-2" data-ref-parent="fig-1-old-new" />

(b)

</div>

</div>

Figure 3

</div>

------------------------------------------------------------------------

### Test 2 - Train on year 1, test on year 2

This test will look at performance of models trained on one year of data
(here 2023) and tested on the next (2025). The baseline model (model 2a
in <a href="#tbl-model" class="quarto-xref">Table 1</a>) will be trained
on the 2023 data using the old vectors and tested on the 2025 data using
the old vectors.

<div id="fig-2-baseline">

<div class="cell-output cell-output-display" execution_count="17">

<div id="fig-2-baseline-1">

<img src="report_files/figure-commonmark/fig-2-baseline-output-1.png"
data-ref-parent="fig-2-baseline" />

(a) Overall and classwise F1 scores for model 2a trained on 2023 old
vectors and tested on 2025 old vectors.

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-2-baseline-2">

<img src="report_files/figure-commonmark/fig-2-baseline-output-2.png"
id="fig-2-baseline-2" data-ref-parent="fig-2-baseline" />

(b)

</div>

</div>

Figure 4

</div>

------------------------------------------------------------------------

Next we’ll compare the baseline model against a model trained on 2023
data and tested on 2025 data but using the new vectors (model 2b in
<a href="#tbl-model" class="quarto-xref">Table 1</a>).

<div id="fig-2-new-new">

<div class="cell-output cell-output-display" execution_count="18">

<div id="fig-2-new-new-1">

<img src="report_files/figure-commonmark/fig-2-new-new-output-1.png"
data-ref-parent="fig-2-new-new" />

(a) Difference in overall and classwise F1 scores between model 2b
(trained on 2023 new vectors and tested on 2025 new vectors) and the
baseline model 2a

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-2-new-new-2">

<img src="report_files/figure-commonmark/fig-2-new-new-output-2.png"
id="fig-2-new-new-2" data-ref-parent="fig-2-new-new" />

(b)

</div>

</div>

Figure 5

</div>

------------------------------------------------------------------------

Finally we’ll look at what happens if you train the model on the old
vectors using existing data (2023) but test on the new vectors using the
new data (2025). This is model 2c in
<a href="#tbl-model" class="quarto-xref">Table 1</a>. This will help
simulate updating the pipeline without re-indexing the existing points.

<div id="fig-2-old-new">

<div class="cell-output cell-output-display" execution_count="19">

<div id="fig-2-old-new-1">

<img src="report_files/figure-commonmark/fig-2-old-new-output-1.png"
data-ref-parent="fig-2-old-new" />

(a) Difference in overall and classwise F1 scores between model 2c
(trained on 2023 old vectors and tested on 2025 new vectors) and the
baseline model 2a

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-2-old-new-2">

<img src="report_files/figure-commonmark/fig-2-old-new-output-2.png"
id="fig-2-old-new-2" data-ref-parent="fig-2-old-new" />

(b)

</div>

</div>

Figure 6

</div>

### Test 3 - Train on year 1 and 30% of year 2, test on remaining 70% of year 2

This test will look at what happens when we train models with a
combination of old and new vectors. This is more representative of what
is likely to happen in practice when we roll out the new pipeline for
multi-year projects. The baseline model here is trained using the 2023
data and 30% of the 2025 data, and tested on the remaining 70% of the
2025 data, all using the old vectors (model 3a in
<a href="#tbl-model" class="quarto-xref">Table 1</a>).

<div id="fig-3-baseline">

<div class="cell-output cell-output-display" execution_count="20">

<div id="fig-3-baseline-1">

<img src="report_files/figure-commonmark/fig-3-baseline-output-1.png"
data-ref-parent="fig-3-baseline" />

(a) Overall and classwise F1 scores for model 3a trained on 2023 old
vectors and 30% of 2025 old vectors, and tested on remaining 70% of 2025
old vectors.

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-3-baseline-2">

<img src="report_files/figure-commonmark/fig-3-baseline-output-2.png"
id="fig-3-baseline-2" data-ref-parent="fig-3-baseline" />

(b)

</div>

</div>

Figure 7

</div>

------------------------------------------------------------------------

Next we’ll look at the performance of the same model as in
<a href="#fig-3-baseline" class="quarto-xref">Figure 7</a> but tested on
the new vectors (model 3b in
<a href="#tbl-model" class="quarto-xref">Table 1</a>). This demonstrates
how an existing model would perform on new data (without training on the
new data)

<div id="fig-3-old-new">

<div class="cell-output cell-output-display" execution_count="21">

<div id="fig-3-old-new-1">

<img src="report_files/figure-commonmark/fig-3-old-new-output-1.png"
data-ref-parent="fig-3-old-new" />

(a) Difference in overall and classwise F1 scores between model 3b
(trained on 2023 old vectors and 30% of 2025 old vectors, and tested on
remaining 70% of 2025 new vectors) and the baseline model 3a

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-3-old-new-2">

<img src="report_files/figure-commonmark/fig-3-old-new-output-2.png"
id="fig-3-old-new-2" data-ref-parent="fig-3-old-new" />

(b)

</div>

</div>

Figure 8

</div>

------------------------------------------------------------------------

Finally, we’ll look at the performance of a model trained on the 2023
data using the old vectors, and 30% of the 2025 data but using the new
vectors, and tested on the remaining 70% of the 2025 data using the new
vectors (model 3c in
<a href="#tbl-model" class="quarto-xref">Table 1</a>). This simulates
what would happen if we rolled out the new pipeline and re-trained using
a combination of the existing data and the new data.

<div id="fig-3-oldnew-new">

<div class="cell-output cell-output-display" execution_count="22">

<div id="fig-3-oldnew-new-1">

<img src="report_files/figure-commonmark/fig-3-oldnew-new-output-1.png"
data-ref-parent="fig-3-oldnew-new" />

(a) Difference in overall and classwise F1 scores between model 3c
(trained on 2023 old vectors and 30% of 2025 new vectors, and tested on
remaining 70% of 2025 new vectors) and the baseline model 3a

</div>

</div>

<div class="cell-output cell-output-display">

<div id="fig-3-oldnew-new-2">

<img src="report_files/figure-commonmark/fig-3-oldnew-new-output-2.png"
id="fig-3-oldnew-new-2" data-ref-parent="fig-3-oldnew-new" />

(b)

</div>

</div>

Figure 9

</div>

------------------------------------------------------------------------

## Summary and recommendations

All the tests show very small changes in overall F1 scores (~0.001) when
comparing the models trained and tested on the new vectors against the
baseline models trained and tested on the old vectors. Changes in
classwise F1 scores are also relatively small, with the majority of
classes showing changes of less than 0.01, and the largest errors around
0.03. This suggests that the changes in the feature vector extraction
pipeline do no result in major changes in the performance of the trained
models and can be safely rolled out.

The tests in experiment 3 also show that the performance of models
trained using existing data from the old pipeline combined with new data
from the new pipeline is very similar to that of models trained on data
from the old pipeline alone, which suggests that existing multi-year
projects can safely transition to the new pipeline without needing to
retrain and re-inference on on all existing points.

## What this means for ReefCloud users

**In short, users should not face any major changes to their own
pipeline after this update. Assessments of F1 scores show little change
and will likely not impact any current projects or comparisons to future
projects, without any additional need for re-annotating existing
projects.**

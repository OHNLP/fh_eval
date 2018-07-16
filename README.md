# Output Format and Evaluation for Family History Extraction

## Subtask 1: Entity Identification

For the first level evaluation, we ask the participants to provide a list of
two types of information:
1) family members of the patient mentioned in the text
2) observations (diseases) in the whole family history

As mentioned before, the participants should
 provide side of family for each family member (in case mentioned in text).

*Note*: for first degree relatives (e.g. parents, childern, siblings),
the side of family should be always be `"NA"`.

*Note*: to reduce the ambiguity in  observation extraction,
 we accept partial matching of the observations. For example, an extraction
 of `"diabetes"` in the phrase of `"type 2 diabetes"` will be considered as a
 true positive when calculating F1 score. However, each observation should
 contain no more than 4 tokens.

### Output Format

Within a single file. Fields are deliminated by tabs.
```
doc_id  FamilyMember family_member   SideOfFamily
doc_id  Observation  text of observation
```

Examples:
```
doc_1	FamilyMember	Brother	NA
doc_1	FamilyMember	Grandfather	Paternal
doc_1	FamilyMember	Father	NA
doc_1	Observation	conduction disorders
```


## Subtask 2: Family History Extraction

In the second level, the participants need to provide
 summarized information between family members and observations.
The output file should be in TSV format which the columns are:

   * Family member
   * Side of family
   * Living status
   * Observation

In case there are more than one observation for one family member,
 the systems should provide those observations in separate rows.

### Output Format

Within a single file. Fields are deliminated by tabs.
```
doc_id  family_member    side_of_family    LivingStatus    living_status_score
doc_id  family_member    side_of_family    Observation    text_of_observation
```

Examples:
```
doc_1	Brother	NA	LivingStatus	4
doc_1	Father	NA	LivingStatus	4
doc_1	Grandfather	Paternal	LivingStatus	0
doc_1	Uncle	Maternal	Observation	ovarian failure
doc_1	Cousin	Paternal	Observation	 phaeochromocytoma
doc_1	Cousin	Paternal	Observation	choreic dysphonia
```


### Living Status Score

We use only one score to represent living status for each individual.

For both "Alive" and "Healthy" properties,  the results
are encoded as:
* Yes: 2
* NA: 1
* No: 0

And the overall score for each individual are the alive score times healthy score.

For example:

For a relative with `"Alive: Yes"` and `"Healthy: Yes"`,
the living status score should be ``2 * 2 = 4``.

For a relative with `"Alive: No"` and `"Healthy: NA"`,
the living status score should be `0 * 1 = 0`.

*Note*: if multiple relatives are under the family member category,
(e.g. multiple maternal aunts)
with different living status scores, use the minimum of the
scores as the final score for that category.

# Evalution

Please run the evaluation script between your output file and the
gold standard file to check your system performance. Required Python 2.7
 or Python 3.5 above.

```
python eval.py subtask path/to/gold_standard path/to/your/prediction
```


The `subtask` field is either `1` or `2`. For instance, for subtask 1:

```
python eval.py 1 gs_subtask1.tsv path/to/your/prediction
```

You will find your system performance in your command line console.

```
TP: 18
FP: 1
FN: 3
Precision: 0.9696969696969697
Recall: 0.9142857142857143
F1: 0.9411764705882354
```
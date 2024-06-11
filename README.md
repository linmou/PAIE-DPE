# PAIE-DPE
repo for paper  Prompt Debiasing via Causal Intervention for Event Argument Extraction

# Experiment settings

### Overall dataset statistics

We evaluate on three common datasets for Event Argument Extraction: ACE05, RAMS, and WikiEvents. ACE05 is a joint information extraction dataset with entity, relation, and event annotations, while RAMS and WikiEvents focus on document-level event extraction. All three datasets are in English and composed of daily news articles. Detailed data statistics are in Table 1.

| Dataset | ACE05 | RAMS  | WikiEvents |
|---------|-------|-------|------------|
|         | #Events |     |            |
| Train   | 17172 | 7329  | 5262       |
| Dev     | 923   | 924   | 378        |
| Test    | 832   | 871   | 492        |
|         | #Args |       |            |
| Train   | 4859  | 17026 | 4552       |
| Dev     | 605   | 2188  | 428        |
| Test    | 576   | 2023  | 566        |
|         | #Event Type | 33    | 139   | 50   |
|         | #Role Type  | 22    | 65    | 59   |

*Table 1: Overall statistics of three datasets*

### Entity Type Balanced Dataset

To study if the entity type of role name in prompts impacts the results, we sample the events whose arguments appear with two entity types: `PER` and `ORG`. Only one role for every event is selected to build the dataset. For ACE05, as shown in Table 2, we build the training set by arguments `Agent`, `Adjudicator`, and `Target`, while `Entity` is the only argument in the test set. For WikiEvents, the test set is composed of `Killer` and `Attacker`, and the training set is composed of the rest arguments in Table 3. The data statistics of ETBD is shown in Table 4.

|           | ACE05 | WikiEvents |
|-----------|-------|------------|
| #Events   |       |            |
| Train     | 446   | 208        |
| Dev       | 44    | 18         |
| Test      | 131   | 144        |
| #Args     |       |            |
| Train     | 467   | 249        |
| Dev       | 44    | 19         |
| Test      | 131   | 272        |

*Table 2: Data statistics for Entity Type Balanced Dataset*

### Syntactic Role Balanced Dataset

For each dataset, we sample the arguments whose minimum number as `nsubj` and `pobj` is ten to build the SRBD dataset. We use the off-the-shelf tool Spacy V3.3 to annotate dependency roles for every argument. Data statistics are shown in Table 5.

|           | ACE05 | RAMS | WikiEvents |
|-----------|-------|------|------------|
| #Events   |       |      |            |
| Train     | 284   | 548  | 168        |
| Dev       | 64    | 100  | 56         |
| Test      | 66    | 122  | 68         |
| #Args     |       |      |            |
| Train     | 295   | 576  | 188        |
| Dev       | 70    | 107  | 64         |
| Test      | 72    | 134  | 80         |

*Table 5: Data statistics for Syntactic Role Balanced Dataset*

### Zero-Shot Learning

Since the event relation can be represented as a graph where an event is linked to others by overlapping arguments, ideally, seen events in the training set and unseen events in the test set should be two different connection components. However, in all three datasets, every event shares at least one common argument with others. Hence, we consider a point biconnected component in the event graph as an event cluster, so that for two events in seen and unseen divisions, they share at most two common arguments. Data statistics are shown in Table 6. The unseen event lists are in Tables 7 and 8.

|           | ACE05 | RAMS  | WikiEvents |
|-----------|-------|-------|------------|
| #Events   |       |       |            |
| Train     | 2284  | 6728  | 2456       |
| Dev       | 224   | 859   | 287        |
| Test      | 196   | 738   | 294        |
| #Args     |       |       |            |
| Train     | 2853  | 31600 | 3365       |
| Dev       | 333   | 4126  | 333        |
| Test      | 278   | 2972  | 452        |

*Table 6: Data statistics for Event level Zero-Shot Learning*

### Distribution Shift

The composition of the distribution shift dataset is very similar to SRBD. The main difference is we select arguments whose dependency role is `nsubj` for the training and development set and collect `pobj` arguments in the SRBD test set for testing. Data statistics are shown in Table 9.

|           | ACE05 | RAMS | WikiEvents |
|-----------|-------|------|------------|
| #Events   |       |      |            |
| Train     | 142   | 274  | 84         |
| Dev       | 32    | 50   | 38         |
| Test      | 33    | 61   | 68         |
| #Args     |       |      |            |
| Train     | 164   | 292  | 105        |
| Dev       | 41    | 56   | 44         |
| Test      | 42    | 72   | 78         |

*Table 9: Data statistics for Distribution Shift experiment*

### Modified Prompts

To generate the prompt with the largest perturbation while maintaining the semantic meaning, we follow these principles:
1. All argument names should be included in the newly designed prompts.
2. The modification of the words used in new prompts should be as few as possible.
3. The syntactic discrepancy should be as much as possible, specifically, an argument's syntactic role in the new prompt should be different from its old role.
4. For events where the model performs poorly (<33% in F1), which means our modification may improve performance, and whose event is hard to modify, we keep their prompts unchanged.

| Events                                             | Arguments            |
|---------------------------------------------------|----------------------|
| Life.Die                                           | Agent                |
| Life.Injure                                        | Agent                |
| Movement.Transport                                 | Agent                |
| Personnel.Nominate                                 | Agent                |
| Justice.Execute                                    | Agent                |
| Business.Start-Org                                 | Agent                |
| Justice.Arrest-Jail                                | Agent                |
| Justice.Extradite                                  | Agent                |
| Transaction.Transfer-Ownership                     | Buyer                |
| Justice.Acquit                                     | Adjudicator          |
| Justice.Appeal                                     | Adjudicator          |
| Justice.Charge-Indict                              | Adjudicator          |
| Justice.Convict                                    | Adjudicator          |
| Justice.Fine                                       | Adjudicator          |
| Justice.Pardon                                     | Adjudicator          |
| Justice.Sentence                                   | Adjudicator          |
| Justice.Sue                                        | Adjudicator          |
| Justice.Trial-Hearing                              | Adjudicator          |
| Conflict.Attack                                    | Target               |
| Conflict.Demonstrate                               | Entity               |
| Contact.Meet                                       | Entity               |
| Contact.Phone-Write                                | Entity               |
| Justice.Fine                                       | Entity               |
| Justice.Release-Parole                             | Entity               |
| Personnel.Elect                                    | Entity               |
| Personnel.End-Position                             | Entity               |
| Personnel.Start-Position                           | Entity               |

*Table 10: Event types and arguments selected for Entity Type Balanced Dataset of ACE05*

| WikiEvents                                         | Unseen Events                              |
|---------------------------------------------------|--------------------------------------------|
| Cognitive.IdentifyCategorize.Unspecified          | Cognitive.Research.Unspecified             |
| Cognitive.TeachingTrainingLearning.Unspecified    | Conflict.Defeat.Unspecified                |
| Control.ImpedeInterfereWith.Unspecified           | Disaster.Crash.Unspecified                 |
| Justice.Acquit.Unspecified                        | Justice.ArrestJailDetain.Unspecified       |
| Justice.ChargeIndict.Unspecified                  | Justice.Convict.Unspecified                |
| Justice.InvestigateCrime.Unspecified              | Justice.ReleaseParole.Unspecified          |
| Justice.Sentence.Unspecified                      | Justice.TrialHearing.Unspecified           |
| Life.Consume.Unspecified                          | Movement.Transportation.Evacuation         |
| Movement.Transportation.IllegalTransportation     | Movement.Transportation.PreventPassage     |
| Movement.Transportation.Unspecified               | Personnel.EndPosition.Unspecified          |
| Personnel.StartPosition.Unspecified               |                                            |

*Table 11: Unseen event list in Zero-Shot Learning for WikiEvents*

| ACE05                         | RAMS                                                             |
|------------------------------|------------------------------------------------------------------|
| Business.Declare-Bankruptcy  | conflict.demonstrate.marchprotestpoliticalgathering             |
| Business.End-Org             | conflict.demonstrate.n/a                                        |
| Business.Merge-Org           | government.agreements.rejectnullifyagreementcontractceasefire  |
| Business.Start-Org           | government.agreements.violateagreement                          |
| Conflict.Attack              | government.formation.n/a                                        |
| Conflict.Demonstrate         | government.formation.startgpe                                   |
| Contact.Meet                 | government.legislate.legislate                                  |
| Contact.Phone-Write          | inspection.sensoryobserve.inspectpeopleorganization             |
| Life.Be-Born                 | inspection.sensoryobserve.monitorelection                       |
| Life.Die                     | inspection.sensoryobserve.physicalinvestigateinspect            |
| Life.Divorce                 | personnel.endposition.firinglayoff                              |
| Life.Injure                  | personnel.endposition.n/a                                       |
| Life.Marry                   | personnel.endposition.quitretire                                |
| Transaction.Transfer-Money   | personnel.startposition.hiring                                  |
|                              | personnel.startposition.n/a                                     |

*Table 12: Unseen event list in Zero-Shot Learning for ACE05 and RAMS.*

# Clinical Services QuestionAnswers Design Detail
## Purpose
To describe the internal details of this composite service in relation to existing components.
This service is slightly more complex than other CRUD api's.

## API definition
> Same can be found in [APIDefinition](APIDefinition.md#composite-service)
> Underlying model definition can be found here [DatabaseDefinition](DatabaseDefinition.md#Overview)

Request Example:
```json
[
  "PHYS_EXAM",
  "XRAY",
  "MRI"
    
]
```

Response Example:
```json
[
  {
    "questionCd" : "Question 1",
    "questionAnswerType" : "RadioYesNo"
  },
  {
    "questionCd" : "Question 2",
    "questionAnswerType" : "TextArea"
  },
  {
    "questionCd" : "Question 2",
    "questionAnswerType" : "NumericPositiveInteger"
  }  
]
```

## Composite Service Design Detail
As this api is taking in a list of `clinicalServiceCd` it is necessary to create a business service to undertake the work of
translating this to the database model.

PsuedoCode as follows for the business service class
```java

/**
 * Given a list of clinicalServiceCodes, return a unique list of QuestionAnswers.
    For each clinical service cd
    call the db to get a list of questions
    dedup this list of question codes
 */
public class UniqueClinicalServiceImpl implements UniqueClinicalService {
    public List<QuestionAnswer> getUniqueClinicalServiceQuestions(List<String> clinicalServiceCodes) {

        // For each clinical service cd
        // call the db to get a list of questions
        // dedup this list of question codes
        ConcurrentHashMap<String, QuestionAnswer> uniqueQuestions = new ConcurrentHashMap<>();
        // Depending on load this can be optimized to run in parallel if needed. Using parallel stream.
        clinicalServiceCodes.stream().map(IQuestionRepository::getQuestionByServiceCd).forEach(questions -> reduceUniqueQuestions(uniqueQuestions, questions));
    }

    /**
     * Checks the hashmap for an existing questionAnswer. If it exists only add it one time.
     */
    private void reduceUniqueQuestions(ConcurrentHashMap<String, QuestionAnswer> uniqueQuestions, QuestionAnswer questionAnswer) {
        uniqueQuestions.getOrCreate(questionAnswer.questionCd, questionAnswer);
    }
}


```

### IQuestionRepository
This repository will maintain the relationship and query to get question answer codes through the many-many relationship between
ClinicalServices, ClinicalServiceQuestions and Questions tables.

### Trade Offs
Design considers that the joins could be done in SQL however not really necessary as overcomplicated SQL is harder to maintain.
Using domain objects and parallelism can get the desired result. This would be wholly dependent on size of payload.
This SQL design choice would also limit future business logic to be applied afterwards (such as version control) which could increase
further the complexity of the SQL queries leading to performance issues.
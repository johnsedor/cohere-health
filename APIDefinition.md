# API Definitions
## Purpose
To describe API definitions for possible use of the given application.
Although not all API's are covered by PO definition some API's are necessary for test data setup and tear down.
Additionally, these API's could be used for interaction and modification of application data in the future.

## Definitions
Definitions are work in progress as relates to PO requirements.
HTTP verbs follow standard definitions and guidelines for GET/POST/PUT/DELETE unless specificallys stated below.

### Clinical Categories
GET /clinical-categories
> Returns a list of clinical categories

GET /clinical-categories/{clinical-category-cd}
> Returns a single clinical category
Response:
```
JSON
[
    {
        "clinicalCategoryCd" : "DIAGNOSIS",
        "clinicalCategoryDescription" : "Diagnosis of a patient issue."        
    },
    {
        "clinicalCategoryCd" : "CONSERVATIVE_THERAPY",
        "clinicalCategoryDescription" : "Conservative Therapy"        
    }
]
```

POST /clinical-categories {body}
> Creates a new clinical category

PUT /clinical-categories/{clinical-category-cd}
> Updates given clinical category

DELETE /clinical-categories/{clinical-category-cd}
> Removes a given clinical category cd.

### Clinical Category Services
GET /clinical-categories/{clinical-category-cd}/clinical-categories-services
> Returns a list of clinical-category-services related to this clinical category

GET /clinical-categories/{clinical-category-cd}/clinical-category-services/{clinical-service-cd}
> Returns a uniquely identifies this service cd

POST /clinical-categories/{clinical-category-cd}/clinical-category-services
> Creates a new service code relationship to clinical-category-cd

PUT (Not needed as you should not be updating a relationship per se in this model)

DELETE /clinical-categories/{clinical-category-cd}/clinical-categories-services/{clinical-service-cd}
> Removes a given clinical-service-cd relationship.

## Clinical Services
GET /clinical-services
> Returns a list of clinical-services

POST /clinical-services
> Creates a clinical service

PUT /clinical-services/{clinical-service-cd}
> Updates a given clinical service based on clinical service cd.

DELETE /clinical-services/{clinical-service-cd}
> Deletes the given clinical service based on clinical service cd. (NOTE: if relationship exists with clinical-category this will error)

## Clinical Service Questions
GET /clinical-services/{clinical-service-cd}/questions
> Returns a list of questions relate to this clinical service.

POST /clinical-services/{clinical-service-cd}/questions
> Create a new relationship from this clinical-service to this question.

PUT (Not needed as relationships really should not be updated)

DELETE /clinical-services/{clinical-service-cd}/question-answers/{question-cd}
> Removes the relationship between the clinical service and question answers.

## Composite Service
Composite service takes in a list of objects.
POST is utilized over GET because of the following: UI team requested that there be one service given a set of codes to return a full, unique list of questions.
GET has a limitation on length, thus a POST allows for room for growth and full object utilization if necessary.

POST /clinical-services-question-answers
> Given a list of clinical services will return a unique list of questions answers for this given post.
> Content will be de-duplicated for answers.

Request: Request should be a list of clinical-service-cds from 
GET /clinical-services 
or 
GET /clinical-categories/{clinical-category-cd}/clinical-categories-services
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

## Questions
GET /questions
> Returns a list of questions

POST /questions
> Creates a new question

PUT /questions/{question-cd}
> Updates an existing question.

DELETE /question/{question-cd}
> Removes an existing question based on question cd. (Note: Error will be thrown if this is linked to any existing service.)

## QuestionAnswers
GET /question-answers
> Returns a list of answers

POST /question-answers
> Creates a new answer type

PUT /question-answer/{question-answer-cd}
> Updates an existing question-answer-cd

DELETE /question-answer/{question-answer-cd}
> Deletes a given question answer based on cd. (Note: Error will be thrown if there is any linked Questions.)
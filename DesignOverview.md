# Design Overview
## Purpose 
Describe the overall application design for the RESTful service component providing questions to the user
given a selected set of services.

## Background
Cohere provides a list of clinical categories IE: Diagnosis, Conservative Therapy, Surgery etc.
These categories are linked to multiple services that can be provided for given category. In turn, each service
has the ability to present criteria to the user as prerequisites questions.

In the interest of brevity for the user, the questions asked must not be duplicative in nature.

## Database Data Model
Database model used for storage and retrieval of questions for a given service.
[Database Definitions](DatabaseDefinitions.md)

## API Model
API Model covers aspects related to UI interactions an contracts.
[API Model](APIDefinitions.md)

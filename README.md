# Enterprise-Web-Application

The Sustainble Development Goal (SDG) tool a someone can use to get to know what SDG suits them the most and what action they can take to work towards them.
This project has been build by 5 students. My (Marco de Boer) contributions to this project where the quiz front-end, backend and testen. 
And also the adminquiz interface where an admin can edit current quizzes, create new ones and choose which one to set live for users to make.

Technologies used:
- JavaScript
- Vue.js
- CSS
- HTML
- Spring
- Java
- JUnit
- Jest
- Docker
- Node.js
- Bootstrap

Some environmental variables will need to be set for running the front-end and backend.

Frontend
- VUE_APP_API_URL 

Backend
In the backend, you could switch the MySQL database to an h2 database by changing application.properties in resources.
Else, you would have to set
- JDBC_DATABASE_URL
- JDBC_DATABASE_USERNAME
- JDBC_DATABASE_PASSWORD

and 
- FRONTEND_URL
- PASSPHRASE (for JWT token)

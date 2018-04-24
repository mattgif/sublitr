# sublitr
https://www.sublitr.com

sublitr is a react/node based submission manager. Users submit to a publication witha sublitr account, and are automatically notified when editors submit a decision. Editors can view, comment on, track, and manage submissions for multiple publications, without needing to download submission files.

#### Table of Contents
* [Screenshots](#screenshots)
* [API](#api)
* [Client](#client)
* [Technology used](#technology)

## Screenshots
| Landing  | Document review |
| ------------- | ------------- |
| <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/landing.png" alt="sublitr landing page" width="200"> | <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/editor_docviewer.png" alt="sublitr document viewer" width="200">  |

| Editor panel | User submission tracker |
| ------------- | ------------- |
| <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/editor_review_pane.png" alt="sublitr reviewer panel" width="200"> | <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/user_submissions.png" alt="sublitr user's submission manager" width="200"> |

| Publication manger | User manager |
| ------------- | ------------- |
| <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/admin-publications.png" alt="sublitr publication manager" width="200"></kbd> | <img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/admin_users.png" alt="sublitr user manager" width="200"></kbd> |

## API
### Repo link: https://github.com/mattgif/sublitr-api
sublitr's API is secured using JSON web tokens (JWT) with Passport.js.

### Auth endpoints

#### POST 'api/auth/login'
  Uses local strategy to check username and password against password hash
  
#### POST 'api/auth/refresh'
  Uses JWT strategy to periodically refresh authenticated users JWT
  
### Submission endpoints

#### GET 'api/submissions'
  Returns array of submissions according to requesters level of access. 
  
  Standard users receive their own submissions as objects in the following format: 
  ````
  {
     id: [id of submission],
     title: String,
     author: String,
     authorID: string,
     submitted: Date,
     status: String, e.g. Accepted, Pending, Declined, etc...,
     publication: String, title of publication submitted to,
     coverLetter: String,
     file: URL pointing to uploaded file location
  }
  ````
  
  Editors receive, in additon to their own submissions as above, all submissions for publications that list them as the editor. These submissions are formatted as above, with an additional reviewerInfo field:
  
  ````
  {
    reviewerInfo: {
      decision: String, e.g. Accepted, Pending, Declined, etc...,
      recommendation: String, Used for internal statuses before alerting submitter e.g. Accept, Consider, etc... 
      lastAction: Date, last time decision or recommendation updated,
      comments: [
        {
          firstName: String,
          lastName: String,
          authorID: String, userId of person making comment,
          text: String,
          date: Date,
          id: String
        }
      ]      
    }
  }
  ````
  
  Admins receive all submissions with all details.
  
#### GET 'api/submissions/:submissionID/:key' 
  Returns the uploaded file associated with a submission
  
#### POST 'api/submissions'
  Takes multipart formdata. Requires 'title' (string), 'publication' string and an uploaded file (currently only PDFs allowed). Optional 'coverLetter' string. 
  
  Returns the submission object as specified above. 
  
#### DELETE 'api/submissions/:submissionID'
  Deletes submission with specified id, and removes uploaded files from server. Must be submitter or admin.
  
#### PUT 'api/submissions/:submissionID'
  Primarily used for updating status (status, decision, recommendation). Returns updated submission object.
  
#### POST 'api/submissions/submissionID/comment'
  Adds a comment to the submission. Must be editor or admin. Requires 'text' string. Returns submission object.
  
#### DELETE 'api/submissions/submissionID/comment/:commentID'
  Deletes comment with id commentID from submission with id submissionID. Must be author of comment, or admin.
  
### Publication Endpoints

#### GET 'api/publications/'
  Returns array of publications in following format: 
  ````
    title: String,
    abbr: String, randomly generated - used for html friendly values
    editors: {
      [editorUserId]: {
        email: [editorEmail],
        id: [editorId],
        name: [editorFirstName editorLastName]
        }    
    },
    image: String, url of uploaded image for publication
  ````

#### DELETE 'api/publications/:id' 
  Deletes publication with specified id. Requires admin privileges.
  
#### PUT 'api/publications/:id'
  Updates publication. Primarily used for adding/removing editors. Editors added will have the editor flag set to true on their user object. Requires admin.
  
#### POST 'api/publications/'
  Creates a enw publication. Requires 'title' string. Optional uploaded image file, and list of editors (JSON.stringified array of user objects). Users listed as editors will have the editor flag set to true on their user object.
  
#### DELETE 'api/publications/:id'
  Deletes the specified publication. Requires admin.
  
### User endpoints
#### GET 'api/users'
  Requires admin. Returns list of users formatted like so:
  ````
  {
    firstName: String,
    lastName: String,
    email: String,
    admin: Bool,
    editor: Bool
    id: String
  }
  ````

#### GET 'api/users/:id'
  Returns specific user object, as specified above. Users can request themselves, otherwise requires admin.
  
#### POST 'api/users'
  Creates a new user. Requires 'firstName' string, 'lastName' string, 'email' string, 'password' string. Creates user object (using hashed password value). Returns user object.
  
#### PUT 'api/users/:id'
  Updates user with specified id. Primarily used for toggling editor boolean. Requires admin.
  
#### DELETE 'api/users/:id'
  Delets user with specified id. Requires admin.
  
## Client
### Repo link:https://github.com/mattgif/sublitr-client

## Technology


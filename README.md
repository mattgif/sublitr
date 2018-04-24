# sublitr
https://www.sublitr.com

sublitr is a react/node based submission manager. Users submit to a publication witha sublitr account, and are automatically notified when editors submit a decision. Editors can view, comment on, track, and manage submissions for multiple publications, without needing to download submission files.

#### Table of Contents
* [Screenshots](#screenshots)
* [API](#api)
* [Client](#client)
* [Technology used](#technology)

## Screenshots
### Landing
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/landing.png" alt="sublitr landing page" width="200"></kbd>
### Document review
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/editor_docviewer.png" alt="sublitr document viewer" width="300"></kbd>
### Editor panel
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/editor_review_pane.png" alt="sublitr reviewer panel" width="300"></kbd>
### User submission tracker
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/user_submissions.png" alt="sublitr user's submission manager" width="300"></kbd>
### Publication manager
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/admin-publications.png" alt="sublitr publication manager" width="300"></kbd>
### User manager
<kbd><img src="https://raw.githubusercontent.com/mattgif/sublitr/master/assets/admin_users.png" alt="sublitr user manager" width="300"></kbd>

## API
### Repo link: https://github.com/mattgif/sublitr-api
sublitr's API is secured using JSON web tokens (JWT) with Passport.js.

### Auth endpoints

#### POST '/login'
  Uses local strategy to check username and password against password hash
  
#### POST '/refresh'
  Uses JWT strategy to periodically refresh authenticated users JWT
  
### Submission endpoints

#### GET '/'
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
  
### GET '/submissions/:submissionID/:key' 
  Returns the uploaded file associated with a submission
  
### POST '/'
  Takes multipart formdata. Requires 'title' (string), 'publication' string and an uploaded file (currently only PDFs allowed). Optional 'coverLetter' string. 
  
  Returns the submission object as specified above. 
  
### DELETE '/:submissionID'
  Deletes submission with specified id, and removes uploaded files from server. Must be submitter or admin.
  
### PUT '/:submissionID'
  Primarily used for updating status (status, decision, recommendation). Returns updated submission object.
  
### POST '/submissionID/comment'
  Adds a comment to the submission. Must be editor or admin. Requires 'text' string. Returns submission object.
  
### DELETE '/submissionID/comment/:commentID'
  Deletes comment with id commentID from submission with id submissionID. Must be author of comment, or admin.

## Client
### Repo link:https://github.com/mattgif/sublitr-client

## Technology


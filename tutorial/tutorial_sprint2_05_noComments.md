[< Tutorial Sprint 2](tutorial_sprint2.md)
# Tutorial - No Comments


### Overview
In a meeting with our customers, they decided to leave the Comments loop out of the project for the first implementation.  So in this step, I'm going to take out our comments from our UI design. (but leave things in place on the server, since I've already implemented them.)


### Remove the comment section from our `ApprovalForm`
Currently the HTML for our comments section looks like:
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->
      <h4>Comments:</h4>
      <div class="op-container op-block-border off-botm10">
      <!-- transaction.comments.forEach(function(comment){ -->

          <p>[[= comment.comment ]]</p>

      <!-- })  -->
      </div>
      <textarea class="form-control" rows="5">Admin Comments</textarea>
      <div class="op-container op-block nomargintop">
          <button class="btn btn-success float-right" type="submit">Accept</button>
          <button class="btn btn-info  float-right offset-15" type="submit">Request</button>
          <button class="btn btn-warning float-right  offset-15" type="submit"><i class="fa fa-hand-paper-o"></i>Deny</button>
      </div>    
```


Since this falls within our `ApprovalForm` template, I can simply add a `.mockup` class reference to any item I don't want to keep.  So change the HTML to:
```html
<!-- [plugin]/assets/opstools/ProcessApproval/views/ProcessApproval/ProcessApproval.ejs  -->
      <h4 class="mockup" >Comments:</h4>
      <div class="op-container op-block-border off-botm10 mockup">
      <!-- transaction.comments.forEach(function(comment){ -->

          <p>[[= comment.comment ]]</p>

      <!-- }) -->
      </div>
      <textarea class="form-control mockup" rows="5">Admin Comments</textarea>
    
      <div class="op-container op-block nomargintop">
          <button class="btn btn-success float-right" type="submit">Accept</button>
          <button class="btn btn-info  float-right offset-15 mockup" type="submit">Request</button>
          <button class="btn btn-warning float-right  offset-15" type="submit"><i class="fa fa-hand-paper-o"></i>Deny</button>
      </div>     
```
> NOTE: this includes the [Request] button as well.

Now reload the OpsPortal and you will see all the comment sections as well as the request button is no longer there.

However, the HTML mockup still has the elements in place, so when our customers decide to implement the comments feature, then we'll simply remove the `.mockup` classes and be ready to go.


---
[< step 4 : Approval Workspace & Instructions](tutorial_sprint2_04_approvalWorkspace.md)
[step 6 : Submitting the Transaction  >](tutorial_sprint2_06_submit.md) 
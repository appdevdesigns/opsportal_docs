[< Tutorial: Server Side Data Type](tutorial_step4.md)
# Tutorial - How Images will be uploaded
Before we go any farther, let's discuss how we will upload images to the server.


### Short Summary
The Ops Portal includes a service for uploading images to the server.

##### To Upload An Image:

- **url** : `POST /opsportal/image`
- **headers** : 
  - `X-CSRF-Token` : `[current csrf token]`
- **parameters** : 
  - `appKey` : a unique key referencing the application responsible for this image
  - `permission` : an `Action Key` value required for a user to access this image.
  - `isWebix` : `(optional)` if `true`, then a successful response will have `status:"server"`
  - `image` : the image you are uploading

>NOTE: this order is important!  The `image` being uploaded must be the **last** parameter.

- **response** :
  - on a successful upload you should get:
  ```json
  {
    "status":"success",
    "data":{
      "uuid":"[some uuid string here]"
    }
  }
  ```

  - on an error you should get:
  ```json
  {
    "status":"error",
    "id" : "E_[SOMEERRORID]",
    "message" : "a text message about the error",
    "code" : "[numericErrorCode]",
    "mlKey" : "[aMultilingualKeyForDisplayingTheMessageInDifferentLanguages]",
    "data" : { "detailedError":"objectHere" }
  }
  ```


##### To Access An Image


- **url** : `GET /opsportal/image/[appKey]/[uuid]`
- **headers** : 
  - `X-CSRF-Token` : `[current csrf token]`
- **response** :
  - on a successful upload you should get the raw image data.
  - on an error you should get an error in a format like the one above.


### This Tutorial
For this tutorial, we will simply reuse this service for our image uploading.  On a successful upload, the service will return a `uuid` value to reference this image.  We will store the `[appKey]/[uuid]` value as our `string` image data, and use that to complete the `/opsportal/image/` url to load the image when our Data Field is displaying itself.

Let's get started ... 


---
[< Step 4 : Server Side Data Type](tutorial_step4.md)
[Step 6 : Add A Form to our Application >](tutorial_step6.md) 
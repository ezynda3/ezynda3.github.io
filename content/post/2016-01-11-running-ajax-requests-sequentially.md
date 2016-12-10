---
author: Ed Zynda
categories:
- Javascript
- JQuery
- Web Development
comments: true
date: 2016-01-11T00:00:00Z
share: true
tags:
- Javascript
- JQuery
- AJAX
title: Running AJAX Requests Sequentially
url: /running-ajax-requests-sequentially/
---

Somewhat recently, I was refactoring a project that was having issues uploading and importing large CSV files. I was running into all kinds of issues from timeout errors to memory allocation exceptions. The solution was to break up the import and upload it in chunks. This alone was pretty easy but I also wanted to include the ability to display the progress of the entire import. The problem was, I was using AJAX to upload the data.

AJAX is pretty useful in that it allows you to make HTTP requests asynchronously without refreshing the entire page. You can update small sections of the page without reloading. This is great if you don't care what order the requests finish. In my case I needed to upload the data in chunks but also calculate the overall progress based on how much had been uploaded vs how much was remaining. Using AJAX as intended, all of the chunks would be sent at the exact same which opens of the possibiity that a chunk that should be uploaded last would finish before an earlier chunk.

After banging my head against the wall dealing with Javascript promises and deferred objects, I decided to try a functional programming technique. The solution ended up being recursion. Let's take a look at what I mean.

```javascript
function recursivePost(url, data) {
  var dfd = $.Deferred();
  var total = data.length;
  var chunk = data.splice(0, 10);
  var payload = {}
            
  payload.data = chunk;
  payload.remaining = total - chunk.length;
            
  $.post(url, payload)
    .success(function (response) {
                    
      updateProgressBar(chunk.length);
                        
      if (response.status == 'complete') {
        dfd.resolve(response);
      } else {
        recursivePost(url, data).done(function(response) {
          dfd.resolve(response);
        });
      }
    })
    .fail(function (response) {
      dfd.reject(response.responseText);
    });
                    
  return dfd.promise();
}
```

The function above starts out by creating a deferred object which we use to return a promise object at the bottom of the function. This allows us to start the upload process and hold off on doing anything else until all the chunks are finished uploading. Then we take the initial data which should be an array and calculate the total. Then we shave off 10 items from the array using array_splice(). Then we construct a payload object that contains the chunk and then the number of items remaining after this upload completes. This will allow our backend code to determine when the entire process is complete in case it needs to run some additional logic after.

Using jQuery's handy post() method we can upload the data to the specified URL. On success we can update our progress bar then we check whether there is more to be uploaded. In my case I let the backend decide by sending a 'complete' status. We could also just check whether payload.reaining = 0. If there are still more items we call the same function from within itself. We also attach done() handler to catch the promise returned by that call. This allows the resolved promise from recursive calls to recursivePost to bubble up to the initial call and then be returned at completion.

If there are no more items to process, we resolve the promise object so the rest of the app knows the upload is complete.

Because we only run the next request after the previous request succeeds, we prevent the AJAX calls from firing at the same time. Each request is in sequential order and progress can be tracked.

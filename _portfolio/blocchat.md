---
layout: post
title: Bloc Chat
thumbnail-path: "img/blocchat.png"
short-description: A real-time chat client using AngularJS and Firebase.

---

{:.center}
![]({{ site.baseurl }}/img/blocchat-LOGO.jpg)

## Explanation
Welcome to **Bloc Chat**, a real-time chat client. An app that builds on my previous knowledge of **AngularJS** and introduces me to googles' **firebase** realtime database. A tool that I have grown to love!



## Problem
Having only used AngularJS once before in my <strong>[BlocJams](../1blocjams/)</strong> project I wanted to explore more of which services are best to use with controllers for specific functionality. This app is also the first time I am using googles realtime database "firebase".


## Solution
Unlike <strong>[BlocJams](../1blocjams/)</strong> I started with a different approach by using wireframes to build toward a specific spec rather than building toward a certain functionality first off.
1. First I needed to create available chat rooms.

<img src="/img/bloc-chat-new-room-modal.jpg">
I created a Room factory in Angular that defines all Room-related API queries, after then a Room controller was created where the room service was injected and using ng-repeat I was able to display chatrooms.

I used firebase child() method (called on an instance of its API object) to either query an existing set of data or reference one intended to populate with data in the future. I also used the $firebaseArray service to ensure the data is returned as an array.
```js
(function() {
  function Room($firebaseArray) {
    var Room = {};
    var ref = firebase.database().ref().child("rooms");
    var rooms = $firebaseArray(ref);

    Room.all = rooms;

    return Room;
  }

  angular
    .module('blocChat')
    .factory('Room', ['$firebaseArray', Room]);
})();
```


I then used UI Bootstrap service to create a $uibModal service for users to create their own rooms and ofcourse to make sure each users name was displayed I injected the ngCookies service in the run blocks dependencies.

```js
(function() {
      function BlocChatCookies($cookies, $uibModal) {
         var currentUser = $cookies.get('blocChatCurrentUser');
          console.log(currentUser);
         if (!currentUser || currentUser === '') {
           $uibModal.open({

             templateUrl: '/templates/user.html',
             controller: 'UserInstanceCtrl as user'
           });

         }
       }

       angular
         .module('blocChat')
         .run(['$cookies', '$uibModal', BlocChatCookies]);
 })();

```


2.  I need to allow users to message in realtime using firebase.

<img src="/img/messages.jpg">
First I allowed for messages to be filtered by correct Room ID so I passed an argument into the  getByRoomId method that contains the roomId associated with a rooms message. With the roomId, I used Firebase's equalTo() method to find all messages whose  roomId property is equal to the roomId in the argument. After this I added a method to my Message factory called send, that takes a message object as an argument and submits it to my Firebase server.
```js
(function() {
function Message($firebaseArray) {
  var Message = {};
  //reference firebase database
  var ref = firebase.database().ref().child("messages");
  var messages = $firebaseArray(ref);
  // filter by correct roomid.
  Message.getByRoomId = function(roomId) {
    return $firebaseArray(ref.orderByChild("roomId").equalTo(roomId));
  };
  //send message data to firebase
  Message.send = function(newMessage) {
    console.log("sending")

    Message.messages.$add({
      content: newMessage,
      sentAt: 12345,
      username: $cookies.get('messengerCurrentUser'),
      roomID: 'khkhohpoj',
    })
  };
  return Message;
}
  angular
    .module('blocChat')
    .factory('Message', ['$firebaseArray', Message]);
})();
```

## Results

<img src="/img/bloc-chat-done.png">

## Languages and Tools Used

FirebaseAngular, AngularJS, UI Bootstrap, HTML, CSS

#Microsoft Mirror#

##"What's a Microsoft Mirror?"##

Our "Microsoft Mirror" is basically a one-way mirror (like you might have seen in Hollywood depictions of interrogation rooms), made "smart" by a simple LED display which sits behind the mirror and displays white UI elements with a black background. When this display is on, you can see both your reflection and the white elements, allowing software to present relevant information while you get ready for your day.

We designed the Microsoft Mirror to be low-cost and simple, so anyone could build it in a couple of hours. We've also open-sourced the web app and shared our bill of materials and assembly instructions both of which you can find here.

##Building the interface##

We designed the mirror user interface (UI) to be as functional as possible, as both a mirror and an info hub. There are some practical implications to this. The UI should be simple and easy to visually digest, so we kept adornment light and typography clear. The screen needs to be readable through the mirrored surface, so we used a high contrast ratio of pure white on pure black. Lastly and most importantly the user needs to see their reflection, so we kept the central area of the mirror clean when the user is logged in.

The mirror is built to be useful to a person getting ready in the morning. This person is likely on a time crunch, wants to be well-prepared for the day and is interested in updates, but possibly doesn't want to be barraged with info before they're fully awake. With that in mind, we placed more-pressing information (weather, time, and a space reserved for alerts) at the top of the mirror near eye-level, and pushed less urgent information down at the bottom where it can be ignored or consciously consumed. Every user will have a slightly different idea about what's important, so this is a great project for exploring personalization through tech.

##Building the web app##

There are multiple pieces at play here. First is what you see displayed behind the mirror. This is a web app created in HTML, CSS, and JavaScript and served from a Node instance hosted on Azure.

![alt text](https://github.com/Criviere/HackGT16/blob/master/magic-mirror-architecture-diagram.png)

Using the [Hosted Web apps bridge](http://microsoftedge.github.io/WebAppsDocs/en-US/win10/HWA.htm), we turned our web app into a Universal Windows App, which not only gives us access to Windows Native APIs but can also run across Windows devices, such as the Raspberry Pi 3 in our case. All the HTML, CSS, and Javascript comes directly from the server, hence the term *hosted.*

##Making it smart##

The most important part of the app and the delightful experience for the user is the facial recognition capability, which personalizes the mirror's display based on the individual in front of it. In the past, this was complex technology out of the reach of most web apps, but, with APIs provided by [Microft's Cognitive Services](https://www.microsoft.com/cognitive-services/), we're able to build it into our mirror with minimal effort.

Microsoft Mirror leverages Microsoft's Cognitive Services [Face API](https://www.microsoft.com/cognitive-services/en-us/face-api) to match the user's face to their profile. The user creates a profile by adding some personal info and taking a selfie, which is then sent to Cognitive Services to get a unique identifier (a *face_id*) which is then stored in Microsoft Mirror's database.

Once they've created a profile, the user can stand in front of the Microsoft Mirror, which will take a picture and request Cognitive Services for the user's *face_id*. This ID is then used to find the user's profile so the mirror can present the user with relevant info.

Below you can see how our Node sends an image as an *octet-stream* to Microsoft Cognitive Services through their REST API. The Cognitive Services' cloud then sends back a *face_id*, which we to our user object.

`request.post({
'url': 'https://api.projectoxford.ai/face/v1.0/facelists/' + your_face_api_list + ' /persistedFaces',
'headers': {
'Content-Type': 'application/octet-stream,'Ocp-Apim-Subscription-Key': your_secret_key_here
},
'body': req.body
}, function(error, response, body) {
  //add the newly created face_id to the user document
});`

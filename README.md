![](https://s3-eu-west-1.amazonaws.com/ih-materials/uploads/upload_d5c5793015fec3be28a63c4fa3dd4d55.png)

# Progressive Web Apps | Intro for you game project

## Learning Goals

After this lesson you will be able to:

- Understand what is a Progressive Web App (PWA)
- Why is it useful
- How you can create a Progressive Web App for your game project


## What is a Progressive Web App ?

Progressive Web Apps (PWA) are user experiences that have the reach of the web, and are:
- **Reliable** - Load instantly and never show the downasaur, even in uncertain network conditions.
- **Fast** - Respond quickly to user interactions with silky smooth animations and no janky scrolling.
- **Engaging** - Feel like a natural app on the device, with an immersive user experience.
This new level of quality allows Progressive Web Apps to earn a place on the user's home screen.

Progressive Web Apps adapt according to the browser where it runs. For example it will handle offline mode on Chrome and it will adapt on other browser.

## Create a Progressive Web App for your game

### What we going to do

Here we are going to create an app:
- Works offline (at least on Chrome)
- Can be displayed on users' home screen of Android phone

### Steps to follow

You will need to:
- Create a `manifest.json` and icon images for your Android app.
- Modify your HTML files. In the example we name it `index.html`.
- Create a `service-worker.js` file that will be included in the main JavaScript file.
- Modify your main JavaScript file. In the example we name it `main.js`.


### `manifest.json`and icon images 

For the `manifest.json` and icon images, the easiest way is to use the following generator: https://app-manifest.firebaseapp.com/

We recommand you to include them in you tree directory like this:
- index.html
- **manifest.json**
- **images/**
  - **icons/**
    - **icon-128x128.png**
    - **icon-152x152.png**
    - **icon-192x192.png**
    - **icon-384x384.png**
    - **icon-512x512.png**
    - **icon-72x72.png**

Your `manifest.json` should look like the following one:
```json
{
  "name": "My Awesome PWA",
  "short_name": "My Awesome PWA",
  "theme_color": "#000000",
  "background_color": "#ffffff",
  "display": "standalone",
  "orientation": "portrait",
  "scope": "/",
  "start_url": "/index.html",
  "icons": [
    {
      "src": "images/icons/icon-72x72.png",
      "sizes": "72x72",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-96x96.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-128x128.png",
      "sizes": "128x128",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-144x144.png",
      "sizes": "144x144",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-152x152.png",
      "sizes": "152x152",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-384x384.png",
      "sizes": "384x384",
      "type": "image/png"
    },
    {
      "src": "images/icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "splash_pages": null
}
```

### Include the manifest in the HTML files

In all your HTML files, you must include your `manifest.json` directly in your header link in the following case:
```html
<!-- ./index.html -->
<!-- ... -->
<link rel="manifest" href="/manifest.json">
<!-- ... -->
```

### The Service Worker

You need to create a file `service-worker.js`, for example at the root of your project. You can simply copy and paste the following code. You will just need to change the 2 first variables:
- `cacheName`: Put the name you want. Every time you will change the name it will refresh the cache.
- `filesToCache`: The files that need to be cached. You should put all the HTML, CSS, JavaScript and images. You don't need to include neither `manifest.json`, `service-worker.js`, and all the image icons.

```js
// ./service-worker.js
var cacheName = 'my-awesome-pwa-v-0-1'; // TODO: change with the name you want
var filesToCache = [ // TODO: put the HTML, CSS, JavaScript and image files
  '/',
  '/index.html',
  '/main.js',
  '/style.css'
];

self.addEventListener('install', function(e) {
  console.log('[ServiceWorker] Install');
  e.waitUntil(
    caches.open(cacheName).then(function(cache) {
      console.log('[ServiceWorker] Caching app shell');
      return cache.addAll(filesToCache);
    })
  );
});

self.addEventListener('activate', function(e) {
  console.log('[ServiceWorker] Activate');
  e.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (key !== cacheName) {
          console.log('[ServiceWorker] Removing old cache', key);
          return caches.delete(key);
        }
      }));
    })
  );
  return self.clients.claim();
});

self.addEventListener('fetch', function(e) {
  console.log('[ServiceWorker] Fetch', e.request.url);
  e.respondWith(
    caches.match(e.request).then(function(response) {
      return response || fetch(e.request);
    })
  );
});
```

### Include the service worker inside your main JavaScript file

In your main JavaScript file, you will need to include the following lines.
```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker
           .register('./service-worker.js')
           .then(function() { console.log('Service Worker Registered'); });
}
``` 

It will register the `service-worker.js` file if the variable `navigator.serviceWorker` exists.

## Extra Resources

- [Progressive Web App Checklist](https://developers.google.com/web/progressive-web-apps/)
- [Progressive Web App Tutorial made by Google](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0)
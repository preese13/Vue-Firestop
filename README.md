# vue-firestop

Hello and welcome to my little store.  Here's some notes or context or other comments that you can also find commented in the code itself
  
1. I ran into some issues with the API, but I was able to spoof responses and get everything done. See `~/data` for what my raw data.  This does mean that filtering and sorting are done on the 
   frontend, which isn't optimal but..... the development senario itself was sub optimal so.  I stand by my work.
2. The Navbar isn't its own component, as it was added to the 404 page at the end of development to help navigate back to the home page after an error.  
   in a production environment it would obviously be its own component but I copy and pasted into other components for visual consistency
3. I filtered out images for the product page image carousel based on their size as the opportunity cost for writing that CSS felt 
   out of the scope of an aptitude test
4. I was playing around with the search functionality before the API went down on me, I've commented it out but left it as it was while I was poking around.  I've subsituted
   that with some filter functionality running on the client side.
5. Sorting _kinda_ missed the boat with regard to the API being down.  By the time I got to sorting I was spoofing the data locally so I ended up having to sort locally.
6. Be warned that I've had an inconsistent error when running `yarn lint`.  It sometimes adds `()` around items in the `fetch.then(data =>` block of code.  
   If you run `yarn lint` and requests fail, check that out.
## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

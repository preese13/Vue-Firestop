# vue-firestop

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


Things I'd do with more Time:
1. css can be cleaned up a bit, both in regard to the css declarations themselves and in regard to the actual visual look.  There's always more you can do
2. In the product page I filtered out images based on size which... normally isn't how things would be done.
3. Filtering products by name could be done more cleanly if given more time
4. Sorting products should be done server side in a prod env _because_:  when the user clicks 'load more' the new products are inserted all over the array of product objects because they aren't coming from the server ordered by name
5. The 'load more' button won't cause any bugs, but you can click that button even after all objects have been loaded into the server.
6. THE NAVBAR!!!!! the vue-bootstrap navbar was a late addition as I realized I had a bit of extra time.  Obviously it should be its own component that's loaded into each of the views BUT I was very tired and this isn't a production environment so I figured I'd rather include it as it currently exists rather than just not do it


https://api.stifirestop.com/products?page=1&search=ez

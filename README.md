# F1 Champions Collection

> Ergast API with Vue.js and Axios

This is a simple, one file SPA that pulls specific data from the Ergast API (http://ergast.com/api/f1) for the F1 series between season 2005 and 2015.

I used Vue.js because it's the latest Framework I've played around with and I'm really starting to enjoy its simplicity and ruling. 

I wanted this SPA to be as light as possible (even though it could be lighter, but that defeats the purpose) while maintaining the 'look and feel' I wanted to introduce to the available data. Being able to use 'BootstrapVue' was simply a time-saver and as their factory layout structure meets my needs, implementing the library components felt like second nature.

If I had the time, I would add more SASS functionality as I believe Vue.js and SASS go together like [coffee and stroopwafeltjes](https://www.google.co.za/search?rlz=1C1CHBF_enZA775ZA775&biw=1920&bih=974&tbm=isch&sa=1&ei=aYA8W5_MM-a0gAaJrr_IBQ&q=coffee+and+stroopwafeltjes&oq=coffee+and+stroopwafeltjes&gs_l=img.3...121971.124652.0.124796.11.9.0.0.0.0.282.771.2-3.3.0....0...1c.1.64.img..9.0.0....0.9iPcnN-FfYo).


# Dependancies
>  - "axios": "^0.18.0",
>  - "bootstrap-vue": "^2.0.0-rc.11",
>  - "vue": "^2.5.11"

# Process

> 1. Used "webpack-simple" for start-up template.
> 2. Used "axios" to get data from Ergast API.
> 3. Used "v-for" (Vue.js) to loop through data and render required info.
> 4. Used "BoostrapVue" to style initial layout and add UX functionality.
> 5. Used "SASS" to fine-tune end result.

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```


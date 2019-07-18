# GKK Records rendering using Svelte
Live at https://gkk-styrkelyft.se/rekord/

This is my first attempt at using Svelte. Any notes about best practices I've missed or other ideas are welcome.

## How to build
1) Clone repository
2) Install depencecies, `npm install`
3) Build, preferably using `npm run dev` which also sets up a dev-server at `localhost:5000`

The file `public/test.html` uses the package in the intended environment, which adds a few additional styles.

## How to use with Wordpress
1) Add the files to the server.
2) Create a new page.
3) Set the HTML-content of the new page to the following (assuming the Svelte built files are placed in `/rekord`):
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
<link rel="stylesheet" href="/rekord/bundle.css">
<style>
  .entry-header {display: none;} 
  .site-content {display: none;} 
  h3 {margin-top: 12px;}
</style>

<script defer="" src="/rekord/bundle.js"></script>
```

The Svelte script is set to mount to an element with id "main", which exists by default in wordpress.
The custom styles are used to remove unwanted Wordpress boilerplate.


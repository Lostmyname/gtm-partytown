# Illustrates issue with preset dataLayer and @builder.io/partytown

Partytown does not adopt preset dataLayer values as when gtm is run inside PartyTown it does not have access to the
window so it defaults the dataLayer to an empty array.

```js
// preset dataLayer variables
window.dataLayer = [{
  type: 'homepage',
  test: 'mockdata',
}];
```

```html
<script type="text/partytown">
  // w[l] is undefined
  (function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
  new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
  j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
  'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
  })(window,document,'script','dataLayer','GTM-WVL9LT8');
</script>
```

To work around this you can define the preset SSR dataLayer variables inside the snippet

```html
<script type="text/partytown">
  const presetDataLayer = [{
    type: 'homepage',
    test: 'mockdata',
  }];

  // replace w[l]=w[l]||[]
  // with    w[l]=presetDataLayer;
  (function(w,d,s,l,i){w[l]=presetDataLayer;w[l].push({'gtm.start':
  new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
  j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
  'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
  })(window,document,'script','dataLayer','GTM-WVL9LT8');
</script>

```

Partytown will still accept dataLayer pushes from main thread scripts or from a GTM instance running inside PartyTown.

```html
<script>
  // faux api call
  setTimeout(() => {
    // standard GTM DL push
    dataLayer.push({
      event: 'addedVariables',
      test2: 'mockdata pushed from main',
    })
  }, 2000);
</script>
```

```html
<script type="text/partytown">
  // faux api call
  setTimeout(() => {
    // standard GTM DL push
    dataLayer.push({
      event: 'addedVariables',
      test2: 'mockdata pushed in PT',
    })
  }, 4000);
</script>
```

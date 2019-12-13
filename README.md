# Dynamsoft JavaScript Barcode SDK for Node

Dynamsoft Barcode Reader for Node.js. Also see [Dynamsoft JavaScript Barcode SDK for Web](https://github.com/dynamsoft-dbr/javascript-barcode/).

Support lots of 1D/2D barcode. You can find full support type list in [Dynamsoft Barcode Reader](https://www.dynamsoft.com/Products/Dynamic-Barcode-Reader.aspx).

Mainly Support Node.js version >= LTS 12.

By use lower level API. Can support Node.js LTS 8. We will talk it later.

## How to Use

Check node version.
```shell
> node -v
v12.13.1
```
Because SDK use `worker_threads` to decode in anthor thread. So we need Node.js LTS 12.

The `webassembly` support is from Node LTS 8. We will talk later if you want use SDK in Node.js LTS 8.

---

Get package from npm.
You can also get it from dist.
```shell
> npm install dynamsoft-node-barcode --save
```
The main js is `/dist/dbr.js`.

---

Create a `js` file and start coding.

Require the main js.

Use npm package you can require in this way:
```js
let Dynamsoft = require("dynamsoft-node-barcode");
```

Any case you can require in this way:
```js
let Dynamsoft = require("path/to/dist/dbr.js");
```

---

The SDK use a lot of `Promise`. So why not write code in a `async` function so you can use `await`?
```js
(async()=>{
    // many work will done here
})();
```
Create reader instance.
```js
let reader = await Dynamsoft.BarcodeReader.createInstance();
```
Decode a file.
```js
let results = await reader.decode('path/to/sample.png');
```
You can get `sample.png` in `/example`.

Or you can use a web image instead:
```js
let results = await reader.decode('https://demo.dynamsoft.com/dbr/img/AllSupportedBarcodeTypes.png');
```
The SDK support image type of `png`, `jpg`, `bmp`, `gif`.

>   If you want decode other type like `pdf`, you need other tool to convert it. `pdf` tool develop by `Dynamsoft` will come soon.
>
>   If you you want decode raw image from camera or others with format like `RGBA`.
>   You need use `deocdeBuffer`.
>   See [C++ API decodeBuffer](https://www.dynamsoft.com/help/Barcode-Reader/class_c_barcode_reader.html) for more details.

Show results.
```js
for(let result of results){
    console.log(result.barcodeText);
}
```

---

Run your code.
```shell
> node your-code.js
```

---

Oh, don't forget to set a productKey. Please visit https://www.dynamsoft.com/CustomerPortal/Portal/TrialLicense.aspx to get trial license.

```js
Dynamsoft.BarcodeReader.productKeys = 'PRODUCT-KEYS';
```

Then execute again.

---

Full code with some modification:

```js
let Dynamsoft = require('dynamsoft-node-barcode');
// Please visit https://www.dynamsoft.com/CustomerPortal/Portal/TrialLicense.aspx to get trial license.
Dynamsoft.BarcodeReader.productKeys = 'PRODUCT-KEYS';

(async()=>{
    let reader = await Dynamsoft.BarcodeReader.createInstance();
    for(let result of await reader.decode('https://demo.dynamsoft.com/dbr/img/AllSupportedBarcodeTypes.png')){
        console.log(result.barcodeText);
    }
    reader.destroy();
    process.exit();
})();

```

## Change Decoding Settings

The API is simlar as other version.

```js
let settings = await reader.getRuntimeSettings();
settings.expectedBarcodesCount = 999;
await reader.updateRuntimeSettings();
```

See [Barcode reading settings Guide](https://www.dynamsoft.com/help/Barcode-Reader/devguide/Guide/BarcodeReadingSettings.html#Struct) for basic.

See [C++ API RuntimeSettings](https://www.dynamsoft.com/help/Barcode-Reader/structtag_public_runtime_settings.html) for details.

Vist [DBR Main Online Demo](https://demo.dynamsoft.com/dbr/barcodereaderdemo.aspx) to test a suitable settings.

Ask [Dynamsoft support](https://www.dynamsoft.com/Company/Contact.aspx) for more help.

## Use in Node.js LTS 8

This way the decoding is in main thread.

Basic decoding.
```js
var dbr = require('path/to/dist/dbr-<version>.node.wasm.js');
dbr.onRuntimeInitialized = ()=>{
    dbr.BarcodeReaderWasm.init('{"productKeys":"PRODUCT-KEYS"}');
    var reader = new dbr.BarcodeReaderWasm(false,-1);
    var fs = require('fs');
    var img = fs.readFileSync('./sample.png');
    var resultsInfo = JSON.parse(reader.decodeFileInMemory(new Uint8Array(img)));
    console.log(resultsInfo);
};
```

Change settings.
```js
var settings = JSON.parse(reader.getRuntimeSettings());
settings.expectedBarcodesCount = 999;
reader.updateRuntimeSettings(JSON.stringify(settings));
```






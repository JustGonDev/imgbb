# imgbb-uploader

Lightweight Nodejs module to upload local pictures files to imgbb API and get display URLs in response.  
Primary use is letting imgBB handle the hosting & serving of images.

[![https://nodei.co/npm/imgbb-uploader.png?downloads=true&downloadRank=true&stars=true](https://nodei.co/npm/imgbb-uploader.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/imgbb-uploader)
[![Known Vulnerabilities](https://snyk.io/test/github/TheRealBarenziah/imgbb-uploader/badge.svg?targetFile=package.json)](https://snyk.io/test/github/TheRealBarenziah/imgbb-uploader?targetFile=package.json)
[![dependencies](https://img.shields.io/badge/dependencies-0-brightgreen)](https://www.npmjs.com/package/imgbb-uploader?activeTab=dependencies)
[![Build Status](https://travis-ci.org/TheRealBarenziah/imgbb-uploader.svg?branch=master)](https://travis-ci.org/TheRealBarenziah/imgbb-uploader)

## Compatibility:

**Node >= 8** ( [async/await](https://node.green/) )  
_Care: this module uses `fs` under the hood. **It WON'T work outside the node environment !**_

_You SHOULDN'T use API keys from your frontend anyway. PLEASE DON'T DO THAT !!!_  
[Blah blah blah, my soul is lost already, bring it on](https://stackoverflow.com/a/63669049/11894221)

**File formats supported by ImgBB API:** `.jpg`, `.png`,`.bmp`,`.gif`, `base64`.

## Install

`npm install imgbb-uploader`

## Use with two string params (legacy, LTS)

- I) [Get a free API key from imgbb](https://api.imgbb.com/) ( estimated time ~1 minute )
- II) [Put that in an environment variable](https://www.npmjs.com/package/dotenv)
- III) **imgbbUploader takes _exactly two_ String arguments** : your API key, and the path to your image :

```javascript
const imgbbUploader = require("imgbb-uploader");

imgbbUploader("your-imgbb-api-key-string", "path/to/your/image.png")
  .then((response) => console.log(response))
  .catch((error) => console.error(error));
```

### `.then((response) => console.log(response))` output example :

```javascript
{
  id: '26Sy9tM',
  title: '5e7599f65f27',
  url_viewer: 'https://ibb.co/26Sy9tM',
  url: 'https://i.ibb.co/z5FrMR2/5e7599f65f27.png',
  display_url: 'https://i.ibb.co/z5FrMR2/5e7599f65f27.png',
  size: 260258,
  time: '1609336605',
  expiration: '0',
  image: {
    filename: '5e7599f65f27.png',
    name: '5e7599f65f27',
    mime: 'image/png',
    extension: 'png',
    url: 'https://i.ibb.co/z5FrMR2/5e7599f65f27.png'
  },
  thumb: {
    filename: '5e7599f65f27.png',
    name: '5e7599f65f27',
    mime: 'image/png',
    extension: 'png',
    url: 'https://i.ibb.co/26Sy9tM/5e7599f65f27.png'
  },
   medium: {
   filename: '5e7599f65f27.png',
    name: '5e7599f65f27',
    mime: 'image/png',
    extension: 'png',
    url: 'https://i.ibb.co/14kK0tt/5e7599f65f27.png'
  },
  delete_url: 'https://ibb.co/26Sy9tM/087a7edaaac26e1c940283df07d0b1d7'
}
```

This async function returns a promise:  
`console.log(imgbbUploader(myKey, myPath)) // Promise { <pending> }`  
Use `await` or `.then` as shown above.

_**Note about imgBB API:** the `medium` Object will only be returned for `.png` and `base64` files !_

## Use with options object (more features, yay! )

From version 1.2.0 onward, you can also pass an options object as param.  
Use it to customize filename and/or a set duration after which the image will be deleted, [cf their docs](https://api.imgbb.com/):

```javascript
const imgbbUploader = require("imgbb-uploader");

const options = {
  apiKey: process.env.IMGBB_API_KEY, // MANDATORY

  imagePath: "./your/image/path", // OPTIONAL (unless options.base64string is falsy)

  name: "yourCustomFilename", // OPTIONAL: pass a custom filename to imgBB API

  expiration: 3600 /* OPTIONAL: pass a numeric value in seconds.
  It must be in the 60-15552000 range (POSIX time ftw).
  Enable this to force your image to be deleted after that time. */,

  base64string:
    "iVBORw0KGgoAAAANSUhEUgAAAAIAAAACCAYAAABytg0kAAAAEklEQVR42mNcLVNbzwAEjDAGACcSA4kB6ARiAAAAAElFTkSuQmCC",
  /* OPTIONAL (unless options.imagePath is falsy)

  Enable this to upload base64-encoded image directly as string. (available from 1.3.0 onward)
  Allows to work with RAM directly for increased performance (skips fs I/O calls).
  Beware: options.imagePath will be ignored as long as options.base64string is defined! 
  */
};

imgbbUploader(options)
  .then((response) => console.log(response))
  .catch((error) => console.error(error));
```

**This module is tiny & totally unlicensed: to better fit your need, please fork away !**  
[Basic instructions for tweaking](https://github.com/TheRealBarenziah/imgbb-uploader/blob/master/CONTRIBUTING.md)

## More examples using option object

Using options.base64string:

```javascript
const imgbbUploader = require("imgbb-uploader");

// Some promise of base64 data
const base64str = () =>
  new Promise((resolve) => {
    return setTimeout(() => {
      resolve(
        "iVBORw0KGgoAAAANSUhEUgAAAAIAAAACCAYAAABytg0kAAAAEklEQVR42mNcLVNbzwAEjDAGACcSA4kB6ARiAAAAAElFTkSuQmCC",
      );
    }, 1000);
  });

// Your barebone async function
const myUrl = async (name) => {
  return await imgbbUploader({
    apiKey: "definitely-not-a-valid-key",
    base64string: await base64str(),
    name: name,
  })
    .then((res) => {
      console.log(`Handle success: ${res.url}`);
      return res.url;
    })
    .catch((e) => {
      console.error(`Handle error: ${e}`);
      return "http://placekitten.com/300/300";
    });
};

myUrl("Dolunay_Obruk-Sama_<3");
```

## Learn more

This module doesn't support array uploads. For heavy duty, you'll probably have to work with [fs.readdir](https://nodejs.org/api/fs.html#fs_fs_readdir_path_options_callback) and [async forEach](https://www.npmjs.com/package/async-foreach) (you may also be interested in [path](https://nodejs.org/api/path.html#path_path) ).

For example, you can create a `baseDir.js` file wherever it suits you:

```javascript
module.exports = require("path").join(__dirname);
```

Then you can require this file elsewhere and use something like `path.join(myDirpath, "subfolder")` to dig into directories programmatically. Once there, you can f.e. `fs.readdir` and iterate `forEach` file of that directory.  
See `fs` documentation and Stack Overflow for more inspiration on the matter.

[CHANGELOG](https://github.com/TheRealBarenziah/imgbb-uploader/blob/master/CHANGELOG.md)



# DEPRECATED
This repository wiil be deprecated.

Please use [hc-pdf-server](https://github.com/uyamazak/hc-pdf-server) instead.

Since I hadn't been able to maintain it for a while, I completely rewrote it in TypeScript and changed the library and other things accordingly.
# hcep-pdf-server

Simple and fast PDF rendering server. Using Headless Chrome & Express & Puppeteer.

GET URL or POST HTML returns PDF binary.

You can run this on Docker container, Kubernetes(GKE), or Google App Engine(beta).

### Headless Chrome
<https://developers.google.com/web/updates/2017/04/headless-chrome>

### Express
<http://expressjs.com/>

### Puppeteer
<https://github.com/GoogleChrome/puppeteer>

## Running on Google App Engine Supported (beta)
Instead of Docker container, You can also run this on Google App Engine.
Please use app.yaml and edit it for your purpose.

```
gcloud app deploy --project your-project
```

More detail:
https://cloud.google.com/appengine/docs/standard/nodejs/using-headless-chrome-with-puppeteer

## Getting Started

### Caution
Since this product is supposed to be used within local network (like Kubernetes, GKE), error control and security measures are minimum, please accept only reliable requests. It does not assume direct disclosure to the outside.

### Clone
git clone this repository.

### Customize Dockefile (optionary)
Please specify whether to use Chromium bundled with Puppeteer (recommend) or Chrome, and change locale setting etc. as necessary.

### Install fonts (optionary)
If you convert pages in Japanese, Chinese or languages other than English, you will need to install each font files. Also, you can use WEB fonts, but since it takes a long time for requesting and downloading them, we recommend that install the font files in the server.


```
cp AnyFonts.ttf ./fonts/
```


### Build image

```
sudo docker build -t hcep-pdf-server:latest .
```

### Run

Below example, run with 8000 port.

```
sudo docker run -it --rm \
    -p 8000:8000 \
    --name hcep-pdf-server \
    hcep-pdf-server:latest
```

## Example

### Get request with url parameter

```
curl "http://localhost:8000?url=http://example.com" -o hcep-pdf-get.pdf
```

### POST request with html parameter

```
curl -sS http://localhost:8000 -v -d html=hcep-pdf-ok -o hcep-pdf-post.pdf
```

Please note that because the page does not have a URL, when sending html in POST method, you can not use a relative path.

So, you need to include external files with domain.

Bad, not working

```
<link href="/static/style.css" rel="stylesheet">
```

OK

```
<link href="http://example.com/static/style.css" rel="stylesheet">
```

## Test
Execute mocha in the container run with the below command.

```
% sudo docker exec -e DEBUG="" varuna-hcep-pdf-server mocha
SERVER_URL: http://localhost:8000
TAREGT_URL: https://www.google.com
HTML_TEST_STRINGS: <html>ok</html>


  requests routes
env: development
Listening on: 8000
GET /hc 200 0.987 ms - -
    ✓ Health Check GET /hc
GET / 400 0.188 ms - -
    ✓ GET / with no url
GET /?url=https://www.google.com 200 798.680 ms - 76794
    ✓ GET / with url https://www.google.com (801ms)
POST / 200 104.470 ms - 8374
    ✓ POST / html=<html>ok</html> (106ms)
GET /screenshot?url=https://www.google.com 200 354.910 ms - 27974
    ✓ GET /screenshot with url https://www.google.com (356ms)
POST /screenshot 200 1911.266 ms - 3732
    ✓ POST /screenshot html=<html>ok</html> (1912ms)

  default pdf options
    ✓ empty return default
    ✓ not exists return default
    ✓ A4 in default presets
    ✓ A3 in default presets

  myPdfOptionPresets set
    ✓ format in A4ShowPageNumberFooter is matched
    ✓ displayHeaderFooter in A4ShowPageNumberFooter is matched
    ✓ headerTemplate in A4ShowPageNumberFooter is matched
    ✓ footerTemplate in A4ShowPageNumberFooter is matched


  14 passing (3s)

testing express-app complete! process.exit()

```

## Env variables

### Browser settings
#### HCEP_USE_CHROMIUM
Whether to use chromium attached to puppeteer.
If you want to run this on Google App Engine, you must set it to "true".

default: false (use installed Chrome by Dockerfile)

#### HCEP_CHROME_BINARY
The path of installed google-chrome binary.
If HCEP_USE_CHROMIUM is true, this value is ignored

default: /usr/bin/google-chrome

#### HCEP_PAGE_TIMEOUT_MSEC
Timeout milliseconds of the browser's Page
default: 10000

### Server settings
#### HCEP_PORT
Listen Port by the express app

default: 8000

#### HCEP_APP_TIMEOUT_MSEC
Timeout milliseconds of the express app

default: 30000

#### HCEP_MAX_REQUEST_SIZE
default: 10MB


### PDF settings

#### HCEP_MY_PDF_OPTION_PRESETS_FILE_PATH
If you want to extend the PDF option presets yourself, create a file with reference to "app/my-pdf-option-presets.js.sample" and specify the file path in this variable.

default: none

example: "./my-pdf-options"

app/my-pdf-options.js
```
module.exports.myPdfOptionPresets = {
  'A4ShowPageNumberFooter': {
    format: 'A4',
    displayHeaderFooter: true,
    headerTemplate: '<span></span>',
    footerTemplate: `<div style="font-size:7pt;text-align:center;padding-bottom:5mm;width:100%;">
      <span class="pageNumber"></span> / <span class="totalPages"></span>
    </div>`
  }
}

```


You can make your PDF options. Read the puppeteer API's docs.
<https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pagepdfoptions>


#### HCEP_PDF_DEFAULT_MARGIN
default: 18mm

#### HCEP_DEFAULT_PDF_OPTION_KEY
default: A4

### Test settings
#### HCEP_TEST_SERVER_URL
default: 'http://localhost:8000'

#### HCEP_TEST_TAREGT_URL
default: 'https://www.google.com'

## Author
uyamazak:[blog](http://uyamazak.hatenablog.com/)

This project has been maintained under the support of yagish履歴書 and is actually used for PDF generation.

### yagish履歴書( bizocean co.,Ltd )
https://rirekisho.yagish.jp/
